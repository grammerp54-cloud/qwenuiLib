
# Qwen UI — Detailed Documentation

**Raw loader URL:**

```lua
[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)
```

Qwen UI is a Lua interface library made to be loaded with one line, then built with simple calls like:

- create window
- create tab
- create section
- create button
- create toggle
- create slider
- create dropdown
- create notification

The goal is: **easy to build, clean to read, better than basic UI libraries, and developer-controlled.**

---

## 1. Basic Loader

Every script starts like this:

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
local Qwen = loadstring(game:HttpGet(RawGistURL))()
```

That returns the Qwen module.

Then you create a window:

```lua
local Window = Qwen:CreateWindow({
    Title = "My Hub"
})
```

That is the minimum start.

---

## 2. Smallest Working Example

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "Qwen Test"
})

local MainTab = Window:CreateTab("Main")
local MainSection = MainTab:CreateSection("Actions")

MainSection:CreateButton({
    Name = "Click Me",
    Function = function()
        Window:Notify({
            Title = "Pressed",
            Message = "Button worked.",
            Type = "Success"
        })
    end
})
```

This creates:

- a window
- one tab
- one section
- one button
- one notification when clicked

---

## 3. CreateWindow Config

`Qwen:CreateWindow(config)` accepts a table.

### Main config keys

| Key | Type | Default | Meaning |
|---|---|---|---|
| `Title` | string | `"Qwen UI"` | Main window title |
| `Subtitle` | string | `""` | Extra text shown beside title |
| `Author` | string | `""` | Stored author name |
| `Version` | string | library version | Stored version label |
| `Theme` | string | `"VapeDark"` | Theme name |
| `ThemeLock` | boolean | `false` | Locks theme from user changes |
| `LockedTheme` | string | `nil` | Forces a locked theme |
| `Device` | string | `"Auto"` | `Auto`, `Desktop`, `Tablet`, `Mobile` |
| `Density` | string | `"Comfort"` | Stored density preference |
| `Animation` | string | `"Smooth"` | Stored animation preference |
| `Width` | number | `580` | Desktop/tablet window width |
| `Height` | number | `390` | Desktop/tablet window height |
| `Corner` | number | `8` | Corner roundness |
| `Accent` | Color3 | `nil` | Overrides accent color |
| `Settings` | table | `{ Enabled = true }` | Enables settings tab |
| `Stats` | table | `{ FPS = false }` | FPS chip settings |
| `Notifications` | table | enabled | Toast notification settings |
| `KeySystem` | table | disabled | Key lock screen settings |
| `UNC` | table | disabled | Verification badge settings |

### Basic window example

```lua
local Window = Qwen:CreateWindow({
    Title = "Qwen Hub",
    Subtitle = "Beta",
    Author = "grammerp54-cloud",
    Theme = "VapeDark",
    Device = "Auto",
    Width = 580,
    Height = 390,
    Corner = 8,
    Settings = {
        Enabled = true
    },
    Stats = {
        FPS = false,
        UpdateRate = 0.5
    },
    Notifications = {
        Enabled = true,
        Duration = 4,
        MaxVisible = 4
    }
})
```

---

## 4. Themes

Current built-in themes:

| Theme name | Style |
|---|---|
| `VapeDark` | Dark purple vape-style dashboard |
| `AcrylicBlue` | Blue glass/dark acrylic style |
| `MidnightPurple` | Deep purple night theme |
| `ObsidianBlack` | Black minimal theme |
| `NeonViolet` | Bright violet neon theme |
| `CrimsonDark` | Red/dark theme |
| `EmeraldNight` | Green/dark theme |
| `LightPorcelain` | Light theme |
| `HighContrast` | Strong contrast theme |

### Use a theme

```lua
local Window = Qwen:CreateWindow({
    Title = "Blue Hub",
    Theme = "AcrylicBlue"
})
```

### Custom accent color

```lua
local Window = Qwen:CreateWindow({
    Title = "Custom Accent",
    Theme = "VapeDark",
    Accent = Color3.fromRGB(255, 120, 40)
})
```

### Lock the theme

To stop users from changing the theme in settings:

```lua
local Window = Qwen:CreateWindow({
    Title = "Locked Theme Hub",
    Theme = "AcrylicBlue",
    ThemeLock = true
})
```

Or force a locked theme directly:

