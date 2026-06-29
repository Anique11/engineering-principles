# Planning Protocol

**Version:** 2

## Purpose

This document defines the protocol for maintaining the Planning State.

It is configuration, not runtime state.

The Planning State contains only runtime state and references this protocol.

A Planning State is **derived, not edited**. Each new Planning State is generated from the authoritative sources defined below.

---

## Planning State Header

Every Planning State begins with the following header.

```text
# Planning State

Generated: <local date/time>
Protocol version: <Planning Protocol version used to generate this Planning State>
```

Requirements:

- `Generated` records the local date and time immediately before generating the Planning State.
- `Protocol version` records the version of the Planning Protocol used to generate the Planning State.
- Both fields are mandatory.

---

## Planning State Format

The Planning State uses the following structure:

1. Planning State
2. Generated
3. Today
4. Upcoming
5. Outstanding
6. Waiting for others
7. Deferred
8. Recurring
9. Notes / constraints
10. Recently completed
11. Protocol

The Protocol section contains only:

> Read and follow the latest **Planning Protocol**.

No protocol details are duplicated in the Planning State.

---

## Authoritative Sources

Planning State generation combines three sources.

### Google Calendar

Authoritative for appointments.

Always read the next seven days.

### Planning State

Authoritative for non-calendar planning state.

Always use the latest Planning State.

### Current chat

Contains changes since the latest Planning State.

---

## Generation Algorithm

When generating a new Planning State:

1. Read the latest Planning Protocol.
2. Read the latest Planning State.
3. Read Google Calendar for the next seven days.
4. Merge Planning State, Calendar and current chat.
5. Resolve completed items:
   - remove them from active sections;
   - add them to **Recently completed** if they were completed since the previous Planning State.
6. Remove obsolete information.
7. Update the Generated timestamp.
8. Set the Protocol version to the version of the Planning Protocol used for generation.
9. Generate a complete Planning State.
10. Include the Protocol reference.
11. Send as a new Planning State message.

---

## Validation

Before generating a Planning State verify:

- the latest Planning Protocol has been read;
- the latest Planning State has been read;
- Google Calendar has been read;
- the Generated timestamp is current.

If any prerequisite cannot be satisfied:

- do not generate a new Planning State;
- report which prerequisite is missing.

---

## Update Policy

- Small planning changes remain in chat until an explicit Planning State update is requested.
- Every Planning State is a complete snapshot.
- Never send partial Planning States.
- Generate a completely new message.
- Never include quoted previous Planning State messages.
- Never include conversational email history.

---

## Recently Completed

This section contains only items completed since the previous Planning State.

Purpose:

- provide continuity between Planning States;
- make recent progress visible;
- help verify that completed work has been removed from active sections.

When generating a Planning State:

- remove items completed before the previous Planning State;
- include only items completed since the previous Planning State;
- if nothing has been completed, leave the section empty or write **None**.

---

## Protocol Maintenance

This document is configuration, not planning state.

- Preserve it verbatim unless explicitly instructed to modify it.
- Protocol-only updates may be published independently of Planning State updates.
- The latest published Planning Protocol supersedes all earlier versions.
- Newly generated Planning States shall record the protocol version used for their generation.

---

## Planning Preferences and Heuristics

### General

- School-day focused work generally starts around 08:45 after breakfast and litterbox.
- Avoid starting deep-focus work during the final hour before leaving unless already immersed.
- Schedule around energy as well as available time.
- Preserve recovery time when appropriate.
- Minimise activation energy by grouping related tasks.
- Working memory should be offloaded into the Planning State whenever practical.

### Laundry

- Wash duration: 2 hours 45 minutes.
- Preferred hanging window: within 2 hours after completion.
- Machine supports delayed start up to 13 hours.
- Plan around the hanging window rather than machine start.
- Treat laundry as a workflow (sort → wash → hang → drying rack), not as a single task.

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