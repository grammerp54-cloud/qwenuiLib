

New raw URL used everywhere below:

```lua
https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua
```

---

# Qwen UI — Documentation (build `0.4.0-vape-cols`)

Qwen UI is a Lua interface library you load with one line, then build with simple calls: create a window, create a tab, create a section, add buttons / toggles / sliders / dropdowns / inputs / keybinds / labels / dividers / images / progress bars, fire notifications, lock with a key system, verify with a UNC badge, and switch themes / density / animation **live**.

This build is the dense multi‑column **VAPE‑style** dashboard: near‑black panels, small drawn accent squares on every section and tab, warm amber highlight in the default theme, collapsible sections, hover accents, screen‑clamped dropdowns, a real top toolbar, top‑right toasts with a progress line, and full mobile / tablet / desktop reflow. The developer API is unchanged from the previous build, so old scripts keep working — you only swap the raw URL.

---

## 1. Loader

Every script starts the same way:

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()
```

`Qwen` is the module. Build from it with `Qwen:CreateWindow(config)`.

---

## 2. Smallest Working Example

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({ Title = "Qwen Test" })

local MainTab = Window:CreateTab("Main")
local MainSection = MainTab:CreateSection("Actions")

MainSection:CreateButton({
    Name = "Click Me",
    Function = function()
        Window:Notify({ Title = "Pressed", Message = "Button worked.", Type = "Success" })
    end
})
```

That gives you: a window, one tab, one section panel, one button row, and a toast on click.

---

## 3. `CreateWindow` Config

| Key | Type | Default | Meaning |
|---|---|---|---|
| `Title` | string | `"Qwen UI"` | Header title |
| `Subtitle` | string | `""` | Small line under the title |
| `Author` | string | `""` | Stored metadata |
| `Version` | string | build version | Stored metadata |
| `Theme` | string | `"VapeDark"` | Starting theme name |
| `ThemeLock` | boolean | `false` | Prevents theme changes (hides the cycle ring + locks the dropdown) |
| `LockedTheme` | string | `nil` | Forces a locked theme; implies `ThemeLock = true` |
| `Device` | string | `"Auto"` | `Auto` / `Desktop` / `Tablet` / `Mobile` |
| `Density` | string | `"Comfort"` | `Compact` / `Comfort` / `Cozy` / `MobileTouch` |
| `Animation` | string | `"Smooth"` | `Full` / `Smooth` / `Reduced` / `Off` / `Auto` |
| `Width` | number | `620` | Desktop/tablet window width |
| `Height` | number | `420` | Desktop/tablet window height |
| `Corner` | number | `6` | Corner roundness (clamped to 10) |
| `Accent` | Color3 | `nil` | Overrides the theme accent + active tint |
| `Watermark` | boolean | `false` | Shows a small `Qwen UI` label bottom‑right |
| `Settings` | table | `{ Enabled = true }` | Adds the auto Settings tab |
| `Stats` | table | `{ FPS = false, UpdateRate = 0.5 }` | FPS chip |
| `Notifications` | table | `{ Enabled = true, Duration = 4, MaxVisible = 4 }` | Toasts |
| `KeySystem` | table | disabled | Lock screen |
| `UNC` | table | disabled | Verification badge |
| `Permissions` | table | `{}` | Reserved for future per‑setting locks |

Full example:

```lua
local Window = Qwen:CreateWindow({
    Title = "Qwen Hub",
    Subtitle = "beta",
    Author = "grammerp54-cloud",
    Theme = "VapeDark",
    Device = "Auto",
    Density = "Comfort",
    Width = 640,
    Height = 430,
    Corner = 6,
    Watermark = false,
    Settings = { Enabled = true },
    Stats = { FPS = true, UpdateRate = 0.5 },
    Notifications = { Enabled = true, Duration = 4, MaxVisible = 4 },
})
```

---

## 4. Themes

Nine built‑in themes. The default `VapeDark` now uses a warm **amber** highlight (matching the reference dashboard), not generic purple.

