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

-- Aba Auto Farm no canto superior esquerdo
local autoFarmAba = Instance.new("Frame", gui)
autoFarmAba.Size = UDim2.new(0, 220, 0, 80)
autoFarmAba.Position = UDim2.new(0, 10, 0, 10)
autoFarmAba.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
autoFarmAba.BorderSizePixel = 0
autoFarmAba.Visible = false

local tituloAutoFarm = Instance.new("TextLabel", autoFarmAba)
tituloAutoFarm.Size = UDim2.new(1, 0, 0, 25)
tituloAutoFarm.Position = UDim2.new(0, 0, 0, 0)
tituloAutoFarm.Text = "Auto Baú"
tituloAutoFarm.TextColor3 = Color3.fromRGB(255, 255, 255)
tituloAutoFarm.Font = Enum.Font.GothamBold
tituloAutoFarm.TextScaled = true
tituloAutoFarm.BackgroundTransparency = 1

-- Toggle estilo bolinha deslizante
_G.AutoBau = false

local toggle = Instance.new("Frame", autoFarmAba)
toggle.Size = UDim2.new(0, 50, 0, 25)
toggle.Position = UDim2.new(1, -60, 0, 40)
toggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
toggle.BorderSizePixel = 0
Instance.new("UICorner", toggle).CornerRadius = UDim.new(1, 0)

local mover = Instance.new("Frame", toggle)
mover.Size = UDim2.new(0, 23, 0, 23)
mover.Position = UDim2.new(0, 1, 0, 1)
mover.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
mover.BorderSizePixel = 0
Instance.new("UICorner", mover).CornerRadius = UDim.new(1, 0)

toggle.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        _G.AutoBau = not _G.AutoBau
        toggle.BackgroundColor3 = _G.AutoBau and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(100, 100, 100)
        mover:TweenPosition(
            _G.AutoBau and UDim2.new(1, -24, 0, 1) or UDim2.new(0, 1, 0, 1),
            Enum.EasingDirection.Out,
            Enum.EasingStyle.Sine,
            0.2,
            true
        )
    end
end)

-- Botão para abrir/fechar a aba Auto Farm
local btnAutoFarm = Instance.new("TextButton", gui)
btnAutoFarm.Size = UDim2.new(0, 50, 0, 25)
btnAutoFarm.Position = UDim2.new(0, 10, 0, 95)
btnAutoFarm.BackgroundColor3 = Color3.fromRGB(90, 0, 150)
btnAutoFarm.Text = "Farm"
btnAutoFarm.TextColor3 = Color3.fromRGB(255, 255, 255)
btnAutoFarm.Font = Enum.Font.GothamBold
btnAutoFarm.TextScaled = true
btnAutoFarm.BorderSizePixel = 0

btnAutoFarm.MouseButton1Click:Connect(function()
    autoFarmAba.Visible = not autoFarmAba.Visible
end)

-- Farm de Baús com segurança
spawn(function()
    while task.wait(1) do
        if _G.AutoBau then
            local char = player.Character or player.CharacterAdded:Wait()
            local hrp = char:WaitForChild("HumanoidRootPart")
            for _, v in pairs(workspace:GetDescendants()) do
                if v:IsA("TouchTransmitter") and v.Parent and (v.Parent.Name == "Chest1" or v.Parent.Name == "Chest2" or v.Parent.Name == "Chest3") then
                    local chest = v.Parent
                    if (hrp.Position - chest.Position).Magnitude > 5 then
                        pcall(function()
                            hrp.CFrame = chest.CFrame + Vector3.new(0, 5, 0)
                            wait(0.5)
                        end)
                    end
                end
            end
        end
    end
end)
