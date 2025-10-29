---
banner: https://i.pinimg.com/736x/e3/a0/85/e3a085296c94a8219565f5641429a5cc.jpg
banner_y: 0.364
notetoolbar: none
dg-publish:
---
>[!multi-column] Arbeitsbereich
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
> [!multi-column] Relevante Dateien
>
> > [!example]+ Letzte Sessions
>>```dataview
> >List
> >From ""
> >WHERE regexmatch("^Session\\s*\\d+$", file.name)
> >SORT number(replace(file.name, "[^0-9]", "")) DESC
> >LIMIT 5
>
>> [!tip]+ Charaktäre
>> - [[Castor Aegis]]
>> - [[Leo Eisenfaust]]
>> - [[Rhuk]]
>> - [[Echo]]
>
> > [!warning]+ Relevante Orte
> > ```dataviewjs
> > // ── Ordnerpfade anpassen, falls nötig ──────────────────────────
> > const SESSIONS_FOLDER = "Public/Session Protocol";
> > const GEO_FOLDER      = "Public/Geographie";
> > // ───────────────────────────────────────────────────────────────
> >
> > // Neueste "Session X" ermitteln (größte Zahl im Namen)
> > const sessions = dv.pages()
> >   .where(p => p.file.folder === SESSIONS_FOLDER || p.file.path.startsWith(`${SESSIONS_FOLDER}/`))
> >   .where(p => /^Session\s*\d+$/i.test(p.file.name))
> >   .sort(p => Number(p.file.name.replace(/\D/g, "")), 'desc');
> >
> > if (sessions.length === 0) {
> >   dv.paragraph("⚠️ Keine Session-Dateien gefunden.");
> > } else {
> >   const latest = sessions[0]; // neueste Session
> >
> >   // Geographie-Dateien filtern, die einen eingehenden Link von dieser Session haben
> >   const places = dv.pages()
> >     .where(p => p.file.folder === GEO_FOLDER || p.file.path.startsWith(`${GEO_FOLDER}/`))
> >     .where(p => (p.file.inlinks ?? [])
> >       .some(l => (l.path || "").replace(/#.*/, "") === latest.file.path))
> >     .limit(5);
> >
> >   if (places.length === 0) {
> >     dv.paragraph(`Keine Orte, die [[${latest.file.name}]] verlinken.`);
> >   } else {
> >     dv.list(places.file.link);
> >   }
> > }
> > ```
>
>
> > [!danger]+ Relevante NPC's
> > ```dataviewjs
> > const SESSIONS_FOLDER = "Public/Session Protocol";
> > const NPC_FOLDERS = [
> >   "Public/Characters/NPC's",
> >   "Public/Monster"
> > ];
> >
> > // Neueste "Session X" ermitteln (größte Zahl im Namen)
> > const sessions = dv.pages()
> >   .where(p => p.file.folder === SESSIONS_FOLDER || p.file.path.startsWith(`${SESSIONS_FOLDER}/`))
> >   .where(p => /^Session\s*\d+$/i.test(p.file.name))
> >   .sort(p => Number(p.file.name.replace(/\D/g, "")), 'desc');
> >
> > if (sessions.length === 0) {
> >   dv.paragraph("⚠️ Keine Session-Dateien gefunden.");
> > } else {
> >   const latest = sessions[0];
> >   const npcs = dv.pages()
> >     .where(p => NPC_FOLDERS.some(folder =>
> >       p.file.folder === folder || p.file.path.startsWith(`${folder}/`)
> >     ))
> >     .where(p => (p.file.inlinks ?? [])
> >       .some(l => (l.path || "").replace(/#.*/, "") === latest.file.path))
> >     .limit(5);
> >
> >   if (npcs.length === 0) {
> >     dv.paragraph(`Keine NPCs oder Monster, die [[${latest.file.name}]] verlinken.`);
> >   } else {
> >     dv.list(npcs.file.link);
> >   }
> > }
> > ```
>
---
> [!multi-column] Neue Dateien
>
> > [!todo]+ Recently Created
> > ```dataview
> > LIST
> > FROM ""
> > SORT file.ctime DESC
> > LIMIT 5
> > ```
>
> > [!cite]+ Recently Modified
>> ```dataview 
> > List 
> > From ""
> > sort file.mtime Desc
> > Limit 5
> > ```