| Theme | Look |
|---|---|
| `VapeDark` | Near‑black + amber accent (default) |
| `AcrylicBlue` | Dark navy + cyan‑blue glass |
| `MidnightPurple` | Deep indigo + soft violet |
| `ObsidianBlack` | Pure black + white accent |
| `NeonViolet` | Black‑violet + magenta |
| `CrimsonDark` | Charcoal + red |
| `EmeraldNight` | Dark green + mint |
| `LightPorcelain` | Light mode + violet |
| `HighContrast` | Black + yellow, sharp borders |

Use one:

```lua
local Window = Qwen:CreateWindow({ Title = "Blue Hub", Theme = "AcrylicBlue" })
```

Custom accent override (keeps the theme but swaps the highlight color):

```lua
local Window = Qwen:CreateWindow({
    Theme = "VapeDark",
    Accent = Color3.fromRGB(255, 120, 40),
})
```

### Locking the theme

Stop users from changing it (the toolbar cycle‑ring disappears and the Settings theme dropdown is replaced by a locked note):

```lua
local Window = Qwen:CreateWindow({ Title = "Locked Hub", Theme = "AcrylicBlue", ThemeLock = true })
```

Or force a locked theme in one key:

```lua
local Window = Qwen:CreateWindow({ Title = "Locked Hub", LockedTheme = "MidnightPurple" })
```

`LockedTheme` automatically sets `ThemeLock = true`.

---

## 5. Live Theme / Density / Animation Switching

This is the headline upgrade of this build. Changing the theme or density **rebuilds the whole shell instantly** — header, sidebar, panels, section frames, tab buttons, toasts, key screen, and FPS chip all recolour or resize on the spot. No more "saved for next reload".

Three new window methods drive it:

```lua
Window:SetTheme("NeonViolet")     -- recolour everything live
Window:GetTheme()                 -- returns current theme name
Window:SetDensity("Compact")      -- resize rows/columns live
Window:SetAnimation("Reduced")    -- stored + triggers a rebuild
```

The Settings tab wires these to real dropdowns, so end users can switch live too (unless the theme is locked).

> **Important — keep your custom rows across a switch (recommended 6‑line patch).**
> In the raw file as‑is, a live theme/density change rebuilds the section *panels* but does **not** automatically replay the buttons/toggles/sliders you added inside them, so custom rows would vanish after a switch. To make every row survive (they reload from your original config, resetting to their `Default` value), paste this block into `BuildSection`, **right before the final `return sectionObj`**:
>
> ```lua
> local prior = secRec.widgets
> secRec.widgets = {}
> for _, rec in ipairs(prior) do
>     add(rec.kind, rec.cfg)
> end
> ```
>
> With it applied, live switching preserves your entire UI structure. (Runtime drag/flip values are not carried across a rebuild in this build — rows return to their configured `Default`.) If you skip the patch, see Troubleshooting §"rows disappear".

---

## 6. Device Modes & Layout Reflow

`Device = "Auto"` detects touch / keyboard / screen size and picks a layout. Force one with `Desktop` / `Tablet` / `Mobile`.

| Mode | Layout |
|---|---|
| **Desktop** | Centered draggable window, left tab rail, sections **wrap into multiple columns** (the VAPE look) |
| **Tablet** | Same as desktop but wider section columns + bigger touch rows |
| **Mobile** | Full‑screen window, **bottom tab rail**, sections stack single‑column full‑width, larger taps |

```lua
local Window = Qwen:CreateWindow({ Title = "Mobile Hub", Device = "Mobile", Theme = "MidnightPurple" })
```

The toolbar's three‑line button toggles the tab rail visibility (handy on desktop/tablet to give content more room; on mobile it hides the bottom rail, so use sparingly).

---

## 7. Density

Density changes row height, font size, padding, section width, header height, and gaps. On mobile it is forced to touch sizes regardless.

| Density | Row | Font | Section width | Feel |
|---|---|---|---|---|
| `Compact` | 26 | 12 | 196 | Dense, many columns |
| `Comfort` | 30 | 13 | 214 | Balanced (default) |
| `Cozy` | 36 | 14 | 232 | Roomy, fewer columns |
| `MobileTouch` | 46 | 15 | full‑width | Big tap targets |

```lua
local Window = Qwen:CreateWindow({ Density = "Compact" })
Window:SetDensity("Cozy")  -- live
```

---

## 8. Tabs

```lua
local MainTab = Window:CreateTab("Main")
local AimTab = Window:CreateTab("Aim")
local VisualTab = Window:CreateTab("Visuals")
```

