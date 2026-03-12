# ABHYASA-CDC-Assessment-Reminder-Automated-Email-Notifier-using-N8N
An automated N8N workflow that monitors the ABHYASA platform and sends email reminders to students who haven't attempted their assigned CDC assessments. Reminders are sent every 10 minutes for short assessments (≤2 hrs) and every 1 hour for longer ones — until the student completes the exam.

Nodes Used

Schedule Trigger — Runs the workflow every minute to continuously check for pending students

Execute SQL Query (1st) — Queries PostgreSQL to find students who are assigned an assessment, haven't attempted it yet, and are due for a reminder based on the cooldown interval

If — Checks if any students were returned (roll_number is not empty); stops the flow if no pending students

Loop Over Items — Iterates through each student one by one

Edit Fields — Prepares the data: student email, name, assessment link (abhyasa.nrcmec.org/assessment/{id}), deadline, and reminder message

Execute SQL Query (2nd) — Logs the notification into notification_log table so the same student isn't spammed before the cooldown expires

Merge — Combines the log result and student data together before validation

If1 — Final check to confirm assessment_id is valid before sending

Send Email — Sends the reminder email via SMTP from Email with the assessment link and deadline


How the Workflow Works


Schedule Trigger fires every minute

PostgreSQL is queried — it finds students who: have an active assessment running right now, haven't submitted yet, and haven't been notified within the cooldown window (10 min for ≤2hr exams, 1 hour for longer ones)

If no students qualify, the workflow stops

For each qualifying student, their details and a reminder message are prepared

The notification is logged to the DB to prevent duplicate emails

After merging and validating the data, an email is sent with the assessment link and deadline

Loop continues until all pending students are notified
