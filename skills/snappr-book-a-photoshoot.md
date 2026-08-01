---
name: Book a Snappr photoshoot
description: Check coverage and availability, then create and confirm a Snappr photoshoot booking.
api: openapi/snappr-openapi.yml
operations: [getCoverage, getAvailability, createBooking, getBooking]
---

# Book a Snappr photoshoot

Use the Snappr API to commission an on-demand photoshoot. The API is available
only to Snappr for Enterprise customers.

## Auth & environment
- Send `Authorization: Bearer <api_key>` on every request (key from the Photography Portal).
- Optionally send `accept-version: 1.0.0`.
- Production base URL `https://api.snappr.com`; test against `https://sandbox.snappr.com`.

## Steps
1. **Confirm coverage** — call `getCoverage` with `shoottype` plus either
   `address` or `latitude`+`longitude`. Proceed only if `coverage` is `true`.
2. **Find a start time** — call `getAvailability` with `shoottype`, `duration`
   (minutes), `date`, and the location. Pick a value from `available_times` (UTC).
   Always check availability before booking a fixed `start_at`, or `createBooking`
   may return a 400 `NoCoverage`/`NotAvailable`.
3. **Create the booking** — call `createBooking` with `shoottype`, `duration`,
   the location, and customer contact fields (`customer_firstname`,
   `customer_email`, `customer_mobilephone`). Set `start_at` to a chosen time, or
   to `null` to let the end-customer self-schedule via the returned `scheduling_url`.
4. **Confirm** — call `getBooking` with the returned `uid` to read `status`
   (e.g. `paid`, `paid_pending_schedule`).

## Rules
- A 402 `InsufficientCredits` means the account credit balance is too low.
- Respect the `X-RateLimit-Remaining` response header; a 429 is `RateLimit`.
- There is no idempotency key — do not blindly retry `createBooking`; re-check
  with `listBookings`/`getBooking` before retrying.