The first tab is selected automatically. Each tab button shows a small drawn accent square + label + an active indicator bar (left bar on desktop, bottom bar on mobile). The second argument of `CreateTab(name, icon)` is accepted but **not rendered** yet — icons are auto‑drawn squares, so passing an asset id does nothing visually in this build.

---

## 9. Sections (collapsible, multi‑column)

```lua
local MainSection = MainTab:CreateSection("Main Options")
local AimSection = AimTab:CreateSection("Aim Settings")
```

Each section is a rounded panel with a header containing a tiny accent square, the title, and a chevron. **Click the header to collapse/expand** it (chevron flips `▾` ↔ `▸`). On desktop/tablet, sections flow horizontally and wrap into columns like the reference image; on mobile they stack.

New section method:

```lua
MainSection:SetCollapsed(true)   -- collapse
MainSection:SetCollapsed(false)  -- expand
```

---

## 10. Buttons

```lua
MainSection:CreateButton({
    Name = "Load Profile",
    Function = function()
        -- developer code
    end
})
```

With description (taller row, muted sub‑line):

```lua
MainSection:CreateButton({
    Name = "Reset Settings",
    Description = "Restores default values.",
    Function = function() end
})
```

Copy text on click:

```lua
MainSection:CreateButton({
    Name = "Copy Key",
    CopyText = "DEMO-KEY-1234",
    Function = function()
        Window:Notify({ Title = "Copied", Message = "Key copied.", Type = "Success" })
    end
})
```

Copy a link / open a URL‑style payload:

```lua
MainSection:CreateButton({
    Name = "Get Key",
    CopyText = "https://your-key-link_here",
})
```

Inline notification on click (no `Function` needed):

```lua
MainSection:CreateButton({
    Name = "Apply",
    Notify = { Title = "Applied", Message = "Saved.", Type = "Success" },
})
```

States:

```lua
MainSection:CreateButton({ Name = "Coming Soon", Disabled = true })
MainSection:CreateButton({ Name = "Admin Only", Locked = true })
MainSection:CreateButton({ Name = "Hidden Debug", Hidden = true })
```

New visual detail: hovering a button fades in a thin **accent line on the left edge** and lifts the row colour. Locked rows get a border + muted text.

Return object:

```lua
local MyButton = MainSection:CreateButton({ Name = "Start" })
MyButton:SetText("Started")
MyButton:SetDisabled(true)
```

| Method | Meaning |
|---|---|
| `SetText(text)` | Change label |
| `SetDisabled(bool)` | Enable/disable |

---

## 11. Toggles

```lua
MainSection:CreateToggle({
    Name = "Enable ESP",
    Default = false,
    Function = function(state)
        -- state is true/false
    end
})
```

With description:

```lua
MainSection:CreateToggle({
    Name = "Silent Mode",
    Description = "Hide activity status.",
    Default = true,
    Function = function(state) end
})
```

Locked / hidden:

```lua
MainSection:CreateToggle({ Name = "Premium Feature", Locked = true })
MainSection:CreateToggle({ Name = "Internal Flag", Hidden = true })
```

New visual detail: when **ON**, the whole row tints with a darkened accent (`RowActive`) and the switch glows the theme accent — exactly like the highlighted rows in the reference image.

Return object:

```lua
local EspToggle = MainSection:CreateToggle({ Name = "Enable ESP", Default = false })
EspToggle:Set(true)
local on = EspToggle:Get()
```

| Method | Meaning |
|---|---|
| `Set(bool)` | Set state |
| `Get()` | Current state |

---

## 12. Sliders

```lua
MainSection:CreateSlider({
    Name = "FOV",
    Min = 0, Max = 120, Step = 1, Default = 60,
    Function = function(value) end
})
```

Suffix:

```lua
MainSection:CreateSlider({
    Name = "Health", Min = 0, Max = 100, Step = 1, Default = 80, Suffix = "%",
    Function = function(value) end
})
```

Decimals:

```lua
MainSection:CreateSlider({
    Name = "Smoothness", Min = 0.1, Max = 5, Step = 0.1, Decimals = 1, Default = 1,
    Function = function(value) end
})
```

With description (taller row):