```lua
local Window = Qwen:CreateWindow({
    Title = "Locked Theme Hub",
    LockedTheme = "MidnightPurple"
})
```

Important: in the current raw build, theme locking is done with `ThemeLock = true` or `LockedTheme = "ThemeName"`.

If the theme is locked, the settings tab will show:

```text
Theme changes are locked by the developer.
```

---

## 5. Device Modes

Qwen UI can adapt to device type.

```lua
Device = "Auto"
```

Auto detects:

- mobile
- tablet
- desktop

You can force one:

```lua
Device = "Mobile"
```

or:

```lua
Device = "Tablet"
```

or:

```lua
Device = "Desktop"
```

### Mobile mode

Mobile mode uses:

- full-screen layout
- bottom tab bar
- larger touch rows
- simpler spacing

Example:

```lua
local Window = Qwen:CreateWindow({
    Title = "Mobile Hub",
    Device = "Mobile",
    Theme = "VapeDark"
})
```

### Desktop mode

Desktop mode uses:

- centered window
- left sidebar tabs
- draggable title area
- compact rows

Example:

```lua
local Window = Qwen:CreateWindow({
    Title = "Desktop Hub",
    Device = "Desktop",
    Width = 620,
    Height = 420
})
```

---

## 6. Creating Tabs

Tabs are created from the window:

```lua
local MainTab = Window:CreateTab("Main")
local AimTab = Window:CreateTab("Aim")
local VisualTab = Window:CreateTab("Visuals")
local SettingsTab = Window:CreateTab("Extras")
```

The first tab is selected automatically.

### Tab with icon placeholder

The current build accepts an icon argument, but icons are not rendered in the tab button yet:

```lua
local MainTab = Window:CreateTab("Main", "rbxassetid://0")
```

For now, use the name only if you want clean results:

```lua
local MainTab = Window:CreateTab("Main")
```

---

## 7. Creating Sections

Sections are created inside tabs:

```lua
local MainSection = MainTab:CreateSection("Main Options")
local AimSection = AimTab:CreateSection("Aim Settings")
local VisualSection = VisualTab:CreateSection("Visual Effects")
```

Each section is a rounded panel with a header.

---

## 8. Buttons

Buttons are created inside a section.

### Simple button

```lua
MainSection:CreateButton({
    Name = "Load Profile",
    Function = function()
        -- developer code here
    end
})
```

### Button with description

```lua
MainSection:CreateButton({
    Name = "Reset Settings",
    Description = "Restores default values.",
    Function = function()
        -- developer code here
    end
})
```

### Button that copies text

```lua
MainSection:CreateButton({
    Name = "Copy Key",
    CopyText = "DEMO-KEY-1234",
    Function = function()
        Window:Notify({
            Title = "Copied",
            Message = "Key copied to clipboard.",
            Type = "Success"
        })
    end
})
```

### Button that copies a link

```lua
MainSection:CreateButton({
    Name = "Get Key",
    CopyText = "https://your-key-link_here",
    Function = function()
        Window:Notify({
            Title = "Link copied",
            Message = "Open it in your browser.",
            Type = "Info"
        })
    end
})
```

### Button with notification

```lua
MainSection:CreateButton({
    Name = "Apply",
    Notify = {
        Title = "Applied",
        Message = "Settings saved.",
        Type = "Success"
    }
})
```

### Disabled button

```lua
MainSection:CreateButton({
    Name = "Coming Soon",
    Disabled = true
})
```

### Locked button

```lua
MainSection:CreateButton({
    Name = "Admin Only",
    Locked = true
})
```

### Hidden button

```lua
MainSection:CreateButton({
    Name = "Hidden Debug",
    Hidden = true,
    Function = function()
        -- still exists internally, but not shown
    end
})
```

### Button return object

```lua
local MyButton = MainSection:CreateButton({
    Name = "Start"
})

MyButton:SetText("Started")
MyButton:SetDisabled(true)
```

Button methods:

| Method | Meaning |
|---|---|
| `SetText(text)` | Changes button text |
| `SetDisabled(boolean)` | Disables or enables button |

---

## 9. Toggles

Toggles are on/off switches.

### Simple toggle

```lua
MainSection:CreateToggle({
    Name = "Enable ESP",
    Default = false,
    Function = function(state)
        -- state is true or false
    end
})
```

### Toggle with description

