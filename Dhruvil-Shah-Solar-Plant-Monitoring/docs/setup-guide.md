# Setup Guide

## Requirements
- An n8n Cloud account or a self-hosted n8n instance
- A SolarLive portal login with read access to your plants
- A Gmail account connected to n8n (OAuth)
- A Google Sheet with one tab per plant
- PDF.co access: n8n Gateway credits, or your own PDF.co API key

## Steps
1. In n8n choose **Import from file** and select `workflow/SolarLive_Monitor.n8n.json`.
2. Open **Login Details** and replace `YOUR_SOLARLIVE_USERNAME` and `YOUR_SOLARLIVE_PASSWORD`.
3. Open each Gmail node (Send Routine Mail, Send Routine Mail with PDF, Send Emergency Mail, Emergency: Login Failed) and select your Gmail credential.
4. Open **Log Daily Row**, select your Google Sheet and credential. Each plant needs a tab, with these headers in row 1: Date, Plant, Generation_kWh, CUF_pct, Tariff, Expected_Earnings_INR, SolarLive_Revenue_INR, Inverter_kWh, Open_Alerts, MTD_kWh.
5. Open **Make PDF** and select your PDF.co credential.
6. In the **Assemble Report** code node, set the recipient email (`recipient@example.com`), plant names, DC capacities and tariffs.
7. In **Add Diagnostics**, set the monthly targets and the panels-per-inverter table to match your plants.
8. In workflow settings, set the timezone to Asia/Kolkata.
9. Run the workflow once manually with the **Daily 8 AM** trigger and check the test mail, then publish the workflow.

## Troubleshooting
- No PDFs in the mail: check the PDF.co or n8n Gateway credit balance. The mail will still arrive without attachments.
- Login alert mails: check the SolarLive username and password.
