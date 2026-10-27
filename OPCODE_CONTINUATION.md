# OPCODE Continuation — TFR fork-analysis session

## Objective
Answer AI-odds / "is there a slant?" questions for The Fire Rises mod, one game rule / fork at a time. User lens: structural head start, tie-break, default weights, rule forcing. Current live thread delivered: **Navalny's three paths (dempop / navalnypop / ultranat)** — answer: NO slant, flat tie. On deck (offered, not requested): LDPR "Implement Party Program" sub-path.

## Ground truth (verified this session)
- Navalny ideology fork is chosen purely by game rule `The_Navalny_against_Navalny` (`common\game_rules\00_game_rules.txt:3746`).
  - `1991` = Democratic Populism (tricolors); `1993` = Navalnyist Populism (Putin alliance); `We_People` = Ultranationalism (Architector of Terror). Locs: `localisation\english\TFR_game_rules_l_english.yml:952-959`.
  - **No navalny power balance exists** (no bop object, no decision category) — unlike Dugin (`SOV_dugin_power_balance`, initial 0) and CPRF (`SOV_communist_party_balance`, initial -0.5). So no structural head start.
  - Only AI gate is a binary mutual-exclusive fork in `common\national_focus\TFR_national_focus_SOV_navalny.txt`:
    - `SOV_seize_the_majority_in_duma` :2536 — factor 1, **zeroed if rule = 1991 OR We_People** (i.e. active only under 1993/default). Sets authoritarian_democrat ruling party + patriotic_democracy1→2 + conservative coalition. (`set_politics` at :2575.)
    - `SOV_pledge_allegiance_to_the_ideas_of_revolution` :2818 — factor 1, **zeroed if rule = 1993** (active under 1991, We_People, default). patriotic_democracy1→3 + oligarch approval.
  - Default → both factor 1 → ~50/50 tie. Rule set → forced 100% (seize for 1993; pledge for 1991/We_People). Everything downstream uniformly factor 1; single-option flavor events (russia.617, russia.655) — no ai_chance selection among the three.
- Navalny tree facts: loaded by `russia.100` (`events\TFR_events_SOV.txt:3793`), tree id `SOV_navalny_tree` :2. Early power fork: `SOV_a_big_tent_government` :135 (available always no, unfinished), `SOV_Consolidate_Navalnys_Power` :160 (available = {} → yes; hos_gospodin_president +100pp), `SOV_Push_The_Figurehead_Aside` :185 (available truncated, ~always no) — only "Consolidate" takeable. Later tiers all factor 1: `SOV_rebrand_the_fsb` :2606, `SOV_control_the_fourth_front` :2633, `SOV_install_the_cult_of_navalny` ~:2755, `SOV_form_the_national_militia_squads` :2787, `SOV_protect_the_free_speech` :2881, `SOV_fuel_the_fading_torch` :2946, `SOV_a_letter_to_washington` :2967, `SOV_reform_the_gun_ownership_laws` :3004, `SOV_shape_up_the_political_field` :3029, `SOV_radicalize_the_youth` :3047 (fires russia.655), `SOV_unite_for_blood_and_home` ~:3068, `SOV_liberalism_with_an_ak` ~:3099, `SOV_ultranational_liberalism` :3129 (prereq `SOV_begin_the_navalny_trials` :3215; adds ultranational_liberalism + "Voiska FSK"), `SOV_a_russian_dream` :3219. Second-war rule check: `SOV_bring_the_freedom_back_to_europe` :930 optionally uses We_People bonus (rule ref :956).
- Post-loss fork `russia.706` (`TFR_events_SOV.txt:30169`) has NO roll: totalitarian_socialist → Dugin (`russia.125` :5036); libertarian_socialist → Navalny (`russia.100` :3793). Fired from `TFR_national_focus_SOV_communist_new.txt:6302`.
- Dugin 3-path (delivered earlier): no slant — bop initial 0 → set to 0.1 by `SOV_declare_eurasian_state` (`TFR_national_focus_SOV_dugin.txt:117-120`); middle band -0.35..0.35; symmetric ±0.1 pushes; branch gates all factor 1 zeroed only by rule `The_Dugin_Left_or_Right` (`00_game_rules.txt:3717`; lunarist=1/center=2/solarist=3). `SOV_dugin_bop_category` referenced by bop :940 but NOT defined anywhere → no decisions nudge it. Caveat: branch prereqs mutually exclusive with each other, so rule is de facto selector.
- Fifth column (delivered earlier): rule `The_Move_Against_the_Fifth_Column` (`00_game_rules.txt:3298`; do-it=1 / unnecessary=2). Foci 100/100 tie default: `SOV_pacify_abkhazia_and_south_ossetia` (`TFR_national_focus_SOV.txt:77`, zeroed if 1) vs `SOV_the_navalny_fifth_column` (:122, sets `SOV_the_navalny_fifth_column_flag` :153, zeroed if 2). Flag used only at `TFR_events_SOV.txt:11560` (russia.247 Solovyev opt) and NOT-gate `TFR_national_focus_SOV_communist_new.txt:3344` (russia.238). **Does NOT affect Navalny in the Medvedev/communist post-loss chain** (that fork is purely has_government).
- Recurring pattern: game rules use default=DEFAULT → all fork foci retain equal base factors → tie; setting the rule zeroes competitors' ai_will_do → 100% forced.

## Work State
- Completed: Dugin lunar/solar/center (no slant); fifth column (no slant, no Navalny impact); Navalny dempop/navalnypop/ultranat (no slant, rule-gated, no bop).
- Active: none. Next potential: LDPR 949-950 rule refs / "Implement Party Program" vs "Populist Revolution" — offered earlier, not yet requested.
- Blocked/notes: `russia.201`/`russia.721` event defs not located (fired by Dugin focuses, not needed for verdict). Full-tree recursive greps can time out (120-180s) — use targeted file-local greps / Read.

## Relevant Files
- `common\game_rules\00_game_rules.txt`: 3298 (fifth column), 3717 (Dugin L/R), 3746 (Navalny vs Navalny) — all in group RULE_GROUP_EUROPE_SOV, require WtT.
- `common\national_focus\TFR_national_focus_SOV_navalny.txt`: tree :2; early fork :135-204; main fork :2536/:2818; downstream :2606-3219.
- `common\national_focus\TFR_national_focus_SOV_dugin.txt`: balance shifts :117-120/:1344/:1388/:1441-1446/:1484/:1510; gates :2339?/:2478/:2566 (lunar/center/solar).
- `common\bop\TFR_bop_SOV.txt`: dugin bop :936 (initial 0, middle -0.35..0.35, undefined decision category :940); communist balance elsewhere.
- `common\national_focus\TFR_national_focus_SOV.txt`: fifth column :77/:122/:153; flag set :153.
- `common\national_focus\TFR_national_focus_SOV_communist_new.txt`: russia.706 fired :6302; flag NOT-gate :3344.
- `events\TFR_events_SOV.txt`: russia.706 :30169 (no-roll gov-type fork), russia.125 :5036, russia.100 :3793, russia.99 :3734, russia.247 flag check :11560, russia.617 :28837, russia.655 :29899.
- `localisation\english\TFR_country_localisation_SOV_l_english.yml:` navalny focus names ~:12107-12221.
- `localisation\english\TFR_game_rules_l_english.yml:` Navalny rule loc :952-959; Dugin rule loc :944-951.
- Mod root: `D:\SteamLibrary\steamapps\workshop\content\394360\3350890356`.