```lua
MainSection:CreateToggle({
    Name = "Silent Mode",
    Description = "Hide activity status.",
    Default = true,
    Function = function(state)
        -- state is true or false
    end
})
```

### Locked toggle

```lua
MainSection:CreateToggle({
    Name = "Premium Feature",
    Default = false,
    Locked = true
})
```

### Hidden toggle

```lua
MainSection:CreateToggle({
    Name = "Internal Flag",
    Default = false,
    Hidden = true
})
```

### Toggle return object

```lua
local EspToggle = MainSection:CreateToggle({
    Name = "Enable ESP",
    Default = false
})

EspToggle:Set(true)
local currentState = EspToggle:Get()
```

Toggle methods:

| Method | Meaning |
|---|---|
| `Set(boolean)` | Sets toggle on/off |
| `Get()` | Returns current state |

---

## 10. Sliders

Sliders are draggable value controls.

### Simple slider

```lua
MainSection:CreateSlider({
    Name = "FOV",
    Min = 0,
    Max = 120,
    Step = 1,
    Default = 60,
    Function = function(value)
        -- value is a number
    end
})
```

### Slider with suffix

```lua
MainSection:CreateSlider({
    Name = "Health",
    Min = 0,
    Max = 100,
    Step = 1,
    Default = 80,
    Suffix = "%",
    Function = function(value)
        -- value is a number
    end
})
```

### Decimal slider

```lua
MainSection:CreateSlider({
    Name = "Smoothness",
    Min = 0.1,
    Max = 5,
    Step = 0.1,
    Decimals = 1,
    Default = 1,
    Function = function(value)
        -- value can be decimal
    end
})
```

### Slider with description

```lua
MainSection:CreateSlider({
    Name = "Distance",
    Description = "Maximum target distance.",
    Min = 10,
    Max = 1000,
    Step = 10,
    Default = 100,
    Suffix = " studs",
    Function = function(value)
        -- value is a number
    end
})
```

### Slider return object

```lua
local FovSlider = MainSection:CreateSlider({
    Name = "FOV",
    Min = 0,
    Max = 120,
    Default = 60
})

FovSlider:Set(90)
local currentFov = FovSlider:Get()
```

Slider methods:

| Method | Meaning |
|---|---|
| `Set(number)` | Sets slider value |
| `Get()` | Returns current value |

---

## 11. Dropdowns

Dropdowns let users choose from a list.

### Simple dropdown

```lua
MainSection:CreateDropdown({
    Name = "Mode",
    Values = { "Normal", "Fast", "Slow" },
    Default = "Normal",
    Function = function(value)
        -- value is the selected option
    end
})
```

### Dropdown with numbers

```lua
MainSection:CreateDropdown({
    Name = "Team Color",
    Values = { 1, 2, 3, 4 },
    Default = 1,
    Function = function(value)
        -- value can be number
    end
})
```

### Dropdown return object

```lua
local ModeDropdown = MainSection:CreateDropdown({
    Name = "Mode",
    Values = { "A", "B", "C" },
    Default = "A"
})

ModeDropdown:Set("B")
local currentMode = ModeDropdown:Get()
```

Dropdown methods:

| Method | Meaning |
|---|---|
| `Set(value)` | Sets selected value |
| `Get()` | Returns selected value |

---

## 12. Inputs

Inputs are text boxes.

### Text input

```lua
MainSection:CreateInput({
    Name = "Username",
    Placeholder = "Enter name...",
    Default = "",
    Function = function(value)
        -- value is text
    end
})
```

### Number input

```lua
MainSection:CreateInput({
    Name = "Amount",
    Type = "Number",
    Placeholder = "0",
    Default = 1,
    Min = 1,
    Max = 100,
    Function = function(value)
        -- value is number
    end
})
```

### Max length input

```lua
MainSection:CreateInput({
    Name = "Code",
    Placeholder = "Enter code",
    MaxLength = 12,
    Function = function(value)
        -- value is text
    end
})
```

### Input return object

```lua
local NameInput = MainSection:CreateInput({
    Name = "Profile",
    Default = "Player"
})

NameInput:Set("NewName")
local currentName = NameInput:Get()
```

Input methods:

| Method | Meaning |
|---|---|
| `Set(text)` | Sets input text |
| `Get()` | Returns input text |

---

## 13. Keybinds

Keybinds capture a keyboard key or mouse input.

### Simple keybind

