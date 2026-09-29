# Character Builder

  

```dataviewjs

const CHARACTER_FOLDER = "Public/3 Backend/Characters/";

const CLASSES_FOLDER = "Public/3 Backend/Classes/";

const SUBCLASSES_FOLDER = "Public/3 Backend/Subclasses/";

const ITEMS_FOLDER = "Public/3 Backend/Items/";

const FEATS_FOLDER = "Public/3 Backend/Feats/";

const SPECIES_FOLDER = "Public/3 Backend/Species/";

  

const ABILITIES = [

  ["str", "STR"], ["dex", "DEX"], ["con", "CON"],

  ["int", "INT"], ["wis", "WIS"], ["cha", "CHA"]

];

  

function clampInt(v, min, max, fallback) {

  const n = Math.floor(Number(v));

  return Number.isFinite(n) ? Math.max(min, Math.min(max, n)) : fallback;

}

function modFromScore(v) { return Math.floor((Number(v) - 10) / 2); }

function modString(v) { return Number(v) >= 0 ? `+${v}` : `${v}`; }

function directMarkdownFiles(folder) {

  const prefix = folder.endsWith("/") ? folder : folder + "/";

  return app.vault.getMarkdownFiles()

    .filter(f => f.path.startsWith(prefix) && !f.path.slice(prefix.length).includes("/"))

    .sort((a,b) => a.basename.localeCompare(b.basename, "de"));

}

function pageFor(file) { return file ? dv.page(file.path) : null; }

function styleCard(el) {

  el.style.padding = "16px";

  el.style.border = "1px solid var(--background-modifier-border)";

  el.style.borderRadius = "14px";

  el.style.background = "var(--background-primary)";

}

function styleInput(el) {

  el.style.cssText += `

    width: 100%;

    min-width: 0;

    box-sizing: border-box;

  `;

}

function makeButton(parent, text) {

  const b = parent.createEl("button", {text});

  b.style.padding = "8px 12px";

  b.style.borderRadius = "8px";

  b.style.border = "1px solid var(--background-modifier-border)";

  b.style.cursor = "pointer";

  return b;

}

function resolveItem(ref) {

  const raw = String(ref ?? "").trim();

  if (!raw) return null;

  const candidates = raw.includes("/")

    ? [raw, raw.endsWith(".md") ? raw : raw + ".md"]

    : [`${ITEMS_FOLDER}${raw}`, `${ITEMS_FOLDER}${raw}.md`];

  for (const path of candidates) {

    const file = app.vault.getAbstractFileByPath(path);

    if (file) return {file, page: dv.page(path), path};

  }

  return null;

}

  

const characterFiles = directMarkdownFiles(CHARACTER_FOLDER);

const classFiles = directMarkdownFiles(CLASSES_FOLDER);

const itemFiles = directMarkdownFiles(ITEMS_FOLDER);

const featFiles = directMarkdownFiles(FEATS_FOLDER);

const speciesFiles = directMarkdownFiles(SPECIES_FOLDER);

  

const root = dv.el("div", "");

root.style.maxWidth = "1100px";

root.style.margin = "0 auto";

root.style.display = "flex";

root.style.flexDirection = "column";

root.style.gap = "14px";

  

const header = root.createEl("div");

styleCard(header);

header.createEl("div", {text:"Character Builder"}).style.cssText =

  "font-size:1.8em;font-weight:700";

header.createEl("div", {

  text:"Character creation and persistent character configuration"

}).style.cssText = "opacity:.7;margin-top:4px";

  

if (!characterFiles.length) {

  root.createEl("div", {text:`No characters found directly in ${CHARACTER_FOLDER}`});

  return;

}

  

const top = root.createEl("div");

styleCard(top);

top.style.display = "grid";

top.style.gridTemplateColumns = "1fr auto";

top.style.gap = "12px";

top.style.alignItems = "end";

  

const selectorWrap = top.createEl("div");

selectorWrap.createEl("div", {text:"Character"}).style.cssText =

  "font-weight:600;font-size:.85em;margin-bottom:6px";

const characterSelect = selectorWrap.createEl("select");

characterSelect.style.cssText += `

  width: 100%;

  min-width: 350px;

  max-width: 100%;

