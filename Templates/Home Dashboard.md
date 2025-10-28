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
// 1) Neueste "Session X" finden (größte Zahl im Namen)
const latestSession = dv.pages()
  .where(p => /^Session\s*\d+$/i.test(p.file.name))
  .sort(p => Number(p.file.name.replace(/[^0-9]/g, "")), 'desc')
  .first();

if (!latestSession) {
  dv.paragraph("⚠️ Keine Session-Dateien gefunden.");
} else {
  // 2) Geographie-Dateien laden (Ordnerpfad anpassen, falls nötig)
  const geoPages = dv.pages("Public/Geographie");

  // 3) Filtern: Nur die, die [[Session N]] in den eingehenden Links haben
  const results = geoPages
    .where(p => Array.isArray(p.file.inlinks) && p.file.inlinks
      .some(link => link.path === latestSession.file.path))
    .limit(5);

  dv.header(3, `Orte, die auf [[${latestSession.file.name}]] verlinken`);
  if (results.length === 0) {
    dv.paragraph("Keine Treffer.");
  } else {
    dv.list(results.map(p => p.file.link));
  }
}

```

```dataview
LIST
FROM "Public/Geographie"
WHERE contains(file.inlinks, [[Session 4]])
LIMIT 5
```



```dataview
LIST
FROM ""
WHERE regexmatch("^Session\\s*\\d+$", file.name)
SORT number(replace(file.name, "[^0-9]", "")) DESC
LIMIT 1
```
