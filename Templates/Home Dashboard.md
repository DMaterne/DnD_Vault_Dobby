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
> > [!note]+ Books
>>```dataview
> > List
> > From #book
> > sort file.mtime Desc
> > limit 5
> > ```
>
> > [!info]+ Recent Projects
> > - [[30 Apps in 30 day]]
> > - [[The Gamified Life]]
> > - [[ZenFocus]]
> > - [[Obsidian Ninja]]
>
> > [!success]+ Journal
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
> >sort file.ctime Desc
> >Limit 5
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
// Calculate days since first note
const files = dv.pages()
const oldestFile = files.sort(f => f.file.ctime)[0]
const daysSinceStart = Math.floor((Date.now() - oldestFile.file.ctime) / (1000 * 60 * 60 * 24))
// Count total notes
const totalNotes = files.length
// Count unique tags
const allTags = files.flatMap(p => p.file.tags).distinct()
const totalTags = allTags.length
// Create a visually appealing display that works in both light and dark modes
dv.paragraph(`<div style="
  background-color: var(--background-secondary);
  border: 1px solid var(--background-modifier-border);
  border-radius: 10px;
  padding: 20px;
  text-align: center;
  font-family: var(--font-text);
  color: var(--text-normal);
">
  <h2 style="color: var(--text-normal);">📊 Obsidian Stats</h2>
  <p style="font-size: 18px; margin: 10px 0;">
    🗓️ You've been using Obsidian for <strong>${daysSinceStart}</strong> days
  </p>
  <p style="font-size: 18px; margin: 10px 0;">
    📝 You have <strong>${totalNotes}</strong> notes
  </p>
  <p style="font-size: 18px; margin: 10px 0;">
    🏷️ You're using <strong>${totalTags}</strong> unique tags
  </p>
</div>`)
```