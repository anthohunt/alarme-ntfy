**English** · [Français](README.fr.md)

# alarme-ntfy

An alarm that **rings in a loop on your phone** at the set time, scheduled from a terminal or by an agent (Claude Code, script, scheduled task). It goes through [ntfy](https://ntfy.sh) (self-hosted server or `ntfy.sh`), so over the Internet only: no VPN, no SSH access to the phone.

## What is in the repository
- `bin/ntfy-alarm`: the command (bash + curl + python3). Sets and cancels alarms, keeps an `alarms.jsonl` log.
- `server/`: a `docker-compose.yml` and a `server.yml` to host your own ntfy server (private access).
- `skill/SKILL.md`: a Claude Code skill that teaches the agent to set the alarm.
- `ntfy.env.example`: the settings template.

Early notice: every dated alarm is preceded by a silent notification two hours before (priority 2).

The command's messages and the skill are in French.

## Install
1. **Server** (or use `https://ntfy.sh` with a long, secret topic):
   ```
   cd server && docker compose up -d
   docker exec -it ntfy ntfy user add --role=admin me
   docker exec -it ntfy ntfy token add me        # note the tk_... token
   ```
   Put an HTTPS reverse proxy in front of port 8080 and adapt `base-url` in `server/etc/server.yml`.
2. **Phone**: install the ntfy app, add your server, subscribe to the topic (for example `Alarms`, case matters) and, in the app settings, keep maximum-priority notifications on.
3. **Command**:
   ```
   sudo install -m 755 bin/ntfy-alarm /usr/local/bin/ntfy-alarm
   mkdir -p ~/.config/ntfy-alarm && cp ntfy.env.example ~/.config/ntfy-alarm/ntfy.env && chmod 600 ~/.config/ntfy-alarm/ntfy.env
   ```
   then edit `ntfy.env` (address, topic, token, time zone).
4. **Try it**: `ntfy-alarm now "Test" "does it ring?"`

## Usage
```
ntfy-alarm "2027-01-15 07:30" "Wake up" "Leaving at 8"
ntfy-alarm now "Title" "Message"
ntfy-alarm cancel <id>
```
The time is read in the `ALARM_TZ` time zone. The command prints the alarm's identifier; it refuses an invalid or past date.

## With Claude Code
Copy `skill/SKILL.md` to `~/.claude/skills/alarm/SKILL.md`.

## Licenses
Code: Apache 2.0 (`LICENSE`, `NOTICE`). Text: CC BY 4.0 (`LICENSE-docs`). ntfy itself (Apache 2.0 / GPL 2.0) is used as a separate program, not redistributed here.
