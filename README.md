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

-- Botão para abrir/fechar a aba Auto Farm
local openFarmBtn = Instance.new("TextButton", main)
openFarmBtn.Size = UDim2.new(0, 120, 0, 30)
openFarmBtn.Position = UDim2.new(0, 10, 0, 50)
openFarmBtn.BackgroundColor3 = Color3.fromRGB(90, 0, 120)
openFarmBtn.Text = "Auto Farm"
openFarmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
openFarmBtn.Font = Enum.Font.GothamBold
openFarmBtn.TextScaled = true
openFarmBtn.BorderSizePixel = 0

-- Frame da aba Auto Farm
local farmAba = Instance.new("Frame", main)
farmAba.Size = UDim2.new(1, -20, 0, 200)
farmAba.Position = UDim2.new(0, 10, 0, 90)
farmAba.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
farmAba.BorderSizePixel = 0
farmAba.Visible = false

local toggles = {
    {label = "Farm de Level", var = "_G.FarmLevel"},
    {label = "Farm de Espada", var = "_G.FarmEspada"},
    {label = "Farm de Arma", var = "_G.FarmArma"},
    {label = "Farm de Itens", var = "_G.FarmItens"},
}

for i, t in ipairs(toggles) do
    _G[t.var] = false

    local holder = Instance.new("Frame", farmAba)
    holder.Size = UDim2.new(1, -20, 0, 30)
    holder.Position = UDim2.new(0, 10, 0, 10 + (i - 1) * 40)
    holder.BackgroundTransparency = 1

    local label = Instance.new("TextLabel", holder)
    label.Size = UDim2.new(0.7, 0, 1, 0)
    label.Text = t.label
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.Font = Enum.Font.Gotham
    label.TextScaled = true
    label.BackgroundTransparency = 1
    label.TextXAlignment = Enum.TextXAlignment.Left

    local toggle = Instance.new("TextButton", holder)
    toggle.Size = UDim2.new(0, 30, 0, 30)
    toggle.Position = UDim2.new(1, -35, 0, 0)
    toggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    toggle.Text = ""
    toggle.BorderSizePixel = 0
    Instance.new("UICorner", toggle).CornerRadius = UDim.new(1, 0)

    local function atualizar()
        toggle.BackgroundColor3 = _G[t.var] and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(100, 100, 100)
    end

    toggle.MouseButton1Click:Connect(function()
        _G[t.var] = not _G[t.var]
        atualizar()
        print(t.label .. ": " .. tostring(_G[t.var]))
    end)

    atualizar()
end

-- Abrir/Fechar a aba
openFarmBtn.MouseButton1Click:Connect(function()
    farmAba.Visible = not farmAba.Visible
end)

-- Auto clicker suave ativado com Farm de Level
spawn(function()
    while task.wait(0.3) do
        if _G.FarmLevel then
            mouse1click()
        end
    end
end)

