# Solar Plant Monitoring Automation (AICA Level 2 Capstone Project)

**Author:** Dhruvil Shah, Chartered Accountant, Ahmedabad
**Brand:** Fintox Your Financial Detox (business automation, virtual CFO, AI automation)
**Video explanation:** https://youtu.be/cwRmJHeqdpQ
**Tools used:** n8n Cloud, SolarLive portal, Gmail, Google Sheets, PDF.co, Meta WhatsApp Cloud API (in progress)

## 1. Problem

Three solar power plants are monitored through the SolarLive web portal. Checking each plant and each inverter by hand every day is slow, and a stopped inverter can go unnoticed for hours, which means lost generation and lost revenue. Management also needs a clean daily record and a way to ask the EPC contractor why an inverter or plant is underperforming.

## 2. Solution

An n8n workflow logs in to SolarLive on a schedule, reads plant and inverter data, and does the checking and reporting automatically.

| Schedule | What the workflow does |
|---|---|
| 8:00 AM daily | Daily report for the previous day, sent as an email with three PDF attachments |
| 9, 11, 1, 3 and 5 | Health check; sends an "Emergency: Need your attention" mail if a plant or inverter is down |
| 1st of every month, 8:30 AM | Monthly generation report |
| Every run | Appends a row per plant to a Google Sheet log |
| Login failure | Sends a separate alert mail so a failed login is never silent |

### Daily report: three PDFs

1. **Solar_Generation_Report** - plant-wise generation, CUF, expected earnings at the PPA tariff, month to date against target, and a 15-day comparison.
2. **Inverter_Wise_Generation** - a table per plant showing each inverter's panels, kWh, kWh per kWp, comparison with the plant average, and a status (Strong, Average, Weak, No output).
3. **Observation** - a numbered table with the columns Sr. No., Plant, Inverter, Issue and a blank "Reply by EPC" column. It lists weak inverters, target shortfalls and open alerts as questions for the EPC, and asks the EPC to confirm that the actual panel count per inverter matches the plan.

## 3. How the logic works

- All capacities are on a DC basis (kWp).
- Monthly targets in kWh per kWp per day: January-February 5.0, March-June 5.75, July-September 4.25, October-December 5.0.
- Inverters are compared on kWh per kWp, so the panel count is allowed for. The tool assumes the panel count per inverter shown in the plant layout and asks the EPC to confirm it.
- Inverter status: *No output* if kWh is zero; *Weak* if below 90% of the plant average; *Strong* at 102% or above; otherwise *Average*.
- A target-shortfall question is raised when a plant achieves less than 90% of its target. The wording changes depending on whether all plants are low (likely weather) or only one (likely a plant-specific cause).
- If PDF generation fails, the workflow falls back to a plain email so the report is never missed.

## 4. Workflow structure

`Schedule triggers -> Mode -> Login -> Plan Requests -> Fetch SolarLive Data -> Assemble Report -> Add Diagnostics -> Inverter PDF Item -> Route Output -> Make PDF -> Download PDF -> Combine PDFs -> Send mail with PDFs`

Route Output also sends data to the Google Sheet log and to the emergency mail path.

## 5. Repository contents

```
Dhruvil-Shah-Solar-Plant-Monitoring/
  README.md
  workflow/SolarLive_Monitor.n8n.json   (sanitised n8n export)
  docs/setup-guide.md
  docs/Project_Scenarios.pdf   (every scenario the workflow handles)
```

## 6. Setup

See `docs/setup-guide.md`. All situations handled by the workflow are described in `docs/Project_Scenarios.pdf`. In short: import the JSON into n8n, add your own Gmail, Google Sheets and PDF.co credentials, enter SolarLive login details, set the recipient and tariffs, and set the timezone to Asia/Kolkata.

## 7. Security note

This export has been cleaned. It contains no passwords, API keys, credential IDs, personal email addresses, or sheet IDs. Placeholders such as `YOUR_SOLARLIVE_PASSWORD` must be replaced after import. Better still, store the SolarLive login in n8n credentials and not in a plain Set node.

## 8. Planned improvements

- WhatsApp alerts through the Meta WhatsApp Cloud API with approved message templates.
- AI voice calls to plant supervisors when an inverter is down, with the reply summarised back to management.
- Cleaning-visit and dust tracker.
- Per-string analysis for the weak inverters.

## 9. What I learned

Browser-style login and scraping of an authenticated portal in n8n, scheduling and routing with Switch and IF nodes, generating formatted PDFs from HTML, handling failures with fallbacks, and turning raw data into questions that a non-technical contractor can answer.