```lua
MainSection:CreateSlider({
    Name = "Distance",
    Description = "Maximum target distance.",
    Min = 10, Max = 1000, Step = 10, Default = 100, Suffix = " studs",
    Function = function(value) end
})
```

Return object:

```lua
local FovSlider = MainSection:CreateSlider({ Name = "FOV", Min = 0, Max = 120, Default = 60 })
FovSlider:Set(90)
local fov = FovSlider:Get()
```

| Method | Meaning |
|---|---|
| `Set(number)` | Set value (snaps to step) |
| `Get()` | Current value |

---

## 13. Dropdowns

```lua
MainSection:CreateDropdown({
    Name = "Mode",
    Values = { "Normal", "Fast", "Slow" },
    Default = "Normal",
    Function = function(value) end
})
```

Numbers work too:

```lua
MainSection:CreateDropdown({ Name = "Team", Values = { 1, 2, 3, 4 }, Default = 1, Function = function(v) end })
```

New behaviour: the popup is **clamped to the screen** — it never flies off the edge, and if the row is near the bottom the list flips *above* the row. The currently selected option is highlighted with the accent tint inside the list.

Return object:

```lua
local ModeDropdown = MainSection:CreateDropdown({ Name = "Mode", Values = { "A", "B" }, Default = "A" })
ModeDropdown:Set("B")
local mode = ModeDropdown:Get()
```

| Method | Meaning |
|---|---|
| `Set(value)` | Select option |
| `Get()` | Current option |

---

## 14. Inputs

```lua
MainSection:CreateInput({
    Name = "Username",
    Placeholder = "Enter name...",
    Default = "",
    Function = function(value) end
})
```

Number input with clamping:

```lua
MainSection:CreateInput({
    Name = "Amount", Type = "Number", Placeholder = "0", Default = 1, Min = 1, Max = 100,
    Function = function(value) end
})
```

Max length:

```lua
MainSection:CreateInput({ Name = "Code", Placeholder = "Enter code", MaxLength = 12, Function = function(v) end })
```

Return object:

```lua
local NameInput = MainSection:CreateInput({ Name = "Profile", Default = "Player" })
NameInput:Set("NewName")
local name = NameInput:Get()
```

| Method | Meaning |
|---|---|
| `Set(text)` | Set text |
| `Get()` | Current text |

---

## 15. Keybinds

```lua
MainSection:CreateKeybind({
    Name = "Menu Key",
    Default = Enum.KeyCode.RightControl,
    Function = function(binding) end
})
```

Click the bind button → it shows `...` → press any key/mouse button → it captures and fires `Function` on every match.

Return object:

```lua
local MenuKey = MainSection:CreateKeybind({ Name = "Menu Key", Default = Enum.KeyCode.RightControl })
MenuKey:Set(Enum.KeyCode.K)
local binding = MenuKey:Get()
```

| Method | Meaning |
|---|---|
| `Set(binding)` | Set binding |
| `Get()` | Current binding |

---

## 16. Labels

```lua
MainSection:CreateLabel({ Text = "This is a note.", Variant = "Muted" })
```

Variants map to theme colours:

| Variant | Colour |
|---|---|
| `Text` | Main text |
| `Muted` | Dim text |
| `Accent` | Theme accent |
| `Success` | Green |
| `Warning` | Amber |
| `Danger` | Red |
| `Info` | Blue |

Optional `Font` and `Size` overrides:

```lua
MainSection:CreateLabel({ Text = "Important", Variant = "Accent", Size = 13, Font = Enum.Font.GothamBold })
```

---

## 17. Dividers

```lua
MainSection:CreateDivider()
```

A thin inset line — use between grouped rows.

---

## 18. Images

Accepts a numeric asset id, an asset string, an `rbxassetid://` string, an `http(s)://` URL, or `nil`.

```lua
MainSection:CreateImage({ Source = 123456789, Width = 150, Height = 90 })
MainSection:CreateImage({ Source = "rbxassetid://123456789", Width = 150, Height = 90 })
MainSection:CreateImage({ Source = "https://example.com/image.png", Width = 160, Height = 100 })
```

Scale type:

```lua
MainSection:CreateImage({ Source = 123456789, Width = 160, Height = 100, ScaleType = Enum.ScaleType.Fit })
```

