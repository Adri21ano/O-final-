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

-- Aba Configurações
local configTab = Instance.new("Frame", main)
configTab.Size = UDim2.new(1, -20, 0, 120)
configTab.Position = UDim2.new(0, 10, 0, 60)
configTab.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
configTab.BorderSizePixel = 0

local configTitle = Instance.new("TextLabel", configTab)
configTitle.Size = UDim2.new(1, 0, 0, 30)
configTitle.Position = UDim2.new(0, 0, 0, 0)
configTitle.Text = "Configurações"
configTitle.TextColor3 = Color3.fromRGB(200, 200, 200)
configTitle.Font = Enum.Font.GothamBold
configTitle.TextScaled = true
configTitle.BackgroundTransparency = 1

-- Auto Clicker Label
local acLabel = Instance.new("TextLabel", configTab)
acLabel.Size = UDim2.new(0, 180, 0, 25)
acLabel.Position = UDim2.new(0, 10, 0, 40)
acLabel.Text = "Auto Clicker:"
acLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
acLabel.Font = Enum.Font.Gotham
acLabel.TextScaled = true
acLabel.BackgroundTransparency = 1
acLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Auto Clicker Toggle
local clickerActive = false
local autoClickConnection

-- Botão Ativar
local btnAtivar = Instance.new("TextButton", configTab)
btnAtivar.Size = UDim2.new(0, 70, 0, 25)
btnAtivar.Position = UDim2.new(0, 200, 0, 40)
btnAtivar.Text = "Ativar"
btnAtivar.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
btnAtivar.Font = Enum.Font.GothamBold
btnAtivar.TextColor3 = Color3.fromRGB(255, 255, 255)
btnAtivar.TextScaled = true

-- Botão Desativar
local btnDesativar = Instance.new("TextButton", configTab)
btnDesativar.Size = UDim2.new(0, 70, 0, 25)
btnDesativar.Position = UDim2.new(0, 280, 0, 40)
btnDesativar.Text = "Desativar"
btnDesativar.BackgroundColor3 = Color3.fromRGB(170, 0, 0)
btnDesativar.Font = Enum.Font.GothamBold
btnDesativar.TextColor3 = Color3.fromRGB(255, 255, 255)
btnDesativar.TextScaled = true

-- Função de Auto Clicker
local function toggleClicker(on)
    if on then
        if not autoClickConnection then
            autoClickConnection = game:GetService("RunService").RenderStepped:Connect(function()
                if clickerActive and game:GetService("UserInputService"):IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                    mouse1click()
                end
            end)
        end
        clickerActive = true
    else
        clickerActive = false
        if autoClickConnection then
            autoClickConnection:Disconnect()
            autoClickConnection = nil
        end
    end
end

btnAtivar.MouseButton1Click:Connect(function()
    toggleClicker(true)
end)

btnDesativar.MouseButton1Click:Connect(function()
    toggleClicker(false)
end)
