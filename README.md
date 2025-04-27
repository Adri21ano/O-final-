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

-- Painel de Configurações (começa invisível)
local configPanel = Instance.new("Frame", main)
configPanel.Size = UDim2.new(0, 150, 0, 200)
configPanel.Position = UDim2.new(0, 10, 0, 50) -- canto superior esquerdo abaixo do título
configPanel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
configPanel.Visible = false
configPanel.BorderSizePixel = 0

-- Botão para abrir/fechar o painel de Configurações
local configButton = Instance.new("TextButton", main)
configButton.Size = UDim2.new(0, 120, 0, 30)
configButton.Position = UDim2.new(0, 10, 0, 10)
configButton.Text = "Configurações"
configButton.BackgroundColor3 = Color3.fromRGB(170, 0, 255)
configButton.TextColor3 = Color3.fromRGB(255, 255, 255)
configButton.Font = Enum.Font.GothamBold
configButton.TextScaled = true
configButton.BorderSizePixel = 0

configButton.MouseButton1Click:Connect(function()
    configPanel.Visible = not configPanel.Visible
end)

task.spawn(function()
    while task.wait(5) do
        -- Atualizar nome do jogador e level
        local level = player:FindFirstChild("Data") and player.Data:FindFirstChild("Level") and player.Data.Level.Value or "???"
        playerStatus.Text = "Level: "..tostring(level)

        -- Atualizar Lua Cheia baseado no FullMoonEvent
        moonStatus.Text = "Lua Cheia em: "..tostring(getNoitesParaLuaCheia()).." noites"

        -- Atualizar tempo para a Fábrica (essa parte é separada, pode ser melhorada)
        local tempoFabrica = game.ReplicatedStorage:FindFirstChild("FactoryTime") and game.ReplicatedStorage.FactoryTime.Value or "?"
        factoryStatus.Text = "Fábrica: "..tostring(tempoFabrica).." min"
    end
end)