| ScaleType | Meaning |
|---|---|
| `Fit` | Keep ratio, fit inside |
| `Stretch` | Stretch to size |
| `Tile` | Repeat |
| `Crop` | Cut overflow |

---

## 19. Progress Bars

Value is `0`–`1`.

```lua
local Bar = MainSection:CreateProgress({ Name = "Loading", Value = 0.25 })
```

Drive it with buttons:

```lua
local Bar = MainSection:CreateProgress({ Name = "Download", Value = 0 })
MainSection:CreateButton({ Name = "+20%", Function = function() Bar:Set(math.min(Bar:Get() + 0.2, 1)) end })
MainSection:CreateButton({ Name = "Reset", Function = function() Bar:Set(0) end })
```

| Method | Meaning |
|---|---|
| `Set(number)` | Set 0–1 (tweened) |
| `Get()` | Current value |

---

## 20. Notifications

Toasts appear **top‑right** with a coloured left bar, a title, an optional message, a close `✕`, and a shrinking progress line. They follow the live theme.

```lua
Window:Notify({ Title = "Saved", Message = "Settings stored.", Type = "Success" })
```

| Type | Colour |
|---|---|
| `Info` | Blue |
| `Success` | Green |
| `Warning` | Amber |
| `Danger` | Red |

Custom duration (`0` = sticky until closed):

```lua
Window:Notify({ Title = "Hold", Message = "Stays longer.", Type = "Warning", Duration = 8 })
```

Read history in code:

```lua
local history = Window:GetHistory()
```

The toolbar speech‑bubble button opens a **notification history panel** (search‑free but full list, newest first, with type tags). New window method to open it programmatically:

```lua
Window:OpenHistory()
```

---

## 21. Key System

Shows a themed lock screen (with a drawn `Q` brand square) before the main UI. Correct key opens; wrong key burns an attempt; at zero a cooldown runs, then attempts reset. `Enter` also submits. Copy/Help buttons copy your safe strings.

Static key:

```lua
local Window = Qwen:CreateWindow({
    Title = "Protected Hub",
    KeySystem = {
        Enabled = true,
        Title = "Qwen Access",
        Description = "Enter your key to continue.",
        Placeholder = "ENTER KEY",
        Key = "DEMO-KEY-1234",
        Attempts = 5,
        Cooldown = 30,
        CopyText = "https://your-key-link_here",
        HelpText = "https://your-help-link_here",
    },
})
```

Callback validation (recommended for custom logic):

```lua
local ValidKeys = { ["KEY-ABC"] = true, ["KEY-XYZ"] = true }

local Window = Qwen:CreateWindow({
    Title = "Protected Hub",
    KeySystem = {
        Enabled = true,
        Title = "Access Required",
        Description = "Enter your access key.",
        Placeholder = "KEY",
        Attempts = 3,
        Cooldown = 20,
        Function = function(input) return ValidKeys[input] == true end,
    },
})
```

Unlimited attempts:

```lua
KeySystem = { Enabled = true, Key = "FREE-KEY", Attempts = 0, Cooldown = 10 }
```

If Settings is enabled, the Settings tab gains a **Relock UI** button that closes the hub and re‑shows the lock screen.

---

## 22. UNC Verification Badge

A neutral verification chip in the header (`UNC OK` / `UNC FAIL` / `UNC --`). It checks a value or runs your callback. Not a bypass system.

Simple:

```lua
local Window = Qwen:CreateWindow({
    Title = "Verified Hub",
    UNC = { Enabled = true, Auto = true, Value = LocalPlayer.Name },
})
```

Callback:

```lua
local Allowed = { PlayerNameHere = true }

local Window = Qwen:CreateWindow({
    Title = "Verified Hub",
    UNC = {
        Enabled = true, Auto = true, Value = LocalPlayer.Name,
        Function = function(value) return Allowed[value] == true end,
    },
})
```

If Settings is enabled, the Settings tab gains a **Retry UNC Check** button.

---

## 23. Settings Tab (auto)

Enable with `Settings = { Enabled = true }` (default on). Disable with `{ Enabled = false }`.

Contents (only what applies is shown):

