# Character Builder

```dataviewjs
const CHARACTER_FOLDER = "Public/3 Backend/";
const CLASSES_FOLDER = "Public/3 Backend/Classes/";

// Optional: Files that should never appear as selectable characters.
const EXCLUDED_CHARACTER_FILES = new Set([
  "Character Builder.md"
]);

function clampInt(value, min, max, fallback) {
  const n = Math.floor(Number(value));
  if (!Number.isFinite(n)) return fallback;
  return Math.max(min, Math.min(max, n));
}

function modFromScore(score) {
  return Math.floor((Number(score) - 10) / 2);
}

function modString(n) {
  return n >= 0 ? `+${n}` : `${n}`;
}

function getMarkdownFilesInFolder(folderPath) {
  const normalized = folderPath.endsWith("/") ? folderPath : `${folderPath}/`;

  return app.vault.getMarkdownFiles()
    .filter(file => {
      if (!file.path.startsWith(normalized)) return false;

      // Only direct children of the folder, not Items/, Classes/, etc.
      const relative = file.path.slice(normalized.length);
      if (relative.includes("/")) return false;

      return !EXCLUDED_CHARACTER_FILES.has(file.name);
    })
    .sort((a, b) => a.basename.localeCompare(b.basename, "de"));
}

function getClassFiles() {
  const normalized = CLASSES_FOLDER.endsWith("/") ? CLASSES_FOLDER : `${CLASSES_FOLDER}/`;

  return app.vault.getMarkdownFiles()
    .filter(file => {
      if (!file.path.startsWith(normalized)) return false;
      const relative = file.path.slice(normalized.length);
      return !relative.includes("/");
    })
    .sort((a, b) => a.basename.localeCompare(b.basename, "de"));
}

function getPage(file) {
  return file ? dv.page(file.path) : null;
}

function styleCard(el) {
  el.style.padding = "16px";
  el.style.border = "1px solid var(--background-modifier-border)";
  el.style.borderRadius = "14px";
  el.style.background = "var(--background-primary)";
}

function styleInput(el) {
  el.style.width = "100%";
  el.style.boxSizing = "border-box";
  el.style.padding = "8px 10px";
  el.style.border = "1px solid var(--background-modifier-border)";
  el.style.borderRadius = "8px";
  el.style.background = "var(--background-primary)";
  el.style.color = "var(--text-normal)";
}

function createField(parent, labelText) {
  const wrap = parent.createEl("div");
  wrap.style.display = "flex";
  wrap.style.flexDirection = "column";
  wrap.style.gap = "6px";

  const label = wrap.createEl("div", { text: labelText });
  label.style.fontSize = "0.85em";
  label.style.fontWeight = "600";
  label.style.opacity = "0.8";

  return wrap;
}

const characterFiles = getMarkdownFilesInFolder(CHARACTER_FOLDER);
const classFiles = getClassFiles();

const root = dv.el("div", "");
root.style.display = "flex";
root.style.flexDirection = "column";
root.style.gap = "18px";
root.style.maxWidth = "1050px";
root.style.margin = "0 auto";

// -----------------------------------------------------------------------------
// Header
// -----------------------------------------------------------------------------

const header = root.createEl("div");
styleCard(header);

const title = header.createEl("div", { text: "Character Builder" });
title.style.fontSize = "1.8em";
title.style.fontWeight = "700";

const subtitle = header.createEl("div", {
  text: "Edit the persistent character backend. Class progression remains defined by the class files."
});
subtitle.style.marginTop = "5px";
subtitle.style.opacity = "0.7";

// -----------------------------------------------------------------------------
// Character selection
// -----------------------------------------------------------------------------

const selectionCard = root.createEl("div");
styleCard(selectionCard);

const selectionTitle = selectionCard.createEl("div", { text: "Character" });
selectionTitle.style.fontWeight = "700";
selectionTitle.style.fontSize = "1.1em";
selectionTitle.style.marginBottom = "10px";

if (characterFiles.length === 0) {
  selectionCard.createEl("div", {
    text: `No character files found directly in ${CHARACTER_FOLDER}`
  });
  return;
}

const characterSelect = selectionCard.createEl("select");
styleInput(characterSelect);

for (const file of characterFiles) {
  const page = getPage(file);
  const displayName = String(page?.name ?? file.basename);

  const option = characterSelect.createEl("option", {
    text: `${displayName} — ${file.basename}`
  });
  option.value = file.path;
}

// Remember selection while Obsidian keeps the view alive.
const rememberedPath = window.__dndBuilderCharacterPath;
if (rememberedPath && characterFiles.some(file => file.path === rememberedPath)) {
  characterSelect.value = rememberedPath;
}

// -----------------------------------------------------------------------------
// Editor
// -----------------------------------------------------------------------------

const editorCard = root.createEl("div");
styleCard(editorCard);

let currentFile = null;
let currentPage = null;
let draft = null;
let dirty = false;

function setDirty(value) {
  dirty = value;
  saveButton.textContent = dirty ? "Save Changes *" : "Save Changes";
  unsavedText.style.display = dirty ? "block" : "none";
}

const editorHeader = editorCard.createEl("div");
editorHeader.style.display = "flex";
editorHeader.style.justifyContent = "space-between";
editorHeader.style.alignItems = "center";
editorHeader.style.gap = "12px";
editorHeader.style.marginBottom = "14px";

const editorTitle = editorHeader.createEl("div", { text: "Core Character Data" });
editorTitle.style.fontWeight = "700";
editorTitle.style.fontSize = "1.1em";

const fileInfo = editorHeader.createEl("div");
fileInfo.style.fontSize = "0.85em";
fileInfo.style.opacity = "0.65";

const generalGrid = editorCard.createEl("div");
generalGrid.style.display = "grid";
generalGrid.style.gridTemplateColumns = "minmax(220px, 1fr) minmax(140px, 180px)";
generalGrid.style.gap = "12px";

const classField = createField(generalGrid, "Class");
const classSelect = classField.createEl("select");
styleInput(classSelect);

const levelField = createField(generalGrid, "Level");

const levelRow = levelField.createEl("div");
levelRow.style.display = "grid";
levelRow.style.gridTemplateColumns = "40px 1fr 40px";
levelRow.style.gap = "6px";

const levelMinus = levelRow.createEl("button", { text: "−" });
const levelInput = levelRow.createEl("input");
levelInput.type = "number";
levelInput.min = "1";
levelInput.max = "20";
levelInput.step = "1";
styleInput(levelInput);
levelInput.style.textAlign = "center";

const levelPlus = levelRow.createEl("button", { text: "+" });

for (const btn of [levelMinus, levelPlus]) {
  btn.style.borderRadius = "8px";
  btn.style.border = "1px solid var(--background-modifier-border)";
  btn.style.background = "var(--background-secondary)";
  btn.style.color = "var(--text-normal)";
  btn.style.cursor = "pointer";
  btn.style.fontWeight = "700";
}

const statsTitle = editorCard.createEl("div", { text: "Ability Scores" });
statsTitle.style.fontWeight = "700";
statsTitle.style.marginTop = "20px";
statsTitle.style.marginBottom = "10px";

const statsGrid = editorCard.createEl("div");
statsGrid.style.display = "grid";
statsGrid.style.gridTemplateColumns = "repeat(6, minmax(90px, 1fr))";
statsGrid.style.gap = "8px";

const ABILITIES = [
  ["str", "STR"],
  ["dex", "DEX"],
  ["con", "CON"],
  ["int", "INT"],
  ["wis", "WIS"],
  ["cha", "CHA"]
];

const statControls = {};

for (const [key, labelText] of ABILITIES) {
  const card = statsGrid.createEl("div");
  card.style.padding = "10px";
  card.style.border = "1px solid var(--background-modifier-border)";
  card.style.borderRadius = "10px";
  card.style.textAlign = "center";

  const label = card.createEl("div", { text: labelText });
  label.style.fontWeight = "700";
  label.style.marginBottom = "7px";

  const input = card.createEl("input");
  input.type = "number";
  input.min = "1";
  input.max = "30";
  input.step = "1";
  styleInput(input);
  input.style.textAlign = "center";

  const modifier = card.createEl("div", { text: "+0" });
  modifier.style.marginTop = "6px";
  modifier.style.fontSize = "0.85em";
  modifier.style.opacity = "0.7";

  statControls[key] = { input, modifier };
}

// -----------------------------------------------------------------------------
// Preview
// -----------------------------------------------------------------------------

const previewCard = root.createEl("div");
styleCard(previewCard);

const previewTitle = previewCard.createEl("div", { text: "Preview" });
previewTitle.style.fontWeight = "700";
previewTitle.style.fontSize = "1.1em";
previewTitle.style.marginBottom = "10px";

const previewGrid = previewCard.createEl("div");
previewGrid.style.display = "grid";
previewGrid.style.gridTemplateColumns = "repeat(4, 1fr)";
previewGrid.style.gap = "8px";

function createPreviewBox(label) {
  const box = previewGrid.createEl("div");
  box.style.padding = "10px";
  box.style.border = "1px solid var(--background-modifier-border)";
  box.style.borderRadius = "10px";
  box.style.textAlign = "center";

  const labelEl = box.createEl("div", { text: label });
  labelEl.style.fontSize = "0.8em";
  labelEl.style.opacity = "0.65";

  const valueEl = box.createEl("div", { text: "-" });
  valueEl.style.fontWeight = "700";
  valueEl.style.fontSize = "1.05em";
  valueEl.style.marginTop = "4px";

  return valueEl;
}

const previewName = createPreviewBox("Character");
const previewClass = createPreviewBox("Class");
const previewLevel = createPreviewBox("Level");
const previewProf = createPreviewBox("Proficiency");

const previewNote = previewCard.createEl("div");
previewNote.style.marginTop = "10px";
previewNote.style.fontSize = "0.85em";
previewNote.style.opacity = "0.7";

// -----------------------------------------------------------------------------
// Save bar
// -----------------------------------------------------------------------------

const saveBar = root.createEl("div");
styleCard(saveBar);
saveBar.style.display = "flex";
saveBar.style.justifyContent = "space-between";
saveBar.style.alignItems = "center";
saveBar.style.gap = "12px";

const statusWrap = saveBar.createEl("div");

const statusText = statusWrap.createEl("div", { text: "Ready." });
statusText.style.fontWeight = "600";

const unsavedText = statusWrap.createEl("div", { text: "Unsaved changes" });
unsavedText.style.fontSize = "0.8em";
unsavedText.style.opacity = "0.65";
unsavedText.style.display = "none";

const buttonWrap = saveBar.createEl("div");
buttonWrap.style.display = "flex";
buttonWrap.style.gap = "8px";

const reloadButton = buttonWrap.createEl("button", { text: "Discard / Reload" });
const saveButton = buttonWrap.createEl("button", { text: "Save Changes" });

for (const btn of [reloadButton, saveButton]) {
  btn.style.padding = "8px 12px";
  btn.style.borderRadius = "8px";
  btn.style.border = "1px solid var(--background-modifier-border)";
  btn.style.cursor = "pointer";
  btn.style.fontWeight = "600";
}

saveButton.style.background = "var(--interactive-accent)";
saveButton.style.color = "var(--text-on-accent)";
reloadButton.style.background = "var(--background-secondary)";
reloadButton.style.color = "var(--text-normal)";

// -----------------------------------------------------------------------------
// Data handling
// -----------------------------------------------------------------------------

function getClassNameFromFile(file) {
  const page = getPage(file);
  return String(page?.name ?? file.basename);
}

function populateClassSelect(selectedClass) {
  classSelect.innerHTML = "";

  const empty = classSelect.createEl("option", { text: "— Select Class —" });
  empty.value = "";

  for (const file of classFiles) {
    const className = getClassNameFromFile(file);
    const option = classSelect.createEl("option", { text: className });
    option.value = className;
  }

  const wanted = String(selectedClass ?? "").trim();

  if (wanted && !classFiles.some(file => getClassNameFromFile(file) === wanted)) {
    const legacy = classSelect.createEl("option", {
      text: `${wanted} (class file not found)`
    });
    legacy.value = wanted;
  }

  classSelect.value = wanted;
}

function getSelectedClassPage() {
  const className = String(draft?.class ?? "").trim();
  if (!className) return null;

  const file = classFiles.find(file => getClassNameFromFile(file) === className);
  return file ? getPage(file) : null;
}

function getClassLevelData() {
  const classPage = getSelectedClassPage();
  if (!classPage?.levels) return null;

  const level = String(clampInt(draft?.level, 1, 20, 1));
  return classPage.levels?.[level] ?? classPage.levels?.[Number(level)] ?? null;
}

function refreshPreview() {
  if (!draft) return;

  previewName.setText(String(currentPage?.name ?? currentFile?.basename ?? "-"));
  previewClass.setText(String(draft.class || "-"));
  previewLevel.setText(String(draft.level ?? 1));

  const levelData = getClassLevelData();
  const prof = levelData?.proficiency_bonus;

  previewProf.setText(prof != null ? modString(Number(prof)) : "-");

  if (levelData) {
    previewNote.setText("Class progression data found for this level.");
  } else if (draft.class) {
    previewNote.setText("No level progression data found for this class/level.");
  } else {
    previewNote.setText("Select a class to preview its progression.");
  }
}

function refreshStatModifiers() {
  for (const [key] of ABILITIES) {
    const control = statControls[key];
    const score = clampInt(control.input.value, 1, 30, 10);
    control.modifier.setText(modString(modFromScore(score)));
  }
}

function syncDraftFromInputs() {
  if (!draft) return;

  draft.class = classSelect.value;
  draft.level = clampInt(levelInput.value, 1, 20, 1);

  for (const [key] of ABILITIES) {
    draft[key] = clampInt(statControls[key].input.value, 1, 30, 10);
  }

  refreshStatModifiers();
  refreshPreview();
  setDirty(true);
}

function loadCharacter(path) {
  const file = app.vault.getAbstractFileByPath(path);

  if (!file) {
    new Notice("Character file not found.");
    return;
  }

  const page = dv.page(path);

  if (!page) {
    new Notice("Character data could not be loaded.");
    return;
  }

  currentFile = file;
  currentPage = page;
  window.__dndBuilderCharacterPath = path;

  draft = {
    class: String(page.class ?? ""),
    level: clampInt(page.level, 1, 20, 1),
    str: clampInt(page.str, 1, 30, 10),
    dex: clampInt(page.dex, 1, 30, 10),
    con: clampInt(page.con, 1, 30, 10),
    int: clampInt(page.int, 1, 30, 10),
    wis: clampInt(page.wis, 1, 30, 10),
    cha: clampInt(page.cha, 1, 30, 10)
  };

  fileInfo.setText(file.path);
  populateClassSelect(draft.class);

  levelInput.value = String(draft.level);

  for (const [key] of ABILITIES) {
    statControls[key].input.value = String(draft[key]);
  }

  refreshStatModifiers();
  refreshPreview();
  setDirty(false);
  statusText.setText("Character loaded.");
}

async function saveCharacter() {
  if (!currentFile || !draft) return;

  syncDraftFromInputs();

  const savedDraft = {
    class: String(draft.class ?? "").trim(),
    level: clampInt(draft.level, 1, 20, 1),
    str: clampInt(draft.str, 1, 30, 10),
    dex: clampInt(draft.dex, 1, 30, 10),
    con: clampInt(draft.con, 1, 30, 10),
    int: clampInt(draft.int, 1, 30, 10),
    wis: clampInt(draft.wis, 1, 30, 10),
    cha: clampInt(draft.cha, 1, 30, 10)
  };

  saveButton.disabled = true;
  reloadButton.disabled = true;
  statusText.setText("Saving…");

  try {
    await app.fileManager.processFrontMatter(currentFile, (fm) => {
      fm.class = savedDraft.class;
      fm.level = savedDraft.level;

      fm.str = savedDraft.str;
      fm.dex = savedDraft.dex;
      fm.con = savedDraft.con;
      fm.int = savedDraft.int;
      fm.wis = savedDraft.wis;
      fm.cha = savedDraft.cha;
    });

    // Keep local state consistent without waiting for Dataview indexing.
    draft = { ...savedDraft };
    currentPage = {
      ...currentPage,
      class: savedDraft.class,
      level: savedDraft.level,
      str: savedDraft.str,
      dex: savedDraft.dex,
      con: savedDraft.con,
      int: savedDraft.int,
      wis: savedDraft.wis,
      cha: savedDraft.cha
    };

    setDirty(false);
    refreshPreview();
    statusText.setText("Saved.");
    new Notice("Character saved.");
  } catch (err) {
    console.error(err);
    statusText.setText("Save failed.");
    new Notice(`Save failed: ${err.message ?? err}`);
  } finally {
    saveButton.disabled = false;
    reloadButton.disabled = false;
  }
}

// -----------------------------------------------------------------------------
// Events
// -----------------------------------------------------------------------------

characterSelect.addEventListener("change", () => {
  if (dirty) {
    const proceed = confirm("Discard unsaved changes and switch character?");
    if (!proceed) {
      characterSelect.value = currentFile?.path ?? characterSelect.value;
      return;
    }
  }

  loadCharacter(characterSelect.value);
});

classSelect.addEventListener("change", syncDraftFromInputs);
levelInput.addEventListener("input", syncDraftFromInputs);

levelMinus.addEventListener("click", () => {
  levelInput.value = String(clampInt(Number(levelInput.value) - 1, 1, 20, 1));
  syncDraftFromInputs();
});

levelPlus.addEventListener("click", () => {
  levelInput.value = String(clampInt(Number(levelInput.value) + 1, 1, 20, 1));
  syncDraftFromInputs();
});

for (const [key] of ABILITIES) {
  statControls[key].input.addEventListener("input", syncDraftFromInputs);
}

reloadButton.addEventListener("click", () => {
  if (!currentFile) return;

  if (dirty) {
    const proceed = confirm("Discard all unsaved changes?");
    if (!proceed) return;
  }

  loadCharacter(currentFile.path);
});

saveButton.addEventListener("click", saveCharacter);

// -----------------------------------------------------------------------------
// Initial load
// -----------------------------------------------------------------------------

loadCharacter(characterSelect.value);
```
