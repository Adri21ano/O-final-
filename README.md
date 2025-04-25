local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "OMaestro"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Função para tornar a tela e os botões móveis
local function makeDraggable(frame)
    local dragging = false
    local dragInput, dragStart, startPos

    local function update(input)
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)

    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then update(input) end
    end)
end

-- Tela principal do scripter
local main = Instance.new("Frame")
main.Size = UDim2.new(0, 380, 0, 300)
main.Position = UDim2.new(0.5, -190, 0.5, -150)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Visible = true
main.Parent = gui
makeDraggable(main)

-- Título
local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "O MAESTRO"
title.BackgroundTransparency = 0
title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(170, 0, 255)
title.TextScaled = true

-- Ícone para esconder/mostrar o scripter
local icon = Instance.new("TextButton", gui)
icon.Size = UDim2.new(0, 50, 0, 50)
icon.Position = UDim2.new(0, 10, 1, -60)
icon.BackgroundColor3 = Color3.fromRGB(170, 0, 255)
icon.Text = "7"
icon.TextColor3 = Color3.fromRGB(255, 255, 255)
icon.Font = Enum.Font.GothamBold
icon.TextScaled = true
icon.BorderSizePixel = 0
makeDraggable(icon)

-- Alternar visibilidade do scripter
icon.MouseButton1Click:Connect(function()
    main.Visible = not main.Visible
end)

-- Auto Farm Panel
local autoFarmFrame = Instance.new("Frame", main)
autoFarmFrame.Size = UDim2.new(1, -20, 0, 150)
autoFarmFrame.Position = UDim2.new(0, 10, 0, 60)
autoFarmFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
autoFarmFrame.BorderSizePixel = 0
autoFarmFrame.Visible = false

local autoFarmTitle = Instance.new("TextLabel", autoFarmFrame)
autoFarmTitle.Size = UDim2.new(1, 0, 0, 30)
autoFarmTitle.Position = UDim2.new(0, 0, 0, 0)
autoFarmTitle.Text = "Auto Farm"
autoFarmTitle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
autoFarmTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
autoFarmTitle.Font = Enum.Font.Gotham
autoFarmTitle.TextScaled = true

-- Botão para abrir/fechar Auto Farm
local toggleAutoFarmPanel = Instance.new("TextButton", main)
toggleAutoFarmPanel.Size = UDim2.new(1, -20, 0, 30)
toggleAutoFarmPanel.Position = UDim2.new(0, 10, 0, 50)
toggleAutoFarmPanel.Text = "Auto Farm"
toggleAutoFarmPanel.BackgroundColor3 = Color3.fromRGB(170, 0, 255)
toggleAutoFarmPanel.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleAutoFarmPanel.Font = Enum.Font.GothamBold
toggleAutoFarmPanel.TextScaled = true

toggleAutoFarmPanel.MouseButton1Click:Connect(function()
    autoFarmFrame.Visible = not autoFarmFrame.Visible
end)

-- Botão deslizante (toggle)
local autoFarmToggle = Instance.new("TextButton", autoFarmFrame)
autoFarmToggle.Size = UDim2.new(1, -20, 0, 40)
autoFarmToggle.Position = UDim2.new(0, 10, 0, 40)
autoFarmToggle.BackgroundColor3 = Color3.fromRGB(100, 0, 100)
autoFarmToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
autoFarmToggle.Font = Enum.Font.GothamBold
autoFarmToggle.TextScaled = true
autoFarmToggle.Text = "Auto Farm: OFF"

local autoFarmAtivo = false

autoFarmToggle.MouseButton1Click:Connect(function()
    autoFarmAtivo = not autoFarmAtivo
    autoFarmToggle.Text = autoFarmAtivo and "Auto Farm: ON" or "Auto Farm: OFF"

    if autoFarmAtivo then
        ativarAutoFarm()
    end
end)

-- Função do auto farm
function ativarAutoFarm()
    task.spawn(function()
        local player = game.Players.LocalPlayer
        local level = player:WaitForChild("leaderstats"):WaitForChild("Level").Value -- ajuste se necessário

        while autoFarmAtivo do
            pegarMissao(level)

            for _, npc in pairs(obterNPCs(level)) do
                atacarNPC(npc)
                if not autoFarmAtivo then break end
            end

            task.wait(1)
        end
    end)
end

-- Simula pegar missão (você pode personalizar com eventos remotos)
function pegarMissao(level)
    print("Pegando missão para nível " .. tostring(level))
    -- Aqui você pode usar: game.ReplicatedStorage.Missoes:FireServer() ou algo do tipo
end

-- Simula encontrar NPCs do nível
function obterNPCs(level)
    local npcs = {}
    for _, obj in pairs(workspace:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and obj.Name:match("NPC") then
            table.insert(npcs, obj)
        end
    end
    return npcs
end

-- Simula ataque auto click estilo combate
function atacarNPC(npc)
    while npc and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 and autoFarmAtivo do
        game:GetService("VirtualInputManager"):SendMouseButtonEvent(0, 0, 0, true, game, 0)
        game:GetService("VirtualInputManager"):SendMouseButtonEvent(0, 0, 0, false, game, 0)
        task.wait(0.1)
    end
end
