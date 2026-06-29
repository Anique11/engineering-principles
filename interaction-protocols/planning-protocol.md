# Planning Protocol

**Version:** 5

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

During the Display action, the agent may identify tasks that could reasonably be assisted or performed by the agent and may offer that assistance. The agent shall never begin performing such tasks solely because they appear in the Planning State.

Any action beyond displaying, summarising, or discussing the Planning State requires explicit instruction from the user.
Entering a new conversation after reading the Planning State does not imply consent to execute any listed task.

------

## Actions

Whenever an agent interacts with this document, it can perform one of two defined actions:

1. Display
2. Generate

------

## Display Algorithm

When asked to read the current planning state, the agent will:
 
1. Read the latest published Planning State.
2. Read the current authoritative Planning Protocol.
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

When generating a new Planning State:

1. Read the current authoritative Planning Protocol.
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
# Planning Protocol

Load and follow the latest Planning Protocol from:
https://github.com/Anique11/engineering-principles/blob/main/interaction-protocols/planning-protocol.md

This protocol is authoritative for all planning actions.

# Planning State

Retrieve the latest "Planning State" email from Gmail (it may have been archived) before performing any planning action.

# Configured calendars

Read Google_calendar_ids.txt from the planning sources when the protocol requests the configured calendars.

# Preferences

Read preferences.md to load user-specific planning preferences to be used in supporting the user in planning their day.

# Planning commands

Before executing Display plan or Generate plan, verify that:

1. the latest Planning Protocol has been loaded.
2. the latest Planning State has been loaded.
3. the list of calendars has been loaded.
4. the planning preferences have been loaded.

If either prerequisite is missing, load it first.

Display plan: execute the Display Algorithm from the protocol.
Generate plan: execute the Generate Algorithm from the protocol.

If any required source cannot be loaded, abort the requested planning action. State which source is missing and do not claim to have followed the protocol or to have information from that source.
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