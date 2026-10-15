

---

# Qwen UI

> **Generational Lua interface library.** Dense multi‑column VAPE‑style dashboard, developer‑controlled settings, live theme / density / animation switching, key system, UNC pill, FPS, images, notifications, and an API built to be *easier than Rayfield*.
>
> Build: **`0.4.2-vape-cols-stable`** · Loader: gist raw URL below · Public API: stable across `0.4.x`.

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/f3bb4d2dce5f638fe0f5fd09fccd4b6e8202c56d/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()
```

---

## 30‑second start

Four steps, every time: **window → tab → section → widget.**

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/f3bb4d2dce5f638fe0f5fd09fccd4b6e8202c56d/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({ Title = "My Hub", Theme = "VapeDark", Stats = { FPS = true } })
local Tab    = Window:CreateTab("Main")
local Sec    = Tab:CreateSection("Controls")

Sec:CreateToggle({ Name = "Enable", Default = false, Function = function(on) end })
Sec:CreateSlider({ Name = "Amount", Min = 0, Max = 100, Default = 50, Function = function(v) end })
Sec:CreateButton({ Name = "Apply", Function = function() Window:Notify({ Title = "Applied", Type = "Success" }) end })
```

That's the whole mental model. Everything else is options.

---

## What's new in `0.4.2`

- **Custom rows now survive *every* live theme / density / animation switch.** (`0.4.1` dropped them on the 2nd switch; this build keeps a persistent record list and replays it on each rebuild.)
- **Section‑header accent square is positioned correctly** and the panel corner is no longer left gapped.
- **No manual patch needed.** Do **not** paste the old 6‑line "live rebuild patch" from the `0.4.0` docs into `0.4.1`/`0.4.2` — it would duplicate every row. Preservation is built in.
- API is **unchanged** — old scripts and old docs build identically; you only swap the raw URL.

---

## Read this before you switch themes in code

A live switch (`SetTheme` / `SetDensity` / `SetAnimation`) **rebuilds the UI from scratch**. That's how the whole shell recolours/resizes instantly — but it means the handles you grabbed *before* the switch now point at destroyed instances.

```lua
-- ❌ Don't do this
local Bar = Sec:CreateProgress({ Name = "Load", Value = 0 })
Window:SetTheme("NeonViolet")     -- rebuilds; Bar is now stale
Bar:Set(0.5)                      -- visual won't move; only re-runs your callback on a dead node

-- ✅ Do this: drive logic from a plain state table, not from handles
local state = { load = 0 }
Sec:CreateProgress({ Name = "Load", Value = 0, Function = function(v) state.load = v end })
Window:SetTheme("NeonViolet")     -- safe: nothing depends on a captured handle
-- after the switch the visible bar shows its configured Default; read/write state.load in your logic
```

Rules of thumb:

| Situation | What happens |
|---|---|
| End‑user switches via the **Settings dropdowns** | Fully fine — those controls are rebuilt and rebound each switch; you hold no handles. |
| You switch via **code** (`Window:SetTheme(...)`) | UI resets visuals to each widget's `Default`; previously captured handles go stale (their `Set` only re‑fires your callback against a dead node). |
| You need a handle to keep working after a code switch | Re‑create the widget after the switch, or don't hold handles across switches at all. |

> Internal Settings widgets, the key screen, the UNC pill, the FPS pill, the notifier, and the toolbar are all rebuilt and rebound automatically on every switch — you never have to touch them.

---

## Dashboard anatomy

This is what you get out of the box (matches the reference layout):

