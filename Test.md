```dataviewjs
const path = "Public/3 Backend/Characters/EIMER.md";

const file = app.vault.getAbstractFileByPath(path);
const character = dv.page(path);

dv.paragraph("Obsidian findet Datei: " + !!file);
dv.paragraph("Dataview findet Charakter: " + !!character);

if (file) {
    dv.paragraph("Tatsächlicher Pfad: " + file.path);
}

if (character) {
    dv.paragraph("Charaktername: " + character.name);
}
```