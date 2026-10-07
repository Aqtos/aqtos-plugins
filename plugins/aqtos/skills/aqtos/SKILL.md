---
name: aqtos
description: How to look things up and get things done in Aqtos (tasks, projects, clients, leads, invoices, expenses, calendar, employees) through the Aqtos tools. Use whenever the user asks about or wants to change anything in their Aqtos workspace.
---

# Working in Aqtos

Aqtos is the user's business platform: projects and tasks, CRM (clients, leads, contacts), finance (invoices,
expenses), HR, documents and calendar. Every Aqtos tool runs as the signed-in user, with exactly their permissions.

## Start of a conversation

Call `get_session_context` once to learn who the user is, their role, today's date and their time zone. Use it to
turn "today", "this week" or "next month" into real dates, and to answer "my ..." questions.

## Answering questions (reading)

1. Call `get_aqtos_entity_schema` for the entity type before querying it. Never guess field names, filter operators
   or enum values.
2. Query with `query_aqtos_entities`. For current or open work, leave out finished states (DONE, CANCELLED,
   COMPLETED, PAID, VOID, CLOSED) unless the user asks for them.
3. Lists such as assignees, members or items belong to the parent record: to find who is on Project X, query the
   Project, not Person.
4. For who is out today, query `AbsentEmployeeView`: everyone currently away and when they're back. For time off
   over a period, such as vacation days taken in a month, query `AbsenceDaysView`; most users see only their own
   absences there, HR sees everyone's. To count days taken in a period:
   - Count only APPROVED and FINISHED absences; PENDING and UNAPPROVED ones were not taken.
   - Include every absence that overlaps the period (`fromDate` before its end and `toDate` on or after its
     start), not only those that start in it.
   - `days` is the working days of the whole absence. For one that crosses the start or end of the period, count
     only its working days inside the period.
5. No results usually means the filter was too narrow. Try a broader one before saying nothing was found.
6. If a result says `not_permitted` or `not_readable`, stop. The user doesn't have access to that data. Say so
   plainly and don't look for it another way.

## Making changes (writing)

1. Find the right command with `search_aqtos_commands` and read its schema: required fields, types, and which
   fields expect an ID.
2. Turn every name the user gave into an ID with `resolve_aqtos_name`, using the entity type from the field's
   `identifier` annotation. Never put a name in an ID field. IDs that came back from a query can be used as they
   are. With several close matches, or `"truncated": true`, ask the user which one they mean instead of picking.
3. Person, Employee and User share the same ID: `Employee:abc` is `Person:abc`.
4. For anything that deletes, archives, cancels, voids or changes many records at once, first tell the user exactly
   what will happen and wait for a clear yes.
5. Run it with `execute_aqtos_command`. For a plain new task or calendar event, `create_task` and
   `create_calendar_event` are simpler.
6. After a change, confirm what was done in one short sentence.

## Files and attachments

Commands take attachments as document IDs (`Document:...`) in their `attachments` field, so a file has to be in
Aqtos first. To attach a file the user has, for example one attached in this chat:

1. If you have the file's exact bytes and it is small, call `upload_document` with them in base64. Only the real
   bytes will do: never a summary, the extracted text or a re-created file. Aqtos checks the content and refuses a
   file that is cut off or altered.
2. If you don't have the exact bytes, the file is large, or `upload_document` returns an error, call
   `create_upload_link`. Give the user the link, ask them to upload the file there and tell you when they're done.
   The link works once, only for them, for 15 minutes.
3. When they say it's done, call `get_uploaded_documents` with the `uploadId`. If it is still PENDING, ask them to
   finish the upload instead of calling it again and again.
4. Put the returned document IDs in the command's `attachments` field.

For a file the user only has as a public link, `upload_document_from_url` stores it straight from the link. A link
that opens a page showing the file, such as a Google Drive "view" link, won't work: ask for the direct download
link, or use the upload link instead.

## Talking to the user

- Refer to records by their names (the task title, the client's name), not by internal IDs.
- If a tool call fails, read the error, fix the input and try again once or twice before asking the user for help.
- If a request is ambiguous (two clients with the same name, no project given), ask a short question instead of
  guessing.