`;

styleInput(characterSelect);

  

for (const file of characterFiles) {

  const pg = pageFor(file);

  const opt = characterSelect.createEl("option", {

    text:`${pg?.name ?? file.basename} — ${file.path}`

  });

  opt.value = file.path;

}

  

if (

  window.__dndBuilderCharacterPath &&

  characterFiles.some(f => f.path === window.__dndBuilderCharacterPath)

) characterSelect.value = window.__dndBuilderCharacterPath;

  

const saveBtn = makeButton(top, "Save Changes");

saveBtn.style.background = "var(--interactive-accent)";

saveBtn.style.color = "var(--text-on-accent)";

saveBtn.style.fontWeight = "700";

  

const tabCard = root.createEl("div");

styleCard(tabCard);

tabCard.style.padding = "10px";

  

const tabBar = tabCard.createEl("div");

tabBar.style.display = "flex";

tabBar.style.flexWrap = "wrap";

tabBar.style.gap = "8px";

  

const content = root.createEl("div");

styleCard(content);

content.style.minHeight = "420px";

  

const status = root.createEl("div");

status.style.fontSize = ".85em";

status.style.opacity = ".7";

  

let currentFile = null;

let currentPage = null;

let draft = null;

let dirty = false;

let activeTab = window.__dndBuilderTab ?? "class";

const tabButtons = {};

  

function markDirty() {

  dirty = true;

  saveBtn.setText("Save Changes *");

  status.setText("Unsaved changes");

}

function clearDirty() {

  dirty = false;

  saveBtn.setText("Save Changes");

  status.setText("Saved state loaded");

}

function selectedClassFile(name) {

  return classFiles.find(f => {

    const p = pageFor(f);

    return String(p?.name ?? f.basename) === String(name ?? "");

  }) ?? null;

}

function selectedClassPage() {

  return pageFor(selectedClassFile(draft?.class));

}

function classLevelData() {

  const cp = selectedClassPage();

  const level = String(draft?.level ?? 1);

  return cp?.levels?.[level] ?? cp?.levels?.[Number(level)] ?? null;

}

function getSubclassConfig() {

  const cfg = selectedClassPage()?.subclass;

  return cfg && typeof cfg === "object" ? cfg : null;

}

function availableSubclassOptions() {

  const cfg = getSubclassConfig();

  return Array.isArray(cfg?.options) ? cfg.options : [];

}

function getSubclassName() {

  return Array.isArray(draft?.subclass)

    ? String(draft.subclass.find(Boolean) ?? "")

    : String(draft?.subclass ?? "");

}

  

/* Builder-side bonuses.

   For now these mirror the character sheet's existing active item bonuses.

   Later ASI/species/background choices can be added as additional sources. */

function evaluateSimpleFormula(formula, ctx) {

  let expr = String(formula ?? "").trim();

  if (!expr) return null;

  const keys = ["str_mod","dex_mod","con_mod","int_mod","wis_mod","cha_mod",

                "str","dex","con","int","wis","cha","prof"];

  for (const key of keys) {

    expr = expr.replace(new RegExp(`\\b${key}\\b`, "g"), String(ctx[key] ?? 0));

  }

  expr = expr.replace(/\bmin\s*\(/g,"Math.min(").replace(/\bmax\s*\(/g,"Math.max(");

  if (!/^[0-9+\-*/().,\sMathminax]*$/.test(expr)) return null;

  try {

    const x = Function(`"use strict";return (${expr})`)();

    return Number.isFinite(x) ? Number(x) : null;

  } catch { return null; }

}

function baseContext() {

  const ctx = {};

  for (const [k] of ABILITIES) {

    ctx[k] = Number(draft?.[k] ?? 10);

    ctx[`${k}_mod`] = modFromScore(ctx[k]);

  }

  ctx.prof = Number(classLevelData()?.proficiency_bonus ?? currentPage?.proficiency_bonus ?? 2);

  return ctx;

}

function abilityItemEffects(key) {

  let additive = 0;

  const setters = [];

  const inventory = Array.isArray(draft?.inventory) ? draft.inventory : [];

  const ctx = baseContext();

  

  for (let i=0; i<inventory.length; i++) {

    const entry = inventory[i];

    const resolved = resolveItem(entry?.item);

    if (!resolved?.page) continue;

    const item = resolved.page;

    const bonuses = Array.isArray(item.bonuses) ? item.bonuses : [];

    for (const b of bonuses) {

      if (String(b?.type ?? "").toLowerCase() !== key) continue;

      const when = String(b.active_when ?? "equipped").toLowerCase();

      const active = when === "always" || (when === "equipped" && entry?.equipped === true);

      if (!active) continue;

      const add = Number(b.value ?? 0);

      if (Number.isFinite(add)) additive += add;

      if (b.set_value != null && Number.isFinite(Number(b.set_value)))

        setters.push(Number(b.set_value));

      if (b.formula) {

        const val = evaluateSimpleFormula(b.formula, ctx);

        if (val != null) setters.push(val);

      }

    }

  }

  return {additive, setters};

}

function getAsiBonus(key) {

  const choices = draft?.asi_choices && typeof draft.asi_choices === "object" ? draft.asi_choices : {};

  let total = 0;

  for (const choice of Object.values(choices)) {

    if (!choice) continue;

    if (String(choice.type ?? "asi") === "asi") {

      if (String(choice.ability_1 ?? "").toLowerCase() === key) total++;

      if (String(choice.ability_2 ?? "").toLowerCase() === key) total++;

      continue;

    }

  

    if (String(choice.type ?? "") === "feat") {

      const featPage=selectedFeatPage(choice.feat);

      const defs=featPage?.choices;

      const state=choice.feat_choices ?? {};

  

      if (defs?.ability_increase && String(state.ability_increase ?? "").toLowerCase()===key)

        total += Number(defs.ability_increase.amount ?? 1);

  

      if (defs?.resilient_ability && String(state.resilient_ability ?? "").toLowerCase()===key)

        total += Number(defs.resilient_ability.amount ?? 1);

    }

  }

  return total;

}

function calculatedAbility(key) {

  const base = Number(draft?.[key] ?? 10);

  const fx = abilityItemEffects(key);

  const asiBonus = getAsiBonus(key);

  const beforeAdd = fx.setters.length ? Math.max(base, ...fx.setters) : base;

  return {base, bonus:beforeAdd-base+fx.additive+asiBonus, final:beforeAdd+fx.additive+asiBonus};

}

function getAllAsiLevels() {

  const cp=selectedClassPage();

  const className=String(cp?.name ?? draft?.class ?? "").trim().toLowerCase();

  

  // Preferred explicit backend form:

  // asi_levels: [4, 8, 12, 16, 19]

  if(Array.isArray(cp?.asi_levels)){

    return cp.asi_levels.map(Number).filter(Number.isFinite).sort((a,b)=>a-b);

  }

  

  // Also recognize ASI entries in the class feature progression.

  const features=Array.isArray(cp?.features)?cp.features:[];

  const fromFeatures=features.filter(f=>{

    const ref=String(f?.feature ?? f?.name ?? "").trim();

    const normalized=ref.toLowerCase();

    if(normalized==="ability score improvement" || normalized==="asi") return true;

  

    // If the class entry points to a Feature file, allow that file to identify itself.

    const fp=ref ? (

      dv.page(`Public/3 Backend/Features/${ref}`) ??

      dv.page(`Public/3 Backend/Features/${ref}.md`) ??

      (ref.includes("/") ? dv.page(ref) : null)

    ) : null;

    const kind=String(fp?.type ?? fp?.choice_type ?? "").trim().toLowerCase();

    return kind==="asi" || kind==="ability_score_improvement" || kind==="asi_or_feat";

  }).map(f=>Number(f?.level)).filter(Number.isFinite);

  

  if(fromFeatures.length) return [...new Set(fromFeatures)].sort((a,b)=>a-b);

  

  // 2014 fallback schedules, so the builder remains usable even before

  // every class backend has explicit asi_levels.

  const schedules={

    artificer:[4,8,12,16,19],

    barbarian:[4,8,12,16,19],

    bard:[4,8,12,16,19],

    cleric:[4,8,12,16,19],

    druid:[4,8,12,16,19],

    fighter:[4,6,8,12,14,16,19],

    monk:[4,8,12,16,19],

    paladin:[4,8,12,16,19],

    ranger:[4,8,12,16,19],

    rogue:[4,8,10,12,16,19],

    sorcerer:[4,8,12,16,19],

    warlock:[4,8,12,16,19],

    wizard:[4,8,12,16,19]

  };

  return schedules[className] ?? [];

}

function getAsiLevels() {

  return getAllAsiLevels().filter(level=>level<=Number(draft?.level ?? 1));

}

function featDisplayName(file) {

  const p=pageFor(file);

  return String(p?.name ?? file.basename);

}

function selectedFeatPage(ref) {

  const raw=String(ref ?? "").trim();

  if(!raw) return null;

  if(raw.includes("/")) return dv.page(raw) ?? dv.page(raw.endsWith(".md")?raw:raw+".md");

  return dv.page(`${FEATS_FOLDER}${raw}`) ?? dv.page(`${FEATS_FOLDER}${raw}.md`);

}

function featChoiceState(choice) {

  if(!choice.feat_choices || typeof choice.feat_choices!=="object") choice.feat_choices={};

  return choice.feat_choices;

}

  
  

function sectionTitle(text, subtext="") {

  const h = content.createEl("div");

  h.createEl("div", {text}).style.cssText = "font-size:1.35em;font-weight:700";

  if (subtext) h.createEl("div", {text:subtext}).style.cssText =

    "opacity:.65;margin-top:3px;margin-bottom:16px";

  else h.style.marginBottom = "16px";

}

function field(parent, label) {

  const w = parent.createEl("div");

  w.createEl("div", {text:label}).style.cssText =

    "font-size:.85em;font-weight:600;opacity:.8;margin-bottom:6px";

  return w;

}

  

function renderClass() {

  content.innerHTML = "";

  sectionTitle("Class", "Class, level, subclass and level-based choices.");

  

  const grid = content.createEl("div");

  grid.style.display = "grid";

  grid.style.gridTemplateColumns = "2fr 1fr";

  grid.style.gap = "12px";

  

  const cw = field(grid, "Class");

  const sel = cw.createEl("select"); styleInput(sel);

  sel.createEl("option",{text:"— Select Class —"}).value = "";

  for (const f of classFiles) {

    const p = pageFor(f), name = String(p?.name ?? f.basename);

    const o = sel.createEl("option",{text:name}); o.value=name;

  }

  if (draft.class && !classFiles.some(f => String(pageFor(f)?.name ?? f.basename) === draft.class)) {

    const o=sel.createEl("option",{text:`${draft.class} (missing file)`}); o.value=draft.class;

  }

  sel.value=draft.class ?? "";

  sel.onchange=()=>{draft.class=sel.value; draft.subclass=""; markDirty(); renderClass();};

  

  const lw = field(grid,"Level");

  const lr = lw.createEl("div");

  lr.style.display="grid"; lr.style.gridTemplateColumns="40px 1fr 40px"; lr.style.gap="6px";

  const minus=makeButton(lr,"−");

  const li=lr.createEl("input"); li.type="number"; li.min="1"; li.max="20"; li.value=String(draft.level); styleInput(li); li.style.textAlign="center";

  const plus=makeButton(lr,"+");

  const changeLevel=n=>{draft.level=clampInt(n,1,20,1); markDirty(); renderClass();};

  minus.onclick=()=>changeLevel(draft.level-1);

  plus.onclick=()=>changeLevel(draft.level+1);

  li.onchange=()=>changeLevel(li.value);

  

  const cfg=getSubclassConfig();

  const unlock=Number(cfg?.unlock_level ?? 1);

  if (cfg && Number(draft.level)>=unlock) {

    const sw=field(content, cfg.label ?? "Subclass");

    sw.style.marginTop="14px";

    const ss=sw.createEl("select"); styleInput(ss);

    ss.createEl("option",{text:"— Select —"}).value="";

    for (const option of availableSubclassOptions()) {

      if (!option) continue;

      const name=String(option.name ?? option.file ?? "");

      const o=ss.createEl("option",{text:name}); o.value=name;

    }

    ss.value=getSubclassName();

    ss.onchange=()=>{draft.subclass=ss.value;markDirty();};

  }

  

  const prog=content.createEl("div");

  prog.style.marginTop="22px";

  prog.createEl("div",{text:"Class Progression"}).style.cssText="font-weight:700;font-size:1.05em;margin-bottom:8px";

  

  const cp=selectedClassPage();

  const features=Array.isArray(cp?.features) ? cp.features : [];

  const unlocked=features.filter(x=>Number(x?.level ?? 1)<=Number(draft.level));

  

  if (!cp) {

    prog.createEl("div",{text:"No class backend loaded."}).style.opacity=".65";

  } else {

    const levelData=classLevelData();

    const summary=prog.createEl("div");

    summary.style.display="flex"; summary.style.gap="8px"; summary.style.flexWrap="wrap"; summary.style.marginBottom="10px";

    const chips=[

      `Level ${draft.level}`,

      `Proficiency ${levelData?.proficiency_bonus != null ? modString(levelData.proficiency_bonus) : "-"}`,

      `Attunement ${levelData?.attunement_slots ?? "-"}`

    ];

    for (const text of chips) {

      const chip=summary.createEl("span",{text});

      chip.style.cssText="padding:5px 9px;border:1px solid var(--background-modifier-border);border-radius:999px;font-size:.85em";

    }

    if (!unlocked.length) prog.createEl("div",{text:"No unlocked class features found."});

    for (const f of unlocked) {

      const row=prog.createEl("div");

      row.style.cssText="display:grid;grid-template-columns:80px 1fr;gap:10px;padding:8px 0;border-bottom:1px solid var(--background-modifier-border-hover)";

      row.createEl("div",{text:`Level ${f.level ?? 1}`}).style.opacity=".65";

      row.createEl("div",{text:String(f.feature ?? "-")});

    }

  }

  

  const choices=content.createEl("div");

  choices.style.marginTop="22px";

  choices.createEl("div",{text:"Level Choices"}).style.cssText="font-weight:700;font-size:1.05em;margin-bottom:10px";

  const asiLevels=getAsiLevels();

  draft.asi_choices=draft.asi_choices&&typeof draft.asi_choices==="object"?draft.asi_choices:{};

  

  if(!asiLevels.length) {

    const allAsi=getAllAsiLevels();

    const msg=allAsi.length

      ? `No ASI unlocked yet. Next Ability Score Improvement: Level ${allAsi.find(x=>x>Number(draft.level)) ?? allAsi[allAsi.length-1]}.`

      : "No ASI progression found for this class. Add asi_levels to the class backend.";

    choices.createEl("div",{text:msg}).style.opacity=".65";

  }

  

  for(const asiLevel of asiLevels){

    const key=String(asiLevel);

    const choice=draft.asi_choices[key]??{type:"asi",ability_1:"",ability_2:"",feat:""};

    draft.asi_choices[key]=choice;

  

    const card=choices.createEl("div");

    card.style.cssText="padding:14px;border:1px solid var(--background-modifier-border);border-radius:12px;margin-bottom:10px";

    const top=card.createEl("div");

    top.style.cssText="display:flex;justify-content:space-between;gap:10px;align-items:center;margin-bottom:10px";

    top.createEl("div",{text:`Level ${asiLevel} — Ability Score Improvement`}).style.fontWeight="700";

  

    const type=top.createEl("select");styleInput(type);type.style.width="150px";

    for(const [v,l] of [["asi","ASI"],["feat","Feat"]]){const o=type.createEl("option",{text:l});o.value=v;}

    type.value=String(choice.type??"asi");

    const body=card.createEl("div");

  

    function draw(){

      body.innerHTML="";

      if(String(choice.type??"asi")==="asi"){

        body.createEl("div",{text:"Choose two +1 increases. The same ability twice gives +2."})

          .style.cssText="font-size:.85em;opacity:.65;margin-bottom:10px";

        const grid=body.createEl("div");grid.style.cssText="display:grid;grid-template-columns:1fr 1fr;gap:10px";

        function score(label,prop){

          const w=field(grid,label),sel=w.createEl("select");styleInput(sel);

          sel.createEl("option",{text:"— Select Ability —"}).value="";

          for(const [ak,al] of ABILITIES){const o=sel.createEl("option",{text:al});o.value=ak;}

          sel.value=String(choice[prop]??"");

          sel.onchange=()=>{choice[prop]=sel.value;choice.feat="";markDirty();};

        }

        score("Increase 1 (+1)","ability_1");score("Increase 2 (+1)","ability_2");

      }else{

        const w=field(body,"Feat"),sel=w.createEl("select");styleInput(sel);

        sel.createEl("option",{text:"— Select Feat —"}).value="";

        for(const file of featFiles){const o=sel.createEl("option",{text:featDisplayName(file)});o.value=file.path;}

        if(choice.feat&&!featFiles.some(f=>f.path===choice.feat)){const o=sel.createEl("option",{text:`${choice.feat} (missing file)`});o.value=choice.feat;}

        sel.value=String(choice.feat??"");

        sel.onchange=()=>{

          choice.feat=sel.value;

          choice.ability_1="";

          choice.ability_2="";

          choice.feat_choices={};

          markDirty();

          draw();

        };

        if(!featFiles.length) body.createEl("div",{text:`No feat files found in ${FEATS_FOLDER}`})

          .style.cssText="font-size:.85em;opacity:.65;margin-top:8px";

  

        const featPage=selectedFeatPage(choice.feat);

        const definitions=featPage?.choices;

        if(featPage && definitions && typeof definitions==="object"){

          const state=featChoiceState(choice);

          const choiceWrap=body.createEl("div");

          choiceWrap.style.cssText="margin-top:12px;padding-top:12px;border-top:1px solid var(--background-modifier-border-hover)";

          choiceWrap.createEl("div",{text:"Feat Choices"}).style.cssText="font-weight:700;margin-bottom:10px";

  

          function singleSelect(title,key,options){

            const w=field(choiceWrap,title), cs=w.createEl("select");styleInput(cs);

            cs.createEl("option",{text:"— Select —"}).value="";

            for(const option of options??[]){

              const o=cs.createEl("option",{text:String(option).toUpperCase()});o.value=String(option);

            }

            cs.value=String(state[key]??"");

            cs.onchange=()=>{state[key]=cs.value;markDirty();};

          }

  

          if(definitions.ability_increase){

            const d=definitions.ability_increase;

            singleSelect(`Ability Increase (+${Number(d.amount??1)})`,"ability_increase",Array.isArray(d.options)?d.options:[]);

          }

  

          if(definitions.resilient_ability){

            const d=definitions.resilient_ability;

            singleSelect("Resilient Ability (+1 & Save Proficiency)","resilient_ability",Array.isArray(d.options)?d.options:[]);

          }

  

          if(definitions.weapon_proficiencies){

            const d=definitions.weapon_proficiencies;

            const count=Math.max(1,Number(d.count??1));

            const note=choiceWrap.createEl("div",{text:`Choose ${count} weapon proficiencies.`});

            note.style.cssText="font-size:.85em;opacity:.65;margin-bottom:8px";

            if(!Array.isArray(state.weapon_proficiencies)) state.weapon_proficiencies=[];

            for(let wi=0;wi<count;wi++){

              const w=field(choiceWrap,`Weapon ${wi+1}`);

              const inp=w.createEl("input");inp.type="text";inp.placeholder="e.g. longsword";styleInput(inp);

              inp.value=String(state.weapon_proficiencies[wi]??"");

              inp.onchange=()=>{state.weapon_proficiencies[wi]=inp.value.trim().toLowerCase();markDirty();};

            }

          }

  

          if(definitions.skill_or_tool_proficiencies){

            const d=definitions.skill_or_tool_proficiencies;

            const count=Math.max(1,Number(d.count??1));

            if(!Array.isArray(state.skill_or_tool_proficiencies)) state.skill_or_tool_proficiencies=[];

            const note=choiceWrap.createEl("div",{text:`Choose ${count} skill or tool proficiencies.`});

            note.style.cssText="font-size:.85em;opacity:.65;margin-bottom:8px";

            for(let pi=0;pi<count;pi++){

              const row=choiceWrap.createEl("div");

              row.style.cssText="display:grid;grid-template-columns:130px 1fr;gap:8px;margin-bottom:8px";

              const typeSel=row.createEl("select");styleInput(typeSel);

              for(const t of ["skill","tool"]){const o=typeSel.createEl("option",{text:t==="skill"?"Skill":"Tool"});o.value=t;}

              const existing=state.skill_or_tool_proficiencies[pi]??{type:"skill",value:""};

              typeSel.value=existing.type??"skill";

              const inp=row.createEl("input");inp.type="text";inp.placeholder="e.g. perception / thieves_tools";styleInput(inp);

              inp.value=existing.value??"";

              const save=()=>{state.skill_or_tool_proficiencies[pi]={type:typeSel.value,value:inp.value.trim().toLowerCase()};markDirty();};

              typeSel.onchange=save;inp.onchange=save;

            }

          }

        }

      }

    }

    type.onchange=()=>{choice.type=type.value;if(choice.type==="asi")choice.feat="";else{choice.ability_1="";choice.ability_2="";}markDirty();draw();};

    draw();

  }

}

  
  

function renderSpecies() {

  content.innerHTML="";

  sectionTitle("Species", "Choose a species/race and configure the choices defined by its backend file.");

  

  const speciesField=field(content,"Species");

  const speciesSelect=speciesField.createEl("select"); styleInput(speciesSelect);

  speciesSelect.createEl("option",{text:"— Select Species —"}).value="";

  

  for(const file of speciesFiles){

    const pg=pageFor(file);

    const opt=speciesSelect.createEl("option",{text:String(pg?.name ?? file.basename)});

    opt.value=file.path;

  }

  

  speciesSelect.value=String(draft.species ?? "");

  speciesSelect.onchange=()=>{

    draft.species=speciesSelect.value;

    draft.species_choices={};

    markDirty();

    renderSpecies();

  };

  

  if(!speciesFiles.length){

    content.createEl("div",{text:`No species files found in ${SPECIES_FOLDER}`})

      .style.cssText="margin-top:10px;font-size:.85em;opacity:.65";

    return;

  }

  

  const speciesPage=draft.species ? dv.page(draft.species) : null;

  if(!speciesPage) return;

  

  const info=content.createEl("div");

  info.style.cssText="margin-top:14px;padding:12px;border:1px solid var(--background-modifier-border);border-radius:10px";

  const facts=[];

  if(speciesPage.source) facts.push(`Source: ${speciesPage.source}`);

  if(speciesPage.size) facts.push(`Size: ${speciesPage.size}`);

  if(speciesPage.speed!=null) facts.push(`Speed: ${speciesPage.speed} ft`);

  if(Array.isArray(speciesPage.languages)) facts.push(`Languages: ${speciesPage.languages.join(", ")}`);

  info.createEl("div",{text:facts.join(" • ")}).style.cssText="font-size:.9em;opacity:.75";

  if(speciesPage.notes) info.createEl("div",{text:String(speciesPage.notes)}).style.cssText="margin-top:8px";

  

  const defs=speciesPage.choices;

  if(!defs || typeof defs!=="object") return;

  if(!draft.species_choices || typeof draft.species_choices!=="object") draft.species_choices={};

  const state=draft.species_choices;

  

  const box=content.createEl("div");

  box.style.cssText="margin-top:14px;padding:14px;border:1px solid var(--background-modifier-border);border-radius:10px";

  box.createEl("div",{text:"Species Choices"}).style.cssText="font-weight:700;margin-bottom:12px";

  

  function optionsFor(def){

    return Array.isArray(def?.options) ? def.options.map(String) : [];

  }

  function singleSelect(label,key,options,placeholder="— Select —"){

    const w=field(box,label), sel=w.createEl("select"); styleInput(sel);

    sel.createEl("option",{text:placeholder}).value="";

    for(const value of options){

      const o=sel.createEl("option",{text:String(value).replaceAll("_"," ")}); o.value=String(value);

    }

    sel.value=String(state[key]??"");

    sel.onchange=()=>{state[key]=sel.value;markDirty();};

  }

  function textChoices(label,key,count,placeholder){

    if(!Array.isArray(state[key])) state[key]=[];

    for(let i=0;i<count;i++){

      const w=field(box,`${label} ${i+1}`);

      const inp=w.createEl("input"); inp.type="text"; inp.placeholder=placeholder; styleInput(inp);

      inp.value=String(state[key][i]??"");

      inp.onchange=()=>{state[key][i]=inp.value.trim().toLowerCase();markDirty();};

    }

  }

  

  for(const [key,def] of Object.entries(defs)){

    if(!def || typeof def!=="object") continue;

    const count=Math.max(1,Number(def.count??1));

    const amount=Number(def.amount??1);

    const opts=optionsFor(def);

  

    if(key==="ability_increase"){

      singleSelect(`Ability Increase (+${amount})`,key,opts);

    } else if(key==="ability_increases"){

      if(!Array.isArray(state[key])) state[key]=[];

      for(let i=0;i<count;i++){

        const w=field(box,`Ability Increase ${i+1} (+${amount})`);

        const sel=w.createEl("select"); styleInput(sel);

        sel.createEl("option",{text:"— Select Ability —"}).value="";

        for(const value of opts){

          const o=sel.createEl("option",{text:String(value).toUpperCase()}); o.value=value;

        }

        sel.value=String(state[key][i]??"");

        sel.onchange=()=>{

          state[key][i]=sel.value;

          if(def.distinct===true){

            state[key]=state[key].filter((v,idx)=>!v || state[key].indexOf(v)===idx);

          }

          markDirty(); renderSpecies();

        };

      }

    } else if(key==="feat"){

      const w=field(box,"Feat"), sel=w.createEl("select"); styleInput(sel);

      sel.createEl("option",{text:"— Select Feat —"}).value="";

      for(const file of featFiles){

        const o=sel.createEl("option",{text:featDisplayName(file)}); o.value=file.path;

      }

      sel.value=String(state[key]??"");

      sel.onchange=()=>{state[key]=sel.value;markDirty();};

    } else if(opts.length){

      singleSelect(key.replaceAll("_"," "),key,opts);

    } else if(key.includes("skill") && count>1){

      textChoices("Skill",key,count,"e.g. perception");

    } else if(key.includes("skill")){

      const w=field(box,"Skill Proficiency");

      const inp=w.createEl("input"); inp.type="text"; inp.placeholder="e.g. perception"; styleInput(inp);

      inp.value=String(state[key]??"");

      inp.onchange=()=>{state[key]=inp.value.trim().toLowerCase();markDirty();};

    } else if(key.includes("tool") && count>1){

      textChoices("Tool",key,count,"e.g. thieves_tools");

    } else if(key.includes("tool")){

      const w=field(box,"Tool Proficiency");

      const inp=w.createEl("input"); inp.type="text"; inp.placeholder="e.g. thieves_tools"; styleInput(inp);

      inp.value=String(state[key]??"");

      inp.onchange=()=>{state[key]=inp.value.trim().toLowerCase();markDirty();};

    } else if(key.includes("language") && count>1){

      textChoices("Language",key,count,"e.g. Draconic");

    } else if(key.includes("language")){

      const w=field(box,"Language");

      const inp=w.createEl("input"); inp.type="text"; inp.placeholder="e.g. Draconic"; styleInput(inp);

      inp.value=String(state[key]??"");

      inp.onchange=()=>{state[key]=inp.value.trim();markDirty();};

    } else {

      const w=field(box,key.replaceAll("_"," "));

      const inp=w.createEl("input"); inp.type="text"; styleInput(inp);

      inp.value=String(state[key]??"");

      inp.onchange=()=>{state[key]=inp.value.trim();markDirty();};

    }

  }

}

  

function renderAbilities() {

  content.innerHTML="";

  sectionTitle("Abilities","Base values and calculated values used by the character sheet.");

  

  const head=content.createEl("div");

  head.style.cssText="display:grid;grid-template-columns:1.2fr 1fr 1fr 1fr 1fr;gap:8px;padding:8px;font-weight:700;border-bottom:1px solid var(--background-modifier-border)";

  for (const t of ["Ability","Base / Raw","Bonuses","Final","Modifier"]) head.createEl("div",{text:t});

  

  for (const [key,label] of ABILITIES) {

    const calc=calculatedAbility(key);

    const row=content.createEl("div");

    row.style.cssText="display:grid;grid-template-columns:1.2fr 1fr 1fr 1fr 1fr;gap:8px;align-items:center;padding:10px 8px;border-bottom:1px solid var(--background-modifier-border-hover)";

    row.createEl("div",{text:label}).style.fontWeight="700";

  

    const input=row.createEl("input"); input.type="number"; input.min="1"; input.max="30"; input.value=String(calc.base); styleInput(input);

    input.onchange=()=>{

      draft[key]=clampInt(input.value,1,30,10);

      markDirty(); renderAbilities();

    };

    row.createEl("div",{text:calc.bonus===0 ? "—" : modString(calc.bonus)});

    const final=row.createEl("div",{text:String(calc.final)}); final.style.fontWeight="700";

    const mod=row.createEl("div",{text:modString(modFromScore(calc.final))}); mod.style.fontWeight="700";

  }

  

  const note=content.createEl("div");

  note.style.cssText="margin-top:16px;padding:12px;border:1px solid var(--background-modifier-border);border-radius:10px;opacity:.75";

  note.setText("Base / Raw is stored in the character backend. The current Bonuses column already reflects active item ability bonuses. ASI, Species and Background sources can be added here without overwriting the raw score.");

}

  

function renderEquipment() {

  content.innerHTML="";

  sectionTitle("Equipment","Manage the existing character inventory. Equipped state remains compatible with the character sheet.");

  

  const add=content.createEl("div");

  add.style.cssText="display:grid;grid-template-columns:1fr auto;gap:8px;margin-bottom:16px";

  const itemSelect=add.createEl("select"); styleInput(itemSelect);

  itemSelect.createEl("option",{text:"— Add Item —"}).value="";

  for (const f of itemFiles) {

    const p=pageFor(f), name=String(p?.name ?? f.basename);

    const o=itemSelect.createEl("option",{text:name}); o.value=f.path;

  }

  const addBtn=makeButton(add,"Add");

  addBtn.onclick=()=>{

    if (!itemSelect.value) return;

    draft.inventory = Array.isArray(draft.inventory) ? draft.inventory : [];

    const existing=draft.inventory.find(e=>{

      const r=resolveItem(e?.item); return r?.path===itemSelect.value;

    });

    if (existing) existing.quantity=Math.max(1,Number(existing.quantity ?? 1))+1;

    else draft.inventory.push({item:itemSelect.value,quantity:1,equipped:false});

    markDirty(); renderEquipment();

  };

  

  const inv=Array.isArray(draft.inventory)?draft.inventory:[];

  if (!inv.length) {

    content.createEl("div",{text:"Inventory is empty."}).style.opacity=".65";

    return;

  }

  

  for (let i=0;i<inv.length;i++) {

    const entry=inv[i], resolved=resolveItem(entry?.item), item=resolved?.page;

    const row=content.createEl("div");

    row.style.cssText="display:grid;grid-template-columns:minmax(220px,2fr) 90px 110px 90px;gap:10px;align-items:center;padding:10px 0;border-bottom:1px solid var(--background-modifier-border-hover)";

    const nameWrap=row.createEl("div");

    nameWrap.createEl("div",{text:String(item?.name ?? resolved?.file?.basename ?? entry?.item ?? "Unknown Item")}).style.fontWeight="600";

    nameWrap.createEl("div",{text:String(item?.type ?? "")}).style.cssText="font-size:.8em;opacity:.6";

  

    const qty=row.createEl("input"); qty.type="number"; qty.min="1"; qty.value=String(Math.max(1,Number(entry.quantity ?? 1))); styleInput(qty);

    qty.onchange=()=>{entry.quantity=Math.max(1,clampInt(qty.value,1,999,1));markDirty();};

  

    const equipLabel=row.createEl("label");

    equipLabel.style.cssText="display:flex;align-items:center;gap:6px";

    const equip=equipLabel.createEl("input"); equip.type="checkbox"; equip.checked=entry.equipped===true;

    equipLabel.createEl("span",{text:"Equipped"});

    equip.onchange=()=>{entry.equipped=equip.checked;markDirty();};

  

    const remove=makeButton(row,"Remove");

    remove.onclick=()=>{draft.inventory.splice(i,1);markDirty();renderEquipment();};

  }

}

  

function renderPlaceholder(type) {

  content.innerHTML="";

  sectionTitle(type, `${type} configuration will use its own backend definitions.`);

  const box=content.createEl("div");

  box.style.cssText="padding:18px;border:1px dashed var(--background-modifier-border);border-radius:12px";

  box.createEl("div",{text:`${type} system prepared`}).style.fontWeight="700";

  box.createEl("div",{

    text:`Next, ${type.toLowerCase()} files can define features, bonuses and choices just like the class backend.`

  }).style.cssText="opacity:.65;margin-top:6px";

}

  

function updateTabs() {

  for (const [key,b] of Object.entries(tabButtons)) {

    const on=key===activeTab;

    b.style.background=on?"var(--interactive-accent)":"var(--background-secondary)";

    b.style.color=on?"var(--text-on-accent)":"var(--text-normal)";

  }

}

function renderTab() {

  window.__dndBuilderTab=activeTab;

  updateTabs();

  if (activeTab==="class") renderClass();

  else if (activeTab==="background") renderPlaceholder("Background");

  else if (activeTab==="species") renderSpecies();

  else if (activeTab==="abilities") renderAbilities();

  else if (activeTab==="equipment") renderEquipment();

}

for (const [key,label] of [

  ["class","Class"],["background","Background"],["species","Species"],

  ["abilities","Abilities"],["equipment","Equipment"]

]) {

  const b=makeButton(tabBar,label);

  b.onclick=()=>{activeTab=key;renderTab();};

  tabButtons[key]=b;

}

  

function loadCharacter(path) {

  const file=app.vault.getAbstractFileByPath(path);

  const pg=file?dv.page(path):null;

  if (!file || !pg) { new Notice("Character could not be loaded."); return; }

  currentFile=file; currentPage=pg; window.__dndBuilderCharacterPath=path;

  draft={

    class:String(pg.class ?? ""),

    subclass:Array.isArray(pg.subclass)?[...pg.subclass]:String(pg.subclass ?? ""),

    level:clampInt(pg.level,1,20,1),

    str:clampInt(pg.str,1,30,10), dex:clampInt(pg.dex,1,30,10),

    con:clampInt(pg.con,1,30,10), int:clampInt(pg.int,1,30,10),

    wis:clampInt(pg.wis,1,30,10), cha:clampInt(pg.cha,1,30,10),

    inventory:Array.isArray(pg.inventory) ? pg.inventory.map(e=>({...e})) : [],

    asi_choices:pg.asi_choices&&typeof pg.asi_choices==="object"?JSON.parse(JSON.stringify(pg.asi_choices)):{},

    species:String(pg.species ?? ""),

    species_choices:pg.species_choices&&typeof pg.species_choices==="object"?JSON.parse(JSON.stringify(pg.species_choices)):{}

  };

  clearDirty(); renderTab();

}

  

async function saveCharacter() {

  if (!currentFile || !draft) {

    new Notice("No character selected.");

    return;

  }

  

  // Resolve the selected file again from the vault instead of relying on a stale object.

  const selectedPath=String(characterSelect.value ?? currentFile.path ?? "").trim();

  const targetFile=app.vault.getAbstractFileByPath(selectedPath);

  

  if(!targetFile || targetFile.extension!=="md"){

    status.setText("Save failed: character file not found");

    new Notice(`Character file not found: ${selectedPath}`);

    return;

  }

  

  saveBtn.disabled=true;

  status.setText(`Saving ${selectedPath}…`);

  

  try {

    const expectedLevel=clampInt(draft.level,1,20,1);

    const expectedClass=String(draft.class ?? "");

  

    await app.fileManager.processFrontMatter(targetFile, fm => {

      fm.class=expectedClass;

  

      const subclass=String(

        Array.isArray(draft.subclass) ? (draft.subclass[0] ?? "") : (draft.subclass ?? "")

      ).trim();

      fm.subclass=subclass || null;

  

      fm.level=expectedLevel;

  

      for(const [key] of ABILITIES){

        fm[key]=clampInt(draft[key],1,30,10);

      }

  

      fm.inventory=JSON.parse(JSON.stringify(draft.inventory ?? []));

      fm.asi_choices=JSON.parse(JSON.stringify(draft.asi_choices ?? {}));

      fm.species=String(draft.species ?? "").trim() || null;

      fm.species_choices=JSON.parse(JSON.stringify(draft.species_choices ?? {}));

    });

  

    // Wait until Obsidian metadata cache sees the newly written frontmatter.

    let verified=null;

    for(let attempt=0;attempt<20;attempt++){

      await new Promise(resolve=>setTimeout(resolve,100));

      const fm=app.metadataCache.getFileCache(targetFile)?.frontmatter;

      if(fm && Number(fm.level)===expectedLevel && String(fm.class ?? "")===expectedClass){

        verified=fm;

        break;

      }

    }

  

    if(!verified){

      // processFrontMatter already completed; verify directly from the file text as fallback.

      const raw=await app.vault.read(targetFile);

      if(!raw.includes(`level: ${expectedLevel}`)){

        throw new Error("The file was written, but the saved frontmatter could not be verified.");

      }

    }

  

    currentFile=targetFile;

    currentPage=dv.page(targetFile.path) ?? currentPage;

    dirty=false;

    saveBtn.setText("Save Changes");

    status.setText(`Saved: ${targetFile.path}`);

    new Notice(`Saved ${targetFile.basename}`);

  

    try { app.workspace.trigger("dataview:refresh-views"); } catch(e) {}

  } catch(err) {

    console.error("Character Builder save error:",err);

    status.setText(`Save failed: ${err?.message ?? err}`);

    new Notice(`Save failed: ${err?.message ?? err}`);

  } finally {

    saveBtn.disabled=false;

  }

}

  

characterSelect.onchange=()=>{

  if (dirty && !confirm("Discard unsaved changes and switch character?")) {

    characterSelect.value=currentFile?.path ?? characterSelect.value; return;

  }

  loadCharacter(characterSelect.value);

};

saveBtn.onclick=saveCharacter;

loadCharacter(characterSelect.value);

```