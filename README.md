# TA/DA Reimbursement Sheet

A mobile-first web app that replaces the monthly **TA/DA travel expense claim** spreadsheet. Enter each day's travel and expenses on your phone, and the app adds everything up and gives you a print-ready **PDF** and a formula-driven **Excel** file.

It is a single HTML file. There is no server, no login and no build step. Your data stays in your own browser.

> Independent project, not affiliated with or endorsed by Teamlease Services Limited. The layout follows a TA/DA sheet format; change the organisation name and disclaimer in **Setup** to suit yours.

<!-- Add screenshots here, for example:
![Claim screen](docs/claim.png) ![Daily entry](docs/entry.png)
-->

## Features

- **Daily entry made easy:** a bottom-sheet form for one date at a time, with previous/next day buttons.
- **Live calculation:** travelling amount, total hours and day total update as you type.
- **Click-to-select routes:** tap places in order to build a route such as `Amla → Betul → Amla`. Tap a place twice to visit it twice, attach an **event** to any place, or use saved **route presets**.
- **Sundays prefilled:** Sundays are added automatically to new claims. They stay fully editable.
- **Any month, any year:** switch between claims, duplicate a claim into another month, delete a claim.
- **Setup once:** employee profile, per-km rate, reusable lists (places, routes, events, statuses) and optional custom fields.
- **PDF export:** one A4 page, **portrait or landscape**, with the full claim, amount in words and signature lines. Long place lists never push it onto a second page: the text shrinks automatically to fit, and the app tells you when the other layout would give bigger text.
- **PDF preview:** see the exact page before you download it, and switch layout inside the preview.
- **Excel export:** a real `.xlsx` with formulas, borders, Indian rupee formatting and print settings.
- **Backup and restore:** download a JSON snapshot and restore it later, on any device.
- **Backup reminders:** the home screen shows when you last backed up, how many changes are not yet backed up, and where your data lives (only on this device and browser). A **count badge** on the Backup tab shows how many changes are not yet backed up (it turns red when a backup is overdue or you have never backed up), and you choose how often to be reminded.
- **Easier daily entry:** status chips, a sticky Save bar, *Copy from previous day*, and *Undo* after deleting an entry.
- **Guides for new users:** a welcome screen with an option to start with your own data, short one-time tips on each screen, and a help guide in English and Hindi. Tips switch off by themselves after the first few visits and can be replayed from Setup.
- **Material 3 Expressive design** in a calm green and blue palette, with a quick **dark mode** toggle in the header (or Auto/Light/Dark in Setup), plus a built-in help guide.
- **One consistent font:** Google's *Noto Sans Devanagari* (with *Noto Sans* as backup) is used for both Hindi and English, so they match.
- **Three text sizes:** Small, Medium (default) or Large in **Setup → Appearance**. Hindi and English scale together, Hindi text is drawn slightly larger so it stays easy to read, and the layout adapts. PDF and Excel output is not affected.
- **Runs from a single file.** Entering data needs no server; the fonts, PDF and Excel libraries load from the internet.

## Getting started

### just open it

Download `index.html` and open it in a modern browser (Chrome, Edge, Safari or Firefox).


## How to use it

1. **Setup (first time):** open **Setup**, fill in your employee profile and per-km rate, and add the places you visit often.
2. **Pick a month:** in **Claim**, choose the month and year, then tap **Open / Create**.
3. **Add a day:** tap **+ Add Daily Entry**. Enter times, distance and other expenses, then choose your places.
4. **Holiday, leave or Sunday:** set the **Status** and save. No expenses are needed.
5. **Check:** the **Summary** tab shows all totals and the amount in words.
6. **Export:** in **Export**, choose Portrait or Landscape, tap **Preview PDF** to check the page, then download the PDF or the Excel file.
7. **Back up:** in **Backup**, download a snapshot regularly.

The app also has a floating **?** button with a step-by-step guide.

## How the numbers work

