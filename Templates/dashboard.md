# 🏰 Kampagnen-Dashboard

Willkommen im Kampagnen-Hub! Hier findest du alle relevanten Infos auf einen Blick.

---

## 🎯 Aktive Quests
```dataview
table status as "Status", auftraggeber as "Auftraggeber", start-date as "Gestartet", description as "Beschreibung"
from "Quests"
where contains(tags, "quest") and status = "Aktiv"
sort start-date asc
```

---

## 📜 Letzte Sessions
```dataview
table date as "Datum", title as "Titel"
from "Session Logs"
where date
sort date desc
limit 5
```

---

## 🎭 Neue oder zuletzt geänderte NPCs
```dataview
table file.mtime as "Zuletzt geändert"
from "Charaktere/NPCs"
sort file.mtime desc
limit 5
```

---

## 🧠 Offene Aufgaben & Lose Enden
```dataview
task from "Session Logs"
where !completed
```

---

## 🗺️ Zuletzt relevante Orte
```dataview
table file.mtime as "Zuletzt geändert"
from "Orte"
sort file.mtime desc
limit 5
```
