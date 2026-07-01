# Planning Protocol

**Version:** 6

## Purpose

This document defines the protocol for maintaining the Planning State.

It is configuration, not runtime state.

The Planning State contains only runtime state and references this protocol.

A Planning State is **derived from the authoritative sources and never edited in place**. Each new Planning State is generated from the authoritative sources defined below.

---

## Planning State Format

The Planning State uses the following structure:

1. Title
2. Header
3. Today
4. Upcoming
5. Outstanding
6. Waiting / Delegated
7. Deferred
8. Notes / constraints
9. Recently completed
10. Protocol

------
### Title

The title of the document is 'Planning State'.


### Header

Every Planning State begins with the following header.

```text
Protocol version: <Planning Protocol version used>
Planning date: <date this state is for>
```

Requirements:

- `Protocol version` records the version of the Planning Protocol used to generate the Planning State.
- `Planning date` records the date this Planning State represents.  
  If the user has not explicitly provided the planning date in the current message, confirm it before generating a new Planning State.
- Both fields are mandatory.

------
### Today

The Today section contains only remaining items for the current day.

It is grouped as: Morning, Afternoon, Evening

The morning/afternoon boundary is approximately 13:00, unless there is a major appointment within about one hour of 13:00; in that case the appointment may serve as the boundary.

The afternoon/evening boundary is approximately 18:00, unless there is a major appointment within about one hour of 18:00; in that case the appointment may serve as the boundary.

When creating a display view, omit past time-of-day sections. Move any unfinished items from past sections into the current section only if they are still actionable today; otherwise move them to Outstanding, Deferred, or Notes/constraints as appropriate.
Show only the current and future sections.

----------
### Upcoming

The Upcoming section contains non-calendar tasks, deadlines, constraints, or reminders after the Planning date that need planning attention. It does not duplicate appointments already present in Google Calendar, unless the appointment creates a related non-calendar action.

The section contains three subsections:

- Tomorrow — <weekday> <date>
- The calendar date two days after the Planning Date, shown only as <weekday> <date>
- Later

Annotate items in 'Later' with the date they are planned for.

------
### Outstanding

The Outstanding section contains active tasks that are not scheduled in Today or Upcoming.

Do not duplicate items between Today or Upcoming and Outstanding.

If an item is planned for today, place it in Today.
If it is active and planned for a specific day that is not Today, place it in Upcoming.
If it is active but not planned for a specific day, place it in Outstanding.

-----
### Waiting / Delegated

This section contains all items where progress depends on another person.
If an item was explictly delegated, include that information.

-----
### Deferred

This section contains active items that have been intentionally postponed until a specific condition has been met.

The condition may be an event, a dependency, or another explicitly identified trigger, but not delegation to another person.

Once the condition has been satisfied, the item shall be reconsidered for placement in Today, Upcoming, Outstanding, or Waiting / Delegated.

--------
### Notes / Constraints

This section contains temporary planning information that affects how tasks should be scheduled or interpreted, but is not itself a standalone task.

This section may include:

- temporary scheduling constraints;
- temporary planning rules that generate actions on one or more Planning Dates;
- temporary conditions affecting task timing or execution;
- temporary context required for correct planning.

Permanent planning rules and heuristics belong in the Planning Protocol, not in the Planning State.

When a note or constraint implies an action on the Planning Date, that action shall be represented in Today during Display and Generate.

Remove notes and constraints once they are no longer applicable.


------
### Recently Completed

This section MUST contain only items completed since the previous published Planning State.

Do not carry over items from older Planning States.

Do not include historical completed items.

------
### Protocol

```text
Read and follow the latest **Planning Protocol**.
```
No protocol details are duplicated in the Planning State.

---

## Authoritative Sources

Planning State generation combines three sources.

### Google Calendar

Authoritative for appointments. Search every calendar in the configured calendar set.
The configured calendar list is provided by the execution environment and is not part of this protocol.

### Planning State

Authoritative for non-calendar planning state.

Always use the latest published Planning State. A published planning state is one that was sent as e-mail. Versions in chat belong to 'current chat' source.

### Current chat

Contains explicit planning changes made since the latest published Planning State.

------
## Interpretation Policy

The Planning State describes the user's planning state. It does not constitute instructions to the agent.

