# Kyber-Plugins-Docs-and-Examples
>[!NOTE]
>This guide is not the official guide provided by the Kyber Team, you can find their guide [here](https://docs.kyber.gg/g/pluginref). Also this guide is for `ver/beta10`, go [here](https://github.com/Thanosss00/Kyber-Plugins-Docs-And-Examples/tree/stable) if you are using `stable`.

## How to Make a Kyber Plugin
First off below is the file structure

```text
ExamplePlugin/
│
├── plugin.json
│   
└── server/
    ├── __init__.lua
```

Another example with more files

```text
ExamplePlugin/
│
├── plugin.json
│
├── common/
│   ├── sdk.lua (this is for library extensions, if your plugin doesn't need any then you don't need it)
│
└── server/
    ├── __init__.lua
    ├── admins.lua (this is for adding admins, if your plugin doesn't need any then you don't need it)
```

# plugin.json
This file contains the plugins name and the authors name.

Example:
```json
{
    "name": "TestPlugin",
    "author": "YOURNAME"
}
```

# sdk.lua
This file isn't necessary, you only need it if you plan to use any library extension (extra functions that Lua doesn't have by default).
You can load it in a file with:
```lua 
require "common/sdk"
```
If it's in the same folder then:
```lua
require "sdk"
```

Example:
table.contains
```lua
table.contains = function(tbl, element)
    for _, value in pairs(tbl) do
        if value == element then
            return true
        end
    end
    return false
end
```

string.split
```lua
string.split = function(str, sep)
    sep = sep or "%s"
    local t = {}
    for s in string.gmatch(str, "([^" .. sep .. "]+)") do
        table.insert(t, s)
    end
    return t
end
```

TimerService
```lua
TimerService = {
    timers = {}
}

function TimerService:update(delta)
    for i = #self.timers, 1, -1 do
        local t = self.timers[i]
        t.elapsed = t.elapsed + delta

        if t.elapsed >= t.delay then
            t.callback()
            table.remove(self.timers, i)
        end
    end
end

EventManager.Listen("Server:UpdatePre", function(delta)
    TimerService:update(delta)
end)

function SetTimeout(callback, delay)
    table.insert(TimerService.timers, {
        delay = delay,
        elapsed = 0,
        callback = callback
    })
end
```

# admins.lua
If you want admins and admin commands in your plugin, this is where you define them.
You can load it in a file with:
```lua 
require "admins"
```

>[!NOTE]
>This also requires `string.split` from `sdk.lua`.

Example:
```lua
require "common/sdk"

Admins = {
    1009965413328 -- "The_Negotiator1"
}

EventManager.Listen("ServerPlayer:SendMessage", function(player, message)
    if not table.contains(Admins, player.playerId) then return end

    if message:len() < 2 then return end
    local messageSplit = string.split(message)

    if #messageSplit <= 0 then return end
    if messageSplit[1]:len() < 3 then return end
    if messageSplit[1]:sub(1, 1) ~= '/' then return end

    local command = messageSplit[1]:lower():sub(2)

    EventManager.SetCancelled(true)

    if command == "exec" or command == "cmd" then
        if #messageSplit <= 1 then
            return
        end

        local executeCmd = table.concat(messageSplit, " ", 2)
        print("Admin " .. player.name .. " executed command: " .. executeCmd)
        Console.Execute(executeCmd)
    end
end)
```

# \_\_init\_\_.lua
This is the main file of your plugin, most of the things will be done here!

Example:
```lua
EventManager.Listen("ServerPlayer:Joined", function(player)
    print(player.name .. " joined the server")
end)
```