```
┌──────────────────────────────────────────────────────────┐
│ [Q]  Title  • subtitle            [Key: Lock][UNC --][FPS 60] │  header
│                                       [≡] [💬] [Options] [○] [✕] │  toolbar
├────────────┬─────────────────────────────────────────────┤
│ ▣ Main     │  ┌─────────────┐  ┌─────────────┐  ┌──────┐ │
│ ▣ Aim      │  │ ▾ Section A │  │ ▾ Section B │  │  ... │ │  sidebar | wrapping columns
│ ▣ Visuals  │  │  [toggle]   │  │  [slider]   │  │      │ │
│ ▣ Settings │  │  [slider]   │  │  [button]   │  │      │ │
│            │  │  [button]   │  │  [dropdown] │  │      │ │
│            │  └─────────────┘  └─────────────┘  └──────┘ │
└────────────┴─────────────────────────────────────────────┘
        ▲ left tab rail (desktop/tablet) — becomes a BOTTOM rail on mobile
```

- **[Q]** brand square (accent colour) + title + optional subtitle.
- **Header pills** (centre): `Key:` (only if key system on), `UNC` (only if UNC on), `FPS` (only if `Stats.FPS`). All recolour live.
- **Toolbar** (right): `≡` toggles the tab rail · `💬` opens notification history · `Options` jumps to Settings · `○` cycles theme (hidden when locked) · `✕` closes.
- **Sidebar / bottom rail**: tab buttons, each with a small accent square + active indicator bar.
- **Content**: section panels that **wrap into columns** on desktop/tablet and **stack full‑width** on mobile. Click a section header to collapse it (`▾` ↔ `▸`).

The toolbar is **drawn chrome, not developer API** — it adds no `CreateX` methods. You never call it; it just makes the hub look finished.

---

## `CreateWindow` config

| Key | Type | Default | Notes |
|---|---|---|---|
| `Title` | string | `"Qwen UI"` | Header title |
| `Subtitle` | string | `""` | Small line under title |
| `Author` | string | `""` | Stored metadata |
| `Version` | string | build ver | Stored metadata |
| `Theme` | string | `"VapeDark"` | Start theme; `nil` ⇒ locked (uses `LockedTheme` or default) |
| `ThemeLock` | bool | `false` | Block theme changes (hides ring + locks dropdown) |
| `LockedTheme` | string | `nil` | Force a locked theme; implies `ThemeLock` |
| `Device` | string | `"Auto"` | `Auto`/`Desktop`/`Tablet`/`Mobile` |
| `Density` | string | `"Comfort"` | `Compact`/`Comfort`/`Cozy`/`MobileTouch` |
| `Animation` | string | `"Smooth"` | `Full`/`Smooth`/`Reduced`/`Off`/`Auto` (stored + triggers rebuild; tween intensities not yet scaled by mode in this build) |
| `Width` | number | `620` | Desktop/tablet window width |
| `Height` | number | `420` | Desktop/tablet window height |
| `Corner` | number | `6` | Roundness, clamped to `10` |
| `Accent` | Color3 | `nil` | Override accent + active tint |
| `Watermark` | bool | `false` | Small `Qwen UI` label bottom‑right |
| `Settings` | table | `{Enabled=true}` | Auto Settings tab |
| `Stats` | table | `{FPS=false,UpdateRate=0.5}` | FPS pill |
| `Notifications` | table | `{Enabled=true,Duration=4,MaxVisible=4}` | Toasts |
| `KeySystem` | table | disabled | Lock screen (see §Key system) |
| `UNC` | table | disabled | Verification pill (see §UNC) |
| `Permissions` | table | `{}` | Reserved for future per‑setting locks |

```lua
local Window = Qwen:CreateWindow({
    Title = "Qwen Hub", Subtitle = "beta", Author = "grammerp54-cloud",
    Theme = "VapeDark", Device = "Auto", Density = "Comfort",
    Width = 640, Height = 430, Corner = 6,
    Settings = { Enabled = true },
    Stats = { FPS = true, UpdateRate = 0.5 },
    Notifications = { Enabled = true, Duration = 4, MaxVisible = 4 },
})
```

---

## Themes

Nine presets. Default `VapeDark` uses a warm **amber** highlight (the reference look), not generic purple.