```lua
MainSection:CreateKeybind({
    Name = "Menu Key",
    Default = Enum.KeyCode.RightControl,
    Function = function(binding)
        -- binding is the captured key/input
    end
})
```

### Keybind that closes window

```lua
MainSection:CreateKeybind({
    Name = "Close Key",
    Default = Enum.KeyCode.End,
    Function = function(binding)
        if binding == Enum.KeyCode.End then
            Window:Close()
        end
    end
})
```

### Keybind return object

```lua
local MenuKey = MainSection:CreateKeybind({
    Name = "Menu Key",
    Default = Enum.KeyCode.RightControl
})

MenuKey:Set(Enum.KeyCode.K)
local currentBinding = MenuKey:Get()
```

Keybind methods:

| Method | Meaning |
|---|---|
| `Set(binding)` | Sets keybind |
| `Get()` | Returns current binding |

---

## 14. Labels

Labels are text blocks.

### Muted label

```lua
MainSection:CreateLabel({
    Text = "This is a note.",
    Variant = "Muted"
})
```

### Accent label

```lua
MainSection:CreateLabel({
    Text = "Important",
    Variant = "Accent"
})
```

### Success label

```lua
MainSection:CreateLabel({
    Text = "All systems normal.",
    Variant = "Success"
})
```

Available variants:

| Variant | Color |
|---|---|
| `Text` | Main text color |
| `Muted` | Dim text |
| `Accent` | Theme accent |
| `Success` | Green |
| `Warning` | Yellow/orange |
| `Danger` | Red |
| `Info` | Blue |

---

## 15. Dividers

Creates a thin line.

```lua
MainSection:CreateDivider()
```

Useful between groups.

Example:

```lua
MainSection:CreateToggle({
    Name = "Feature A"
})

MainSection:CreateDivider()

MainSection:CreateToggle({
    Name = "Feature B"
})
```

---

## 16. Images

Images support:

- Roblox asset ID as number
- Roblox asset ID as string
- `rbxassetid://` string
- HTTP/HTTPS URL
- `nil` for blank

### Asset ID image

```lua
MainSection:CreateImage({
    Source = 123456789,
    Width = 140,
    Height = 90
})
```

### Asset string image

```lua
MainSection:CreateImage({
    Source = "rbxassetid://123456789",
    Width = 140,
    Height = 90
})
```

### URL image

```lua
MainSection:CreateImage({
    Source = "https://example.com/image.png",
    Width = 160,
    Height = 100
})
```

### Scale types

```lua
MainSection:CreateImage({
    Source = 123456789,
    Width = 160,
    Height = 100,
    ScaleType = Enum.ScaleType.Fit
})
```

Common scale types:

| ScaleType | Meaning |
|---|---|
| `Enum.ScaleType.Fit` | Keeps ratio, fits inside |
| `Enum.ScaleType.Stretch` | Stretches to size |
| `Enum.ScaleType.Tile` | Repeats image |
| `Enum.ScaleType.Crop` | Cuts overflow |

---

## 17. Progress Bars

Progress bars show 0 to 1 value.

### Simple progress

```lua
local LoadBar = MainSection:CreateProgress({
    Name = "Loading",
    Value = 0.25
})
```

### Update progress with button

```lua
local LoadBar = MainSection:CreateProgress({
    Name = "Loading",
    Value = 0
})

MainSection:CreateButton({
    Name = "+10%",
    Function = function()
        LoadBar:Set(math.min(LoadBar:Get() + 0.1, 1))
    end
})

MainSection:CreateButton({
    Name = "Reset",
    Function = function()
        LoadBar:Set(0)
    end
})
```

Progress methods:

| Method | Meaning |
|---|---|
| `Set(number)` | Sets progress from 0 to 1 |
| `Get()` | Returns current progress |

---

## 18. Notifications

Notifications are created from the window:

```lua
Window:Notify({
    Title = "Saved",
    Message = "Your settings were saved.",
    Type = "Success"
})
```

### Notification types

| Type | Use |
|---|---|
| `Info` | Normal message |
| `Success` | Worked |
| `Warning` | Careful |
| `Danger` | Failed/error |

### Custom duration

```lua
Window:Notify({
    Title = "Wait",
    Message = "This stays longer.",
    Type = "Warning",
    Duration = 8
})
```

### Notification history

The bell button opens a history panel.

You can also read history in code:

```lua
local history = Window:GetHistory()
```

`history` is a table of notification records.

