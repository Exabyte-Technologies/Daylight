# Daylight

A lightweight, read-only ICS calendar viewer. Open a calendar subscription link in month, week, day, or agenda view without installing an app.

Daylight runs in your browser. Its calendar libraries, styles, and application code are bundled into `calendar.html`, with no build step or CDN dependency. The favicon files sit alongside the page.

## Features

- **Four layouts:** month, week, day, and a monthly agenda.
- **Date navigation:** previous/next, today, a date picker, and Monday or Sunday week starts.
- **ICS feeds and files:** connect an HTTP, HTTPS, or webcal subscription link, or import a local `.ics` file.
- **Recurring events:** recurrence rules, additional dates, excluded dates, and moved or cancelled occurrences.
- **Event details:** times, locations, descriptions, and event links, including all-day and multi-day events.
- **Search:** filter the visible dates by title, location, or description.
- **Time zones:** display timed events in your browser’s local time, with embedded VTIMEZONE data and standard IANA time-zone rules.
- **Responsive layout:** desktop and mobile views, keyboard-accessible event details, and navigation shortcuts.

With no feed connected, the calendar stays empty. There are no sample events.

## Get started

1. Download or clone this repository.
2. Open `calendar.html` in a modern browser.
3. Paste an ICS subscription link into **ICS calendar link**, then choose **Connect calendar**. Alternatively, choose **Or import an .ics file**.

Keep `favicon.svg` and `favicon.ico` next to `calendar.html` to retain the browser-tab icon. No package installation is required.

To serve the folder locally, run this command from the repository directory:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Then open [http://127.0.0.1:8000/calendar.html](http://127.0.0.1:8000/calendar.html).

## Load a feed from the URL

Pass the complete, URL-encoded ICS link in the `ics` query parameter:

```text
calendar.html?ics=https%3A%2F%2Fexample.com%2Fevents.ics&view=week
```

| Parameter | Accepted values | Default |
| --- | --- | --- |
| `ics` | A URL-encoded HTTP, HTTPS, or webcal feed link | No feed |
| `url` | Alias for `ics`; `ics` takes priority if both are supplied | — |
| `view` | `month`, `week`, `day`, `agenda` | `month` |
| `date` | A valid date in `YYYY-MM-DD` format | Today |
| `weekStart` | `mon` or `sun` | `mon` |

Encode the **entire feed URL**, including any query parameters it already contains. To construct a link safely in JavaScript:

```js
const page = new URL('https://your-site.example/calendar.html');
page.searchParams.set('ics', 'https://provider.example/events.ics?token=YOUR_TOKEN');
page.searchParams.set('view', 'week');
page.searchParams.set('date', '2026-10-13');
console.log(page.href);
```

Navigating the calendar updates the view, date, and week-start parameters in the page URL. Connected feed URLs remain in that URL; importing a file removes the feed parameter.

## Controls

| Control | Action |
| --- | --- |
| Month / Week / Day / Agenda | Change the layout |
| Previous / Next arrows | Move by one period in the selected layout |
| Today | Return to today |
| Date picker | Jump to a date; available on wider screens |
| Search this view | Filter events in the visible date range |
| Refresh | Fetch the connected feed again |
| Click an event, or focus it and press Enter / Space | Open event details |
| Escape | Close event details |
| Left / Right arrow keys | Navigate when you are not using an input or other control |
| T | Return to today when you are not using an input or other control |

On mobile, use **Calendar feed** to open the feed and import controls.

## Feed access and CORS

The feed URL must return ICS text, rather than a calendar webpage. The provider must also allow browser cross-origin requests (**CORS**). A subscription that works in Outlook or Apple Calendar can still be blocked in a browser.

If a feed cannot be loaded:

1. Check that you have the provider’s subscription or export URL.
2. Download the `.ics` file and import it into Daylight.
3. If you control the feed server, configure it to allow requests from the origin hosting Daylight.

An HTML-only page cannot bypass a provider’s CORS restrictions. Daylight does not use a third-party proxy. Requests omit authentication cookies, so a feed must be public or grant access through its subscription URL. `webcal://` links are converted to HTTPS. An HTTPS-hosted page requires an HTTPS feed.

Some providers reject requests from a local file’s `null` origin. Serving or hosting Daylight can help if the provider allows that origin; it does not automatically solve CORS restrictions.

## Privacy and limitations

- Feed contents are fetched directly from the selected provider into your browser. The page does not send them to another service.
- Feed URLs may contain private access tokens. Sharing a Daylight URL containing a feed link also shares access to that feed.
- Calendar data is not saved in browser storage. Imported files stay in the current tab and must be imported again after reloading.
- Daylight is read-only: it does not create, edit, or delete events in the source calendar.
- Feed refresh is manual. Imported files cannot be refreshed from their provider.
- Timed events use the browser’s local time zone. Unknown timezone identifiers show a warning and are treated as local time.
- Files and feeds are limited to approximately 10 MB. Very large recurrence histories may be rejected to keep the page responsive.

## Hosting

Upload `calendar.html`, `favicon.svg`, and `favicon.ico` together to a static web host. No server-side application or build process is needed.

For GitHub Pages, configure publishing from the branch and folder containing these files, then open the published site with `/calendar.html` appended. The entry point is `calendar.html`; this repository does not include an `index.html` homepage.

## Files

```text
Daylight/
├── calendar.html   # Calendar UI, styles, application code, and bundled libraries
├── favicon.svg     # Scalable sun icon
├── favicon.ico     # 16, 32, and 48 px browser icon fallback
├── README.md
└── LICENSE
```

To modify the calendar, edit `calendar.html`. Its application script appears near the end of the file, after the bundled library code and license notices.

## Libraries and license

- [FullCalendar 6.1.20](https://github.com/fullcalendar/fullcalendar/tree/v6.1.20) — MIT.
- [ical.js 2.2.1](https://github.com/kewisch/ical.js/tree/v2.2.1) — Mozilla Public License 2.0.

Daylight’s original code and favicon assets are covered by the repository’s [MIT license](LICENSE). Bundled third-party code retains its own license; the corresponding notices are included in `calendar.html`.
