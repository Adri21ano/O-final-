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
local openAutoFarmBtn = Instance.new("TextButton", main)
openAutoFarmBtn.Size = UDim2.new(0, 120, 0, 30)
openAutoFarmBtn.Position = UDim2.new(0, 10, 0, 50)
openAutoFarmBtn.BackgroundColor3 = Color3.fromRGB(100, 0, 150)
openAutoFarmBtn.Text = "Auto Farm"
openAutoFarmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
openAutoFarmBtn.Font = Enum.Font.GothamBold
openAutoFarmBtn.TextScaled = true
openAutoFarmBtn.BorderSizePixel = 0

-- Aba oculta de Auto Farm
local autoFarmFrame = Instance.new("Frame", main)
autoFarmFrame.Size = UDim2.new(1, -20, 0, 180)
autoFarmFrame.Position = UDim2.new(0, 10, 0, 90)
autoFarmFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
autoFarmFrame.BorderSizePixel = 0
autoFarmFrame.Visible = false

local autoFarmTitle = Instance.new("TextLabel", autoFarmFrame)
autoFarmTitle.Size = UDim2.new(1, 0, 0, 30)
autoFarmTitle.Position = UDim2.new(0, 0, 0, 0)
autoFarmTitle.Text = "Auto Farm"
autoFarmTitle.TextColor3 = Color3.fromRGB(200, 200, 200)
autoFarmTitle.Font = Enum.Font.GothamBold
autoFarmTitle.TextScaled = true
autoFarmTitle.BackgroundTransparency = 1

-- Botão de exemplo dentro da aba
local btnFarm = Instance.new("TextButton", autoFarmFrame)
btnFarm.Size = UDim2.new(1, -20, 0, 35)
btnFarm.Position = UDim2.new(0, 10, 0, 50)
btnFarm.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
btnFarm.Text = "Ativar Farm de Macacos"
btnFarm.TextColor3 = Color3.fromRGB(255, 255, 255)
btnFarm.Font = Enum.Font.GothamBold
btnFarm.TextScaled = true
btnFarm.BorderSizePixel = 0

btnFarm.MouseButton1Click:Connect(function()
    print("Farm de Macacos ativado!")
    -- Adicione a função de farm real aqui
end)

-- Mostrar/Ocultar aba ao clicar no botão
openAutoFarmBtn.MouseButton1Click:Connect(function()
    autoFarmFrame.Visible = not autoFarmFrame.Visible
end)
