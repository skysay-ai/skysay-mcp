---
name: skysay-confirm-business-hours
description: Call a business through Skysay to confirm opening hours for a particular branch and date, including holiday hours and last-admission times. Use when the user wants opening times verified by phone.
---

# Confirm business hours through Skysay

Use the [Skysay phone skill](../skysay-phone/SKILL.md) and its [calling workflow](../skysay-phone/references/phone-tasks.md) for connection, authorization, call submission and evidence. This skill defines the question and the facts required for success.

Before calling `create_outbound_call`, load both shared files and satisfy their authorization and submit-once steps. If either file is unavailable, stop and repair the installation before dialling.

## Resolve the question

Identify the business and branch, verified phone number, the specific date or weekday schedule to check, and the branch's timezone. Resolve “tomorrow” to a calendar date. Distinguish public opening hours from a specific service's hours, such as the kitchen, pharmacy counter, deliveries or last admission. Ask for the service or date when ambiguity changes the answer.

The caller identity comes from the user-approved plan or the selected agent. Use known local business hours when choosing when to call and follow the user's timing instructions.

## Call brief

Include these questions and boundaries in the per-call instructions:

- Verify that the agent reached the intended branch.
- Ask whether it is open on the exact local date and request opening and closing times, including any midday break.
- Ask whether holiday or special-date hours override the ordinary schedule when relevant.
- Confirm any user-requested service cutoff or last-admission time separately.
- Read back the date, local hours and any exceptions, and wait for confirmation.
- Keep the enquiry informational. Return booking, purchase or callback proposals to the user for a decision.

Use `purpose: "status_check"` when exposed. Send the instructions in `create_outbound_call.runtime_config.instructions` with the selected caller number and compatible hosted agent.

## Assess the answer

An explicit answer that the business is closed is a successful hours check when the intended branch and requested date are confirmed. It satisfies the informational goal. Use `unavailable` when the business cannot provide the requested hours. Use `needs_user` for a wrong or ambiguous branch, or a follow-up commitment requiring a decision.

Use `succeeded` only when the other party confirms the requested branch and date or schedule, the open/closed answer, and all material requested hours and service cutoffs. An automated recording can establish success when it explicitly gives those exact facts for the requested date. A generic weekly greeting alone cannot establish holiday hours. Record whether the source was a person or an automated message.

If captions are missing or the time references are ambiguous, use `unknown`. Report the date, local timezone, confirmed open/closed status, opening intervals or service cutoffs, evidence source and Skysay call ID. This outcome describes an enquiry and makes no reservation.