---

## 19. Key System

The key system shows a lock screen before the main UI.

### Static key

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
        HelpText = "https://your-help-link_here"
    }
})
```

### Callback key validation

Better for developers who want custom logic:

```lua
local ValidKeys = {
    ["KEY-ABC"] = true,
    ["KEY-XYZ"] = true
}

local Window = Qwen:CreateWindow({
    Title = "Protected Hub",
    KeySystem = {
        Enabled = true,
        Title = "Access Required",
        Description = "Enter your access key.",
        Placeholder = "KEY",
        Attempts = 3,
        Cooldown = 20,
        Function = function(input)
            return ValidKeys[input] == true
        end
    }
})
```

### Unlimited attempts

```lua
Attempts = 0
```

Example:

```lua
KeySystem = {
    Enabled = true,
    Key = "FREE-KEY",
    Attempts = 0,
    Cooldown = 10
}
```

### Key system behavior

If key system is enabled:

- main window is hidden
- lock screen appears
- user enters key
- correct key opens UI
- wrong key reduces attempts
- when attempts reach zero, cooldown starts
- after cooldown, attempts reset

### Relock button

If settings are enabled and key system is enabled, the settings tab gets:

```text
Relock UI
```

This closes the UI and shows the lock screen again.

---

## 20. UNC Verification Panel

UNC is a neutral verification badge. It can check a value or run a developer callback.

### Simple UNC

```lua
local Window = Qwen:CreateWindow({
    Title = "Verified Hub",
    UNC = {
        Enabled = true,
        Auto = true,
        Value = LocalPlayer.Name
    }
})
```

### UNC callback

```lua
local AllowedNames = {
    PlayerNameHere = true
}

local Window = Qwen:CreateWindow({
    Title = "Verified Hub",
    UNC = {
        Enabled = true,
        Auto = true,
        Value = LocalPlayer.Name,
        Function = function(value)
            return AllowedNames[value] == true
        end
    }
})
```

### UNC badge states

The badge shows:

| State | Meaning |
|---|---|
| `UNC OK` | Passed |
| `UNC FAIL` | Failed |
| `UNC --` | Not checked yet |

If settings are enabled, the settings tab gets:

```text
Retry UNC Check
```

---

## 21. Settings Tab

The settings tab is automatic.

Enable it:

```lua
Settings = {
    Enabled = true
}
```

Disable it:

```lua
Settings = {
    Enabled = false
}
```

Example:

```lua
local Window = Qwen:CreateWindow({
    Title = "Clean Hub",
    Settings = {
        Enabled = false
    }
})
```

### What settings tab contains

When enabled, it can show:

- theme info
- theme dropdown, if not locked
- density dropdown
- animation dropdown
- FPS toggle
- notifications toggle
- UNC retry button, if UNC enabled
- relock button, if key system enabled

Important:

- FPS toggle works live.
- Notifications toggle works live.
- Theme dropdown currently tells user to reload; it does not fully rebuild live in this raw build.
- Density and animation are stored preferences for now.

---

## 22. Locking And Hiding Controls

### Hide a widget

Use:

```lua
Hidden = true
```

Example:

```lua
MainSection:CreateButton({
    Name = "Debug",
    Hidden = true
})
```

The widget is not shown.

### Lock a button or toggle

Use:

```lua
Locked = true
```

Example:

```lua
MainSection:CreateToggle({
    Name = "Premium",
    Locked = true
})
```

Current locked support:

| Widget | Locked supported? |
|---|---|
| Button | Yes |
| Toggle | Yes |
| Slider | Not yet |
| Dropdown | Not yet |
| Input | Not yet |
| Keybind | Not yet |

For now, use `Hidden = true` for widgets you do not want users to see.

---

## 23. Common Developer Pattern

Usually you want to store values in a table.

```lua
local state = {
    enabled = false,
    fov = 60,
    mode = "Normal"
}
```

Then connect widgets to it:

```lua
MainSection:CreateToggle({
    Name = "Enable",
    Default = false,
    Function = function(value)
        state.enabled = value
    end
})

MainSection:CreateSlider({
    Name = "FOV",
    Min = 0,
    Max = 120,
    Default = 60,
    Function = function(value)
        state.fov = value
    end
})

MainSection:CreateDropdown({
    Name = "Mode",
    Values = { "Normal", "Fast", "Slow" },
    Default = "Normal",
    Function = function(value)
        state.mode = value
    end
})