| Theme | Look | | Theme | Look |
|---|---|---|---|---|
| `VapeDark` | near‑black + amber (default) | | `CrimsonDark` | charcoal + red |
| `AcrylicBlue` | dark navy + cyan glass | | `EmeraldNight` | dark green + mint |
| `MidnightPurple` | indigo + soft violet | | `LightPorcelain` | light mode + violet |
| `ObsidianBlack` | pure black + white | | `HighContrast` | black + yellow, sharp borders |
| `NeonViolet` | black‑violet + magenta | | | |

```lua
Theme = "AcrylicBlue"                                  -- pick one
Accent = Color3.fromRGB(255, 120, 40)                  -- keep theme, swap highlight
Theme = "AcrylicBlue", ThemeLock = true                -- lock it (users can't change)
LockedTheme = "MidnightPurple"                         -- force a locked theme in one key
```

When locked: the toolbar ring disappears, the Settings theme dropdown is replaced by a *"locked by developer"* note, and `Window:SetTheme(...)` becomes a no‑op.

---

## Device & Density

`Device = "Auto"` reads touch / keyboard / viewport and picks a layout. Force one with `Desktop` / `Tablet` / `Mobile`.

| Mode | Layout |
|---|---|
| **Desktop** | centred draggable window · left tab rail · sections wrap into columns |
| **Tablet** | same, but section width floored at `232` + bigger touch rows |
| **Mobile** | full‑screen window · **bottom** tab rail · sections stack single‑column · larger taps |

Density map (mobile forces touch sizes regardless):

| Density | Row | Font | Section W | Header | Gap | Sub |
|---|---|---|---|---|---|---|
| `Compact` | 26 | 12 | 196 | 30 | 4 | 11 |
| `Comfort` | 30 | 13 | 214 | 34 | 6 | 11 |
| `Cozy` | 36 | 14 | 232 | 38 | 8 | 12 |
| `MobileTouch` | 46 | 15 | full‑width | 44 | 8 | 13 |

```lua
Density = "Compact"
Window:SetDensity("Cozy")     -- live, rows kept
```

---

## Tabs & Sections

```lua
local MainTab = Window:CreateTab("Main")        -- first tab auto‑selects
local AimTab  = Window:CreateTab("Aim")
local Sec     = MainTab:CreateSection("Main Options")
Sec:SetCollapsed(true)                           -- collapse the panel
Sec:SetCollapsed(false)                          -- expand
```

- Tab button 2nd arg `CreateTab(name, icon)` is **accepted but not rendered** yet — icons are auto‑drawn accent squares, so an asset id does nothing visually in this build.
- Section header = small accent square + title + chevron; **click the header to collapse/expand**.
- On desktop/tablet sections flow horizontally and wrap into columns; on mobile they stack.

---

## Widgets

Every widget is `Sec:CreateX({ ... })`. Common fields across all: `Name`, `Hidden` (returns `nil`, not shown). `Description` only renders on **Button / Toggle / Slider**. `Locked` only works on **Button / Toggle** (others use `Hidden`).

### Button

```lua
local B = Sec:CreateButton({
    Name = "Copy Key",
    Description = "Copies your key to clipboard.",   -- optional taller row
    CopyText = "DEMO-KEY-1234",                      -- copies on click
    Url = "https://your-key-link_here",              -- also copies (link to clipboard)
    Notify = { Title = "Copied", Type = "Success" }, -- optional inline toast
    Function = function() end,                       -- your logic
    -- Callback = function() end,                    -- alias for Function
    -- Disabled = false, Locked = false, Hidden = false,
})
B:SetText("Copied!")
B:SetDisabled(true)
```

| Param | Type | Notes |
|---|---|---|
| `Name` | string | label |
| `Description` | string | muted sub‑line (makes row taller) |
| `CopyText` | string | copied to clipboard on click |
| `Url` | string | copied to clipboard on click (does **not** open a browser) |
| `Notify` | table | fired automatically after click |
| `Function` / `Callback` | fn | your logic |
| `Disabled` / `Locked` / `Hidden` | bool | states |

