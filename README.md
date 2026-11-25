# Gym Automation with n8n

An n8n workflow that automates a gym's daily operations: member signup, WhatsApp welcome messages, membership expiry reminders, and a weekly report for the owner.

**Flow:** Form → Google Sheet → WhatsApp → Reminder → Report

## Features

1. **Member signup**
   - A web form collects name, WhatsApp number, plan (Monthly / Quarterly / Yearly) and fee paid.
   - The member is saved to Google Sheets with the join date and expiry date calculated automatically (30 / 90 / 365 days).
   - A welcome message is sent on WhatsApp.
2. **Expiry reminders**
   - Runs every day at 9:00 AM.
   - Members whose membership expires in 3 days, or today, get a WhatsApp reminder.
3. **Weekly owner report**
   - Runs every Monday at 9:00 AM.
   - Sends the owner a WhatsApp summary: total, active and expired members, new members in the last 7 days, memberships expiring this week, and income from new members.

## Tech Stack

- [n8n](https://n8n.io) (Form Trigger, Schedule Trigger, Code, Google Sheets, WhatsApp Business Cloud nodes)
- Google Sheets as the database
- WhatsApp Business Cloud API (Meta)

## Workflow Overview

| Flow | Trigger | Steps |
|------|---------|-------|
| Signup | Member Signup Form | Prepare Member Data → Save to Members Sheet → WhatsApp Welcome |
| Reminder | Daily 9AM Check | Get Members → Find Expiring Memberships → WhatsApp Renewal Reminder |
| Report | Weekly Report Trigger (Monday 9AM) | Get Members → Build Weekly Report → WhatsApp Report to Owner |

## Setup

### 1. Google Sheet

Create a sheet with a tab named `Members` and these columns (exact spelling):

| Name | Phone | Plan | Fee | Join Date | Expiry | Status |
|------|-------|------|-----|-----------|--------|--------|

### 2. Import the workflow

In n8n: **Workflows → Import from File** and select `gym-automation-n8n.json`.

### 3. Configure credentials

- **Google Sheets nodes (3):** select your Google Sheets credential and set your Sheet ID.
- **WhatsApp nodes (4):** select your WhatsApp Business Cloud credential and set your Phone Number ID.
- In **WhatsApp Report to Owner**, replace `OWNER_PHONE` with the owner's number in international format, e.g. `923001234567`.

### 4. Test and activate

1. Open the form's test URL and submit a sample member.
2. Check that the row appears in the sheet and the welcome message arrives.
3. Activate the workflow.

## Notes

- Phone numbers are normalized automatically: `03001234567` becomes `923001234567`.
- Per Meta's rules, business-initiated WhatsApp messages need an approved message template. In testing mode, messages only go to verified numbers.
- The reminder window (3 days before expiry) can be changed in the **Find Expiring Memberships** code node.

## Possible Improvements

- Add an AI node to write a personalized motivational message per member.
- Mark members as `Expired` in the sheet automatically.
- Send the weekly report by email as well.

## License

MIT