MainSection:CreateButton({
    Name = "Apply",
    Function = function()
        Window:Notify({
            Title = "Applied",
            Message = state.mode .. " | FOV: " .. tostring(state.fov),
            Type = "Success"
        })
    end
})
```

This is the cleanest way to build.

---

## 24. Full Example Hub

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local state = {
    mainEnabled = false,
    fov = 60,
    smoothness = 1,
    mode = "Normal"
}

local Window = Qwen:CreateWindow({
    Title = "Qwen Example Hub",
    Subtitle = "Docs Demo",
    Theme = "VapeDark",
    Device = "Auto",
    Width = 600,
    Height = 400,
    Settings = {
        Enabled = true
    },
    Stats = {
        FPS = true
    },
    Notifications = {
        Enabled = true,
        Duration = 4,
        MaxVisible = 4
    }
})

local MainTab = Window:CreateTab("Main")
local AimTab = Window:CreateTab("Aim")
local VisualTab = Window:CreateTab("Visuals")

local MainSection = MainTab:CreateSection("Main Controls")
local AimSection = AimTab:CreateSection("Aim Controls")
local VisualSection = VisualTab:CreateSection("Visual Controls")

MainSection:CreateToggle({
    Name = "Enable Hub",
    Description = "Turns the main feature on.",
    Default = false,
    Function = function(value)
        state.mainEnabled = value
    end
})

MainSection:CreateButton({
    Name = "Reset",
    Description = "Resets stored values.",
    Function = function()
        state.fov = 60
        state.smoothness = 1
        state.mode = "Normal"
        Window:Notify({
            Title = "Reset",
            Message = "Values restored.",
            Type = "Info"
        })
    end
})

AimSection:CreateSlider({
    Name = "FOV",
    Min = 0,
    Max = 120,
    Step = 1,
    Default = 60,
    Suffix = " degrees",
    Function = function(value)
        state.fov = value
    end
})

AimSection:CreateSlider({
    Name = "Smoothness",
    Min = 0.1,
    Max = 5,
    Step = 0.1,
    Decimals = 1,
    Default = 1,
    Function = function(value)
        state.smoothness = value
    end
})

AimSection:CreateDropdown({
    Name = "Aim Mode",
    Values = { "Normal", "Fast", "Slow" },
    Default = "Normal",
    Function = function(value)
        state.mode = value
    end
})

VisualSection:CreateToggle({
    Name = "Show Box",
    Default = true
})

VisualSection:CreateToggle({
    Name = "Show Name",
    Default = true
})

VisualSection:CreateToggle({
    Name = "Show Health",
    Default = false
})

VisualSection:CreateDivider()

VisualSection:CreateLabel({
    Text = "Visual options are stored locally in this example.",
    Variant = "Muted"
})
```

---

## 25. Locked Theme + Key System Example

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
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
        HelpText = "https://your-help-link_here"
    },
    Settings = {
        Enabled = true
    }
})

local MainTab = Window:CreateTab("Main")
local MainSection = MainTab:CreateSection("Controls")

MainSection:CreateToggle({
    Name = "Feature",
    Default = false
})

MainSection:CreateButton({
    Name = "Test Notification",
    Function = function()
        Window:Notify({
            Title = "Working",
            Message = "Key system and locked theme are active.",
            Type = "Success"
        })
    end
})
```

---

## 26. Mobile Compact Example

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "Mobile Hub",
    Theme = "MidnightPurple",
    Device = "Mobile",
    Settings = {
        Enabled = true
    },
    Stats = {
        FPS = true
    }
})

local MainTab = Window:CreateTab("Main")
local MainSection = MainTab:CreateSection("Touch Controls")

MainSection:CreateToggle({
    Name = "Large Toggle",
    Default = false
})

MainSection:CreateSlider({
    Name = "Large Slider",
    Min = 0,
    Max = 100,
    Default = 50
})

MainSection:CreateButton({
    Name = "Big Button",
    Function = function()
        Window:Notify({
            Title = "Mobile",
            Message = "Touch layout active.",
            Type = "Info"
        })
    end
})
```

---

## 27. Notification Tester Example