| Item | Rule |
| --- | --- |
| Travelling amount | Distance (km) × per-km rate. An optional per-entry rate or amount override is available. |
| Total hours | Actual time between From and To, shown as `HH:MM`. A To time earlier than From is treated as an overnight trip. No rounding. |
| Day total | Travelling + Conveyance + Lodging + Food (with supporting) + Food (without supporting) + Boarding + Mobile |
| Monthly totals | The sum of the daily entries. Nothing is typed in by hand. |
| Amount in words | Indian system (lakh, crore), for example `Thirty-six thousand five hundred sixteen Indian rupees only.` |
| One row per day | Several visits on one day go into a single row's route. Expenses are never counted twice. |

Blank and zero are treated differently, and zero-expense days (leave, holiday, Sunday) are valid.

## Your data and privacy

Everything is stored in your browser's **IndexedDB** on your device. Nothing is sent to any server. Clearing site data or using a private window can erase it, so use **Backup → Download Snapshot** regularly.

### Backup format (schema 2)

```json
{
  "app": "tada-claim",
  "schema": 2,
  "created": "2026-10-30T00:00:00.000Z",
  "settings": { "profile": {}, "rate": 6, "places": [], "routes": [], "events": [], "statuses": [], "custom": [] },
  "claims": [
    {
      "id": "2026-10", "year": 2026, "month": 10,
      "entries": [
        {
          "id": "…", "date": "2026-10-01", "from": "09:00", "to": "18:00",
          "km": 192, "rateOv": null, "fareOv": null,
          "conv": null, "lodg": null, "fws": null, "fwo": 150, "board": null, "mob": null,
          "places": [{ "p": "Amla", "ev": "" }],
          "status": "Working", "remarks": "", "cf": {}
        }
      ]
    }
  ]
}
```

Restore validates the file first, then lets you **replace everything** or **import claims only**, with a confirmation. Older schema 1 backups are upgraded automatically. Backups from a newer version are refused.

## Customising

All of this is near the top of the script in `index.html`:

- **Credit and contact links:** edit the `CONTACT` object (`email`, `github`, `whatsapp`, `instagram`). A full link or just a handle, number or address works. For WhatsApp use the number with country code, for example `919876543210`. Empty entries show as dimmed buttons.
- **Default settings:** edit `defS()` for the default organisation name, rate, places (Amla, Betul, Jabalpur, Chhindwara, Bhopal, Itarsi, Narmadapuram, Seoni, Indore, Balaghat, Sanchi), two example route presets and statuses.
- **Sample claim:** `SAMPLE_WORK` holds the dummy working days. The app builds the sample for the **previous month** from today's date (real Sundays, spare weekdays marked Holiday/Leave), so the total is always about ₹25,640 and uses only the default places. Replace or remove it, and remove the first-run seeding in the start-up code at the bottom if you do not want it.

## Project files

```
index.html                  the whole app (HTML, CSS, JavaScript)
sample-claim-oct2026.json   a saved copy of the generated sample (as it looks in October 2026), restorable from Backup
test.js                     logic tests (Node.js)
README.md                   this file
```

## Tests

```bash
node test.js
```

Checks the calculations (fares, the previous-month sample across every month of 2026-2028, hours including overnight, daily and monthly totals, amount in words), route text, backup migration and settings clean-up. It also regenerates `sample-claim-oct2026.json` from the app's own sample.

## Known limitations

- The PDF uses the standard PDF font, so it prints `Rs.` and `->` instead of `₹` and `→`, and cannot draw Hindi or other non-Latin text. Excel handles all of these.
- Data lives in one browser on one device. Use backups to move or protect it.
- Not packaged as an installable offline app (no service worker yet).
- The app asks the browser to protect its storage, but no browser can promise that: clearing site data or private mode still erases it. That is why backup reminders exist.

## Built with

- [ExcelJS](https://github.com/exceljs/exceljs) for the Excel file
- [jsPDF](https://github.com/parallax/jsPDF) and [jsPDF-AutoTable](https://github.com/simonbengtsson/jsPDF-AutoTable) for the PDF
- [PDF.js](https://github.com/mozilla/pdf.js) to draw the PDF preview (loaded only when you open a preview; if it cannot load, the browser's own PDF viewer is used)
- Plain HTML, CSS and JavaScript, with IndexedDB for storage

## Credits

Made by **Pranay** · [Instagram @itspdp](https://www.instagram.com/itspdp)

## License

Add a license of your choice, for example MIT, as a `LICENSE` file in the repository.
