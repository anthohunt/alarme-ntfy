---
name: alarm
description: Use when the user wants a reminder that SOUNDS on their phone at a precise time ("set an alarm for 7am", "remind me tomorrow at 9 to call the dentist", "alarm in 20 minutes"). Posts an alarm that rings in a loop through a ntfy server. Not for plain to-dos (use a task list) nor for calendar events.
---

# Set a phone alarm through ntfy

Use `ntfy-alarm` (installed from this repository), never a hand-written curl, so the pre-notice,
the registry and the cancellation keep working.

```bash
ntfy-alarm "YYYY-MM-DD HH:MM" "Title" ["message"]
ntfy-alarm now "Title" ["message"]
ntfy-alarm cancel <id>
```

• The time is read in the time zone set by `ALARM_TZ` in the settings file, never in the server's clock
  (a server often runs in UTC: two hours off in Paris).
• The script prints the alarm `id`: keep it, it is the only way to cancel.
• Every dated alarm is preceded by a silent pre-notice two hours before (priority 2).
• The script refuses `date invalide` and `date dans le passe`; it never silently corrects a date.
• One call = one alarm, once. No recurrence.
• Success is confirmed only when the script prints the `id` and the time: the server has accepted and scheduled it.