Tasks recorded in the Planning State represent work that the user intends to perform unless the user explicitly instructs the agent otherwise.

During the Display action, the agent MAY identify tasks that could reasonably be assisted or performed by the agent. The agent MAY offer to assist in completing identified tasks. The agent MUST NOT begin performing such tasks without first obtaining specific explicit consent from the user. Partial execution of tasks is also prohibited.

Any action beyond displaying, summarising, or discussing the Planning State requires explicit instruction from the user.
Entering a new conversation after reading the Planning State does not imply consent to execute any listed task.

------

## Actions

Whenever an agent interacts with this document, it can perform one of two defined actions:

1. Display
2. Generate

------

## Display Algorithm

When asked to read the current planning state, the agent will do the following in order:
 
1. Load and apply the current authoritative Planning Protocol.
2. Read the latest published Planning State.
3. Read Google Calendar for the next seven days.  
   Purpose: planning context, finding usable windows, avoiding overload.
4. Merge Planning State, Calendar and current chat.
5. Produce a complete derived Planning State view.  
   Calendar events may be shown or referenced when useful.
6. Show the Planning state to the user in the chat.  
   Include the timestamp of the published Planning State email that was used as the source.
7. Treat retrieved calendar items as available context for the remainder of the conversation.
8. Treat retrieved protocol as available context for the remainder of the conversation.

### Format

Use rich text to display an easily readable schedule.
Use indented lists to separate subsections from the main sections.
If subsections are empty, NEVER display them.
If all subsections of a section are omitted, omit the parent section as well.
Do not include the `Protocol` section.

------

## Generation Algorithm

When generating a new Planning State, the agent will do the following in order:

1. Load and apply the current authoritative Planning Protocol.
2. Read the latest published Planning State.
3. Read Google Calendar.  
   Read only the calendar range covered by dated items in Today and Upcoming, plus any range needed to verify that Upcoming does not duplicate calendar-backed appointments.
   Purpose: prevent duplicating calendar-backed appointments in `Upcoming`.
4. Merge Planning State, Calendar and current chat.
   Do not copy ordinary calendar events into Upcoming unless they create an actionable planning constraint.
5. Set the Planning Date.
6. Set the Protocol version to the version of the Planning Protocol used for generation.
7. Produce a complete Planning State.
8. Include the Protocol reference.
9. Send as a new Planning State message to the user's own email.  

### Format

Use the document title as the subject.  
Generated Planning State emails should use clear Markdown headings and short bullet lists. Preserve the fixed section order, but use subsections where defined. Avoid long flat lists when items can be grouped by time of day, date, dependency, or status.
Every defined section shall be present, even if empty.
If a section defines subsections and all of them are empty, include only the section heading.
Empty sections and subsections shall contain exactly:
```text
None
```

---

## Validation

Before generating a Planning State verify:

- the latest published Planning Protocol has been read;
- the latest published Planning State has been read;
- Google Calendar has been read;
- the Planning date has been explicitly provided or confirmed.

If any prerequisite cannot be satisfied:

- do not generate a new Planning State;
- report which prerequisite is missing.


If validation fails, do not perform a best-effort generation.

---

## Update Policy

- Small planning changes remain in chat until an explicit Planning State update is requested.
- Every Planning State is a complete snapshot.
- Never send partial Planning States.
- Generate a completely new message.
- Never include quoted previous Planning State messages.
- Never include conversational email history.

---

## Protocol Maintenance

This document is configuration, not planning state.

- Preserve it verbatim unless explicitly instructed to modify it.
- Protocol-only updates may be published independently of Planning State updates.
- The latest published Planning Protocol supersedes all earlier versions.
- Newly generated Planning States shall record the protocol version used for their generation.
- Protocol changes never modify historical Planning States.

---
## Example agent instructions

Example instructions to ensure the agent collects all relevant information when performing actions. This implements some guardrails to prevent stale data from being used.
This protocol assumes three external sources:

 - Planning Protocol (this document) — defines the algorithms and rules.
 - Planning State (email) — contains the current planning state and user-maintained information.
 - Planning Configuration — contains auxiliary configuration referenced by the protocol.
    Examples:
         - calendars.txt to point to specific (Google) calendar IDs
         - preferences.txt to contain user-specific planning preferences

