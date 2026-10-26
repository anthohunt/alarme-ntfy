# alarme-ntfy

Une alarme qui **sonne en boucle sur votre téléphone** à l'heure dite, posée depuis un terminal ou par un agent (Claude Code, script, tâche planifiée). Elle passe par [ntfy](https://ntfy.sh) (serveur auto-hébergé ou `ntfy.sh`), donc par Internet seulement : pas de VPN, pas d'accès SSH au téléphone.

## Ce que contient le dépôt
• `bin/ntfy-alarm` : la commande (bash + curl + python3). Pose, annule, tient un registre `alarms.jsonl`.
• `server/` : un `docker-compose.yml` et un `server.yml` pour héberger votre propre serveur ntfy (accès privé).
• `skill/SKILL.md` : un skill pour Claude Code qui apprend à l'agent à poser l'alarme.
• `ntfy.env.example` : le modèle de réglages.

Pré-avis : toute alarme datée est précédée d'une notification silencieuse deux heures avant (priorité 2).

## Installer
1. **Serveur** (ou utilisez `https://ntfy.sh` avec un sujet long et secret) :
   ```
   cd server && docker compose up -d
   docker exec -it ntfy ntfy user add --role=admin moi
   docker exec -it ntfy ntfy token add moi        # note le jeton tk_...
   ```
   Mettez un reverse proxy HTTPS devant le port 8080 et adaptez `base-url` dans `server/etc/server.yml`.
2. **Téléphone** : installez l'application ntfy, ajoutez votre serveur, abonnez-vous au sujet (par exemple `Alarms`, la casse compte) et, dans les réglages de l'app, gardez les notifications de priorité maximale.
3. **Commande** :
   ```
   sudo install -m 755 bin/ntfy-alarm /usr/local/bin/ntfy-alarm
   mkdir -p ~/.config/ntfy-alarm && cp ntfy.env.example ~/.config/ntfy-alarm/ntfy.env && chmod 600 ~/.config/ntfy-alarm/ntfy.env
   ```
   puis éditez `ntfy.env` (adresse, sujet, jeton, fuseau).
4. **Essai** : `ntfy-alarm now "Test" "ça sonne ?"`

## Utiliser
```
ntfy-alarm "2027-01-15 07:30" "Réveil" "Départ à 8h"
ntfy-alarm now "Titre" "Message"
ntfy-alarm cancel <id>
```
L'heure est lue dans le fuseau `ALARM_TZ`. La commande affiche l'identifiant de l'alarme ; elle refuse une date invalide ou passée.

## Avec Claude Code
Copiez `skill/SKILL.md` dans `~/.claude/skills/alarm/SKILL.md`.

## Licences
Code : Apache 2.0 (`LICENSE`, `NOTICE`). Texte : CC BY 4.0 (`LICENSE-docs`). ntfy lui-même (Apache 2.0 / GPL 2.0) est utilisé comme programme séparé, non redistribué ici.
