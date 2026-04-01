## Overview

```dataviewjs  
const CHARACTER_PATH = "Public/3 Backend/Georgius Obermüller.md";
const c = dv.page(CHARACTER_PATH);

function modFromScore(score) {
  return Math.floor((score - 10) / 2);
}

function modString(n) {
  return n >= 0 ? `+${n}` : `${n}`;
}

function profValue(isProf, base, profBonus) {
  return base + (isProf ? profBonus : 0);
}

function getAbilityModByName(name) {
  const key = String(name ?? "").toLowerCase();
  if (key === "str") return modFromScore(c.str ?? 10);
  if (key === "dex") return modFromScore(c.dex ?? 10);
  if (key === "con") return modFromScore(c.con ?? 10);
  if (key === "int") return modFromScore(c.int ?? 10);
  if (key === "wis") return modFromScore(c.wis ?? 10);
  if (key === "cha") return modFromScore(c.cha ?? 10);
  return 0;
}

if (!c) {
  dv.paragraph("Character-Datei nicht gefunden.");
} else {
  const wrapper = dv.el("div", "");
  wrapper.style.display = "flex";
  wrapper.style.flexDirection = "column";
  wrapper.style.gap = "18px";

  // HEADER
  const headerRow = wrapper.createEl("div");
headerRow.style.display = "grid";
headerRow.style.gridTemplateColumns = "1fr 220px 260px";
headerRow.style.gap = "18px";
headerRow.style.alignItems = "stretch";

// LEFT: HEADER CARD
const header = headerRow.createEl("div");
header.style.padding = "16px";
header.style.border = "1px solid var(--background-modifier-border)";
header.style.borderRadius = "14px";
header.style.display = "grid";
header.style.gridTemplateColumns = "1fr 360px";
header.style.gap = "16px";
header.style.alignItems = "center";

const headerText = header.createEl("div");

const title = headerText.createEl("div", { text: c.name ?? "Unbenannter Charakter" });
title.style.fontSize = "1.8em";
title.style.fontWeight = "700";

const subtitle = headerText.createEl("div");
subtitle.style.opacity = "0.75";
subtitle.style.marginTop = "4px";
subtitle.style.display = "flex";
subtitle.style.flexDirection = "column";
subtitle.style.gap = "4px";

function addHeaderInfoRow(parent, label, value) {
  const row = parent.createEl("div");
  
  const labelEl = row.createEl("span", { text: `${label}: ` });
  labelEl.style.fontWeight = "600";

  row.createEl("span", { text: String(value ?? "-") });
}

addHeaderInfoRow(subtitle, "Race", c.race ?? "Unknown Race");
addHeaderInfoRow(subtitle, "Class", c.class ?? "Unknown Class");
addHeaderInfoRow(subtitle, "Subclass", c.subclass ?? "-");
addHeaderInfoRow(subtitle, "Level", c.level ?? 1);

const meta = headerText.createEl("div");
meta.style.opacity = "0.7";
meta.style.marginTop = "6px";
meta.style.fontSize = "0.95em";
meta.style.display = "flex";
meta.style.flexDirection = "column";
meta.style.gap = "4px";

function addMetaInfoRow(parent, label, value) {
  const row = parent.createEl("div");

  const labelEl = row.createEl("span", { text: `${label}: ` });
  labelEl.style.fontWeight = "600";

  row.createEl("span", { text: String(value ?? "-") });
}

addMetaInfoRow(meta, "Background", c.background ?? "-");
addMetaInfoRow(meta, "Alignment", c.alignment ?? "-");
addMetaInfoRow(meta, "Player", c.player ?? "-");

const portraitWrap = header.createEl("div");
portraitWrap.style.display = "flex";
portraitWrap.style.flexDirection = "column";
portraitWrap.style.justifyContent = "center";
portraitWrap.style.alignItems = "center";
portraitWrap.style.gap = "8px";

const portraitPath = c.portrait ?? "Public/1 Assets/Bilder/NPC und Monster/NPC_Placeholder.png";
const portraitFile = app.vault.getAbstractFileByPath(portraitPath);
const portraitUrl = app.vault.adapter.getResourcePath(portraitPath);

const portraitAnchor = portraitWrap.createEl("a");
portraitAnchor.href = "#";
portraitAnchor.style.display = "block";
portraitAnchor.style.width = "240px";
portraitAnchor.style.height = "240px";
portraitAnchor.style.flexShrink = "0";

portraitAnchor.addEventListener("click", async (evt) => {
  evt.preventDefault();
  if (portraitFile) {
    await app.workspace.getLeaf(true).openFile(portraitFile);
  }
});

const portrait = portraitAnchor.createEl("img");
portrait.src = portraitUrl;
portrait.alt = c.name ?? "Character Portrait";
portrait.style.width = "240px";
portrait.style.height = "240px";
portrait.style.minWidth = "240px";
portrait.style.maxWidth = "240px";
portrait.style.minHeight = "240px";
portrait.style.maxHeight = "240px";
portrait.style.objectFit = "cover";
portrait.style.objectPosition = "center center";
portrait.style.display = "block";
portrait.style.borderRadius = "20px";
portrait.style.border = "1px solid var(--background-modifier-border)";
portrait.style.background = "var(--background-secondary)";
portrait.style.boxSizing = "border-box";


// MIDDLE: COMBAT VALUES CARD
const comValCard = headerRow.createEl("div");
comValCard.style.padding = "16px";
comValCard.style.border = "1px solid var(--background-modifier-border)";
comValCard.style.borderRadius = "14px";
comValCard.style.display = "flex";
comValCard.style.flexDirection = "column";
comValCard.style.gap = "10px";

const comValTitle = comValCard.createEl("div", { text: "Combat Values" });
comValTitle.style.fontWeight = "700";
comValTitle.style.fontSize = "1.1em";
comValTitle.style.marginBottom = "4px";

const comValGrid = comValCard.createEl("div");
comValGrid.style.display = "grid";
comValGrid.style.gridTemplateColumns = "1fr";
comValGrid.style.gap = "8px";

const combatValues = [
  ["Armor Class", c.ac ?? 10],
  ["Initiative", modString(c.initiative_bonus ?? modFromScore(c.dex ?? 10))],
  ["Speed", `${c.speed ?? 30} ft`],
];

for (const [label, value] of combatValues) {
  const row = comValGrid.createEl("div");
  row.style.padding = "10px";
  row.style.border = "1px solid var(--background-modifier-border)";
  row.style.borderRadius = "10px";
  row.style.textAlign = "center";

  const labelEl = row.createEl("div", { text: label });
  labelEl.style.fontSize = "0.8em";
  labelEl.style.opacity = "0.7";
  labelEl.style.marginBottom = "4px";

  const valueEl = row.createEl("div", { text: String(value) });
  valueEl.style.fontSize = "1.2em";
  valueEl.style.fontWeight = "700";
}

// RIGHT: HP CARD
const hpCard = headerRow.createEl("div");
hpCard.style.padding = "16px";
hpCard.style.border = "1px solid var(--background-modifier-border)";
hpCard.style.borderRadius = "14px";
hpCard.style.display = "flex";
hpCard.style.flexDirection = "column";
hpCard.style.gap = "12px";

const hpTitle = hpCard.createEl("div", { text: "Hit Points" });
hpTitle.style.fontWeight = "700";
hpTitle.style.fontSize = "1.1em";

const characterFile = app.vault.getAbstractFileByPath(CHARACTER_PATH);

let currentHp = Number(c.hp_current ?? 0);
const maxHp = Number(c.hp_max ?? 0);
let tempHpValue = Number(c.hp_temp ?? 0);

const hpMain = hpCard.createEl("div", {
  text: `${currentHp} / ${maxHp}`
});
hpMain.style.fontSize = "1.8em";
hpMain.style.fontWeight = "700";
hpMain.style.textAlign = "center";
hpMain.style.padding = "8px";
hpMain.style.border = "1px solid var(--background-modifier-border)";
hpMain.style.borderRadius = "10px";

const tempHp = hpCard.createEl("div", {
  text: `Temp HP: ${tempHpValue}`
});
tempHp.style.opacity = "0.8";
tempHp.style.textAlign = "center";

function refreshHpDisplay() {
  hpMain.setText(`${currentHp} / ${maxHp}`);
  tempHp.setText(`Temp HP: ${tempHpValue}`);
  tempInput.value = String(tempHpValue);
  deathSavesCard.style.display = currentHp <= 0 ? "block" : "none";
}

async function saveHpValues() {
  if (!characterFile) return;

  await app.fileManager.processFrontMatter(characterFile, (fm) => {
    fm.hp_current = currentHp;
    fm.hp_temp = tempHpValue;
  });
}

const actionRow = hpCard.createEl("div");
actionRow.style.display = "grid";
actionRow.style.gridTemplateColumns = "1fr auto auto";
actionRow.style.gap = "8px";
actionRow.style.alignItems = "center";

const hpValueInput = actionRow.createEl("input");
hpValueInput.type = "number";
hpValueInput.placeholder = "Wert eingeben";
hpValueInput.style.width = "100%";
hpValueInput.style.padding = "8px 10px";
hpValueInput.style.border = "1px solid var(--background-modifier-border)";
hpValueInput.style.borderRadius = "8px";
hpValueInput.style.background = "var(--background-primary)";
hpValueInput.style.color = "var(--text-normal)";

const damageBtn = actionRow.createEl("button", { text: "Damage" });
damageBtn.style.padding = "8px 12px";
damageBtn.style.borderRadius = "8px";
damageBtn.style.border = "1px solid var(--background-modifier-border)";
damageBtn.style.cursor = "pointer";
damageBtn.style.background = "var(--background-secondary)";
damageBtn.style.color = "var(--text-normal)";
damageBtn.style.fontWeight = "600";

const healBtn = actionRow.createEl("button", { text: "Heal" });
healBtn.style.padding = "8px 12px";
healBtn.style.borderRadius = "8px";
healBtn.style.border = "1px solid var(--background-modifier-border)";
healBtn.style.cursor = "pointer";
healBtn.style.background = "var(--background-secondary)";
healBtn.style.color = "var(--text-normal)";
healBtn.style.fontWeight = "600";

const tempRow = hpCard.createEl("div");
tempRow.style.display = "grid";
tempRow.style.gridTemplateColumns = "70px 1fr auto";
tempRow.style.gap = "8px";
tempRow.style.alignItems = "center";

const tempLabel = tempRow.createEl("div", { text: "Temp HP" });
tempLabel.style.fontWeight = "600";

const tempInput = tempRow.createEl("input");
tempInput.type = "number";
tempInput.placeholder = String(tempHpValue);
tempInput.value = String(tempHpValue);
tempInput.style.width = "100%";
tempInput.style.padding = "8px 10px";
tempInput.style.border = "1px solid var(--background-modifier-border)";
tempInput.style.borderRadius = "8px";
tempInput.style.background = "var(--background-primary)";
tempInput.style.color = "var(--text-normal)";

const tempSaveBtn = tempRow.createEl("button", { text: "Set" });
tempSaveBtn.style.padding = "8px 12px";
tempSaveBtn.style.borderRadius = "8px";
tempSaveBtn.style.border = "1px solid var(--background-modifier-border)";
tempSaveBtn.style.cursor = "pointer";
tempSaveBtn.style.background = "var(--background-secondary)";
tempSaveBtn.style.color = "var(--text-normal)";
tempSaveBtn.style.fontWeight = "600";

damageBtn.addEventListener("click", async () => {
  let amount = Number(hpValueInput.value ?? 0);
  if (!Number.isFinite(amount) || amount <= 0) return;

  amount = Math.floor(amount);

  if (tempHpValue > 0) {
    const absorbed = Math.min(tempHpValue, amount);
    tempHpValue -= absorbed;
    amount -= absorbed;
  }

  if (amount > 0) {
    currentHp = Math.max(0, currentHp - amount);
  }

  hpValueInput.value = "";
  refreshHpDisplay();
  await saveHpValues();
});

healBtn.addEventListener("click", async () => {
  let amount = Number(hpValueInput.value ?? 0);
  if (!Number.isFinite(amount) || amount <= 0) return;

  amount = Math.floor(amount);
  currentHp = Math.min(maxHp, currentHp + amount);

  hpValueInput.value = "";
  refreshHpDisplay();
  await saveHpValues();
});

tempSaveBtn.addEventListener("click", async () => {
  const value = Number(tempInput.value ?? 0);
  tempHpValue = Math.max(0, Math.floor(value || 0));
  refreshHpDisplay();
  await saveHpValues();
});

const deathSavesCard = hpCard.createEl("div");
deathSavesCard.style.marginTop = "8px";
deathSavesCard.style.padding = "10px";
deathSavesCard.style.border = "1px solid var(--background-modifier-border)";
deathSavesCard.style.borderRadius = "10px";
deathSavesCard.style.display = currentHp <= 0 ? "block" : "none";

const deathSavesTitle = deathSavesCard.createEl("div", { text: "Death Saves" });
deathSavesTitle.style.fontWeight = "700";
deathSavesTitle.style.marginBottom = "8px";

function createDeathSaveRow(labelText) {
  const row = deathSavesCard.createEl("div");
  row.style.display = "grid";
  row.style.gridTemplateColumns = "70px repeat(3, 20px)";
  row.style.gap = "8px";
  row.style.alignItems = "center";
  row.style.marginBottom = "6px";

  const label = row.createEl("div", { text: labelText });
  label.style.fontWeight = "600";

  for (let i = 0; i < 3; i++) {
    const checkbox = row.createEl("input");
    checkbox.type = "checkbox";
  }
}

createDeathSaveRow("Fail");
createDeathSaveRow("Success");

  // ABILITY SCORES
  const abilitiesCard = wrapper.createEl("div");
  abilitiesCard.style.padding = "8px";
  abilitiesCard.style.border = "1px solid var(--background-modifier-border)";
  abilitiesCard.style.borderRadius = "7px";

  const abilitiesTitle = abilitiesCard.createEl("div", { text: "Ability Scores" });
  abilitiesTitle.style.fontWeight = "700";
  abilitiesTitle.style.marginBottom = "6px";
  abilitiesTitle.style.fontSize = "1.1em";

  const abilityGrid = abilitiesCard.createEl("div");
  abilityGrid.style.display = "grid";
  abilityGrid.style.gridTemplateColumns = "repeat(6, 1fr)";
  abilityGrid.style.gap = "6px";

  const stats = [
    ["STR", c.str ?? 10],
    ["DEX", c.dex ?? 10],
    ["CON", c.con ?? 10],
    ["INT", c.int ?? 10],
    ["WIS", c.wis ?? 10],
    ["CHA", c.cha ?? 10],
  ];

  for (const [label, score] of stats) {
    const mod = modFromScore(score);

    const card = abilityGrid.createEl("div");
    card.style.padding = "14px";
    card.style.border = "1px solid var(--background-modifier-border)";
    card.style.borderRadius = "12px";
    card.style.textAlign = "center";

    const labelEl = card.createEl("div", { text: label });
    labelEl.style.fontSize = "0.85em";
    labelEl.style.opacity = "0.7";

    const modEl = card.createEl("div", { text: modString(mod) });
    modEl.style.fontSize = "1.5em";
    modEl.style.fontWeight = "700";
    modEl.style.marginTop = "6px";

    const scoreEl = card.createEl("div", { text: String(score) });
    scoreEl.style.fontSize = "0.85em";
    scoreEl.style.opacity = "0.75";
    scoreEl.style.marginTop = "4px";
  }

  // MAIN GRID
const mainGrid = wrapper.createEl("div");
mainGrid.style.display = "grid";
mainGrid.style.gridTemplateColumns = "1fr 1fr 2fr";
mainGrid.style.gap = "18px";

const leftCol = mainGrid.createEl("div");
leftCol.style.display = "flex";
leftCol.style.flexDirection = "column";
leftCol.style.gap = "9px";

const middleCol = mainGrid.createEl("div");
middleCol.style.display = "flex";
middleCol.style.flexDirection = "column";
middleCol.style.gap = "9px";

const rightCol = mainGrid.createEl("div");
rightCol.style.display = "flex";
rightCol.style.flexDirection = "column";
rightCol.style.gap = "9px";

  // SAVING THROWS
  const savesCard = leftCol.createEl("div");
  savesCard.style.padding = "8px";
  savesCard.style.border = "1px solid var(--background-modifier-border)";
  savesCard.style.borderRadius = "7px";

  const savesTitle = savesCard.createEl("div", { text: "Saving Throws" });
  savesTitle.style.fontWeight = "700";
  savesTitle.style.marginBottom = "6px";
  savesTitle.style.fontSize = "1.1em";

  const saveData = [
    ["STR", modFromScore(c.str ?? 10), c.str_save_prof ?? false],
    ["DEX", modFromScore(c.dex ?? 10), c.dex_save_prof ?? false],
    ["CON", modFromScore(c.con ?? 10), c.con_save_prof ?? false],
    ["INT", modFromScore(c.int ?? 10), c.int_save_prof ?? false],
    ["WIS", modFromScore(c.wis ?? 10), c.wis_save_prof ?? false],
    ["CHA", modFromScore(c.cha ?? 10), c.cha_save_prof ?? false],
  ];

  for (const [label, base, prof] of saveData) {
    const row = savesCard.createEl("div");
    row.style.display = "flex";
    row.style.justifyContent = "space-between";
    row.style.padding = "6px 0";
    row.style.borderBottom = "1px solid var(--background-modifier-border-hover)";

    row.createEl("div", { text: `${prof ? "●" : "○"} ${label}` });

    const right = row.createEl("div", {
      text: modString(profValue(prof, base, c.proficiency_bonus ?? 2))
    });
    right.style.fontWeight = "600";
  }

  // SKILLS
  const skillsCard = middleCol.createEl("div");
  skillsCard.style.padding = "6px";
  skillsCard.style.border = "1px solid var(--background-modifier-border)";
  skillsCard.style.borderRadius = "5px";

  const skillsTitle = skillsCard.createEl("div", { text: "Skills" });
  skillsTitle.style.fontWeight = "700";
  skillsTitle.style.marginBottom = "6px";
  skillsTitle.style.fontSize = "1.1em";

  const skills = [
    ["Acrobatics", modFromScore(c.dex ?? 10), c.acrobatics_prof ?? false],
    ["Animal Handling", modFromScore(c.wis ?? 10), c.animal_handling_prof ?? false],
    ["Arcana", modFromScore(c.int ?? 10), c.arcana_prof ?? false],
    ["Athletics", modFromScore(c.str ?? 10), c.athletics_prof ?? false],
    ["Deception", modFromScore(c.cha ?? 10), c.deception_prof ?? false],
    ["History", modFromScore(c.int ?? 10), c.history_prof ?? false],
    ["Insight", modFromScore(c.wis ?? 10), c.insight_prof ?? false],
    ["Intimidation", modFromScore(c.cha ?? 10), c.intimidation_prof ?? false],
    ["Investigation", modFromScore(c.int ?? 10), c.investigation_prof ?? false],
    ["Medicine", modFromScore(c.wis ?? 10), c.medicine_prof ?? false],
    ["Nature", modFromScore(c.int ?? 10), c.nature_prof ?? false],
    ["Perception", modFromScore(c.wis ?? 10), c.perception_prof ?? false],
    ["Performance", modFromScore(c.cha ?? 10), c.performance_prof ?? false],
    ["Persuasion", modFromScore(c.cha ?? 10), c.persuasion_prof ?? false],
    ["Religion", modFromScore(c.int ?? 10), c.religion_prof ?? false],
    ["Sleight of Hand", modFromScore(c.dex ?? 10), c.sleight_of_hand_prof ?? false],
    ["Stealth", modFromScore(c.dex ?? 10), c.stealth_prof ?? false],
    ["Survival", modFromScore(c.wis ?? 10), c.survival_prof ?? false],
  ];

  for (const [name, base, prof] of skills) {
    const row = skillsCard.createEl("div");
    row.style.display = "flex";
    row.style.justifyContent = "space-between";
    row.style.padding = "6px 0";
    row.style.borderBottom = "1px solid var(--background-modifier-border-hover)";

    row.createEl("div", { text: `${prof ? "●" : "○"} ${name}` });

    const value = row.createEl("div", {
      text: modString(profValue(prof, base, c.proficiency_bonus ?? 2))
    });
    value.style.fontWeight = "600";
  }

  // TAB CARD
  const tabCard = rightCol.createEl("div");
  tabCard.style.padding = "16px";
  tabCard.style.border = "1px solid var(--background-modifier-border)";
  tabCard.style.borderRadius = "14px";
  tabCard.style.display = "flex";
  tabCard.style.flexDirection = "column";
  tabCard.style.gap = "12px";

  const tabTitle = tabCard.createEl("div", { text: "Character Details" });
  tabTitle.style.fontWeight = "700";
  tabTitle.style.fontSize = "1.1em";

  const tabBar = tabCard.createEl("div");
  tabBar.style.display = "flex";
  tabBar.style.gap = "8px";
  tabBar.style.flexWrap = "wrap";

  const tabContent = tabCard.createEl("div");
  tabContent.style.padding = "10px";
  tabContent.style.border = "1px solid var(--background-modifier-border-hover)";
  tabContent.style.borderRadius = "10px";
  tabContent.style.minHeight = "220px";

  let activeTab = "actions";
  const tabButtons = {};

  function clearEl(el) {
    el.innerHTML = "";
  }

  function renderFeatures() {
  clearEl(tabContent);

  const title = tabContent.createEl("div", { text: "Features & Traits" });
  title.style.fontWeight = "700";
  title.style.fontSize = "1.05em";
  title.style.marginBottom = "10px";

  const features = Array.isArray(c.features) ? c.features : [];

  if (features.length === 0) {
    tabContent.createEl("div", { text: "Keine Features eingetragen." });
    return;
  }

  const searchWrap = tabContent.createEl("div");
  searchWrap.style.marginBottom = "10px";

  const searchInput = searchWrap.createEl("input");
  searchInput.type = "text";
  searchInput.placeholder = "Features durchsuchen...";
  searchInput.style.width = "100%";
  searchInput.style.padding = "8px 10px";
  searchInput.style.border = "1px solid var(--background-modifier-border)";
  searchInput.style.borderRadius = "8px";
  searchInput.style.background = "var(--background-primary)";
  searchInput.style.color = "var(--text-normal)";

  const tableWrap = tabContent.createEl("div");
  tableWrap.style.display = "flex";
  tableWrap.style.flexDirection = "column";
  tableWrap.style.gap = "6px";

  function renderFeatureRows(filterText = "") {
    tableWrap.innerHTML = "";

    const header = tableWrap.createEl("div");
    header.style.display = "grid";
    header.style.gridTemplateColumns = "2fr 1fr 120px";
    header.style.gap = "12px";
    header.style.padding = "8px 10px";
    header.style.fontWeight = "700";
    header.style.borderBottom = "1px solid var(--background-modifier-border)";

    header.createEl("div", { text: "Name" });
    header.createEl("div", { text: "Source" });
    header.createEl("div", { text: "Notes" });

    const filtered = features.filter(item => {
      const name = String(item?.name ?? item ?? "").toLowerCase();
      const source = String(item?.source ?? "").toLowerCase();
      const notes = String(item?.notes ?? "").toLowerCase();
      const query = String(filterText ?? "").trim().toLowerCase();

      if (!query) return true;
      return name.includes(query) || source.includes(query) || notes.includes(query);
    });

    if (filtered.length === 0) {
      const empty = tableWrap.createEl("div", { text: "Keine passenden Features gefunden." });
      empty.style.padding = "10px";
      empty.style.opacity = "0.7";
      return;
    }

    for (const item of filtered) {
      const featureName = typeof item === "string" ? item : String(item?.name ?? "-");
      const featureSource = typeof item === "string" ? "-" : String(item?.source ?? "-");
      const featureNotes = typeof item === "string" ? "" : String(item?.notes ?? "");

      const row = tableWrap.createEl("div");
      row.style.border = "1px solid var(--background-modifier-border)";
      row.style.borderRadius = "8px";
      row.style.overflow = "hidden";

      const summary = row.createEl("div");
      summary.style.display = "grid";
      summary.style.gridTemplateColumns = "2fr 1fr 120px";
      summary.style.gap = "12px";
      summary.style.padding = "10px";
      summary.style.alignItems = "center";

      summary.createEl("div", { text: featureName });
      summary.createEl("div", { text: featureSource });

      const notesButtonWrap = summary.createEl("div");
      const hasNotes = featureNotes.trim().length > 0;

      const notesButton = notesButtonWrap.createEl("button", {
        text: hasNotes ? "Show" : "-"
      });
      notesButton.style.padding = "4px 8px";
      notesButton.style.borderRadius = "6px";
      notesButton.style.border = "1px solid var(--background-modifier-border)";
      notesButton.style.cursor = hasNotes ? "pointer" : "default";
      notesButton.style.background = "var(--background-secondary)";
      notesButton.style.color = "var(--text-normal)";

      let notesOpen = false;
      let notesEl = null;

      if (hasNotes) {
        notesButton.addEventListener("click", () => {
          notesOpen = !notesOpen;

          if (notesOpen) {
            notesButton.setText("Hide");

            notesEl = row.createEl("div");
            notesEl.style.padding = "0 10px 10px 10px";
            notesEl.style.borderTop = "1px solid var(--background-modifier-border)";
            notesEl.style.background = "var(--background-primary-alt)";

            const notesTitle = notesEl.createEl("div", { text: "Notes" });
            notesTitle.style.fontWeight = "600";
            notesTitle.style.marginTop = "8px";
            notesTitle.style.marginBottom = "4px";
            notesTitle.style.opacity = "0.85";

            notesEl.createEl("div", { text: featureNotes });
          } else {
            notesButton.setText("Show");
            if (notesEl) notesEl.remove();
            notesEl = null;
          }
        });
      }
    }
  }

  renderFeatureRows();

  searchInput.addEventListener("input", () => {
    renderFeatureRows(searchInput.value);
  });
}

  function renderInventory() {
  clearEl(tabContent);

  const title = tabContent.createEl("div", { text: "Inventory" });
  title.style.fontWeight = "700";
  title.style.fontSize = "1.05em";
  title.style.marginBottom = "10px";

  const inventory = Array.isArray(c.inventory) ? c.inventory : [];

  if (inventory.length === 0) {
    tabContent.createEl("div", { text: "Kein Inventar eingetragen." });
    return;
  }

  const searchWrap = tabContent.createEl("div");
  searchWrap.style.marginBottom = "10px";

  const searchInput = searchWrap.createEl("input");
  searchInput.type = "text";
  searchInput.placeholder = "Inventar durchsuchen...";
  searchInput.style.width = "100%";
  searchInput.style.padding = "8px 10px";
  searchInput.style.border = "1px solid var(--background-modifier-border)";
  searchInput.style.borderRadius = "8px";
  searchInput.style.background = "var(--background-primary)";
  searchInput.style.color = "var(--text-normal)";

  const tableWrap = tabContent.createEl("div");
  tableWrap.style.display = "flex";
  tableWrap.style.flexDirection = "column";
  tableWrap.style.gap = "6px";

  function renderInventoryRows(filterText = "") {
    tableWrap.innerHTML = "";

    const header = tableWrap.createEl("div");
    header.style.display = "grid";
    header.style.gridTemplateColumns = "2fr 80px 80px 120px";
    header.style.gap = "12px";
    header.style.padding = "8px 10px";
    header.style.fontWeight = "700";
    header.style.borderBottom = "1px solid var(--background-modifier-border)";

    header.createEl("div", { text: "Name" });
    header.createEl("div", { text: "Weight" });
    header.createEl("div", { text: "Qty" });
    header.createEl("div", { text: "Notes" });

    const filtered = inventory.filter(item => {
      const name = String(item?.name ?? "").toLowerCase();
      const notes = String(item?.notes ?? "").toLowerCase();
      const query = String(filterText ?? "").trim().toLowerCase();

      if (!query) return true;
      return name.includes(query) || notes.includes(query);
    });

    if (filtered.length === 0) {
      const empty = tableWrap.createEl("div", { text: "Keine passenden Einträge gefunden." });
      empty.style.padding = "10px";
      empty.style.opacity = "0.7";
      return;
    }

    for (const item of filtered) {
      const row = tableWrap.createEl("div");
      row.style.border = "1px solid var(--background-modifier-border)";
      row.style.borderRadius = "8px";
      row.style.overflow = "hidden";

      const summary = row.createEl("div");
      summary.style.display = "grid";
      summary.style.gridTemplateColumns = "2fr 80px 80px 120px";
      summary.style.gap = "12px";
      summary.style.padding = "10px";
      summary.style.alignItems = "center";

      summary.createEl("div", { text: String(item?.name ?? "-") });
      summary.createEl("div", { text: String(item?.weight ?? "-") });
      summary.createEl("div", { text: String(item?.quantity ?? 1) });

      const notesButtonWrap = summary.createEl("div");

      const hasNotes = String(item?.notes ?? "").trim().length > 0;
      const notesButton = notesButtonWrap.createEl("button", {
        text: hasNotes ? "Show" : "-"
      });
      notesButton.style.padding = "4px 8px";
      notesButton.style.borderRadius = "6px";
      notesButton.style.border = "1px solid var(--background-modifier-border)";
      notesButton.style.cursor = hasNotes ? "pointer" : "default";
      notesButton.style.background = "var(--background-secondary)";
      notesButton.style.color = "var(--text-normal)";

      let notesOpen = false;
      let notesEl = null;

      if (hasNotes) {
        notesButton.addEventListener("click", () => {
          notesOpen = !notesOpen;

          if (notesOpen) {
            notesButton.setText("Hide");

            notesEl = row.createEl("div");
            notesEl.style.padding = "0 10px 10px 10px";
            notesEl.style.borderTop = "1px solid var(--background-modifier-border)";
            notesEl.style.background = "var(--background-primary-alt)";

            const notesTitle = notesEl.createEl("div", { text: "Notes" });
            notesTitle.style.fontWeight = "600";
            notesTitle.style.marginTop = "8px";
            notesTitle.style.marginBottom = "4px";
            notesTitle.style.opacity = "0.85";

            notesEl.createEl("div", { text: String(item.notes) });
          } else {
            notesButton.setText("Show");
            if (notesEl) notesEl.remove();
            notesEl = null;
          }
        });
      }
    }
  }

  renderInventoryRows();

  searchInput.addEventListener("input", () => {
    renderInventoryRows(searchInput.value);
  });
}

function renderActions() {
  clearEl(tabContent);

  const title = tabContent.createEl("div");
  title.style.display = "grid";
  title.style.gridTemplateColumns = "220px 80px 120px";
  title.style.gap = "100px";
  title.style.fontWeight = "700";
  title.style.marginBottom = "8px";
  title.style.padding = "0 10px";

  title.createEl("div", { text: "Actions" });
  title.createEl("div", { text: "Hit" });
  title.createEl("div", { text: "Damage" });

  const searchWrap = tabContent.createEl("div");
  searchWrap.style.marginBottom = "10px";

  const searchInput = searchWrap.createEl("input");
  searchInput.type = "text";
  searchInput.placeholder = "Actions durchsuchen...";
  searchInput.style.width = "100%";
  searchInput.style.padding = "8px 10px";
  searchInput.style.border = "1px solid var(--background-modifier-border)";
  searchInput.style.borderRadius = "8px";
  searchInput.style.background = "var(--background-primary)";
  searchInput.style.color = "var(--text-normal)";

  const actionContainer = tabContent.createEl("div");

  function sectionLabel(type) {
    if (type === "action") return "Action";
    if (type === "bonus_action") return "Bonus Action";
    if (type === "reaction") return "Reaction";
    if (type === "other") return "Other";
    return type;
  }

  function addInfoRow(parent, label, value) {
    const row = parent.createEl("div");
    row.style.display = "flex";
    row.style.justifyContent = "space-between";
    row.style.gap = "10px";
    row.style.padding = "2px 0";

    const left = row.createEl("div", { text: label });
    left.style.opacity = "0.7";

    const right = row.createEl("div", { text: String(value ?? "-") });
    right.style.fontWeight = "600";
    right.style.textAlign = "right";
  }

  function getAbilityModForAction(statName) {
    const key = String(statName ?? "").trim().toLowerCase();
    if (key === "str") return modFromScore(c.str ?? 10);
    if (key === "dex") return modFromScore(c.dex ?? 10);
    if (key === "con") return modFromScore(c.con ?? 10);
    if (key === "int") return modFromScore(c.int ?? 10);
    if (key === "wis") return modFromScore(c.wis ?? 10);
    if (key === "cha") return modFromScore(c.cha ?? 10);
    return 0;
  }

  const actionRefs = Array.isArray(c.actions) ? c.actions : [];

const actions = actionRefs
  .map(path => dv.page(String(path)))
  .filter(p => p)
  .filter(p => {
    const category = String(p.category ?? "").trim().toLowerCase();
    const rawType = String(p.action_type ?? "").trim().toLowerCase();
    const type = rawType.replace(/\s+/g, "_");

    return category !== "spell" && (
      type === "" ||
      type === "action" ||
      type === "bonus_action" ||
      type === "reaction"
    );
  });

const actionList = Array.from(actions).sort((a, b) => {
  const order = { action: 0, bonus_action: 1, reaction: 2 };

  const typeA = String(a.action_type ?? "").trim().toLowerCase().replace(/\s+/g, "_");
  const typeB = String(b.action_type ?? "").trim().toLowerCase().replace(/\s+/g, "_");

  if ((order[typeA] ?? 99) !== (order[typeB] ?? 99)) {
    return (order[typeA] ?? 99) - (order[typeB] ?? 99);
  }

  const nameA = String(a.name ?? a.file.name);
  const nameB = String(b.name ?? b.file.name);
  return nameA.localeCompare(nameB);
});

  if (actionList.length === 0) {
    actionContainer.createEl("div", { text: "Keine Actions gefunden." });
    return;
  }

  const groupedActions = {
    action: [],
    bonus_action: [],
    reaction: [],
    other: []
  };

  for (const action of actionList) {
    const rawType = String(action.action_type ?? "").trim().toLowerCase();
    const type = rawType.replace(/\s+/g, "_");

    if (groupedActions[type]) {
      groupedActions[type].push(action);
    } else {
      groupedActions.other.push(action);
    }
  }

  function renderActionRows(filterText = "") {
    actionContainer.innerHTML = "";
    const query = String(filterText ?? "").trim().toLowerCase();

    for (const type of ["action", "bonus_action", "reaction", "other"]) {
      const entries = groupedActions[type].filter(action => {
        const name = String(action.name ?? action.file.name ?? "").toLowerCase();
        const notes = String(action.notes ?? action.effect ?? "").toLowerCase();
        const mode = String(action.mode ?? "").toLowerCase();
        const damageType = String(action.damage_type ?? "").toLowerCase();

        if (!query) return true;
        return (
          name.includes(query) ||
          notes.includes(query) ||
          mode.includes(query) ||
          damageType.includes(query)
        );
      });

      if (!entries || entries.length === 0) continue;

      const section = actionContainer.createEl("div");
      section.style.marginTop = "12px";

      const sectionTitle = section.createEl("div", { text: sectionLabel(type) });
      sectionTitle.style.fontWeight = "700";
      sectionTitle.style.fontSize = "1.05em";
      sectionTitle.style.marginBottom = "8px";
      sectionTitle.style.paddingBottom = "4px";
      sectionTitle.style.borderBottom = "1px solid var(--background-modifier-border)";

      for (const action of entries) {
        const details = section.createEl("details");
        details.style.border = "1px solid var(--background-modifier-border)";
        details.style.borderRadius = "10px";
        details.style.marginTop = "8px";
        details.style.padding = "0";
        details.style.overflow = "hidden";

        const summaryEl = details.createEl("summary");
        summaryEl.style.cursor = "pointer";
        summaryEl.style.padding = "10px";
        summaryEl.style.display = "grid";
        summaryEl.style.gridTemplateColumns = "220px 80px 120px";
        summaryEl.style.gap = "100px";
        summaryEl.style.alignItems = "center";
        summaryEl.style.fontWeight = "700";
        summaryEl.style.listStyle = "none";

        const leftSummary = summaryEl.createEl("div", {
          text: action.name ?? action.file.name
        });
        leftSummary.style.fontSize = "1.05em";

        const attackStat = action.attack_stat ?? action.damage_bonus_stat ?? "str";
        const attackMod = getAbilityModForAction(attackStat);
        const profBonus = c.proficiency_bonus ?? 2;
        const isProficient = action.proficient ?? true;
        const toHitBonus = attackMod + (isProficient ? profBonus : 0);

        let damageTextA = "-";
        if (action.dice_count && action.dice_size) {
          damageTextA = `${action.dice_count}d${action.dice_size}`;

          const dmgBonus =
            Number(action.damage_bonus ?? 0) +
            getAbilityModForAction(action.damage_bonus_stat);

          if (dmgBonus > 0) damageTextA += `+${dmgBonus}`;
          if (dmgBonus < 0) damageTextA += `${dmgBonus}`;
        } else if (action.damage) {
          damageTextA = String(action.damage);
        }

        const hitText =
          String(action.mode ?? "").toLowerCase() === "attack"
            ? modString(toHitBonus)
            : "-";

        const hitEl = summaryEl.createEl("div", { text: hitText });
        hitEl.style.opacity = "0.7";
        hitEl.style.fontSize = "0.9em";
        hitEl.style.fontWeight = "400";

        const damageEl = summaryEl.createEl("div", { text: damageTextA });
        damageEl.style.opacity = "0.7";
        damageEl.style.fontSize = "0.9em";
        damageEl.style.fontWeight = "400";

        const card = details.createEl("div");
        card.style.padding = "10px";
        card.style.borderTop = "1px solid var(--background-modifier-border)";

        const infoBlock = card.createEl("div");

        addInfoRow(infoBlock, "Type", sectionLabel(type));
        addInfoRow(infoBlock, "Mode", action.mode ?? "-");
        addInfoRow(infoBlock, "Shape", action.shape ?? "-");

        if (action.range != null) addInfoRow(infoBlock, "Range", action.range);
        if (action.radius != null) addInfoRow(infoBlock, "Radius", action.radius);
        if (action.damage_type != null) addInfoRow(infoBlock, "Damage Type", action.damage_type);

        let damageText = "-";
        if (action.damage) {
          damageText = `${action.damage}${action.damage_type ? ` ${action.damage_type}` : ""}`;
        } else if (action.dice_count && action.dice_size) {
          damageText = `${action.dice_count}d${action.dice_size}${action.damage_bonus ? ` + ${action.damage_bonus}` : ""}${action.damage_type ? ` ${action.damage_type}` : ""}`;
        }
        addInfoRow(infoBlock, "Damage", damageText);

        if (String(action.mode ?? "").toLowerCase() === "attack") {
          addInfoRow(infoBlock, "To Hit", modString(toHitBonus));
        }

        if (action.save_ability) addInfoRow(infoBlock, "Save", String(action.save_ability).toUpperCase());
        if (action.resource_cost != null) addInfoRow(infoBlock, "Resource Cost", action.resource_cost);
        if (action.requires_los != null) addInfoRow(infoBlock, "Requires LoS", action.requires_los ? "Ja" : "Nein");
        if (action.friendly_fire != null) addInfoRow(infoBlock, "Friendly Fire", action.friendly_fire ? "Ja" : "Nein");

        const effectBlock = card.createEl("div");
        effectBlock.style.marginTop = "8px";

        const effectTitle = effectBlock.createEl("div", { text: "Notes / Effect" });
        effectTitle.style.fontWeight = "600";
        effectTitle.style.marginBottom = "4px";
        effectTitle.style.opacity = "0.85";

        effectBlock.createEl("div", {
          text: action.effect ?? action.notes ?? "Kein Effekttext eingetragen."
        });
      }
    }

    if (actionContainer.innerHTML.trim() === "") {
      const empty = actionContainer.createEl("div", { text: "Keine passenden Actions gefunden." });
      empty.style.padding = "10px";
      empty.style.opacity = "0.7";
    }
  }

  renderActionRows();

  searchInput.addEventListener("input", () => {
    renderActionRows(searchInput.value);
  });
}

function renderSpells() {
  clearEl(tabContent);

  const title = tabContent.createEl("div", { text: "Spells" });
  title.style.fontWeight = "600";
  title.style.marginBottom = "10px";

  const searchWrap = tabContent.createEl("div");
  searchWrap.style.marginBottom = "10px";

  const searchInput = searchWrap.createEl("input");
  searchInput.type = "text";
  searchInput.placeholder = "Spells durchsuchen...";
  searchInput.style.width = "100%";
  searchInput.style.padding = "8px 10px";
  searchInput.style.border = "1px solid var(--background-modifier-border)";
  searchInput.style.borderRadius = "8px";
  searchInput.style.background = "var(--background-primary)";
  searchInput.style.color = "var(--text-normal)";

  const spellcastingAbility = String(c.spellcasting_ability ?? "int").toLowerCase();
  const spellMod = getAbilityModByName(spellcastingAbility);
  const prof = c.proficiency_bonus ?? 2;
  const spellAttackBonus = c.spell_attack_bonus ?? (spellMod + prof);
  const spellSaveDC = c.spell_save_dc ?? (8 + spellMod + prof);

  function addSummaryBox(parent, label, value) {
    const box = parent.createEl("div");
    box.style.padding = "8px";
    box.style.border = "1px solid var(--background-modifier-border)";
    box.style.borderRadius = "8px";
    box.style.textAlign = "center";

    const labelEl = box.createEl("div", { text: label });
    labelEl.style.fontSize = "0.8em";
    labelEl.style.opacity = "0.7";

    const valueEl = box.createEl("div", { text: String(value) });
    valueEl.style.fontSize = "1.2em";
    valueEl.style.fontWeight = "700";
    valueEl.style.marginTop = "4px";
  }

  function addInfoRow(parent, label, value) {
    const row = parent.createEl("div");
    row.style.display = "flex";
    row.style.justifyContent = "space-between";
    row.style.gap = "10px";
    row.style.padding = "2px 0";

    const left = row.createEl("div", { text: label });
    left.style.opacity = "0.7";

    const right = row.createEl("div", { text: String(value ?? "-") });
    right.style.fontWeight = "600";
    right.style.textAlign = "right";
  }

  function levelLabel(level) {
    return level === 0 ? "Cantrips" : `Level ${level}`;
  }

  function addSpellSlotCheckboxes(parent, level, slotCount) {
    if (level === 0 || slotCount <= 0) return;

    const slotWrapper = parent.createEl("div");
    slotWrapper.style.display = "flex";
    slotWrapper.style.alignItems = "center";
    slotWrapper.style.gap = "8px";
    slotWrapper.style.flexWrap = "wrap";
    slotWrapper.style.marginTop = "6px";
    slotWrapper.style.marginBottom = "8px";

    const label = slotWrapper.createEl("div", { text: "Slots:" });
    label.style.fontSize = "0.9em";
    label.style.opacity = "0.75";

    for (let i = 0; i < slotCount; i++) {
      const boxLabel = slotWrapper.createEl("label");
      boxLabel.style.display = "flex";
      boxLabel.style.alignItems = "center";
      boxLabel.style.gap = "4px";
      boxLabel.style.cursor = "pointer";

      const checkbox = boxLabel.createEl("input");
      checkbox.type = "checkbox";
    }
  }

  const summaryGrid = tabContent.createEl("div");
  summaryGrid.style.display = "grid";
  summaryGrid.style.gridTemplateColumns = "repeat(3, 1fr)";
  summaryGrid.style.gap = "8px";
  summaryGrid.style.marginBottom = "12px";

  addSummaryBox(summaryGrid, "Modifier", modString(spellMod));
  addSummaryBox(summaryGrid, "Spell Attack", modString(spellAttackBonus));
  addSummaryBox(summaryGrid, "Spell Save DC", spellSaveDC);

  const spellContainer = tabContent.createEl("div");

  const actionRefs = Array.isArray(c.actions) ? c.actions : [];

const spells = actionRefs
  .map(path => dv.page(String(path)))
  .filter(p => p)
  .filter(p => String(p.category ?? "").trim().toLowerCase() === "spell")
  .sort((a, b) => {
    const levelA = Number(a.level ?? 99);
    const levelB = Number(b.level ?? 99);

    if (levelA !== levelB) return levelA - levelB;

    const nameA = String(a.name ?? a.file.name);
    const nameB = String(b.name ?? b.file.name);
    return nameA.localeCompare(nameB);
  });

  if (!spells || spells.length === 0) {
    spellContainer.createEl("div", { text: "Keine Spells gefunden." });
    return;
  }

  const groupedSpells = {
    0: [],
    1: [],
    2: [],
    3: [],
    4: [],
    5: []
  };

  for (const spell of spells) {
    const lvl = Number(spell.level ?? 0);
    if (lvl >= 0 && lvl <= 5) {
      groupedSpells[lvl].push(spell);
    }
  }

  function renderSpellRows(filterText = "") {
    spellContainer.innerHTML = "";
    const query = String(filterText ?? "").trim().toLowerCase();

    for (let level = 0; level <= 5; level++) {
      const levelSpells = groupedSpells[level].filter(spell => {
        const name = String(spell.name ?? spell.file.name ?? "").toLowerCase();
        const school = String(spell.school ?? "").toLowerCase();
        const notes = String(spell.notes ?? spell.effect ?? "").toLowerCase();
        const damageType = String(spell.damage_type ?? "").toLowerCase();

        if (!query) return true;
        return (
          name.includes(query) ||
          school.includes(query) ||
          notes.includes(query) ||
          damageType.includes(query)
        );
      });

      if (!levelSpells || levelSpells.length === 0) continue;

      const section = spellContainer.createEl("div");
      section.style.marginTop = "12px";

      const sectionTitle = section.createEl("div", { text: levelLabel(level) });
      sectionTitle.style.fontWeight = "700";
      sectionTitle.style.fontSize = "1.05em";
      sectionTitle.style.marginBottom = "8px";
      sectionTitle.style.paddingBottom = "4px";
      sectionTitle.style.borderBottom = "1px solid var(--background-modifier-border)";

      const slotCount = Number(c.spell_slots?.[level] ?? 0);
      addSpellSlotCheckboxes(section, level, slotCount);

      for (const spell of levelSpells) {
        const details = section.createEl("details");
        details.style.border = "1px solid var(--background-modifier-border)";
        details.style.borderRadius = "10px";
        details.style.marginTop = "8px";
        details.style.padding = "0";
        details.style.overflow = "hidden";

        const summaryEl = details.createEl("summary");
        summaryEl.style.cursor = "pointer";
        summaryEl.style.padding = "10px";
        summaryEl.style.display = "flex";
        summaryEl.style.justifyContent = "space-between";
        summaryEl.style.alignItems = "center";
        summaryEl.style.gap = "10px";
        summaryEl.style.fontWeight = "700";

        const leftSummary = summaryEl.createEl("div", {
          text: spell.name ?? spell.file.name
        });
        leftSummary.style.fontSize = "1.05em";

        const rightSummary = summaryEl.createEl("div", {
          text: `${spell.school ?? "-"}`
        });
        rightSummary.style.opacity = "0.7";
        rightSummary.style.fontSize = "0.9em";
        rightSummary.style.fontWeight = "400";

        const card = details.createEl("div");
        card.style.padding = "10px";
        card.style.borderTop = "1px solid var(--background-modifier-border)";

        const infoBlock = card.createEl("div");

        addInfoRow(infoBlock, "Casting Time", spell.casting_time ?? spell.action_type ?? "-");
        addInfoRow(infoBlock, "Range", spell.range ?? "-");
        addInfoRow(infoBlock, "Duration", spell.duration ?? "-");
        addInfoRow(infoBlock, "Concentration", spell.concentration ? "Ja" : "Nein");

        if (spell.uses_attack_roll) {
          addInfoRow(infoBlock, "To Hit", modString(spellAttackBonus));
        } else {
          addInfoRow(infoBlock, "To Hit", "-");
        }

        if (spell.uses_save) {
          addInfoRow(
            infoBlock,
            "Save",
            `${String(spell.save_ability ?? "?").toUpperCase()} DC ${spellSaveDC}`
          );
        } else {
          addInfoRow(infoBlock, "Save", "-");
        }

        let damageText = "-";
        if (spell.damage) {
          damageText = `${spell.damage}${spell.damage_type ? ` ${spell.damage_type}` : ""}`;
        } else if (spell.dice_count && spell.dice_size) {
          damageText = `${spell.dice_count}d${spell.dice_size}${spell.damage_type ? ` ${spell.damage_type}` : ""}`;
        }
        addInfoRow(infoBlock, "Damage", damageText);

        const effectBlock = card.createEl("div");
        effectBlock.style.marginTop = "8px";

        const effectTitle = effectBlock.createEl("div", { text: "Effect" });
        effectTitle.style.fontWeight = "600";
        effectTitle.style.marginBottom = "4px";
        effectTitle.style.opacity = "0.85";

        effectBlock.createEl("div", {
          text: spell.effect ?? spell.notes ?? "Kein Effekttext eingetragen."
        });
      }
    }

    if (spellContainer.innerHTML.trim() === "") {
      const empty = spellContainer.createEl("div", { text: "Keine passenden Spells gefunden." });
      empty.style.padding = "10px";
      empty.style.opacity = "0.7";
    }
  }

  renderSpellRows();

  searchInput.addEventListener("input", () => {
    renderSpellRows(searchInput.value);
  });
}

async function renderNotes() {
  clearEl(tabContent);

  const title = tabContent.createEl("div", { text: "Notes" });
  title.style.fontWeight = "700";
  title.style.fontSize = "1.05em";
  title.style.marginBottom = "10px";

  const file = app.vault.getAbstractFileByPath(CHARACTER_PATH);
  if (!file) {
    tabContent.createEl("div", { text: `Character-Datei nicht gefunden: ${CHARACTER_PATH}` });
    return;
  }

  const content = await app.vault.cachedRead(file);

  function extractSection(markdown, headingName) {
    const lines = markdown.split(/\r?\n/);
    const target = headingName.trim().toLowerCase();

    let start = -1;
    let baseLevel = -1;

    for (let i = 0; i < lines.length; i++) {
      const match = lines[i].match(/^(#{1,6})\s+(.*?)\s*$/);
      if (!match) continue;

      const level = match[1].length;
      const text = match[2].trim().toLowerCase();

      if (text === target) {
        start = i + 1;
        baseLevel = level;
        break;
      }
    }

    if (start === -1) return null;

    let end = lines.length;
    for (let i = start; i < lines.length; i++) {
      const match = lines[i].match(/^(#{1,6})\s+(.*?)\s*$/);
      if (!match) continue;

      const level = match[1].length;
      if (level <= baseLevel) {
        end = i;
        break;
      }
    }

    return lines.slice(start, end).join("\n").trim();
  }

  const notesSection = extractSection(content, "Notes");

  if (!notesSection) {
    tabContent.createEl("div", { text: "Kein Abschnitt '## Notes' gefunden." });
    return;
  }

  const notesBox = tabContent.createEl("div");
  notesBox.style.padding = "10px";
  notesBox.style.border = "1px solid var(--background-modifier-border)";
  notesBox.style.borderRadius = "10px";
  notesBox.style.lineHeight = "1.5";

  await MarkdownRenderer.render(
    app,
    notesSection,
    notesBox,
    CHARACTER_PATH,
    dv.container
  );
}

  function updateTabStyles() {
    for (const key in tabButtons) {
      const btn = tabButtons[key];
      const isActive = key === activeTab;

      btn.style.padding = "6px 10px";
      btn.style.borderRadius = "8px";
      btn.style.border = "1px solid var(--background-modifier-border)";
      btn.style.cursor = "pointer";
      btn.style.fontSize = "0.9em";
      btn.style.background = isActive
        ? "var(--interactive-accent)"
        : "var(--background-secondary)";
      btn.style.color = isActive
        ? "var(--text-on-accent)"
        : "var(--text-normal)";
    }
  }

async function renderActiveTab() {
  if (activeTab === "actions") renderActions();
  if (activeTab === "features") renderFeatures();
  if (activeTab === "inventory") renderInventory();
  if (activeTab === "spells") renderSpells();
  if (activeTab === "notes") await renderNotes();
  updateTabStyles();
}

  function makeTabButton(key, label) {
    const btn = tabBar.createEl("button", { text: label });
    btn.addEventListener("click", () => {
      activeTab = key;
      renderActiveTab();
    });
    tabButtons[key] = btn;
  }
  
  makeTabButton("actions", "Actions");
  makeTabButton("spells", "Spells");
  makeTabButton("inventory", "Inventory");
  makeTabButton("features", "Features");
  makeTabButton("notes", "Notes");

  renderActiveTab();

  const sensesCard = leftCol.createEl("div");
sensesCard.style.padding = "16px";
sensesCard.style.border = "1px solid var(--background-modifier-border)";
sensesCard.style.borderRadius = "14px";

const sensesTitle = sensesCard.createEl("div", { text: "Senses" });
sensesTitle.style.fontWeight = "700";
sensesTitle.style.marginBottom = "12px";
sensesTitle.style.fontSize = "1.1em";

const profBonus = c.proficiency_bonus ?? 2;

const passivePerception =
  10 + modFromScore(c.wis ?? 10) + (c.perception_prof ? profBonus : 0);

const passiveInvestigation =
  10 + modFromScore(c.int ?? 10) + (c.investigation_prof ? profBonus : 0);

const passiveInsight =
  10 + modFromScore(c.wis ?? 10) + (c.insight_prof ? profBonus : 0);

const senseStats = [
  ["Passive Perception", passivePerception],
  ["Passive Investigation", passiveInvestigation],
  ["Passive Insight", passiveInsight],
];

for (const [label, value] of senseStats) {
  const row = sensesCard.createEl("div");
  row.style.display = "flex";
  row.style.justifyContent = "space-between";
  row.style.padding = "6px 0";
  row.style.borderBottom = "1px solid var(--background-modifier-border-hover)";

  row.createEl("div", { text: label });

  const valueEl = row.createEl("div", { text: String(value) });
  valueEl.style.fontWeight = "600";
}
}
```