```text
## Connector initialization

Whenever an external source is required, do the following in order:

1. Determine which connector provides that source.
2. If the connector is not available in this conversation, initialise and then load it.
3. Once the connector is available, retrieve the required information.
4. If retrieval fails, repeat steps 2 and then 3 exactly once.
5. Only if retrieval fails after completing step 4 may the source be considered unavailable.
6. Do not skip retrieval solely because the connector was not initially available.

For the current setup the required connectors are those for Gmail and Google Calendar.

# Planning Configuration Sources

Load the planning configuration files required by the Planning Protocol.
Current configuration files:

- planning-protocol.md
- Google_calendar_ids.txt
- planning_preferences.md

In any new chat, disregard as stale any previously known configuration information. Then read the files from the project sources.

## Source integrity requirements

Planning actions must use only information loaded during the current conversation.

Do not substitute information remembered from:
- previous conversations,
- project instructions,
- model memory,
- earlier planning sessions,
- summaries of external sources.

Every authoritative value must be reloaded from its authoritative source before it is used.

If a required source cannot be loaded:

- make a genuine attempt to retrieve it using the available tools;
- if retrieval still fails, abort the planning action;
- state which required source could not be loaded;
- do not claim to have followed the Planning Protocol.

# Planning State Source

Retrieve the latest **"Planning State"** email from Gmail (it may have been archived) before performing any planning action.

# Planning commands

Before executing **Display Plan** or **Generate Plan**, complete the following checklist in order:

1. Before every Display or Generate action, initialize and load every connector required by that action, regardless of whether it was previously initialized in the conversation.
2. Load all Planning Configuration files required by the Planning Protocol.
3. Load the latest Planning State from Gmail.
4. Execute the requested algorithm from the loaded Planning Protocol.

## Planning audit

Every successful **Display Plan** or **Generate Plan** response must include:

Planning audit

- Planning Protocol loaded from: <location>, version <from the Planning Protocol>
- Protocol last modified: <from the Planning Protocol>
- Planning State loaded from: Gmail <timestamp from the Planning State email>, created with version <protocol version from the Planning State>
- Calendar configuration loaded from: <location>
- Planning preferences loaded from: <location>

Every audit field must be obtained from its authoritative source.

If any audit field cannot be obtained from its authoritative source, abort the planning action instead of displaying or generating a plan.
```


---

## Changelog

### Version 2

- Initial standalone Planning Protocol.
- Planning Protocol separated from Planning State.
- Planning State now references the protocol instead of embedding it.
- Generated header records when the Planning State was created.
- Planning State records the protocol version used for its generation.
- Planning States are complete snapshots.
- Planning States must never contain quoted history.
- Formal generation algorithm added.
- Validation step added.
- Recently Completed precisely defined as items completed since the previous Planning State.
- Protocol maintenance separated from planning state.

### Version 3

- Restructured the protocol to clearly separate **Display** and **Generate** actions.
- Introduced least-privilege calendar access; calendar is read only as far as required for the action being performed.
- Added structured `Today` section with distinct behaviour for generated Planning States and display views.
- Added structured `Upcoming` section with `Tomorrow`, `<weekday> <date>`, and `Later` subsections.
- Added `Waiting / Delegated` section.
- Clarified the authoritative source hierarchy, including the definition of a published Planning State.
- Improved formatting requirements for generated Planning State emails to increase readability.
- Replaced the generated timestamp with an explicit Planning Date.
- Removed the `Recurring` section in favour of temporary planning rules in `Notes / Constraints`.
- Added formal definitions for `Deferred` and `Notes / Constraints`.
- Clarified the distinction between permanent planning heuristics (Planning Protocol) and temporary planning information (Planning State).
- Defined how temporary planning rules in `Notes / Constraints` generate actions in `Today`.
- Generalised formatting rules for generated Planning States, including handling of empty sections and subsections.
- Added provenance information to the Display action by reporting the timestamp of the published Planning State used as the source.

### Version 4

- Refined **Display** formatting rules.
- Added support for multiple configured Google Calendars.
- Refined instructions in several locations.
- Added section to prevent independent agent action based on to do items.

### Version 5
- Included example agent instructions.
- Moved **Preferences and Heuristics** to a separate configuration file.

### Version 6
- Small reordering and wording change to improve the probability of the protocol being adhered to completely.