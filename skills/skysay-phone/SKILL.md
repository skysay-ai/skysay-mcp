---
name: skysay-phone
description: Use Skysay to give an agent a real phone number, check coverage and prices, place AI voice calls, send SMS and read call results. Use when the user asks to call or text someone, get a phone number, or check a Skysay call. Always show the cost and get the user's approval before any call, text, number purchase or top-up.
---

# Skysay phone

Skysay is a hosted MCP server (`https://api.skysay.ai/mcp`, OAuth) that gives an agent real phone numbers for calls and SMS. Tool names below come from https://skysay.ai/docs/tool-reference. Treat the live `tools/list` as canonical.

## Ground rules

- **Approval before anything that contacts a person or spends money.** Before a call, an SMS, a number request or a wallet top-up, show the user exactly what will happen and the expected cost, then wait for an explicit yes. OAuth consent is not approval of an action.
- **Never invent values.** Phone numbers, approver names, purposes and message text come from the user, not from you. Do not fill `user_authorization` with placeholders.
- **Never ask for secrets in chat.** No email codes, passwords, API keys, card numbers or payment tokens. Sign-in happens on Skysay's own page.
- **Only your own workspace.** Leave `organization_id` out; it defaults to the connected workspace. Other workspaces are refused.
- **An ID is not a result.** A call ID does not mean the call was answered; a message ID does not mean it was delivered; a number request does not mean the number is active. Check the status.
- **Do not retry refusals.** `policy_blocked`, `live_blocked` and `needs_review` need a person to fix the named issue. Do not loop.
- **Don't overpromise coverage.** Numbers are offered in 100+ countries, but channels differ by country and number type. Outside the US, Canada, UK and Mexico, outbound calls and SMS need carrier review. Check live data; never promise a channel.

## 1. Connect and check the workspace

1. Call `account_overview` and summarise the wallet, numbers and agents.
2. If a tool returns a `403` scope challenge (`InsufficientMcpScope`), tell the user which permission is missing (for example `calls:write`, `sms:send`, `numbers:write`, `wallet:write`) and ask them to reconnect and tick it on the Skysay consent screen.
3. `get_capabilities` shows the workspace's channels, destinations, caps and usage.

The $5 welcome credit is only for browser testing inside Skysay. It does not pay for numbers, phone calls or SMS, which need purchased funds.

## 2. Get a number

1. Ask for the country (full name) and, optionally, city or area code.
2. Call `get_coverage`, `get_number_catalog` and `check_number_requirements` for that country and number type.
3. Show the user: number type, voice/SMS capability, setup fee, monthly fee and any documents or carrier review needed.
4. Only after the user approves those prices, call `request_number` with `customer_purchase_confirmed: true`.
5. The request may wait for funds, documents or operator approval. Use `list_numbers` and `get_number_rules` to check whether it is actually active and which channels it has.

## 3. Place a call

1. Confirm there is an active, outbound-capable caller number (`list_numbers`, `get_number_rules`) and a configured voice agent (`list_agents`, `get_agent`).
2. Call `evaluate_outbound_policy` for the planned call to get the policy decision and cost.
3. Show the user the caller number, recipient, the agent's task, and the expected cost. Wait for an explicit yes.
4. Call `create_outbound_call` once, recording the real approver and reason. On an uncertain response, check the existing call before trying again; never dial twice.
5. Poll `get_call` until it reaches a terminal status, then report what happened. With `recordings:read`, `get_call_transcript` returns the transcript.

Only call numbers the user owns or people who agreed to be called. Follow AI-disclosure and recording rules for the user's use case.

## 4. Send a text

1. Check the sender number can send SMS (`get_number_rules`). A listed country does not mean SMS sending is enabled.
2. Call `evaluate_outbound_policy` for the message.
3. Show the user the sender, recipient, exact text and cost. Wait for an explicit yes.
4. Call `send_sms` once. Do not auto-retry an ambiguous send; check `list_conversations` (`sms:read`) first.
5. Report the delivery status. `live_blocked` means it was not sent and will not be sent later.

Telemarketing, mass outreach, lead generation and other blocked categories are refused by policy.

## 5. Check calls and messages

- `list_calls` and `get_call` for status and outcome.
- `get_call_transcript` and `get_call_recording` need `recordings:read`.
- `list_conversations` reads SMS threads (needs `sms:read`).
- Paged tools return `pagination.has_more` and `next_cursor`. Keep paging only while `has_more` is true.

## 6. Add funds (only if asked)

1. Read the wallet currency with `account_overview`.
2. Show the exact amount, currency and purpose. Wait for an explicit yes.
3. With the user's separately granted `wallet:write`, call `create_wallet_topup` and give them the Stripe checkout link. Funds count only after Skysay confirms payment.

## Troubleshooting

| Symptom | What to do |
| --- | --- |
| 401 or a sign-in loop | Reconnect Skysay and finish OAuth. |
| 403 scope challenge | Reconnect and tick the named permission. |
| Call or SMS refused | Read `get_capabilities` and `get_number_rules`; carrier review, channel support, funds or caps may block it. |
| Number request waiting | Check required documents and funds; it is not active yet. |

Docs: https://skysay.ai/docs/grok-bot · https://skysay.ai/docs/mcp · Support: https://skysay.ai/contact
