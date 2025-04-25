-- O MAESTRO - Scripter Profissional Blox Fruits
-- Desenvolvido com base no Ninja Hub e outros hubs populares

-- Carregar UI Library
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/shlexware/Orion/main/source"))()

local Window = OrionLib:MakeWindow({
    Name = "O MAESTRO - Blox Fruits",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "OMaestroHub"
})

OrionLib:MakeNotification({
    Name = "Bem-vindo!",
    Content = "O MAESTRO está ativo!",
    Image = "rbxassetid://4483345998",
    Time = 5
})

-- Variáveis Globais
getgenv().AutoFarm = false
getgenv().AutoStats = false
getgenv().StatType = "Melee"
getgenv().AutoHaki = false

-- Função de Auto Haki
function EnableHaki()
    local args = {[1] = "Buso"}
    game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))
end

-- AUTO FARM
local FarmTab = Window:MakeTab({
    Name = "Auto Farm",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

FarmTab:AddToggle({
    Name = "Auto Farm",
    Default = false,
    Callback = function(Value)
        AutoFarm = Value
        while AutoFarm do task.wait()
            pcall(function()
                EnableHaki()
                for i, v in pairs(game:GetService("Workspace").Enemies:GetChildren()) do
                    if v:FindFirstChild("HumanoidRootPart") and v:FindFirstChild("Humanoid") and v.Humanoid.Health > 0 then
                        repeat task.wait()
                            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame * CFrame.new(0,10,0)
                            game:GetService("VirtualInputManager"):SendKeyEvent(true, "Z", false, game)
                        until v.Humanoid.Health <= 0 or not AutoFarm
                    end
                end
            end)
        end
    end
})

-- AUTO STATS
local StatsTab = Window:MakeTab({
    Name = "Auto Stats",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

StatsTab:AddDropdown({
    Name = "Escolha o Atributo",
    Default = "Melee",
    Options = {"Melee", "Defense", "Sword", "Gun", "Blox Fruit"},
    Callback = function(Value)
        StatType = Value
    end
})

StatsTab:AddToggle({
    Name = "Auto Stats",
    Default = false,
    Callback = function(Value)
        AutoStats = Value
        while AutoStats do task.wait(1)
            pcall(function()
                game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("AddPoint", StatType, 1)
            end)
        end
    end
})

-- TELEPORT
local TeleportTab = Window:MakeTab({
    Name = "Teleporte",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local ilhas = {
    ["Inicio"] = Vector3.new(939, 125, 1252),
    ["Jungle"] = Vector3.new(-1235, 11, 475),
    ["Desert"] = Vector3.new(1300, 12, 4200),
    ["Frozen Village"] = Vector3.new(110, 50, -1400)
}

for nome, pos in pairs(ilhas) do
    TeleportTab:AddButton({
        Name = nome,
        Callback = function()
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(pos)
        end
    })
end

-- FINALIZAR UI
OrionLib:Init()