Returns: `Element`, `SetText(text)`, `SetDisabled(bool)`.

### Toggle

```lua
Sec:CreateToggle({
    Name = "Enable ESP", Description = "Highlights players.",
    Default = false,
    Function = function(on) end,    -- on = true/false
    -- Callback = function(on) end, Locked = false, Hidden = false,
})
```

When **ON** the whole row tints with a darkened accent and the switch glows — exactly the highlighted rows in the reference image. Returns: `Element`, `Set(bool)`, `Get()`.

### Slider

```lua
Sec:CreateSlider({
    Name = "FOV", Description = "Target cone.",
    Min = 0, Max = 120, Step = 1, Default = 60,
    Suffix = " deg",          -- appended to the value label
    Decimals = 0,             -- set 1+ (with Step<1) for float values
    Function = function(v) end,
    -- Callback = function(v) end, Hidden = false,
})
```

Returns: `Element`, `Set(number)` (snaps to `Step`), `Get()`.

### Dropdown

```lua
Sec:CreateDropdown({
    Name = "Mode",
    Values = { "Normal", "Fast", "Slow" },   -- strings or numbers
    Default = "Normal",
    Function = function(v) end,
    -- Callback = function(v) end, Hidden = false,
})
```

Popup is **clamped to the screen** and flips *above* the row near the bottom edge; the selected option is highlighted inside the list. Returns: `Element`, `Set(value)`, `Get()`.

### Input

```lua
Sec:CreateInput({
    Name = "Amount", Placeholder = "0", Default = 1,
    Type = "Number",     -- "Text" (default) | "Number"
    Min = 1, Max = 100,  -- only enforced when Type = "Number"
    MaxLength = 32,
    Function = function(v) end,
    -- Callback = function(v) end, Hidden = false,
})
```

Commits on focus‑loss. Returns: `Element`, `Set(text)`, `Get()`.

### Keybind

```lua
Sec:CreateKeybind({
    Name = "Menu Key",
    Default = Enum.KeyCode.RightControl,
    Function = function(binding) end,   -- fires on capture AND on every match
    -- Hidden = false,
})
```

Click the bind button → shows `...` → press any key/mouse button → captured. (Keybinds use `Function` only; there is no `Callback` alias here.) Returns: `Element`, `Set(binding)`, `Get()`.

### Label / Divider / Image / Progress

```lua
Sec:CreateLabel({ Text = "Note", Variant = "Muted", Size = 12, Font = Enum.Font.Gotham })
Sec:CreateDivider()
Sec:CreateImage({ Source = 123456789, Width = 150, Height = 90, ScaleType = Enum.ScaleType.Fit })
local Bar = Sec:CreateProgress({ Name = "Download", Value = 0, Function = function(v) end })
```

- `Variant`: `Text` · `Muted` · `Accent` · `Success` · `Warning` · `Danger` · `Info`.
- `Source` accepts: number asset id · `"rbxassetid://…"` · `"http(s)://…"` · `nil` (blank). `ScaleType`: `Fit`/`Stretch`/`Tile`/`Crop`.
- Progress value is `0`–`1`, tweened.

Returns: Label/Divider/Image = the instance itself. Progress = `Element`, `Set(0–1)`, `Get()`.

---

## Notifications

Top‑right toasts with a coloured left bar, title, optional message, a close `✕`, and a shrinking progress line. They follow the live theme.

```lua
Window:Notify({ Title = "Saved", Message = "Settings stored.", Type = "Success", Duration = 4 })
local history = Window:GetHistory()    -- array of { Title, Message, Type }
Window:OpenHistory()                   -- open the history panel programmatically
```

| Type | Colour | | `Duration` | |
|---|---|---|---|---|
| `Info` | blue | | `>0` seconds then auto‑dismiss |
| `Success` | green | | `0` = sticky until closed |
| `Warning` | amber | | |
| `Danger` | red | | |

