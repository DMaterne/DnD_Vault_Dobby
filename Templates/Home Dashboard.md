---
banner: https://i.pinimg.com/736x/e3/a0/85/e3a085296c94a8219565f5641429a5cc.jpg
banner_y: 0.364
notetoolbar: none
dg-publish:
---
>[!multi-column| Arbeitsbereich]
>
>> [!success]+ Templates
>> - [[City Template]]
>> - [[Map Template]]
>> - [[Session Template]]
>> - [[NPC Template]]
>
>> [!info]+ Wichtige Notizen
>> - [[Quest - Expedition zur Erschließung der südöstlichen Wälder| Expedition der mutierten Wälder]]
>> - [[Mindmap Expedition.canvas|Mindmap Expedition]]
>> - [[Ziele]]
>> - [[Home Dashboard]]

---
> [!multi-column]
>
> > [!note]+ Letzte Sessions
>>```dataview
> >List
> >From ""
> >WHERE regexmatch("^Session\\s*\\d+$", file.name)
> >SORT number(replace(file.name, "[^0-9]", "")) DESC
> >LIMIT 5
>
> > [!info]+ Relevante Orte
> > - [[30 Apps in 30 day]]
> > - [[The Gamified Life]]
> > - [[ZenFocus]]
> > - [[Obsidian Ninja]]
>
> > [!info]+ Relevante NPCs
>> ```dataview
> > List
> > From "003 REFLECT/Journal"
> > sort file.mtime Desc
> > Limit 5
> > ```
---

---
> [!multi-column]
>
> > [!tip]+ Recently Created
>>```dataview
> >List
> >From ""
> >WHERE regexmatch("^Session\\s*\\d+$", file.name)
> >SORT number(replace(file.name, "[^0-9]", "")) DESC
> >LIMIT 4
> >```
>
> > [!example]+ Recently Modified
>> ```dataview 
> > List 
> > From ""
> > sort file.mtime Desc
> > Limit 5
> > ```

```dataviewjs
const FOLDER = "Public/Session Protocol";
const SHOW_OUTPUT = true;

const sessions = dv.pages()
  .where(p => p.file.path.startsWith(`${FOLDER}/`) || p.file.folder === FOLDER)
  .where(p => /^Session\s*\d+$/i.test(p.file.name))
  .sort(p => Number(p.file.name.replace(/\D/g, "")), 'desc');

if (sessions.length === 0) {
  dv.paragraph("⚠️ Keine Session-Dateien gefunden.");
} else {
  const latestSession = sessions[0].file.link;
  const latestName    = sessions[0].file.name;
  const latestPath    = sessions[0].file.path;

  if (SHOW_OUTPUT) dv.paragraph(`Neueste Session: ${latestSession}`);
  // latestSession / latestName / latestPath stehen dir hier zur Verfügung
}


```

```dataview
LIST
FROM "Public/Geographie"
WHERE contains(file.inlinks,[[Session 4]])
LIMIT 5
```



```dataviewjs
// ── Ordnerpfade anpassen, falls nötig ──────────────────────────
const SESSIONS_FOLDER = "Public/Session Protocol";
const GEO_FOLDER      = "Public/Geographie";
// ───────────────────────────────────────────────────────────────

// Neueste "Session X" ermitteln (größte Zahl im Namen)
const sessions = dv.pages()
  .where(p => p.file.folder === SESSIONS_FOLDER || p.file.path.startsWith(`${SESSIONS_FOLDER}/`))
  .where(p => /^Session\s*\d+$/i.test(p.file.name))
  .sort(p => Number(p.file.name.replace(/\D/g, "")), 'desc');

if (sessions.length === 0) {
  dv.paragraph("⚠️ Keine Session-Dateien gefunden.");
} else {
  const latest = sessions[0]; // neueste Session

  // Geographie-Dateien filtern, die einen eingehenden Link von dieser Session haben
  const places = dv.pages()
    .where(p => p.file.folder === GEO_FOLDER || p.file.path.startsWith(`${GEO_FOLDER}/`))
    .where(p => (p.file.inlinks ?? [])
      .some(l => (l.path || "").replace(/#.*/, "") === latest.file.path))
    .limit(5);

  if (places.length === 0) {
    dv.paragraph(`Keine Orte, die [[${latest.file.name}]] verlinken.`);
  } else {
    //dv.header(3, `Relevante Orte aus [[${latest.file.name}]]`);
    dv.list(places.file.link);
  }
}


```