- **Interface** section: theme note; theme dropdown (live) *or* a "locked by developer" note; density dropdown (live); animation dropdown (live rebuild).
- **Behavior** section: FPS Counter toggle (live), Notifications toggle (live), Watermark toggle (live), plus **Retry UNC Check** (if UNC enabled) and **Relock UI** (if key system enabled).

```lua
local Window = Qwen:CreateWindow({ Title = "Clean Hub", Settings = { Enabled = false } })
```

---

## 24. Toolbar Chrome (visual, not developer API)

The top bar is drawn, not built by you, so it adds **no new `CreateX` methods**. It contains:

- Brand `Q` square + title + subtitle (left)
- FPS chip (center, only if `Stats.FPS`)
- Three‑line button → toggles the tab rail
- Speech‑bubble button → opens notification history
- **Options** pill → jumps to the Settings tab (if enabled)
- Ring button → cycles theme (hidden when locked)
- Red `✕` → closes the window

These are internal; you never call them. They exist so the hub *looks* like the reference dashboard out of the box.

---

## 25. Locking & Hiding Controls

`Hidden = true` removes a row from view entirely. `Locked = true` shows it disabled with a border (currently supported on **buttons** and **toggles**). For sliders/dropdowns/inputs/keybinds, use `Hidden` for now.

```lua
MainSection:CreateButton({ Name = "Debug", Hidden = true })
MainSection:CreateToggle({ Name = "Premium", Locked = true })
```

---

## 26. Recommended State Pattern

Store values in a table, wire widgets to it:

```lua
local state = { enabled = false, fov = 60, mode = "Normal" }

MainSection:CreateToggle({ Name = "Enable", Default = false, Function = function(v) state.enabled = v end })
MainSection:CreateSlider({ Name = "FOV", Min = 0, Max = 120, Default = 60, Function = function(v) state.fov = v end })
MainSection:CreateDropdown({ Name = "Mode", Values = { "Normal", "Fast", "Slow" }, Default = "Normal", Function = function(v) state.mode = v end })
MainSection:CreateButton({
    Name = "Apply",
    Function = function()
        Window:Notify({ Title = "Applied", Message = state.mode .. " | FOV " .. tostring(state.fov), Type = "Success" })
    end
})
```

---

## 27. Full Example Hub (multi‑column, live theme, FPS)

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local state = { mainEnabled = false, fov = 60, smoothness = 1, mode = "Normal" }

local Window = Qwen:CreateWindow({
    Title = "Qwen Example Hub",
    Subtitle = "docs demo",
    Theme = "VapeDark",
    Device = "Auto",
    Density = "Comfort",
    Width = 660,
    Height = 440,
    Settings = { Enabled = true },
    Stats = { FPS = true },
    Notifications = { Enabled = true, Duration = 4, MaxVisible = 4 },
})

local MainTab = Window:CreateTab("Main")
local AimTab = Window:CreateTab("Aim")
local VisualTab = Window:CreateTab("Visuals")

local MainSection = MainTab:CreateSection("Main Controls")
local AimSection = AimTab:CreateSection("Aim Controls")
local VisualSection = VisualTab:CreateSection("Visual Effects")

MainSection:CreateToggle({ Name = "Enable Hub", Description = "Turns the main feature on.", Default = false, Function = function(v) state.mainEnabled = v end })
MainSection:CreateButton({ Name = "Reset", Description = "Restores defaults.", Function = function()
    state.fov = 60; state.smoothness = 1; state.mode = "Normal"
    Window:Notify({ Title = "Reset", Message = "Values restored.", Type = "Info" })
end })

AimSection:CreateSlider({ Name = "FOV", Min = 0, Max = 120, Step = 1, Default = 60, Suffix = " deg", Function = function(v) state.fov = v end })
AimSection:CreateSlider({ Name = "Smoothness", Min = 0.1, Max = 5, Step = 0.1, Decimals = 1, Default = 1, Function = function(v) state.smoothness = v end })
AimSection:CreateDropdown({ Name = "Aim Mode", Values = { "Normal", "Fast", "Slow" }, Default = "Normal", Function = function(v) state.mode = v end })

