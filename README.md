# AA Events Calendar — Editor's Guide

A single-page calendar web app that pulls its events from one spreadsheet. This README is written for the **non-technical person who maintains the spreadsheet** — no coding required. Open the spreadsheet, add or change a row, save, and the calendar updates for everyone.

---

## Table of contents

- [What this is](#what-this-is)
- [How it works](#how-it-works)
- [Using the calendar (what users see)](#using-the-calendar-what-users-see)
- [Managing events (the spreadsheet)](#managing-events-the-spreadsheet)
  - [Column reference (all 27 columns)](#column-reference-all-27-columns)
  - [Categories and colors](#categories-and-colors)
  - [Formats, Status, Language, yes/no fields](#formats-status-language-yesno-fields)
  - [Date & time formats](#date--time-formats)
- [Event recipes (copy-paste examples)](#event-recipes-copy-paste-examples)
  1. [One-time in-person event](#1-one-time-in-person-event)
  2. [One-time virtual (Zoom) event](#2-one-time-virtual-zoom-event)
  3. [Hybrid event (in-person + Zoom)](#3-hybrid-event-in-person--zoom)
  4. [Multi-day event](#4-multi-day-event)
  5. [All-day event (no specific times)](#5-all-day-event-no-specific-times)
  6. [Tentative event](#6-tentative-event)
  7. [Private event (home party, hidden address)](#7-private-event-home-party-hidden-address)
  8. [Monthly recurring meeting](#8-monthly-recurring-meeting)
  9. [Last-Saturday recurring meeting](#9-last-saturday-recurring-meeting)
  10. [Weekly recurring meeting (ongoing)](#10-weekly-recurring-meeting-ongoing)
  11. [Bounded series (e.g., every Wed in April only)](#11-bounded-series-eg-every-wed-in-april-only)
  12. [Speaker meeting — one-time](#12-speaker-meeting--one-time)
  13. [Speaker meeting — with homegroup](#13-speaker-meeting--with-homegroup)
  14. [Speaker series — recurring](#14-speaker-series--recurring)
  15. [Occurrence override — change one month only](#15-occurrence-override--change-one-month-only)
  16. [Occurrence override — mark one month Tentative](#16-occurrence-override--mark-one-month-tentative)
- [Common mistakes and how to fix them](#common-mistakes-and-how-to-fix-them)
- [Troubleshooting](#troubleshooting)
- [Advanced: shareable URLs](#advanced-shareable-urls)
- [For developers](#for-developers)

---

## What this is

A mobile-first, single-page calendar for AA events. Opens in any modern browser. No login, no backend, no database — just one published Google Sheet feeds it.

The editor's job: **keep the spreadsheet correct**. Everything else (colors, filters, recurring expansion, printing, sharing) the app handles automatically.

---

## How it works

1. Events live in a **Google Sheet** (`meetings.csv` when exported).
2. The sheet is **Published to the web as CSV** (File → Share → Publish to web → CSV).
3. The calendar app fetches that CSV every time someone opens it.
4. Changes you save in the sheet appear in the calendar within a minute (Google's publish delay).

You do not need to touch any code to add, edit, or remove events. You only edit the sheet.

---

## Using the calendar (what users see)

| Area | What it does |
|------|---------|
| **Topbar — Calendar icon** | Title / brand |
| **Topbar — Clock icon** | Jump to today in Month view |
| **Topbar — Funnel icon** | Open the filter drawer (Category / Format / Language / Status). Live-applies as you tap chips. Badge shows active filter count. |
| **Tabs** | Month · Week · Today · This Weekend · Next 7 days · Next 30 days · Upcoming · Recurring · All · ★ Starred |
| **Search bar** | Searches title, category, notes, venue, city, platform. Private events do NOT leak address or meeting info. |
| **Event card** | Tap to open details. Star button saves to Starred tab (persists on the device). |
| **Calendar cell** | Tap a day with events to see a list for that day. Today is highlighted. |
| **Detail sheet** | Full details + Share + Add to calendar (.ics). Meeting ID, passcode, and Zoom link each have a one-tap **copy** button. |
| **Live badges** | Red pulsing **Now** while an event is in progress. Amber **Starts in X min** within 60 minutes before start. |
| **Print** | `Ctrl+P` / `Cmd+P` prints the current view cleanly (topbar, drawer, search hidden). |

URLs are shareable: tabs, search, selected event, and open day all encode in the URL.

---

## Managing events (the spreadsheet)

Each **row** = one event. Column order matters — do not reorder columns.

### Column reference (all 27 columns)

| # | Column | Required? | Format | Purpose |
|---|--------|-----------|--------|---------|
| 1 | `id` | Yes, unique | Number | Unique row ID. Each row must have its own number. |
| 2 | `title` | Yes* | Text | Event name shown everywhere. *Blank allowed only on override rows — see below.* |
| 3 | `category` | Yes | One of the list | Drives the color. See [Categories and colors](#categories-and-colors). |
| 4 | `start_date` | Yes** | `YYYY-MM-DD` | Date the event starts. **Leave blank** for unbounded recurring templates. |
| 5 | `start_time` | Recommended | `HH:MM` 24-hour | Start time in local time. Blank = treated as midnight. |
| 6 | `end_date` | Optional | `YYYY-MM-DD` | End date. Blank = single-day event. For bounded recurring, this is the **last possible** date. |
| 7 | `end_time` | Optional | `HH:MM` 24-hour | End time. Blank is OK. |
| 8 | `all_day` | Optional | `true` / `false` | If `true`, time is ignored and the card shows “(all day)”. |
| 9 | `recurring` | Optional | `true` / `false` | `true` marks the row as a recurring *template* that spawns instances. |
| 10 | `recurrence_pattern` | If `recurring=true` | Text — see [patterns](#recurrence-patterns) | How the template repeats. |
| 11 | `language` | Optional | `English` / `Spanish` / `Both` | Used for the filter and the small EN / ES / EN/ES badge. |
| 12 | `format` | Optional | `In-Person` / `Virtual` / `Hybrid` | Pins an icon and drives filter options. |
| 13 | `venue_name` | Optional | Text | Where it is held. |
| 14 | `address` | Optional | Text | Street address (quote if it contains a comma). |
| 15 | `city` | Optional | Text | |
| 16 | `state` | Optional | 2-letter, e.g., `TX` | |
| 17 | `zip` | Optional | `75088` or `75088-1234` | |
| 18 | `virtual_platform` | Optional | `Zoom`, `Teams`, `Google Meet`… | Shown in the Join row. |
| 19 | `virtual_link` | Optional | Full URL | The join link. Gets a **copy** button in the detail sheet. |
| 20 | `meeting_id` | Optional | Text (can contain spaces) | Shown under Meeting ID with a copy button. |
| 21 | `passcode` | Optional | Text | Shown under Passcode with a copy button. |
| 22 | `cost` | Optional | `$40.00`, `Free`, anything | Shown as a pill when present. |
| 23 | `status` | Yes | `Confirmed` / `Tentative` / `Cancelled` | Tentative shows a dashed pill on the calendar and a Tentative tag on cards. |
| 24 | `source_url` | Optional | URL | Flyer / details link. |
| 25 | `notes` | Optional | Text | Free notes. Use `Homegroup: <name>` to have the homegroup appear as its own row in the detail sheet. |
| 26 | `private` | Optional | `true` / `false` | `true` hides address, city, Zoom link, meeting ID, passcode everywhere (including search and share text). |
| 27 | `override_of` | Optional | An `id` of a recurring row | Turns this row into an **occurrence override** for that series (see recipe 15). |

> Columns are comma-separated. If any field contains a **comma**, wrap that field in `"double quotes"`. Apostrophes (`'`) do not need anything special. Empty fields are just two commas in a row: `,,`.

### Categories and colors

Only these 15 category values are recognized. Any other value still works but will be green by default.

| Category | Color swatch | Typical use |
|---|---|---|
| `Assembly` | ![#6A1B9A](https://placehold.co/12x12/6A1B9A/6A1B9A.png) `#6A1B9A` (deep purple) | Spring / Summer / Fall area assemblies |
| `Workshop` | ![#1976D2](https://placehold.co/12x12/1976D2/1976D2.png) `#1976D2` (blue) | GV / Grapevine workshops |
| `Conference` | ![#C62828](https://placehold.co/12x12/C62828/C62828.png) `#C62828` (red) | Multi-day conferences |
| `Convention` | ![#B71C1C](https://placehold.co/12x12/B71C1C/B71C1C.png) `#B71C1C` (dark red) | State / regional conventions |
| `Anniversary` | ![#F9A825](https://placehold.co/12x12/F9A825/F9A825.png) `#F9A825` (amber) | Group / area anniversaries |
| `Social` | ![#F57C00](https://placehold.co/12x12/F57C00/F57C00.png) `#F57C00` (orange) | Parties, bingo, socials |
| `TownHall` | ![#00897B](https://placehold.co/12x12/00897B/00897B.png) `#00897B` (teal-green) | Town hall / open forum |
| `Committee` | ![#2E7D32](https://placehold.co/12x12/2E7D32/2E7D32.png) `#2E7D32` (green) | Committee and chair meetings |
| `District` | ![#1B5E20](https://placehold.co/12x12/1B5E20/1B5E20.png) `#1B5E20` (dark green) | District business meetings |
| `Group Conscience` | ![#827717](https://placehold.co/12x12/827717/827717.png) `#827717` (olive) | Group conscience |
| `Community` | ![#5E35B1](https://placehold.co/12x12/5E35B1/5E35B1.png) `#5E35B1` (violet) | Citywide / community gatherings |
| `Birthday` | ![#D81B60](https://placehold.co/12x12/D81B60/D81B60.png) `#D81B60` (pink) | Group birthday celebrations |
| `Training` | ![#0277BD](https://placehold.co/12x12/0277BD/0277BD.png) `#0277BD` (ocean blue) | Bootcamps, trainings |
| `Roundup` | ![#4527A0](https://placehold.co/12x12/4527A0/4527A0.png) `#4527A0` (indigo) | Roundups |
| `Speaker` | ![#00838F](https://placehold.co/12x12/00838F/00838F.png) `#00838F` (dark cyan) | Speaker meetings of any topic |

Spelling is **case-sensitive**. Use `TownHall` not `townhall` or `Town Hall`. Use `Group Conscience` with the space.

### Formats, Status, Language, yes/no fields

- **`format`**: exactly one of `In-Person`, `Virtual`, `Hybrid`.
- **`status`**: `Confirmed`, `Tentative`, or `Cancelled`. Blank = Confirmed.
- **`language`**: `English`, `Spanish`, or `Both` (Bilingual).
- **`all_day` / `recurring` / `private`**: use the lowercase strings `true` or `false`. Blank is treated as `false`.

### Date & time formats

- **Dates**: always `YYYY-MM-DD`. Example: `2026-04-17` for April 17, 2026.
- **Times**: always `HH:MM` on a **24-hour clock**. Example: `19:00` for 7 pm, `07:30` for 7:30 am.
- The calendar shows times in **local time** (no timezone column). For Texas events leave it as-is; if an event is in another timezone, add a note in `notes`.

### Recurrence patterns

The `recurrence_pattern` field understands two families of patterns. Capitalization does not matter.

**Monthly:**
- `First <day> of every month`
- `Second <day> of every month`
- `Third <day> of every month`
- `Fourth <day> of every month`
- `Fifth <day> of every month` (skipped in months without a 5th occurrence)
- `Last <day> of every month`

`<day>` = `Sunday`, `Monday`, `Tuesday`, `Wednesday`, `Thursday`, `Friday`, `Saturday`.

**Weekly:**
- `Every <day>`
- `Weekly on <day>`

Any pattern that does not match these is silently ignored (the template appears on the Recurring tab but no instances are placed on the calendar).

**Bounding a series** with `start_date` + `end_date`:
- If both are blank on a `recurring=true` row, the app generates 12 months of instances forward from today.
- If either is set, only instances inside that window are generated.

---

## Event recipes (copy-paste examples)

Each example is a full CSV row you can paste in as the next row. Columns that are blank appear as back-to-back commas. Replace the **id** with the next unused number.

> The order of columns is always: `id, title, category, start_date, start_time, end_date, end_time, all_day, recurring, recurrence_pattern, language, format, venue_name, address, city, state, zip, virtual_platform, virtual_link, meeting_id, passcode, cost, status, source_url, notes, private, override_of`.

### 1. One-time in-person event

**Use when:** a single event at a physical venue.

```csv
100,Spring Potluck,Social,2026-04-18,17:00,2026-04-18,21:00,false,false,,English,In-Person,Rowlett Community Centre,5300 Main St,Rowlett,TX,75088,,,,,,Confirmed,,Bring a dish to share,false,
```

### 2. One-time virtual (Zoom) event

**Use when:** Zoom-only, no physical venue.

```csv
101,New Sponsor Orientation,Training,2026-05-04,19:00,2026-05-04,20:00,false,false,,English,Virtual,,,,,,Zoom,https://us06web.zoom.us/j/12345678901,12345678901,welcome,,Confirmed,,,false,
```

Leave `venue_name`, `address`, `city`, `state`, `zip` blank. Fill `virtual_platform`, `virtual_link`, `meeting_id`, and `passcode`.

### 3. Hybrid event (in-person + Zoom)

**Use when:** people can attend in person **or** join on Zoom.

```csv
102,GV Workshop — Ross Avenue Group,Workshop,2026-04-18,14:00,2026-04-18,17:00,false,false,,English,Hybrid,Trevor's Place,1421 N. Peak St.,Dallas,TX,,Zoom,https://zoom.us/j/88888888888,88888888888,grapevine,,Confirmed,,,false,
```

Fill both the physical venue **and** the virtual platform info.

### 4. Multi-day event

**Use when:** the event spans more than one calendar day.

```csv
103,Spring Assembly,Assembly,2026-03-20,17:00,2026-03-22,12:00,false,false,,English,In-Person,Hilton Garden Inn Duncanville,800 North Main St,Duncanville,TX,75116,,,,,,Confirmed,,Multi-day assembly,false,
```

The event will appear as a block on each day from `start_date` through `end_date` on the calendar.

### 5. All-day event (no specific times)

**Use when:** dates are known but times are TBD or the event runs the whole day.

```csv
104,Big Country Conference,Conference,2026-09-04,,2026-09-06,,true,false,,English,In-Person,Abilene Convention Center,1100 N 6th St,Abilene,TX,79601,,,,,,Confirmed,https://bigcountryaaconference.org/,,false,
```

Set `all_day=true` and leave `start_time` / `end_time` blank.

### 6. Tentative event

**Use when:** the event is planned but not yet confirmed.

Set `status=Tentative`. The calendar pill gets a dashed outline and cards show a “Tentative” tag.

```csv
105,Summer Speaker Picnic,Social,2026-07-11,11:00,2026-07-11,16:00,false,false,,English,In-Person,Harry Myers Park,815 E Washington St,Rowlett,TX,75088,,,,,,Tentative,,Final location pending,false,
```

### 7. Private event (home party, hidden address)

**Use when:** the venue should **not** be shown publicly (home parties, invitation-only).

Set `private=true`. The address, city, state, zip, Zoom link, meeting ID, and passcode are all hidden in the UI, search results, and share text. The event card and detail sheet show “Private — contact host for details.”

```csv
106,Miriam J Graduation Party,Social,2026-05-22,17:00,2026-05-22,22:00,false,false,,English,In-Person,,3122 Encino Dr,Dallas,TX,75228,,,,,,Confirmed,,,true,
```

Fill the address for your own records — the app will not show it.

### 8. Monthly recurring meeting

**Use when:** meets the same nth weekday of every month, ongoing.

Leave `start_date` and `end_date` blank. Fill `recurring=true` and a pattern like `Second Sunday of every month`.

```csv
107,District 22 Monthly Meeting,District,,12:00,,13:00,false,true,Second Sunday of every month,English,Virtual,,,,,,Zoom,,679-274-5608,Service1st,,Confirmed,,,false,
```

The app generates the next 12 months of instances automatically.

### 9. Last-Saturday recurring meeting

```csv
108,Rowlett Group Birthday Celebration,Birthday,,17:00,,,false,true,Last Saturday of every month,English,In-Person,Casa View Baptist Church,130 E Interstate 30,Garland,TX,75043,,,,,,Confirmed,,,false,
```

### 10. Weekly recurring meeting (ongoing)

**Use when:** meets every week on the same day, with no planned end.

```csv
109,Thursday Big Book Study,Committee,,19:30,,20:30,false,true,Every Thursday,English,Hybrid,Rowlett Group,5300 Main St,Rowlett,TX,75088,Zoom,https://zoom.us/j/77777777777,77777777777,bigbook,,Confirmed,,,false,
```

### 11. Bounded series (e.g., every Wed in April only)

**Use when:** a repeating series that runs for a limited time — a month, a season, a conference series.

Fill **both** `start_date` and `end_date` on the recurring template. The pattern is still `Every Wednesday`; the dates just bound the window.

```csv
110,Spring Speaker Series — Mix,Speaker,2026-04-01,19:00,2026-04-29,20:30,false,true,Every Wednesday,English,In-Person,Rowlett Group,5300 Main St,Rowlett,TX,75088,,,,,,Tentative,,Rotating guest speakers from various groups,false,
```

On the calendar you will see Apr 1, 8, 15, 22, 29 — and nothing after the end date.

### 12. Speaker meeting — one-time

**Use when:** a guest speaker on a specific date.

Put the speaker's name and topic in the `title`. Use `category=Speaker`. Optionally include the homegroup in `notes` (see recipe 13).

```csv
111,Mike B. — Steps,Speaker,2026-04-21,19:00,2026-04-21,20:30,false,false,,English,In-Person,Keller Group,540 Keller Pkwy,Keller,TX,76248,,,,,,Tentative,,,false,
```

Topic conventions — use one of: `Mix`, `Story`, `Steps`, `Traditions`, `Concepts`.

### 13. Speaker meeting — with homegroup

**Use when:** you know the speaker's home group and want it shown separately (not buried in notes).

Add `Homegroup: <group name>` to the `notes` field. The detail sheet will display a dedicated **Homegroup** row.

```csv
112,Linda K. — Traditions,Speaker,2026-04-24,19:30,2026-04-24,20:30,false,false,,English,Virtual,,,,,,Zoom,https://zoom.us/j/1234567890,1234567890,speaker,,Tentative,,Homegroup: Tyler Big Book Group,false,
```

Both `Homegroup: X` and `homegroup - X` work (case-insensitive, colon or dash). If you have other notes too, put them on separate lines or join with `|`:

```
Homegroup: Tyler Big Book Group | Brings his sponsor
```

### 14. Speaker series — recurring

**Use when:** a weekly speaker series for a fixed stretch (e.g., all of April 2026).

```csv
113,Concept Study — Monday Speakers,Speaker,2026-04-06,19:00,2026-04-27,20:00,false,true,Every Monday,English,Hybrid,Golden Triangle Group,540 Keller Parkway #A,Keller,TX,,Zoom,https://zoom.us/j/9876543210,9876543210,concept,,Tentative,,Rotating speakers; topic is Concepts,false,
```

### 15. Occurrence override — change one month only

**Use when:** a recurring series already exists and one specific occurrence has **different** information. Classic example: John D. is speaking at CityWide Dallas in May 2026, but every other month is just the normal CityWide meeting.

How it works: add a new row whose `override_of` column is the `id` of the series, and whose `start_date` is the date of the occurrence to replace. **Blank fields inherit from the template; filled fields override.**

Assuming CityWide Dallas is `id=20`:

```csv
114,CityWide Dallas — John D.,,2026-05-09,,,,,,,,,,,,,,,,,,,,,Homegroup: District 22 Group,,20
```

What happens:
- Title is overridden to `CityWide Dallas — John D.`
- Notes is overridden with the homegroup
- **Everything else** — category, time, venue, address, format, status — inherits from the CityWide template
- The normal May 9 occurrence is removed and replaced by this one; all other months stay unchanged
- The override still shows the ↻ Recurring tag so users know it is part of the series

### 16. Occurrence override — mark one month Tentative

**Use when:** same pattern but you only want to change the status for one occurrence.

```csv
115,,,2026-06-13,,,,,,,,,,,,,,,,,,,Tentative,,,,20
```

- Title, venue, category, time — all inherited from the CityWide template
- Only `status=Tentative` is overridden for that one Saturday
- All other CityWide Saturdays stay Confirmed

> **Important for overrides**: the `start_date` must match a date the series actually meets. For "Second Saturday of every month", use the second Saturday of the month you are overriding.

---

## Common mistakes and how to fix them

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Event does not appear at all | Wrong `start_date` format | Use `YYYY-MM-DD`, e.g., `2026-04-17`. |
| Event appears at midnight | `start_time` missing or wrong format | Use `HH:MM` 24-hour, e.g., `19:00`. |
| Recurring event shows nothing on calendar | Pattern text not recognized | Must match one of the patterns in [Recurrence patterns](#recurrence-patterns). |
| Calendar is green instead of the expected color | Category spelled differently | Spelling is case-sensitive. Use exact values in [Categories and colors](#categories-and-colors). |
| Override appears **alongside** the normal series entry (duplicate on that date) | `start_date` on the override does not match a real occurrence of the series | Pick the exact date the series meets. |
| Override shows up with blank title or missing fields | `override_of` points to an id that does not exist or is not `recurring=true` | Fix the id; the series row needs `recurring=true`. |
| Address shown on a private event | `private` is not exactly `true` | Lowercase `true` only. Blank or `false` means public. |
| Bingo appears on the wrong day | Timezone confusion | The app uses local time. For non-local events, note the timezone in `notes`. |
| Event title gets cut off at a comma | Field contains a `,` but is not quoted | Wrap the field in `"..."`. Google Sheets does this automatically on export. |
| Changes do not show up in the app | Google's publish cache | Wait up to 1 minute and reload the app, or force-refresh (`Ctrl+F5`). |

---

## Troubleshooting

**Nothing loads and the app shows "Unable to load events"**
- The Google Sheet's CSV publish URL is down, private, or was revoked. Re-publish the sheet to the web (File → Share → Publish to web → CSV).
- Check that the sheet has not been deleted.
- If opening the file directly from disk, serve it via a local web server (see the on-screen instructions).

**A recurring series is not filling in past occurrences**
- Unbounded recurring templates (no `start_date`/`end_date`) only generate **future** instances to avoid cluttering past months with auto-generated rows.
- If you want past occurrences shown, set `start_date` and `end_date` to bound the series (see recipe 11).

**My star / favorites are gone**
- Favorites are stored in the browser, not the spreadsheet. Clearing browser data or switching browsers resets them. This is intentional — favorites are per-user.

**Search does not find a private event's venue**
- By design. Private events scrub location and join info from search, share text, and ICS exports.

---

## Advanced: shareable URLs

The app reflects the current view in the URL. You can paste any of these URLs to land someone on exactly the same view:

| URL parameter | Meaning | Example |
|---------------|---------|---------|
| `?view=` | Which tab | `?view=week`, `?view=upcoming`, `?view=starred` |
| `?date=` | Calendar cursor (Month/Week) | `?view=calendar&date=2026-05-01` |
| `?q=` | Search query | `?q=bingo` |
| `?range=` | Date-range chip | `?range=today`, `?range=7`, `?range=30`, `?range=weekend` |
| `?event=` | Open an event's detail sheet | `?event=4` or `?event=27-2026-04-22` |
| `?day=` | Open the day list sheet | `?day=2026-04-18` |

Multiple parameters combine with `&`: `?view=calendar&date=2026-05-01&q=speaker`.

---

## For developers

All the logic lives in one self-contained `index.html` — vanilla HTML/CSS/JS, no build step.

Key components inside the `<script>` block:

- `CSV_URL_B64` — base64-encoded Google Sheets CSV URL. Change it to point at a different sheet.
- `CATEGORIES` array — the color palette. Add a row here to introduce a new category color; the filter chip appears automatically when an event uses it.
- `parseRecurrencePattern` + `expandRecurring` — recurrence engine. Supports monthly (`<nth> <dow>`) and weekly (`every <dow>`) patterns.
- `mergeOverrides` — applies `override_of` rows on raw CSV (before boolean coercion) so blank fields inherit correctly.
- `STATE` — single source of truth for view, filters, search, range, current event, favorites (persisted to `localStorage` under `aa-events-favs:v1`).

Adding a new recurrence kind means extending `parseRecurrencePattern` (return a new `kind`) and handling that kind inside `expandRecurring`.

No external dependencies. To test locally:

```
python -m http.server
# then open http://localhost:8000
```
