--[[
Painel completo para Blox Fruits - Arceus X Neo
AutoFarm | AutoBoss | AutoClick | Anti-Kick/Ban
Feito para testes com amigo
--]]

-- Proteção Anti-Kick/Ban
pcall(function()
    local mt = getrawmetatable(game)
    setreadonly(mt, false)
    local namecall = mt.__namecall
    mt.__namecall = newcclosure(function(self, ...)
        local method = getnamecallmethod()
        if method == "Kick" or method == "Ban" then return nil end
        return namecall(self, ...)
    end)
end)

local plr = game.Players.LocalPlayer
local tweenService = game:GetService("TweenService")
local screenGui = Instance.new("ScreenGui", game.CoreGui)
screenGui.Name = "KaitunHub"

-- UI Principal
local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Size = UDim2.new(0, 400, 0, 300)
mainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true

-- Título
local title = Instance.new("TextLabel", mainFrame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "Kaitun Hub - Blox Fruits"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
title.TextScaled = true

-- Abas
local tabs = {"AutoFarm", "AutoBoss", "Config"}
local tabButtons = {}
local currentTab = nil
local contentFrames = {}

for i, name in ipairs(tabs) do
    local btn = Instance.new("TextButton", mainFrame)
    btn.Size = UDim2.new(0, 120, 0, 30)
    btn.Position = UDim2.new(0, (i - 1) * 120, 0, 45)
    btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextScaled = true
    tabButtons[name] = btn

    local frame = Instance.new("Frame", mainFrame)
    frame.Size = UDim2.new(1, -20, 1, -90)
    frame.Position = UDim2.new(0, 10, 0, 80)
    frame.BackgroundTransparency = 1
    frame.Visible = false
    contentFrames[name] = frame

    btn.MouseButton1Click:Connect(function()
        for _, f in pairs(contentFrames) do f.Visible = false end
        contentFrames[name].Visible = true
    end)
end

-- Botão AutoFarm
local farmOn = false
local btnFarm = Instance.new("TextButton", contentFrames.AutoFarm)
btnFarm.Size = UDim2.new(0, 200, 0, 50)
btnFarm.Position = UDim2.new(0, 100, 0, 30)
btnFarm.Text = "Ativar AutoFarm"
btnFarm.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
btnFarm.TextColor3 = Color3.fromRGB(255, 255, 255)
btnFarm.TextScaled = true

-- Botão AutoBoss
local bossOn = false
local btnBoss = Instance.new("TextButton", contentFrames.AutoBoss)
btnBoss.Size = UDim2.new(0, 200, 0, 50)
btnBoss.Position = UDim2.new(0, 100, 0, 30)
btnBoss.Text = "Ativar AutoBoss"
btnBoss.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
btnBoss.TextColor3 = Color3.fromRGB(255, 255, 255)
btnBoss.TextScaled = true

-- Botão AutoClick
local clickOn = false
local btnClick = Instance.new("TextButton", contentFrames.Config)
btnClick.Size = UDim2.new(0, 200, 0, 50)
btnClick.Position = UDim2.new(0, 100, 0, 30)
btnClick.Text = "Ativar AutoClick"
btnClick.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
btnClick.TextColor3 = Color3.fromRGB(255, 255, 255)
btnClick.TextScaled = true

-- Funções
function teleport(pos)
    local hrp = plr.Character and plr.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        local tween = tweenService:Create(hrp, TweenInfo.new(1), {CFrame = CFrame.new(pos)})
        tween:Play()
        tween.Completed:Wait()
    end
end

function startAutoFarm()
    spawn(function()
        while farmOn do
            pcall(function()
                local lvl = plr.Data.Level.Value
                local qList = {
                    {level = 1, quest = "BanditQuest1", npcPos = Vector3.new(1060, 16, 1548), mob = "Bandit", mobPos = Vector3.new(1140, 17, 1630)},
                    {level = 700, quest = "MarineQuest3", npcPos = Vector3.new(-2441, 73, -3213), mob = "Marine Captain", mobPos = Vector3.new(-2500, 72, -3270)},
                    {level = 1250, quest = "IceQuest1", npcPos = Vector3.new(1164, 401, -13287), mob = "Arctic Warrior", mobPos = Vector3.new(1240, 400, -13300)},
                    {level = 2400, quest = "DoughIslandQuest", npcPos = Vector3.new(-1350, 35, -7100), mob = "Cocoa Warrior", mobPos = Vector3.new(-1450, 35, -7150)}
                }
                for i = #qList, 1, -1 do
                    local q = qList[i]
                    if lvl >= q.level then
                        teleport(q.npcPos)
                        wait(1)
                        local npc = workspace:FindFirstChild(q.quest)
                        if npc then fireclickdetector(npc.ClickDetector) end
                        wait(0.5)
                        teleport(q.mobPos)
                        local enemy = workspace.Enemies:FindFirstChild(q.mob)
                        if enemy and enemy.Humanoid.Health > 0 then
                            repeat
                                teleport(enemy.HumanoidRootPart.Position + Vector3.new(0, 10, 0))
                                wait(0.2)
                            until enemy.Humanoid.Health <= 0 or not farmOn
                        end
                        break
                    end
                end
            end)
            wait(1)
        end
    end)
end

function startAutoBoss()
    spawn(function()
        while bossOn do
            local bosses = {
                {name = "Mob Leader", pos = Vector3.new(-2850, 8, 530)},
                {name = "Don Swan", pos = Vector3.new(2287, 15, 879)}
            }
            for _, b in pairs(bosses) do
                local boss = workspace.Enemies:FindFirstChild(b.name)
                if boss and boss.Humanoid.Health > 0 then
                    teleport(b.pos)
                    repeat
                        teleport(boss.HumanoidRootPart.Position + Vector3.new(0, 10, 0))
                        wait(0.2)
                    until boss.Humanoid.Health <= 0 or not bossOn
                end
            end
            wait(15)
        end
    end)
end

function startAutoClick()
    spawn(function()
        while clickOn do
            local tool = plr.Character and plr.Character:FindFirstChildOfClass("Tool")
            if tool then tool:Activate() end
            wait(0.1)
        end
    end)
end

-- Conexões dos botões
btnFarm.MouseButton1Click:Connect(function()
    farmOn = not farmOn
    btnFarm.Text = farmOn and "Desativar AutoFarm" or "Ativar AutoFarm"
    btnFarm.BackgroundColor3 = farmOn and Color3.fromRGB(180, 0, 0) or Color3.fromRGB(0, 150, 0)
    if farmOn then startAutoFarm() end
end)

btnBoss.MouseButton1Click:Connect(function()
    bossOn = not bossOn
    btnBoss.Text = bossOn and "Desativar AutoBoss" or "Ativar AutoBoss"
    btnBoss.BackgroundColor3 = bossOn and Color3.fromRGB(180, 0, 0) or Color3.fromRGB(0, 150, 0)
    if bossOn then startAutoBoss() end
end)

btnClick.MouseButton1Click:Connect(function()
    clickOn = not clickOn
    btnClick.Text = clickOn and "Desativar AutoClick" or "Ativar AutoClick"
    btnClick.BackgroundColor3 = clickOn and Color3.fromRGB(180, 0, 0) or Color3.fromRGB(0, 150, 0)
    if clickOn then startAutoClick() end
end)

-- Abre primeira aba por padrão
contentFrames.AutoFarm.Visible = true