The toolbar `💬` button also opens the history panel (newest first, type‑tagged).

---

## Key system

Themed lock screen (drawn `Q` brand square) shown **before** the main UI. Correct key opens; wrong key burns an attempt; at zero a cooldown runs, then attempts reset; `Enter` also submits; `Copy`/`Help` copy your safe strings. A `Key:` pill appears in the header while enabled.

```lua
-- static key
KeySystem = {
    Enabled = true, Title = "Qwen Access", Description = "Enter your key to continue.",
    Placeholder = "ENTER KEY", Key = "DEMO-KEY-1234",
    Attempts = 5,            -- 0 = unlimited
    Cooldown = 30,
    CopyText = "https://your-key-link_here",
    HelpText = "https://your-help-link_here",
}

-- callback validation (recommended for custom logic)
local Valid = { ["KEY-ABC"] = true, ["KEY-XYZ"] = true }
KeySystem = {
    Enabled = true, Title = "Access Required", Placeholder = "KEY",
    Attempts = 3, Cooldown = 20,
    Function = function(input) return Valid[input] == true end,
}
```

If Settings is enabled, the Settings tab gains a **Relock UI** button (closes the hub and re‑shows the lock screen).

---

## UNC verification pill

A neutral header chip (`UNC OK` / `UNC FAIL` / `UNC --`). Checks a value or runs your callback. **Not a bypass system.**

```lua
UNC = { Enabled = true, Auto = true, Value = LocalPlayer.Name }

-- callback form
local Allowed = { PlayerNameHere = true }
UNC = {
    Enabled = true, Auto = true, Value = LocalPlayer.Name,
    Function = function(v) return Allowed[v] == true end,
}
```

If Settings is enabled, the Settings tab gains a **Retry UNC Check** button.

---

## Settings tab (auto)

`Settings = { Enabled = true }` (default on) builds a **Settings** tab with two sections:

- **Interface** — theme label; theme dropdown (live) *or* a *"locked by developer"* note; density dropdown (live); animation dropdown (live rebuild).
- **Behavior** — FPS Counter toggle (live) · Notifications toggle (live) · Watermark toggle (live) · **Retry UNC Check** (only if UNC on) · **Relock UI** (only if key system on).

```lua
Settings = { Enabled = false }   -- hide it entirely for a clean hub
```

---

## Recipes (copy‑paste)

**State pattern** — store values in a table, wire widgets to it (this is also what makes live switches safe):

```lua
local state = { enabled = false, fov = 60, mode = "Normal" }
Sec:CreateToggle({ Name = "Enable", Default = false, Function = function(v) state.enabled = v end })
Sec:CreateSlider({ Name = "FOV", Min = 0, Max = 120, Default = 60, Function = function(v) state.fov = v end })
Sec:CreateDropdown({ Name = "Mode", Values = { "Normal", "Fast", "Slow" }, Default = "Normal", Function = function(v) state.mode = v end })
Sec:CreateButton({ Name = "Apply", Function = function()
    Window:Notify({ Title = "Applied", Message = state.mode .. " | FOV " .. tostring(state.fov), Type = "Success" })
end })
```

