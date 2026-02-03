# Unnamed Addon Docs Helper

This document is a comprehensive guide to the Unnamed Cheat API, compiled from official documentation and real-world script examples.

## Table of Contents
1. [API Reference](#api-reference)
   - [Utility](#utility)
   - [Ragebot](#ragebot)
   - [Desync](#desync)
   - [Cache](#cache)
   - [Player](#player)
   - [Local](#local)
   - [Misc](#misc)
2. [UI Creation](#ui-creation)
3. [Real-World Examples](#real-world-examples)

---

## API Reference

### Utility
Methods for general script interaction and UI feedback.

#### `api:notify`
Sends a notification to the user.
```lua
api:notify(message: string, lifetime: number?)
```
**Example:** `api:notify("Hello World", 5)`

#### `api:add_connection`
Registers an RBXScriptConnection to be cleaned up when the script unloads.
```lua
api:add_connection(connection: RBXScriptConnection): RBXScriptConnection
```
**Example:** `api:add_connection(workspace.ChildAdded:Connect(print))`

#### `api:set_lua_name` / `api:get_lua_name`
Sets or gets the script name used for config storage.
```lua
api:set_lua_name(name: string): nil
api:get_lua_name(): string
```

#### `api:override_key_state`
Forces a keybind to be active/inactive.
```lua
api:override_key_state(key: string | table, override: boolean): nil
```
**Example:** `api:override_key_state("silent_keybind", true)`

#### `api:get_flag`
Retrieves the value of a specific UI flag directly.
```lua
api:get_flag(flag: string): any
```
**Example:** `local range = api:get_flag("range_slider")`

### Ragebot
Methods for interacting with the ragebot.

#### `api:is_ragebot`
Checks if the ragebot is currently active.
```lua
function api:is_ragebot(): boolean
```

#### `api:set_ragebot`
Forces the ragebot to be enabled or disables the override.
```lua
function api:set_ragebot(enabled: boolean)
```

#### `api:get_ragebot_status`
Returns the current action and target data.
```lua
function api:get_ragebot_status(): (string, any?)
```
- Returns: Status string (e.g., "killing", "buying") and associated data.

#### `api:add_ragebot_target`
Manually adds a player to the ragebot's target list.
```lua
api:add_ragebot_target(player: Player)
```

#### `api:set_target` / `api:get_target`
Sets or gets the current primary target.
```lua
api:set_target(target: Player?)
function api:get_target(type: string?): Player?
```
- `type` (for get): "silent", "ragebot", or nil (current).

#### `api:ragebot_strafe_override`
Overrides the ragebot's strafe position logic.
```lua
api:ragebot_strafe_override(callback: function): nil
```
**Example:**
```lua
api:ragebot_strafe_override(function(pos, unsafe, part)
    return not unsafe and CFrame.new(pos + Vector3.yAxis * 100)
end)
```

### Desync
Methods for controlling desync and fake lag.

#### `api:set_fake`
Sets the fake position manually.
```lua
api:set_fake(override: boolean, cframe: CFrame?, refresh: boolean?)
```

#### `api:can_desync`
Checks if desync is possible (not busy).
```lua
api:can_desync(): boolean
```

#### `api:get_client_cframe` / `api:get_desync_cframe`
Returns client-side (visual) or server-side (actual) CFrame.
```lua
api:get_client_cframe(): CFrame
api:get_desync_cframe(): CFrame
```

#### `api:set_desync_cframe`
Sets the server-side position for a single frame.
```lua
api:set_desync_cframe(cframe: CFrame)
```

### Cache
Methods for accessing optimized game data.

#### `api:get_tool_cache`
Returns info about the held tool (ammo, gun type, etc.).
```lua
api:get_tool_cache(): table
```

#### `api:get_data_cache`
Returns player data (Currency, Wanted, Crew, etc.).
```lua
api:get_data_cache(player: Player): table
```

#### `api:get_status_cache`
Returns player status (K.O, Dead, Armor, Grabbed).
```lua
api:get_status_cache(player: Player): table
```

#### `api:get_target_cache`
Returns specific target info (`part`, `player`).
```lua
api:get_target_cache(type: string): table
```
- Type: "ragebot", "aimbot", "silent".

#### `api:get_character_cache`
Optimized access to character parts.
```lua
api:get_character_cache(player: Player): table?
```

### Player
Methods for player checks.

#### `api:is_crew`
Checks if two players are in the same crew/team.
```lua
api:is_crew(player: Player, target: Player): boolean
```

### Local
Methods for local player actions.

#### `api:get_current_vehicle`
Returns the vehicle the player is driving.
```lua
api:get_current_vehicle(): Instance?
```

#### `api:buy_vehicle`
Buys/Finds a vehicle.
```lua
api:buy_vehicle(cframe: CFrame?)
```

#### `api:force_shoot`
Forces a tool to shoot at a specific location.
```lua
api:force_shoot(handle: Instance, target: Instance, origin: Vector3, pos: Vector3, visual: bool)
```

#### `api:buy_item`
Buys an item or ammo.
```lua
api:buy_item(name: string, ammo: boolean?, equipped: boolean?)
```

#### `api:teleport`
Teleports player or vehicle.
```lua
api:teleport(cframe: CFrame)
```

#### `api:chat`
Sends a chat message.
```lua
api:chat(message: string)
```

### Misc

#### `api:on_command`
Registers a chat command listener.
```lua
api:on_command(cmd: string, callback: function)
```

#### `api:redeem_codes`
Redeems all codes.
```lua
api:redeem_codes()
```

---

## UI Creation

#### Creating a Tab and Groupbox
```lua
local tabs = { lua = api:AddTab("My Tab") }
local group = tabs.lua:AddLeftGroupbox("My Group")
```

#### Elements
```lua
group:AddToggle("my_toggle", { Text = "Toggle Me", Default = false })
group:AddButton("Click Me", function() print("Clicked") end)
```

---

## Real-World Examples
Patterns extracted from local scripts.

### 1. Crew Target Addon
Demonstrates target access and finding crew members.
```lua
api:set_lua_name("Crew Target Addon")
local Tab = api:GetTab("ragebot") or api:AddTab("ragebot")
local Main = Tab:AddRightGroupbox("Crew Targeting")

Main:AddButton("Check Crew", function()
    local target = api:get_target("silent")
    if target then 
        local data = target:FindFirstChild("DataFolder")
        -- Logic to check data.Information.Crew.Value
    end
end)
```

### 2. Tool Grip Addon
Demonstrates unload events and UI interaction.
```lua
api:on_event("unload", function()
    -- cleanup code
end)

api:get_ui_object("ExistingToggle"):SetValue(true)
```

### 3. Shape Addon
Demonstrates using global API fallback.
```lua
if not api and getgenv().api then getgenv().api = api end
```

### 4. Advanced Strafe Override (`hvh_helper_ultimate.lua`)
Complex usage of `ragebot_strafe_override` with physics prediction.
```lua
api:ragebot_strafe_override(function(current_pos, unsafe, target_part)
    if unsafe then return end
    
    local origin = target_part.Position
    local dt = game:GetService("RunService").Heartbeat:Wait()
    
    -- Linear Prediction: P = P0 + V*t
    local velocity = target_part.AssemblyLinearVelocity
    local predicted = origin + (velocity * 0.135) -- 135ms prediction
    
    -- Orbit Logic: Calculate offset based on angle
    local angle = os.clock() * 5
    local radius = 15
    local offset = Vector3.new(math.cos(angle)*radius, 0, math.sin(angle)*radius)
    
    local final_pos = predicted + offset
    
    -- Return CFrame looking at target, and variable second arg as focus point
    return CFrame.lookAt(final_pos, origin), origin
end)
```

### 6. Dynamic Menu (`dynamic_menu_example.lua`)
Shows how to hide/show elements based on toggles.
```lua
local Main = Group:AddToggle("Master", { Text = "Master" })
local Sub = Group:AddToggle("Sub", { Text = "Sub Feature" })

Main:OnChanged(function()
    Sub:SetVisible(Main.Value)
end)
```

### 5. Autonomous AI (`aegis_ai.lua`)
Demonstrates flag manipulation and state management.
```lua
-- Safe flag setter helper
local function setFlag(flagName, value)
    -- Try Toggles first
    if Toggles[flagName] then 
        Toggles[flagName]:SetValue(value)
        return true 
    end
    -- Try raw API object as fallback
    pcall(function()
        local uiObj = api:get_ui_object(flagName)
        if uiObj then uiObj:SetValue(value) end
    end)
end

-- Force enabling Ragebot features programmatically
setFlag("ragebot_enabled", true)
setFlag("SilentEnabled", true)
api:override_key_state("ragebot_keybind", true)
api:set_ragebot(true)
```
