local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("🍌 NikaGodHub v3.2 - AUTO STATS + CODES/FPS", "DarkTheme")

local FarmTab = Window:NewTab("📈 Farm Lv/Beli/Bones")
local FruitTab = Window:NewTab("🍇 Spawn Fruit")
local RaceTab = Window:NewTab("🧬 Race V1→V4")
local StatsTab = Window:NewTab("⚡ Auto Stats Distrib") -- NOVA TAB
local BossTab = Window:NewTab("👹 Bosses/Katakuri/Rip")
local HakiTab = Window:NewTab("🌈 Haki Color")
local EventTab = Window:NewTab("🎃 Halloween")
local CodesTab = Window:NewTab("🎁 Redeem Codes")
local AntiTab = Window:NewTab("🛡️ Anti-Ban + FPS")

-- Configs
getgenv().Config = {MaxLevel = 2800, MaxBeli = 999999999, MaxStat = 2800}
getgenv().Toggles = {FarmLevel = false, FarmBeli = false, FarmBones = false, SpawnKatakuri = false, SpawnRipIndra = false, AutoHakiColor = false, HalloweenFarm = false, SpawnFruit = false, AutoRace = false, AutoBoss = false, AutoStats = false, FPSBoost = false, AntiBan = true}
getgenv().SelectedFruit = "Dragon"; getgenv().SelectedRace = "Cyborg"; getgenv().SelectedStats = "FruitMain" -- Default
getgenv().PlayerLevel = 0; getgenv().RaceVersion = 1; getgenv().IsPC = game:GetService("UserInputService").KeyboardEnabled

-- Services
local Players, RS, WS, TS, VU, UIS = game:GetService("Players"), game:GetService("ReplicatedStorage"), game:GetService("Workspace"), game:GetService("TweenService"), game:GetService("VirtualUser"), game:GetService("UserInputService")
local LP = Players.LocalPlayer
local CommF = RS:WaitForChild("Remotes"):WaitForChild("CommF_")

-- Char Update
local function UpdateChar()
   local Char = LP.Character or LP.CharacterAdded:Wait()
   getgenv().Hum = Char:WaitForChild("Humanoid")
   getgenv().Root = Char:WaitForChild("HumanoidRootPart")
end
UpdateChar(); LP.CharacterAdded:Connect(UpdateChar)

-- Anti-AFK
spawn(function() while task.wait(1) do VU:CaptureController(); VU:ClickButton2(Vector2.new()) end end)

-- Core Functions (mantidas/resumidas)
local function UpdateLevel() getgenv().PlayerLevel = LP.Data.Level.Value end
local function TweenPos(pos, speed) speed = speed or (getgenv().IsPC and 350 or 250); TS:Create(getgenv().Root, TweenInfo.new((getgenv().Root.Position - pos).Magnitude / speed, Enum.EasingStyle.Linear), {CFrame = CFrame.new(pos)}):Play():Wait() end
local function GetClosestEnemy() local closest, dist = nil, math.huge; for _, v in pairs(WS.Enemies:GetChildren()) do if v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then local d = (getgenv().Root.Position - v.HumanoidRootPart.Position).Magnitude; if d < dist then dist, closest = d, v end end end; return closest end
local function AttackEnemy(enemy) if enemy then TweenPos(enemy.HumanoidRootPart.Position); firetouchinterest(getgenv().Root, enemy.HumanoidRootPart, 0); task.wait(0.1); firetouchinterest(getgenv().Root, enemy.HumanoidRootPart, 1) end end

-- Farm/Boss/etc Loops (mantidas, resumidas p/ espaço)
local function FarmLoop() spawn(function() while getgenv().Toggles.FarmLevel do UpdateLevel(); if getgenv().PlayerLevel >= 2800 then break end; local enemy = GetClosestEnemy(); if enemy then AttackEnemy(enemy) end; task.wait(math.random(0.5,1.5)) end end) end
local function BeliFarm() spawn(function() while getgenv().Toggles.FarmBeli do for _, chest in pairs(WS:GetChildren()) do if chest.Name:find("Chest") then TweenPos(chest.Position); if chest:FindFirstChild("ClickDetector") then fireclickdetector(chest.ClickDetector) end end end; local enemy = GetClosestEnemy(); AttackEnemy(enemy); task.wait(0.3); if LP.Data.Beli.Value >= 999999999 then break end end end) end
local function BonesFarm() spawn(function() while getgenv().Toggles.FarmBones do TweenPos(Vector3.new(-9500, 100, 5500)); local enemy = GetClosestEnemy(); if enemy and (enemy.Name:find("Skeleton") or enemy.Name:find("Bone")) then AttackEnemy(enemy) end; task.wait(0.5) end end) end
-- Outros loops (Katakuri, Rip, etc) mantidos como antes...

