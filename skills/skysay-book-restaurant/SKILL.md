---
name: skysay-book-restaurant
description: Book a restaurant table by phone through Skysay, negotiate within the user's permitted times and report the confirmed reservation. Use for table bookings or dining reservation calls.
---

# Book a restaurant through Skysay

Use the [Skysay phone skill](../skysay-phone/SKILL.md) and its [calling workflow](../skysay-phone/references/phone-tasks.md) for connection, approval, call submission and evidence. This skill supplies the reservation brief and success requirements.

Before calling `create_outbound_call`, load both shared files and satisfy their authorization and submit-once steps. If either file is unavailable, stop and repair the installation before dialling.

## Collect the reservation

Resolve the restaurant and branch, verified phone number, absolute date, restaurant timezone, party size and booking name. Establish the preferred time, any acceptable time window and ranked alternatives. Share a callback number only when the user supplied or approved it. Carry special requests such as seating or accessibility as requests, and verify them separately if they are essential to success.

If the user says “around seven”, clarify or propose the acceptable window. An alternative day, branch, seating type or price is accepted only within their authorization. If a deposit, card guarantee or cancellation fee is required, return the terms for the user's decision unless already approved.

Follow the user's chosen booking channel. If they requested the easiest available booking path, check a suitable online route when the client supports it. If a reservation is already confirmed online, report that confirmation and avoid a duplicate call. A request to book by phone uses this call workflow directly.

Use the restaurant's known local opening hours and the user's approved call time. Put missing material details into one concise question. Continue preparing the brief and resource checks while waiting.

## Call brief

Include these facts in the per-call instructions, using the shared calling template:

- Ask for the preferred date, time and party size under the approved booking name.
- Try the authorized alternative times in the agreed order. A restaurant offer inside the approved window may be accepted.
- Request essential accommodations and establish whether the restaurant confirmed them.
- Read back the restaurant branch, exact local date and time, party size and booking name. Wait for the host to confirm the reservation.
- Ask for any reservation reference and relevant arrival instructions. Record a reference only when the host provides it.
- Return any out-of-window offer or new charge to the user. Leave only the user-approved voicemail, or end without one.

Use `purpose: "reservation"` in the call request when the schema exposes purpose. Store the task in `runtime_config.instructions`; select an existing compatible hosted agent.

## Assess the result

Report `succeeded` only when the host's conversation confirms the booking, the exact local date and time, party size and booking name, within the approved alternatives. The voice agent's request to reserve is not confirmation. A host saying they will check and call back leaves the booking unconfirmed: report `needs_user` and identify the approved callback number if established. If none was agreed, explain that the restaurant may call back the Skysay caller number and show that chosen number. Do not imply this skill is monitoring incoming calls.

Use `unavailable` for an explicit refusal of the requested and permitted alternatives. Use `needs_user` for a deposit, an unapproved alternative or a conditional offer needing a decision. If the confirmation turn is missing, use `unknown` even when the summary estimates success. A voicemail without confirmation uses `no_answer`.

After a settled call with an `unknown` booking result, or after a voicemail requesting a booking, an authorized follow-up must first ask whether any reservation already exists under the booking name across all authorized dates, time windows, branch choices and other alternatives the first call could have accepted. Search beyond the preferred slot. If one exists, confirm its details and assess it as the original result without making another booking. If the lookup is inconclusive, report `unknown`; request a new booking only after the host establishes that no relevant reservation exists. A disconnected call after speaking can also have made a booking. Ordinary approved retries are appropriate for confirmed pre-contact failures, busy or unanswered calls only when there was no conversation or message that could have created a reservation. Reconcile an uncertain call-creation request under the shared submit-once rules before making any follow-up call.

For a confirmed reservation, give the restaurant, branch, date, local time and timezone, party size, booking name, essential accommodations, reference if provided, and the Skysay call ID. Create a calendar event or send a confirmation only when that action is also authorized.