```lua
local RawGistURL = "https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "Notification Tester",
    Theme = "NeonViolet"
})

local Tab = Window:CreateTab("Alerts")
local Section = Tab:CreateSection("Types")

Section:CreateButton({
    Name = "Info",
    Function = function()
        Window:Notify({
            Title = "Info",
            Message = "Normal message.",
            Type = "Info"
        })
    end
})

Section:CreateButton({
    Name = "Success",
    Function = function()
        Window:Notify({
            Title = "Success",
            Message = "Action completed.",
            Type = "Success"
        })
    end
})

Section:CreateButton({
    Name = "Warning",
    Function = function()
        Window:Notify({
            Title = "Warning",
            Message = "Check your settings.",
            Type = "Warning"
        })
    end
})

Section:CreateButton({
    Name = "Danger",
    Function = function()
        Window:Notify({
            Title = "Danger",
            Message = "Something failed.",
            Type = "Danger"
        })
    end
})
```

---

## 28. Progress + Image + Input Example

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "Media Example",
    Theme = "EmeraldNight"
})

local Tab = Window:CreateTab("Media")
local Section = Tab:CreateSection("Widgets")

Section:CreateImage({
    Source = 123456789,
    Width = 160,
    Height = 100
})

local Bar = Section:CreateProgress({
    Name = "Download",
    Value = 0
})

Section:CreateButton({
    Name = "Add 20%",
    Function = function()
        Bar:Set(math.min(Bar:Get() + 0.2, 1))
    end
})

Section:CreateButton({
    Name = "Reset",
    Function = function()
        Bar:Set(0)
    end
})

Section:CreateInput({
    Name = "File Name",
    Placeholder = "model.txt",
    Default = "model.txt",
    MaxLength = 32
})
```

---

## 29. API Cheat Sheet

### Window

```lua
Qwen:CreateWindow(config)
```

Returns:

```lua
Window:CreateTab(name)
Window:Notify(options)
Window:GetHistory()
Window:Open()
Window:Close()
Window:Destroy()
```

### Tab

```lua
Window:CreateTab(name)
```

Returns:

```lua
Tab:CreateSection(name)
```

### Section

```lua
Tab:CreateSection(name)
```

Returns:

```lua
Section:CreateButton(config)
Section:CreateToggle(config)
Section:CreateSlider(config)
Section:CreateDropdown(config)
Section:CreateInput(config)
Section:CreateKeybind(config)
Section:CreateLabel(config)
Section:CreateDivider()
Section:CreateImage(config)
Section:CreateProgress(config)
```

---

## 30. Widget Quick Reference

### Button

```lua
Section:CreateButton({
    Name = "Button",
    Description = "Optional",
    Function = function() end,
    CopyText = "optional copy",
    Notify = { Title = "Done", Type = "Success" },
    Disabled = false,
    Locked = false,
    Hidden = false
})
```

### Toggle

```lua
Section:CreateToggle({
    Name = "Toggle",
    Description = "Optional",
    Default = false,
    Function = function(value) end,
    Locked = false,
    Hidden = false
})
```

### Slider

```lua
Section:CreateSlider({
    Name = "Slider",
    Description = "Optional",
    Min = 0,
    Max = 100,
    Step = 1,
    Decimals = 0,
    Default = 50,
    Suffix = "",
    Function = function(value) end,
    Hidden = false
})
```

### Dropdown

```lua
Section:CreateDropdown({
    Name = "Dropdown",
    Values = { "A", "B" },
    Default = "A",
    Function = function(value) end,
    Hidden = false
})
```

### Input

```lua
Section:CreateInput({
    Name = "Input",
    Placeholder = "Text",
    Default = "",
    Type = "Text",
    Min = nil,
    Max = nil,
    MaxLength = nil,
    Function = function(value) end,
    Hidden = false
})
```

### Keybind

```lua
Section:CreateKeybind({
    Name = "Keybind",
    Default = Enum.KeyCode.RightControl,
    Function = function(binding) end,
    Hidden = false
})
```

### Label

```lua
Section:CreateLabel({
    Text = "Note",
    Variant = "Muted",
    Font = Enum.Font.Gotham,
    Size = 12
})
```

### Image

```lua
Section:CreateImage({
    Source = 123456789,
    Width = 140,
    Height = 90,
    ScaleType = Enum.ScaleType.Fit
})
```

### Progress

```lua
Section:CreateProgress({
    Name = "Progress",
    Value = 0.5,
    Function = function(value) end
})
```

---

## 31. Troubleshooting

### UI does not load

Check the raw URL. It must be the direct raw link.

Use:

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
local Qwen = loadstring(game:HttpGet(RawGistURL))()
```