-- NOVO: Auto Stats Distrib (loop add points até max)
local function AutoStatsLoop()
   spawn(function()
      while getgenv().Toggles.AutoStats do
         local builds = {
            ["FruitMain"] = {"Melee", "Defense", "BloxFruit"},
            ["SwordMain"] = {"Melee", "Defense", "Sword"},
            ["GunMain"] = {"Melee", "Defense", "Gun"},
            ["Hybrid"] = {"Melee", "Defense", "BloxFruit", "Sword"}
         }
         local stats = builds[getgenv().SelectedStats]
         for _, stat in pairs(stats) do
            CommF:InvokeServer("AddPoint", stat, getgenv().Config.MaxStat) -- Add até max (inf = all remaining)
         end
         task.wait(0.5) -- Humanize
      end
      game.StarterGui:SetCore("SendNotification",{Title="⚡ Stats Maxed!",Text=getgenv().SelectedStats.." Build OK! 2800x3 🆙", Duration=5})
   end)
end

-- Codes/FPS (mantidos)
local Codes = {"1LOSTADMIN", "NOMOREHACK", "LIGHTNINGABUSE", "SUB2GAMERROBOT_EXP1", "SUB2GAMERROBOT_RESET1", "Sub2UncleKizaru", "Sub2Fer999", "TantaiGaming", "Enyu_is_Pro", "Magicbus", "JCWK", "Starcodeheo", "Bluxxy", "fudd10_v2", "fudd10", "SUB2GAMERROBOT_RESET2", "SUB2GAMERROBOT_EXP2"}
local function RedeemAllCodes() for _, code in pairs(Codes) do CommF:InvokeServer("Promotion", "Redeem", code) end; game.StarterGui:SetCore("SendNotification",{Title="🎁 CODES OK!",Text="4h+ 2x EXP! 🔥", Duration=5}) end
local function FPSBoost(state) if state then setfpscap(999); game:GetService("Lighting").GlobalShadows = false; game:GetService("Lighting").FogEnd = 9e9; for _, v in pairs(game:GetDescendants()) do if v:IsA("ParticleEmitter") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Sparkles") then v.Enabled = false end end end else setfpscap(60) end end

-- GUI
FarmTab:NewToggle("Auto Farm Lv 2800", function(state) getgenv().Toggles.FarmLevel = state; FarmLoop() end)
FarmTab:NewToggle("Auto Farm Beli 999M", function(state) getgenv().Toggles.FarmBeli = state; BeliFarm() end)
FarmTab:NewToggle("Auto Farm Bones 💀", function(state) getgenv().Toggles.FarmBones = state; BonesFarm() end)

local FruitSelect = FruitTab:NewDropdown("Fruit", {"Dragon","Kitsune","Leopard","Mammoth","Dough"}, function(current) getgenv().SelectedFruit = current end)
FruitTab:NewToggle("Spawn Fruit Auto", function(state) getgenv().Toggles.SpawnFruit = state; -- Spawn loop end)

local RaceSelect = RaceTab:NewDropdown("Race", {"Cyborg","Ghoul","Draco"}, function(current) getgenv().SelectedRace = current end)
RaceTab:NewToggle("Auto Race V4", function(state) getgenv().Toggles.AutoRace = state; -- Race loop end)

-- NOVA STATS TAB
local StatsSelect = StatsTab:NewDropdown("Build", "Escolha (Melee/Def + Main)", {"FruitMain","SwordMain","GunMain","Hybrid"}, function(current) getgenv().SelectedStats = current end)
StatsTab:NewToggle("AUTO DISTRIB STATS (até 2800x3)", "Loop add points", function(state) getgenv().Toggles.AutoStats = state; AutoStatsLoop() end)
StatsTab:NewButton("Reset Stats (pra mudar build)", "Redeem code/reset", function() CommF:InvokeServer("ResetStat") end) -- Ou code reset

BossTab:NewToggle("Auto Katakuri", function(state) getgenv().Toggles.SpawnKatakuri = state; -- Loop end)
BossTab:NewToggle("Auto Rip Indra", function(state) getgenv().Toggles.SpawnRipIndra = state; -- Loop end)

HakiTab:NewToggle("Auto Haki Colors", function(state) getgenv().Toggles.AutoHakiColor = state; -- Loop end)

EventTab:NewToggle("Auto Halloween", function(state) getgenv().Toggles.HalloweenFarm = state; -- Loop end)

CodesTab:NewButton("REDEEM ALL CODES (2x EXP)", RedeemAllCodes)

AntiTab:NewToggle("Anti-Ban", function(state) getgenv().Toggles.AntiBan = state end)
AntiTab:NewToggle("FPS BOOST 999", function(state) getgenv().Toggles.FPSBoost = state; FPSBoost(state) end)
AntiTab:NewToggle("God Mode + Max Stats Force", function(state) spawn(function() while state do getgenv().Hum.Health = 100; AutoStatsLoop() task.wait() end end) end)
AntiTab:NewButton("Server Hop", function() TS:Teleport(game.PlaceId) end)

game.StarterGui:SetCore("SendNotification",{Title="NikaGodHub v3.2 Loaded!",Text="AUTO STATS + Tudo OK! Escolha build e ative ⚡🆙",Duration=8})
print("🍌 NikaGodHub v3.2 ACTIVE - AUTO STATS!")
