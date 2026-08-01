---
name: Retrieve photoshoot images and videos
description: List bookings and download the delivered images and videos for a Snappr shoot.
api: openapi/snappr-openapi.yml
operations: [listBookings, listBookingImages, listBookingVideos]
---

# Retrieve photoshoot images and videos

## Auth & environment
- `Authorization: Bearer <api_key>`; base URL `https://api.snappr.com`.

## Steps
1. **Find the booking** — call `listBookings` (paginate with `limit`/`offset`;
   response carries `results`, `count`, `limit`, `offset`, `total`). Match on your
   `internal_id` or `title`, then take the booking `uid`.
2. **Get images** — call `listBookingImages` with the `booking_uid`. Each result
   has `url_original` (full-resolution, time-limited S3 URL) and `url_thumb`.
3. **Get videos** — call `listBookingVideos` with the same `booking_uid` for any
   `url_original` video assets.

## Rules
- Asset URLs are signed and expire — fetch/store them promptly, do not cache the URL.
- Images/videos are only present once the shoot `status` indicates delivery.
- Respect `X-RateLimit-Remaining`.