# How to use a Plugin on a server
>[!NOTE]
>Plugins must be packaged as .kbplugin files (zip with a .kbplugin extension) when you are ready to use them on a server.
>Right now plugins can only be used on **Dedicated Servers**, you can read my [guide](https://github.com/Thanosss00/Kyber-Dedicated-Server-Guide), or the [official guide](https://docs.kyber.gg/g/hosting/dedicated-servers/prereq) provided by the Kyber Team.

# Console
## How to register a command
```lua
Console.Register(groupName, name, description, instanceTable, function(self, args)
    -- logic
end)
```
Example:
```lua
Console.Register("MyGroup", "hello", "Prints hello", instanceTable, function(self, args)
    print("Hello")
end)
```

## How to run a command
```lua
Console.Execute("string")
```
Example:
```lua
Console.Execute("hello")
```

## Access settings object
```lua
local settings = Console.GetSettings("GroupName")
```

## Console commands 
>[!NOTE]
>Some of these commands work only on the main menu (e.g. `Kyber.DarthVader`, `Kyber.Luke` etc). So I will split them into General Console Commands(these are from the base game), In Game and Main Menu commands.

## General Console Commands
- `AutoPlayers.HeroSpawnProbability_Gameplay Float32`
- `AutoPlayers.HeroVehicleSpawnProbability_Gameplay Float32`
- `AutoPlayers.HeroSpawnProbability Float32`
- `AutoPlayers.HeroVehicleSpawnProbability Float32`
- `Autoplayers.HeroStrafeProbabilityPerFrame Float32`

- `GameTime.MaxVariableFps Float32`

- `NetworkPerfOverlay.Enable Boolean`

- `PerfOverlay.DrawFps Boolean`
- `PerfOverlay.DrawGraph Boolean`
- `PerfOverlay.DrawCpuGraph Boolean`
- `PerfOverlay.DrawGpuGraph Boolean`
- `PerfOverlay.DrawFrameGraph Boolean`

- `UIDrawEnable Boolean`

## Kyber Console Commands
### In Game Commands
- `Kyber.addplayer`
- `Kyber.AddLevel <level> <mode>`

- `Kyber.BundleDebugFontSize Float32`

- `Kyber.countdown`
- `Kyber.CrashGame`
- `Kyber.demooverlay`
- `Kyber.DisableTeamBalancing Boolean`

- `Kyber.endgameteam1`
- `Kyber.endgameteam2`
- `Kyber.endofround`
- `Kyber.eventwelcometimer`
- `Kyber.EnableLighting1`
- `Kyber.EnableLighting2`
- `Kyber.eortest`
- `Kyber.eor`
- `Kyber.ExtentDebug`
- `Kyber.EnableShuffleTeams Boolean`
- `Kyber.EnableUnlimitedPowerEvent Boolean`

- `Kyber.forcestartevent`
- `Kyber.FullTeamSwap`

- `Kyber.grid`
- `Kyber.GiveBattlepoints <player> <amount>`
- `Kyber.GrantUnlock <player> <guid>`

- `Kyber.HotReloadLua`

- `Kyber.IgnoreControllerError`

- `Kyber.JoinServer <ip> <port>`

- `Kyber.LoadLevel <level> <mode>`
- `Kyber.LoadSPLevel <level> <startPoint> <initialSubLevel>`
- `Kyber.LoadSPLevel`
- `Kyber.LogPlayerStats`
- `Kyber.LogLastEbxLoad`

- `Kyber.mvpdebug`
- `Kyber.MaterialID.On`
- `Kyber.MaterialID.Off`

- `Kyber.outline_off`
- `Kyber.outline_on`
- `Kyber.ObjectiveDamageScale`

- `Kyber.PlayerJoinedGroup`
- `Kyber.PlayerLeftGroup`
- `Kyber.PlayerCancelled`
- `Kyber.PlayerManagerAddr`
- `Kyber.Platform GamePlatform`

- `Kyber.resettimer`
- `Kyber.resetonboarding`
- `Kyber.Restart`
- `Kyber.RevokeUnlock <player> <guid>`
- `Kyber.RenderBundles Boolean`
- `Kyber.RenderPropertyDebug Boolean`
- `Kyber.RenderCameraDebug Boolean`

- `Kyber.startgame`
- `Kyber.starttimer`
- `Kyber.safezone`
- `Kyber.SendStats`
- `Kyber.SaveLocation`
- `Kyber.SetTeam <player> <team>`
- `Kyber.SetTeamByIndex <playerIndex> <team>`
- `Kyber.SetTeamById <playerId> <team>`
- `Kyber.ShuffleTeams`
- `Kyber.SetBattlepoints <player> <amount>`
- `Kyber.SaveData`

- `Kyber.toggleMusic`
- `Kyber.toggleVO`
- `Kyber.toggleAmbience`
- `Kyber.Teleport <player> <x> <y> <z>`
- `Kyber.TestSetActive <player> <gpId> <unknown> <vurId> <skinInfoId>`
- `Kyber.TestUpdateActive <player> <gpId>`
- `Kyber.TestSetAbility <player> <abilityId> <slot>`

- `Kyber.unused1 Int32`
- `Kyber.unused2 Int32`
- `Kyber.unused3 Int32`
- `Kyber.unused4 Int32`

- `Kyber.VoipMute`
- `Kyber.VoipListCapturesDevices`
- `Kyber.VoipSetCaptureDevice <id [from VoipListCaptureDevices]>`
- `Kyber.VoipSetInputVolume <volume [0-100, default=50, logarithmic/sensitive]>`
- `Kyber.VoipSetOutputVolume <volume [0-100, default=50, logarithmic/sensitive]>`

### Main Menu Commands
- `Kyber.Arcade_Run_1`
- `Kyber.Arcade_Run_2`
- `Kyber.Arcade_Run_4`
- `Kyber.Arcade_Run_5`
- `Kyber.Arcade_Run_6`
- `Kyber.Arcade_Run_7`
- `Kyber.Arcade_Run_8`
- `Kyber.Arcade_Run_3`
- `Kyber.Arcade_Run_1_coop`
- `Kyber.Arcade_Run_2_coop`
- `Kyber.Arcade_Run_3_coop`
- `Kyber.Arcade_Run_4_coop`
- `Kyber.Arcade_Run_5_coop`
- `Kyber.Arcade_Run_6_coop`
- `Kyber.Arcade_Run_7_coop`
- `Kyber.Arcade_Run_8_coop`
- `Kyber.Arcade_Run_1_versus`
- `Kyber.Arcade_Run_2_versus`
- `Kyber.Arcade_Run_3_versus`
- `Kyber.Arcade_Run_4_versus`
- `Kyber.Arcade_Run_5_versus`
- `Kyber.Arcade_Run_6_versus`
- `Kyber.Arcade_Run_7_versus`
- `Kyber.Arcade_Run_8_versus`

- `Kyber.Bomber`
- `Kyber.BobaFett`
- `Kyber.Bossk`
- `Kyber.Blast`
- `Kyber.BlastNav`
- `Kyber.Broadcast <message>`
- `Kyber.BundleDebugFontSize Float32`

- `Kyber.COOPStarfighter1Onslaught`
- `Kyber.COOPStarfighter1TeamBlast`
- `Kyber.COOPStarfighter2Onslaught`
- `Kyber.COOPStarfighter2TeamBlast`
- `Kyber.COOPStarfighter3TeamBlast`
- `Kyber.COOPStarfighter3Onslaught`
- `Kyber.COOPStarfighter4TeamBlast`
- `Kyber.COOPStarfighter4Onslaught`
- `Kyber.COOPStarfighter5TeamBlast`
- `Kyber.COOPStarfighter5Onslaught`
- `Kyber.COOPStarfighter6TeamBlast`
- `Kyber.COOPStarfighter6Onslaught`
- `Kyber.Chewbacca`
- `Kyber.CustomArcade_Tatooine`
- `Kyber.COOP_NavGR`
- `Kyber.COOP_NavResistance`
- `Kyber.COOP_NavSA`
- `Kyber.COOP_NavFO`
- `Kyber.CrashGame`

- `Kyber.DarthVader`
- `Kyber.DarthMaul`
- `Kyber.Doku`
- `Kyber.demooverlay`
- `Kyber.DisableTeamBalancing Boolean`

- `Kyber.Enforcer`
- `Kyber.Emperor`
- `Kyber.EwokHunt`
- `Kyber.Extraction`
- `Kyber.ExtractionNav`
- `Kyber.EwokHuntNav`
- `Kyber.EpicCard`
- `Kyber.ExtentDebug`
- `Kyber.EnableShuffleTeams Boolean`
- `Kyber.EnableUnlimitedPowerEvent Boolean`

- `Kyber.FE_UI_MP`
- `Kyber.FE_UI_SP`
- `Kyber.Fighter`
- `Kyber.Finn`
- `Kyber.FullTeamSwap`

- `Kyber.Grievous`
- `Kyber.GalacticAssault`
- `Kyber.GalacticAssaultNav`
- `Kyber.grid`
- `Kyber.GiveBattlepoints <player> <amount>`
- `Kyber.GrantUnlock <player> <guid>`

- `Kyber.Heavy`
- `Kyber.HanSolo`
- `Kyber.Hvv`
- `Kyber.HeroShowdown`
- `Kyber.HeroStarFighter`
- `Kyber.HeroStarFighterNav`
- `Kyber.HeroShowdownNav`
- `Kyber.HeroesVsVillainsNav`
- `Kyber.HotReloadLua`

- `Kyber.Interceptor`
- `Kyber.IdenVersio`
- `Kyber.InstantAction_Nav`
- `Kyber.Infiltrator`
- `Kyber.IgnoreControllerError`

- `Kyber.JoinServer <ip> <port>`

- `Kyber.Kylo`

- `Kyber.Luke`
- `Kyber.Lando`
- `Kyber.Leia`
- `Kyber.LoadLevel <level> <mode>`
- `Kyber.LoadSPLevel <level> <startPoint> <initialSubLevel>`
- `Kyber.LoadSPLevel`
- `Kyber.LogPlayerStats`
- `Kyber.LogLastEbxLoad`

- `Kyber.MilleniumFalconLando`
- `Kyber.MilleniumFalconRey`
- `Kyber.MilleniumFalcon`

- `Kyber.Officer`
- `Kyber.ObiWan`

- `Kyber.Phasma`
- `Kyber.PlayerJoinedGroup`
- `Kyber.PlayerLeftGroup`
- `Kyber.PlayerCancelled`
- `Kyber.PlayerManagerAddr`
- `Kyber.Platform GamePlatform`

- `Kyber.Rey`
- `Kyber.RZ-2A-Wing`
- `Kyber.resetonboarding`
- `Kyber.Restart`
- `Kyber.RevokeUnlock <player> <guid>`
- `Kyber.RenderBundles Boolean`
- `Kyber.RenderPropertyDebug Boolean`
- `Kyber.RenderCameraDebug Boolean`

- `Kyber.SoloStarfighter1Onslaught`
- `Kyber.SoloStarfighter1TeamBlast`
- `Kyber.SoloStarfighter2Onslaught`
- `Kyber.SoloStarfighter2TeamBlast`
- `Kyber.SoloStarfighter3TeamBlast`
- `Kyber.SoloStarfighter3Onslaught`
- `Kyber.SoloStarfighter4TeamBlast`
- `Kyber.SoloStarfighter4Onslaught`
- `Kyber.SoloStarfighter5TeamBlast`
- `Kyber.SoloStarfighter5Onslaught`
- `Kyber.SoloStarfighter6TeamBlast`
- `Kyber.SoloStarfighter6Onslaught`
- `Kyber.Specialist`
- `Kyber.Speeder`
- `Kyber.Scimitar`
- `Kyber.Slave1`
- `Kyber.SpaceBattles`
- `Kyber.Strike`
- `Kyber.StrikeNav`
- `Kyber.StarfighterAssaultNav`
- `Kyber.SPNav`
- `Kyber.safezone`
- `Kyber.SendStats`
- `Kyber.SaveLocation`

- `Kyber.TieSilencer`
- `Kyber.TieAdvanced`
- `Kyber.TieLnFighter`
- `Kyber.T-65BX-Wing`
- `Kyber.T-70X-Wing`
- `Kyber.Tutorial`
- `Kyber.TitanPrequelNav`
- `Kyber.TitanSequelNav`
- `Kyber.TutorialConsole`
- `Kyber.Teleport <player> <x> <y> <z>`
- `Kyber.TestSetActive <player> <gpId> <unknown> <vurId> <skinInfoId>`
- `Kyber.TestUpdateActive <player> <gpId>`
- `Kyber.TestSetAbility <player> <abilityId> <slot>`

- `Kyber.unused1 Int32`
- `Kyber.unused2 Int32`
- `Kyber.unused3 Int32`
- `Kyber.unused4 Int32`

- `Kyber.VersusStarfighter1TeamBlast`
- `Kyber.VersusStarfighter2TeamBlast`
- `Kyber.VersusStarfighter3TeamBlast`
- `Kyber.VersusStarfighter4TeamBlast`
- `Kyber.VersusStarfighter5TeamBlast`
- `Kyber.VersusStarfighter6TeamBlast`
- `Kyber.VoipMute`
- `Kyber.VoipListCapturesDevices`
- `Kyber.VoipSetCaptureDevice <id [from VoipListCaptureDevices]>`
- `Kyber.VoipSetInputVolume <volume [0-100, default=50, logarithmic/sensitive]>`
- `Kyber.VoipSetOutputVolume <volume [0-100, default=50, logarithmic/sensitive]>`

- `Kyber.Yoda`

# Data System
## Get Type Information
```lua
local ti = dc.typeInfo
print(ti.name)
print(ti:IsKindOf("GameComponentData"))
```

## DataContainer
Reading a DataContainer field:
```lua
value = container.FieldName
```
Example:
```lua
local hp = soldier.Health
```

Writing a DataContainer field:
```lua
container.FieldName = value
```
Example:
```lua
soldier.Health = 150
```

You can check the type of a DataContainer with `.Is()`:
```lua
if dc:Is("SoldierEntityData") then
    -- logic
end
```

## Create a DataContainer
```lua
local obj = SomeType()
```
Example:
```lua
local data = SoldierEntityData()
data.Health = 100
```

## Create a ValueType
```lua
local v = Vec3()
```
Example:
```lua
local pos = Vec3()
pos.x = 1
pos.y = 2
pos.z = 3
```

## Loop Through an FBArray
```lua
for _, v in pairs(array) do
    -- logic
end
```
Example:
```lua
for _, obj in pairs(inst.objects) do
    print(obj.typeInfo.name)
end
```

You can also extend an FBArray's size:
```lua
array:extend(5)
```

# Entity
## Creating an entity
```lua
EntityManager.Create(dataContainer)
```
Example:
```lua
local data = SomeEntityData()
local ent = EntityManager.Create(data)
```

## Get entities
```lua
EntityManager.GetList(typeName, realm)
```
Example:
```lua
local ents = EntityManager.GetList("SoldierEntity", Realm.Realm_Server)
```

## Read an entity property
```lua
entity:Read(fieldName, typeName)
```
Example:
```lua
local t = entity:Read("Transform", "LinearTransform")
```

## Write an entity property
```lua
entity:Write(fieldName, typeName, value)
```
Example:
```lua
local transform = LinearTransform()
transform.x = 1
transform.y = 2
transform.z = 3
entity:Write("Transform", "LinearTransform", transform)
```

## Firing an entity event
There are two ways to fire events on an entity:

`FireEvent` immediately processes the event internally:
```lua
entity:FireEvent(EntityEvent("OnDestroy"))
```

`Event` queues it to be executed in the entity's context:
```lua
entity:Event(EntityEvent("Spawn"))
```

You can also use `ServerPlayerEvent` in places where `EntityEvent` is accepted:
```lua
entity:Event(ServerPlayerEvent("Spawn"))
```

## Entity properties
```lua
entity.data  
entity.bus   
```

# Event Manager
## Listen to an Event
```lua
EventManager.Listen(eventName, function(...)
    -- logic
end)
```

```lua
EventManager.Listen(eventName, inst,  function(...)
    -- logic
end)
```

Example:
```lua
EventManager.Listen("ServerPlayer:Joined", function(player)
    print(player.name .. " joined!")
end)
```

## Cancel an event
**This must be called inside an event listener.**
```lua
EventManager.SetCancelled(true)
```

Example:
```lua
EventManager.Listen("ServerPlayer:SendMessage", function(player, message)
    EventManager.SetCancelled(true)
end)
```

## All Events
### `Server:Init`
```lua
EventManager.Listen("Server:Init", function()
    print("Server initialized!")
end)
```

### `Server:UpdatePre`
```lua
EventManager.Listen("Server:UpdatePre", function(delta)
    -- logic
end)
```

### `ServerPlayer:Joined`
```lua
EventManager.Listen("ServerPlayer:Joined", function(player)
    print(player.name .. " joined")
end)
```

### `ServerPlayer:Disconnect`
```lua
EventManager.Listen("ServerPlayer:Disconnect", function(player)
    print(player.name .. " left")
end)
```

### `ServerPlayer:Killed`
```lua
EventManager.Listen("ServerPlayer:Killed", function(victim, inflictor, weaponName)
    print(victim.name .. " was killed by " .. inflictor.name .. " with " .. tostring(weaponName))
end)
```

### `ServerPlayer:Spawned`
```lua
EventManager.Listen("ServerPlayer:Spawned", function(player)
    print(player.name .. " has spawned")
end)
```

### `ServerPlayer:SendMessage`
```lua
EventManager.Listen("ServerPlayer:SendMessage", function(player, message)
    print(player.name .. " said: " .. message)
end)
```

### `Level:Loaded` 
```lua
EventManager.Listen("Level:Loaded", function(level, mode)
    print("Level loaded:", level, mode)
end)
```

### `Level:Complete`
```lua
EventManager.Listen("Level:Complete", function()
    print("Round over!")
end)
```

### `ResourceManager:PartitionLoaded`
```lua
EventManager.Listen("ResourceManager:PartitionLoaded", function(partitionName, instance)
    -- logic
end)
```

### `DedicatedServer:PerformanceStatsMessage`
Message sent every 10s that notifies the server of its performance statistics.

```lua
EventManager.Listen("DedicatedServer:PerformanceStatsMessage", function(tps, avgTickTime, worstTickTime)
    -- logic
end)
```

# `ChatFilter:Filter`
Event runs every time a message is filtered.
```lua
EventManager.Listen("ChatFilter:Filter", function(sender, message, filteredMessage)
    -- logic
end)
```

## Clear
```lua
Clear() -- Clears all blocked phrases & regex.
```

```lua
ClearBlockedPhrases() -- Clears all blocked phrases.
```

```lua
ClearBlockedRegex() -- Clears all blocked regex.
```

## Enable/Disable
```lua
Enable() -- Enable chat filter filtering messages. Note: Even when disabled, you can still access these table functions.
```

```lua
Disable() -- Disable chat filter filtering messages. Note: Even when disabled, you can still access these table functions.
```

```lua
IsEnabled() -- Returns if the chat filter is enabled.
```

## Filter
```lua
AddBlockedPhrase("string") -- Add blocked phrase to chat filter. Filtering of this type is case insensitive. Returns whether the phrase was added to the list or already existed (false)
```

```lua
AddBlockedRegex("string") -- Add blocked regex to chat filter. Returns whether the regex was added to the list or already existed (false)
```

```lua
SetFilterCharacter("string") -- Sets the chat filter's replacement character. For example, if set to '#', "blah badword" -> "blah #######"
```

```lua
GetFilterCharacter() -- Gets the chat filter's replacement character.
```

# Map Rotation
```lua
AddMap(level: string, mode: string): void -- Adds a map entry to the end of the rotation. Raises an error if either level or mode is an empty string.
```
A `Level` is this `Levels/MP/DeathStar02_01/DeathStar02_01` and a `Mode` is this `Mode1`.

```lua
Clear(level: string, mode: string): void -- Clears the entire rotation and resets it with a single required starting entry. Raises an error if either level or mode is an empty string.
```

```lua
GetNextMap(): MapRotationEntry -- Returns the next map entry in the rotation without removing it.
```

```lua
RemoveNextMap(): void -- Removes the next map entry from the rotation.
```

```lua
GetCurrentEntryIndex(): integer -- Returns the current index within the map rotation list.
```

```lua
GetList(): MapRotationEntry -- Returns the full list of map rotation entries.
```

# Client Events
>[!WARNING]
>Client plugins are currently disabled as seen [here](https://github.com/ArmchairDevelopers/Kyber/blob/c589c5d221d70da0c23034073351cb64f95341d9/Module/Source/Script/ScriptManager.cpp#L171-L181). Client side events exist in the source, but client plugins are not loaded, so these APIs cannot be used.

### `Client:UpdatePre(deltaTime)`

### `Client:UpdatePost(deltaTime)` 

### `ClientPlayer:Spawned()`

### `ClientCameraManager:Push(scene)`

## Resource & Entity Events
`ResourceManager:PartitionLoaded`
```lua
EventManager.Listen("ResourceManager:PartitionLoaded", function(partitionName, inst)
    if inst.typeInfo.name ~= "LayerData" then return end
    print("Loaded LayerData:", partitionName)
end)
```

`EntityFactory:Create:<Name>`
```lua
EventManager.Listen("EntityFactory:Create:SoldierEntityData", function(data)
    print("Soldier entity created")
end)
```

# Hook
## Listening to hooks
```lua
HookManager.Listen(hookName, object, method)
```
Example:
```lua
HookManager.Listen("OnWeaponFired", Obj, Obj.OnWeaponFired)
```

# PlayerManager
## Getting a player:
```lua
PlayerManager.GetPlayer(name)
```
Example:
```lua
local player = PlayerManager.GetPlayer("The_Negotiator1")
```

## Getting all players:
```lua
PlayerManager.GetPlayers()
```
Example:
```lua
for _, player in pairs(PlayerManager.GetPlayers()) do
    print(player.name)
end
```

## Creating a bot:
```lua
PlayerManager.CreatePlayer(name)
```
Example:
```lua
local bot = PlayerManager.CreatePlayer("Bot1")
```

# ServerPlayer
## Weapons
```lua
player:SetWeapon("weaponName")
player:GetWeapon() -- returns the weapon as a DataContainer
```
Example:
```lua
player:SetWeapon("Gameplay/Equipment/LongRange/NT242/U_LongRange_NT242")
```

## Team & stats
You can change a player's team:
```lua
player:SetTeam(teamId)
```

Light side = `1`, Dark side = `2`:
```lua
player:SetTeam(1)
player:SetTeam(2)
```

Set score, kills, assists, deaths, set and give battlepoints:
```lua
-- All of the arguments here are integers
player:SetScore(1000)
player:SetKills(5)
player:SetAssists(5)
player:SetDeaths(67)
player:SetBattlepoints(500)
player:GiveBattlepoints(100)
```

## Customization
```lua
player:SetCustomizationAsset("assetName")
```

Example:
```lua
player:SetCustomizationAsset("Gameplay/Kits/Hero/Anakin/Kits/Kit_Hero_Anakin")
```

## Unlocks
```lua
player:SetUnlock("Guid", boolean)
```

Example:
```lua
player:SetUnlock("Guid", true) 
```

## Visibility
```lua
player:SetInvisible(boolean)
```

Example:
```lua
player:SetInvisible(true)
```

## Ammo & health
```lua
-- Both arguments are integers
player:SetHealth(200)
player:SetMaxHealth(300)
```

## Abilities
```lua
SetAbility(abilityId)
```
Example:
```lua
player:SetAbility("Gameplay/Kits/Hero/Anakin/Abilities/PullDominance/DefaultAbility_Anakin_PullDominance")
```

## Chat
```lua
ForceSendChatMessage(channel, message):
```

Example:
```lua
player:ForceSendChatMessage(ChatChannel.Team, "Hello")
```
```lua
player:SendChatMessage("message")
```

Example:
```lua
player:SendChatMessage("Hello again")
```

## Input
```lua
SetInputEnabled(actionId, boolean)
```

Example:
```lua
player:SetInputEnabled(2, false) -- true to enable, false to disable
```

## Kick
```lua
player:Kick("reason")
```

Example:
```lua
player:Kick("skill issue")

## Synced settings
```lua
player:SendSyncedSettings()
```

## ServerPlayer fields
```lua
player.name              -- string
player.playerId          -- integer
player.team              -- integer
player.battlepoints      -- integer
player.score             -- integer
player.kills             -- integer
player.assists           -- integer
player.deaths            -- integer
player.characterEntity   -- Entity
player.vehicleEntity     -- Entity
player.activeKit         -- DataContainer
player.isBot             -- boolean
player.isSpawned         -- boolean
```

# ResourceManager
```lua
ResourceManager.LookupDataContainer(name)
```
Example:
```lua
local dc = ResourceManager.LookupDataContainer("Weapons/Blaster_E11")
```

# Socket
## Creating a listening server
```lua
local server = SocketManager.Create(9000)
```

## Connecting to a socket
```lua
local socket = SocketManager.CreateSend("127.0.0.1", 9000)
```
>[!IMPORTANT]
>The URI must be `"127.0.0.1"`. External addresses are not allowed.

## Accepting clients
Returns `nil` if no client is ready:
```lua
local client = server:Accept()
```

## Receiving
```lua
local msg = client:Recv(1024)
```

## Sending
```lua
client:Send("Hi")
```

## Closing
```lua
client:Close()
client:Shutdown()      
client:ShutdownSend()   
```

# Utils
## Render camera transform
```lua
local t = Utils.GetClientCameraTransform()
```

## Active gameplay camera transform
```lua
Utils.GetActiveCameraTransform(t)
```

## Allowed Libraries
```
base
package
coroutine
math
os
string
table
```

## Unavailable functions
```
os.execute
os.exit
os.remove
os.rename
os.setlocale
os.tmpname
```

# Example Plugins

***[Example Plugins](https://github.com/ArmchairDevelopers/PluginExamples) provided by the Kyber Developers***

**[Plugins](https://github.com/Thanosss00/Kyber-Plugins) I made**

## Print a message
```lua
print("Hi")
```

## Give every player 500 battlepoints when they spawn
```lua
EventManager.Listen("ServerPlayer:Spawned", function(player)
    player:SetBattlepoints(500)
end)
```

## Delay actions
You can delay actions by using the `TimerService`, create `common/sdk.lua` and paste this:
```lua
TimerService = {
    timers = {}
}

function TimerService:update(delta)
    for i = #self.timers, 1, -1 do
        local t = self.timers[i]
        t.elapsed = t.elapsed + delta

        if t.elapsed >= t.delay then
            t.callback()
            table.remove(self.timers, i)
        end
    end
end

EventManager.Listen("Server:UpdatePre", function(delta)
    TimerService:update(delta)
end)

function SetTimeout(callback, delay)
    table.insert(TimerService.timers, {
        delay = delay,
        elapsed = 0,
        callback = callback
    })
end
```
Then in \_\_init\_\_.lua:
```lua
require "common/sdk"

SetTimeout(function()
    print("Idk")
end, 5) -- delays by 5 seconds
```

## Broadcast a message
```lua
local function Broadcast(msg)
    Console.Execute("Kyber.Broadcast " .. msg)
end

Broadcast("Hi")
```

You can also do it with a delay:
```lua
require "common/sdk"

SetTimeout(function()
    Broadcast("Hi again")
end, 5)
```

## Define constants
```lua
-- Light side is 1, Dark is 2
local TEAM_LIGHT = 1
local TEAM_DARK = 2
local MAX_HEALTH = 200
local STARTING_BP = 500
```
Example:
```lua
player:SetTeam(TEAM_LIGHT)
player:SetHealth(MAX_HEALTH)
player:SetBattlepoints(STARTING_BP)
```

## Count real players
```lua
local function CountHumans()
    local c = 0
    for _, p in ipairs(PlayerManager.GetPlayers()) do
        if not p.isBot then c = c + 1 end
    end
    return c
end
```

## Swap team plugin
```lua
EventManager.Listen("ServerPlayer:SendMessage", function(player, message)
    if message == "!swap" then
        if player.team == 1 then
            player:SetTeam(2)
        elseif player.team == 2 then
            player:SetTeam(1)
        end
    end
end)
```

You can also do it as a function:
```lua
local function Swap(player)
    if player.team == 1 then
        player:SetTeam(2)
    elseif player.team == 2 then
        player:SetTeam(1)  
    end
end

-- Then you call the faction in ServerPlayer:SendMessage
EventManager.Listen("ServerPlayer:SendMessage", function(player, message)
    if message == "!swap" then Swap(player) end
end)
```

You can also make it only work on the character selection screen, which is included in the [HVVPlaygroundPlugin](https://github.com/ArmchairDevelopers/PluginExamples/blob/82d12bbc6c6b44499954751656dea3027759f327/HVVPlaygroundPlugin/server/__init__.lua#L104-L128)

Example:
```lua
EventManager.Listen("ServerPlayer:SendMessage", function(player, message)
    if message:len() < 2 then return end
    local messageSplit = string.split(message)

    if #messageSplit <= 0 then return end
    if messageSplit[1]:len() < 3 then return end
    if messageSplit[1]:sub(1, 1) ~= '!' and messageSplit[1]:sub(1, 1) ~= '/' then return end

    local command = messageSplit[1]:lower():sub(2)
    EventManager.SetCancelled(true)

    if command == "swapteam" or command == "swap" or command == "st" then
        if player == nil then return end

        if not player.isSpawned then
            player:SetTeam((math.fmod(player.team, 2)) + 1)
        end
    end
end)
```

## Auto Balance Teams
```lua
EventManager.Listen("ServerPlayer:Joined", function(player)
    local players = PlayerManager.GetPlayers()
    local light = 0
    local dark = 0

    for _, p in pairs(players) do
        if p.team == 1 then
            light = light + 1
        elseif p.team == 2 then
            dark = dark + 1
        end
    end

    if light > dark then
        player:SetTeam(2)
    else
        player:SetTeam(1)
    end
end)
```

## Admin commands
First make an `admins.lua` file (see the example from earlier), then in `__init__.lua`:
```lua
require "admins"

EventManager.Listen("ServerPlayer:SendMessage", function(player, message)
    if not table.contains(Admins, player.playerId) then return end
    if message:len() < 2 then return end
    local messageSplit = string.split(message)

    if #messageSplit <= 0 then return end
    if messageSplit[1]:len() < 3 then return end
    if messageSplit[1]:sub(1, 1) ~= '/' then return end

    local command = messageSplit[1]:lower():sub(2)
    EventManager.SetCancelled(true)

    if command == "testcommand" then
        -- logic
    elseif command == "addanyothercommand" then
        -- logic
    else
        print("Invalid command: " .. command)
    end
end)
```

## Extract the map name from the full level path
For example: `Levels/MP/DeathStar02_01/DeathStar02_01` → `DeathStar02_01`
```lua
local function ExtractMapName(levelPath)
    return levelPath:match("([^/]+)$") or levelPath
end

EventManager.Listen("Level:Loaded", function(level, mode)
    print("Map name:", ExtractMapName(level))
end)
```

## StartRound function
```lua
require "common/sdk"

local MIN_PLAYERS = 2

local function CountHumans()
    local c = 0
    for _, p in ipairs(PlayerManager.GetPlayers()) do
        if not p.isBot then c = c + 1 end
    end
    return c
end

local function StartRound()
    if CountHumans() < MIN_PLAYERS then
        print("Not enough players")
        return
    end
    -- You can also do this without the SetTimeout
    SetTimeout(function()
        Broadcast("Starting soon...")
    end, 10)
end

EventManager.Listen("Level:Loaded", function(level, mode)
    -- I suggest using the SetTimeout here because maps dont load instantly and the game might start while you are in the loading screen
    SetTimeout(function()
        StartRound()
    end, 5)
end)
```

## EndRound function
Example:
```lua
local function EndRound()
    -- You can also do this without the SetTimeout
    SetTimeout(function()
        Console.Execute("endofround")
    end, 1)
end
```

## Credits
[Magix](https://github.com/MagixGames) for the [Official Kyber Docs](https://github.com/ArmchairDevelopers/KyberDocs/tree/feat/plugins-api/content/docs/pluginref) on GitHub and soon on their [website](https://docs.kyber.gg/g/pluginref)./