VisualSection:CreateToggle({ Name = "Show Box", Default = true })
VisualSection:CreateToggle({ Name = "Show Name", Default = true })
VisualSection:CreateToggle({ Name = "Show Health", Default = false })
VisualSection:CreateDivider()
VisualSection:CreateLabel({ Text = "Visual options stored locally in this example.", Variant = "Muted" })
```

Because sections wrap, this renders as side‑by‑side columns on desktop — exactly the reference layout.

---

## 28. Locked Theme + Key System Example

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "Protected Hub",
    Theme = "AcrylicBlue",
    ThemeLock = true,
    Device = "Auto",
    KeySystem = {
        Enabled = true,
        Title = "Access Required",
        Description = "Enter your key to open the hub.",
        Placeholder = "XXXX-XXXX",
        Key = "QWEN-DEMO",
        Attempts = 3,
        Cooldown = 20,
        CopyText = "https://your-key-link_here",
        HelpText = "https://your-help-link_here",
    },
    Settings = { Enabled = true },
})

local MainTab = Window:CreateTab("Main")
local MainSection = MainTab:CreateSection("Controls")
MainSection:CreateToggle({ Name = "Feature", Default = false })
MainSection:CreateButton({ Name = "Test", Function = function() Window:Notify({ Title = "Working", Message = "Locked theme + key active.", Type = "Success" }) end })
```

---

## 29. Mobile Compact Example

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({ Title = "Mobile Hub", Theme = "MidnightPurple", Device = "Mobile", Settings = { Enabled = true }, Stats = { FPS = true } })

local MainTab = Window:CreateTab("Main")
local S = MainTab:CreateSection("Touch Controls")
S:CreateToggle({ Name = "Large Toggle", Default = false })
S:CreateSlider({ Name = "Large Slider", Min = 0, Max = 100, Default = 50 })
S:CreateButton({ Name = "Big Button", Function = function() Window:Notify({ Title = "Mobile", Message = "Touch layout active.", Type = "Info" }) end })
```

---

## 30. Notification Tester Example

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({ Title = "Notification Tester", Theme = "NeonViolet" })
local Tab = Window:CreateTab("Alerts")
local S = Tab:CreateSection("Types")

S:CreateButton({ Name = "Info", Function = function() Window:Notify({ Title = "Info", Message = "Normal.", Type = "Info" }) end })
S:CreateButton({ Name = "Success", Function = function() Window:Notify({ Title = "Success", Message = "Completed.", Type = "Success" }) end })
S:CreateButton({ Name = "Warning", Function = function() Window:Notify({ Title = "Warning", Message = "Check settings.", Type = "Warning" }) end })
S:CreateButton({ Name = "Danger", Function = function() Window:Notify({ Title = "Danger", Message = "Failed.", Type = "Danger" }) end })
S:CreateButton({ Name = "Open History", Function = function() Window:OpenHistory() end })
```

---

## 31. Media Example (image + progress + input)

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({ Title = "Media Example", Theme = "EmeraldNight" })
local Tab = Window:CreateTab("Media")
local S = Tab:CreateSection("Widgets")