**Drive a progress bar with buttons** (no stale‑handle risk if you don't switch theme in code):

```lua
local Bar = Sec:CreateProgress({ Name = "Download", Value = 0 })
Sec:CreateButton({ Name = "+20%", Function = function() Bar:Set(math.min(Bar:Get() + 0.2, 1)) end })
Sec:CreateButton({ Name = "Reset", Function = function() Bar:Set(0) end })
```

**Copy‑link button** (the `Url` field copies, it does not open):

```lua
Sec:CreateButton({ Name = "Get Key", Url = "https://your-key-link_here",
    Function = function() Window:Notify({ Title = "Link copied", Type = "Info" }) end })
```

**Locked theme + key + mobile, all together:**

```lua
local Window = Qwen:CreateWindow({
    Title = "Protected Hub", Theme = "AcrylicBlue", ThemeLock = true, Device = "Mobile",
    KeySystem = { Enabled = true, Key = "QWEN-DEMO", Attempts = 3, Cooldown = 20,
        CopyText = "https://your-key-link_here", HelpText = "https://your-help-link_here" },
    Settings = { Enabled = true }, Stats = { FPS = true },
})
```

---

## Mega example

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/f3bb4d2dce5f638fe0f5fd09fccd4b6e8202c56d/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local state = { mainEnabled = false, fov = 60, smoothness = 1, mode = "Normal" }

local Window = Qwen:CreateWindow({
    Title = "Qwen Example Hub", Subtitle = "docs demo", Theme = "VapeDark",
    Device = "Auto", Density = "Comfort", Width = 660, Height = 440,
    Settings = { Enabled = true }, Stats = { FPS = true },
    Notifications = { Enabled = true, Duration = 4, MaxVisible = 4 },
})

local MainTab, AimTab, VisTab = Window:CreateTab("Main"), Window:CreateTab("Aim"), Window:CreateTab("Visuals")
local MainSec, AimSec, VisSec = MainTab:CreateSection("Main Controls"), AimTab:CreateSection("Aim Controls"), VisTab:CreateSection("Visual Effects")

MainSec:CreateToggle({ Name = "Enable Hub", Description = "Turns the main feature on.", Default = false, Function = function(v) state.mainEnabled = v end })
MainSec:CreateButton({ Name = "Reset", Description = "Restores defaults.", Function = function()
    state.fov, state.smoothness, state.mode = 60, 1, "Normal"
    Window:Notify({ Title = "Reset", Message = "Values restored.", Type = "Info" })
end })

AimSec:CreateSlider({ Name = "FOV", Min = 0, Max = 120, Step = 1, Default = 60, Suffix = " deg", Function = function(v) state.fov = v end })
AimSec:CreateSlider({ Name = "Smoothness", Min = 0.1, Max = 5, Step = 0.1, Decimals = 1, Default = 1, Function = function(v) state.smoothness = v end })
AimSec:CreateDropdown({ Name = "Aim Mode", Values = { "Normal", "Fast", "Slow" }, Default = "Normal", Function = function(v) state.mode = v end })

VisSec:CreateToggle({ Name = "Show Box", Default = true })
VisSec:CreateToggle({ Name = "Show Name", Default = true })
VisSec:CreateToggle({ Name = "Show Health", Default = false })
VisSec:CreateDivider()
VisSec:CreateLabel({ Text = "Visual options stored locally in this example.", Variant = "Muted" })
```

Sections wrap, so this renders as side‑by‑side columns on desktop — the reference layout.

---

## API cheat sheet

```
Qwen:CreateWindow(config) -> Window

Window:CreateTab(name[, icon]) -> Tab
Window:Notify(options)            Window:GetHistory() -> table
Window:Open() / Close() / Destroy()
Window:SetTheme(name)  Window:GetTheme() -> string      -- live, rows kept
Window:SetDensity(name)           -- live, rows kept
Window:SetAnimation(name)         -- live rebuild, rows kept
Window:ToggleTabs()   Window:OpenHistory()
Window.Gui / .Main / .Theme / .Config

Tab:CreateSection(name) -> Section        Tab.Button / Tab.Page

Section:CreateButton / CreateToggle / CreateSlider / CreateDropdown /
         CreateInput / CreateKeybind / CreateLabel / CreateDivider /
         CreateImage / CreateProgress
Section:SetCollapsed(bool)                Section.Frame / Section.List

Widget returns:
  Button   -> Element, SetText, SetDisabled
  Toggle   -> Element, Set(bool),   Get()
  Slider   -> Element, Set(number), Get()
  Dropdown -> Element, Set(value),  Get()
  Input    -> Element, Set(text),   Get()
  Keybind  -> Element, Set(bind),   Get()
  Progress -> Element, Set(0..1),   Get()
  Label / Divider / Image -> the instance itself
```

---

## Version history

| Build | Highlights | Known gap (fixed later) |
|---|---|---|
| `0.4.0-vape-cols` | dense multi‑column VAPE layout, collapsible sections, drawn monochrome icons, live rebuild, screen‑clamped dropdowns, hover accent, active‑row tint, watermark, top toolbar, theme‑following notifier/key/FPS | rows vanished after a switch (needed a manual patch) |
| `0.4.1-vape-cols-fix` | fixed `MakeFPS` `:connect` nil‑call crash; built row‑preservation in (old manual patch became obsolete); restored Key + UNC header pills | rows dropped on the **2nd** switch; header accent square misplaced |
| **`0.4.2-vape-cols-stable`** *(this)* | persistent record list ⇒ rows survive **every** switch, no duplication; header icon + corner fixed; API unchanged | — |

No new widget‑creation methods were added in any of these, so old scripts and old docs build the same way — you only swap the raw URL.

---

## Troubleshooting

| Symptom | Cause / fix |
|---|---|
| `Qwen` is `nil` / UI doesn't load | Wrong or blocked raw URL; ensure it's the direct `…/raw/<commit>/QwenUi.lua` link and HTTP is enabled. |
| Window missing | Key system waiting for a key · window closed · executor can't parent to `CoreGui` (falls back to `PlayerGui`) · script errored before `CreateWindow`. |
| Rows vanish on 2nd theme switch | You're on `0.4.1`. Use this `0.4.2` URL. |
| Header accent square in top‑left / corner gapped | `0.4.1` icon glitch; `0.4.2` fixes it. |
| Duplicate rows after a switch | You pasted the old `0.4.0` manual patch — remove it; preservation is built in. |
| Handle stops working after I switch theme in code | Expected (see *Read this before you switch themes*). Use the Settings dropdowns, or drive from a state table, or re‑create widgets after a code switch. |
| Theme won't change | Locked (`ThemeLock`/`LockedTheme`). Intended — ring + dropdown hidden/replaced. |
| Settings tab missing | `Settings = { Enabled = true }`. |
| FPS pill missing | `Stats = { FPS = true }` or enable in Settings. |
| Notifications not showing | `Notifications = { Enabled = true }`; `Duration = 0` makes them sticky. |
| Button does nothing | Use `Function` (alias `Callback`). |
| Toggle/Slider not updating your var | Read the value passed into `Function`. |
| Slider value odd | `Step = 1` for ints; `Step = 0.1, Decimals = 1` for floats. |
| Dropdown off‑screen | It's clamped now; near the bottom it flips above the row. |
| Tab icons not showing | `CreateTab(name, icon)` accepts the arg but icons are auto‑drawn squares in this build. |
| `Url` button didn't open a browser | By design — `Url` *copies* the link to clipboard (some environments block opening URLs). |

---

## Responsible use

Qwen UI is a **neutral interface library**. It contains no exploit functionality, game‑manipulation logic, bypass systems, memory editing, or harmful automation. The key system and UNC pill are neutral UI components for developers to configure responsibly.

---

## Final template

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/f3bb4d2dce5f638fe0f5fd09fccd4b6e8202c56d/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "My Hub", Theme = "VapeDark", Device = "Auto", Density = "Comfort",
    Settings = { Enabled = true }, Stats = { FPS = true },
})

local Tab = Window:CreateTab("Main")
local Sec = Tab:CreateSection("Main")

Sec:CreateToggle({ Name = "Enable", Default = false, Function = function(v) end })
Sec:CreateSlider({ Name = "Amount", Min = 0, Max = 100, Default = 50, Function = function(v) end })
Sec:CreateButton({ Name = "Action", Function = function() Window:Notify({ Title = "Action", Message = "Done.", Type = "Success" }) end })
```

---