If `Qwen` is nil, the URL is wrong or HTTP is blocked.

---

### Window does not appear

Possible reasons:

- key system is enabled and waiting for key
- window was closed
- executor cannot parent to `CoreGui`
- script errored before `CreateWindow`

The library tries `CoreGui` first, then falls back to `PlayerGui`.

---

### Theme does not change in settings

If theme is locked, it will not change.

If theme is not locked, the current build may require reload because live theme rebuilding is not fully connected yet.

---

### Settings tab missing

Check:

```lua
Settings = {
    Enabled = true
}
```

---

### FPS counter missing

Check:

```lua
Stats = {
    FPS = true
}
```

Or enable it in settings tab.

---

### Notifications not appearing

Check:

```lua
Notifications = {
    Enabled = true
}
```

Also check:

```lua
Duration = 4
```

If duration is `0`, toast stays until manually closed.

---

### Button does nothing

Use `Function`:

```lua
Function = function()
    -- code
end
```

`Callback` also works as an alias:

```lua
Callback = function()
    -- code
end
```

---

### Toggle does not update variable

Make sure you use the value passed into the function:

```lua
Function = function(state)
    myValue = state
end
```

---

### Slider value is weird

Set step correctly.

For whole numbers:

```lua
Step = 1
```

For decimals:

```lua
Step = 0.1,
Decimals = 1
```

---

### Dropdown popup appears in wrong place

Current dropdown popup opens below the row using absolute position. If the row is near the bottom of the screen, the popup may go off-screen. Use shorter lists or place dropdowns higher in the section.

---

## 32. Recommended Build Style

Use this pattern:

```lua
local state = {}

local Window = Qwen:CreateWindow({
    Title = "Hub"
})

local Tab = Window:CreateTab("Main")
local Section = Tab:CreateSection("Options")

Section:CreateToggle({
    Name = "Feature",
    Default = false,
    Function = function(value)
        state.feature = value
    end
})

Section:CreateSlider({
    Name = "Value",
    Min = 0,
    Max = 100,
    Default = 50,
    Function = function(value)
        state.value = value
    end
})

Section:CreateButton({
    Name = "Apply",
    Function = function()
        Window:Notify({
            Title = "Applied",
            Type = "Success"
        })
    end
})
```

This keeps your script clean and easy to expand.

---

## 33. Important Notes For This Raw Build

This documentation matches the current raw file behavior.

### Currently active

- window creation
- tabs
- sections
- buttons
- toggles
- sliders
- dropdowns
- inputs
- keybinds
- labels
- dividers
- images
- progress bars
- notifications
- notification history panel
- FPS chip
- key system lock screen
- UNC badge
- settings tab
- theme locking
- device auto detection
- mobile bottom layout
- desktop draggable window
- hidden widgets
- locked buttons/toggles

### Currently stored but limited

- `Density`
- `Animation`
- `Author`
- `Version`
- `Watermark`
- `Permissions`

These are accepted in config, but not all of them fully affect the UI in this raw build yet.

---

## 34. Final Simple Template

Copy this and edit it:

```lua
local RawGistURL = "[https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/9cd94b576db1d72f48c9a07ff39c4fdeb9e5a408/QwenUi.lua](https://gist.githubusercontent.com/grammerp54-cloud/3757d93037913683434e8ae25fde0dcf/raw/6b3fbb8b8ea28a064427871009380bf992a03e13/QwenUi.lua)"
local Qwen = loadstring(game:HttpGet(RawGistURL))()

local Window = Qwen:CreateWindow({
    Title = "My Hub",
    Theme = "VapeDark",
    Device = "Auto",
    Settings = {
        Enabled = true
    },
    Stats = {
        FPS = true
    }
})

local MainTab = Window:CreateTab("Main")
local MainSection = MainTab:CreateSection("Main")

MainSection:CreateToggle({
    Name = "Enable",
    Default = false,
    Function = function(value)
    end
})

MainSection:CreateSlider({
    Name = "Amount",
    Min = 0,
    Max = 100,
    Default = 50,
    Function = function(value)
    end
})

MainSection:CreateButton({
    Name = "Action",
    Function = function()
        Window:Notify({
            Title = "Action",
            Message = "Done.",
            Type = "Success"
        })
    end
})
