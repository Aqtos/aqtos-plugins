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
4. No results usually means the filter was too narrow. Try a broader one before saying nothing was found.
5. If a result says `not_permitted` or `not_readable`, stop. The user doesn't have access to that data. Say so
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

## Talking to the user

- Refer to records by their names (the task title, the client's name), not by internal IDs.
- If a tool call fails, read the error, fix the input and try again once or twice before asking the user for help.
- If a request is ambiguous (two clients with the same name, no project given), ask a short question instead of
  guessing.