S:CreateImage({ Source = 123456789, Width = 160, Height = 100 })
local Bar = S:CreateProgress({ Name = "Download", Value = 0 })
S:CreateButton({ Name = "+20%", Function = function() Bar:Set(math.min(Bar:Get() + 0.2, 1)) end })
S:CreateButton({ Name = "Reset", Function = function() Bar:Set(0) end })
S:CreateInput({ Name = "File Name", Placeholder = "model.txt", Default = "model.txt", MaxLength = 32 })
```

---

## 32. API Cheat Sheet

**Window**

```lua
Qwen:CreateWindow(config) -> Window
Window:CreateTab(name[, icon]) -> Tab
Window:Notify(options)
Window:GetHistory() -> table
Window:Open() / Window:Close() / Window:Destroy()
Window:SetTheme(name)            -- NEW (live)
Window:GetTheme() -> string      -- NEW
Window:SetDensity(name)          -- NEW (live)
Window:SetAnimation(name)        -- NEW (rebuild)
Window:ToggleTabs()              -- NEW (show/hide rail)
Window:OpenHistory()             -- NEW
```

**Tab**

```lua
Tab:CreateSection(name) -> Section
```

**Section**

```lua
Section:CreateButton(cfg) / CreateToggle(cfg) / CreateSlider(cfg) / CreateDropdown(cfg)
Section:CreateInput(cfg) / CreateKeybind(cfg) / CreateLabel(cfg) / CreateDivider()
Section:CreateImage(cfg) / CreateProgress(cfg)
Section:SetCollapsed(bool)       -- NEW
```

**Widget returns**

| Widget | Methods |
|---|---|
| Button | `SetText`, `SetDisabled` |
| Toggle | `Set`, `Get` |
| Slider | `Set`, `Get` |
| Dropdown | `Set`, `Get` |
| Input | `Set`, `Get` |
| Keybind | `Set`, `Get` |
| Progress | `Set`, `Get` |
| Label / Divider / Image | the instance itself |

---

## 33. What Changed From the Previous Build

- Default `VapeDark` recoloured to **amber** to match the reference dashboard.
- Sections now **wrap into multiple columns** on desktop/tablet (single column on mobile).
- **Collapsible sections** with chevron + `SetCollapsed`.
- **Drawn monochrome icons** (accent squares, speech bubble, ring, three‑line menu) replace emoji glyphs — cleaner, more "human".
- **Live theme switching** (`SetTheme` / `GetTheme` / toolbar ring / Settings dropdown) recolours the entire shell instantly.
- **Live density switching** (`SetDensity` / Settings dropdown) resizes rows + columns instantly.
- **Animation** stored + triggers rebuild (`SetAnimation`).
- **Screen‑clamped dropdowns** that flip above the row near the bottom edge.
- **Hover accent line** on buttons; **active‑row tint** on toggles.
- **Watermark** option + live Settings toggle.
- **Top toolbar** chrome: brand square, title/subtitle, centered FPS chip, rail toggle, history bubble, Options pill, theme ring, close.
- Notifier, key screen, and FPS chip now **follow the live theme**.
- New window methods: `SetTheme`, `GetTheme`, `SetDensity`, `SetAnimation`, `ToggleTabs`, `OpenHistory`.
- New section method: `SetCollapsed`.
- Clean teardown on `Destroy` (no leaked connections).

No new widget‑creation methods were added, so old scripts and old docs still build the same way.

---

## 34. Troubleshooting

**UI does not load** — confirm the raw URL is the direct `…/raw/<commit>/QwenUi.lua` link and HTTP is enabled. If `Qwen` is `nil`, the URL is wrong or blocked.

**Window missing** — key system may be waiting for a key; or the window was closed; or the executor cannot parent to `CoreGui` (it falls back to `PlayerGui`); or the script errored before `CreateWindow`.

**Custom rows disappear after changing theme/density** — apply the 6‑line **Live Rebuild Patch** in §5. Without it, a rebuild recreates section panels but not the rows inside them.

**Theme won't change** — it is locked (`ThemeLock = true` or `LockedTheme` set). That is intended; the cycle ring and dropdown are hidden/replaced.

**Settings tab missing** — set `Settings = { Enabled = true }`.

**FPS counter missing** — set `Stats = { FPS = true }` or enable it in Settings.

**Notifications not showing** — set `Notifications = { Enabled = true }`; `Duration = 0` makes them sticky until closed.

**Button does nothing** — use `Function` (or the alias `Callback`).

**Toggle/slider not updating your variable** — read the value passed into `Function` (`function(state)` / `function(value)`).

**Slider value looks odd** — set `Step = 1` for whole numbers, or `Step = 0.1, Decimals = 1` for decimals.

**Dropdown opens off‑screen** — it is now clamped; if a row is at the very bottom the list flips above it automatically.

**Tab icons not showing** — `CreateTab(name, icon)` accepts the argument but icons are auto‑drawn squares in this build; asset ids are not rendered yet.

---

## 35. Final Copy‑Paste Template

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "My Hub",
    Theme = "VapeDark",
    Device = "Auto",
    Density = "Comfort",
    Settings = { Enabled = true },
    Stats = { FPS = true },
})

local MainTab = Window:CreateTab("Main")
local MainSection = MainTab:CreateSection("Main")

MainSection:CreateToggle({ Name = "Enable", Default = false, Function = function(v) end })
MainSection:CreateSlider({ Name = "Amount", Min = 0, Max = 100, Default = 50, Function = function(v) end })
MainSection:CreateButton({ Name = "Action", Function = function() Window:Notify({ Title = "Action", Message = "Done.", Type = "Success" }) end })
```

---
