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

-- Aba de Auto Farm (canto superior direito)
local autoFarmGui = Instance.new("Frame", gui)
autoFarmGui.Size = UDim2.new(0, 200, 0, 80)
autoFarmGui.Position = UDim2.new(1, -210, 0, 10)
autoFarmGui.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
autoFarmGui.BorderSizePixel = 0

local title = Instance.new("TextLabel", autoFarmGui)
title.Size = UDim2.new(1, 0, 0, 25)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = "Auto Farm"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.BackgroundTransparency = 1

-- Toggle Auto Baú (bolinha deslizante)
_G.AutoBau = false

local toggleLabel = Instance.new("TextLabel", autoFarmGui)
toggleLabel.Size = UDim2.new(0.6, 0, 0, 30)
toggleLabel.Position = UDim2.new(0, 10, 0, 35)
toggleLabel.Text = "Auto Baú"
toggleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleLabel.Font = Enum.Font.Gotham
toggleLabel.TextScaled = true
toggleLabel.BackgroundTransparency = 1
toggleLabel.TextXAlignment = Enum.TextXAlignment.Left

local toggle = Instance.new("Frame", autoFarmGui)
toggle.Size = UDim2.new(0, 50, 0, 25)
toggle.Position = UDim2.new(1, -60, 0, 37)
toggle.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
toggle.BorderSizePixel = 0
toggle.Name = "AutoBauToggle"
Instance.new("UICorner", toggle).CornerRadius = UDim.new(1, 0)

local circle = Instance.new("Frame", toggle)
circle.Size = UDim2.new(0, 23, 0, 23)
circle.Position = UDim2.new(0, 1, 0, 1)
circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
circle.BorderSizePixel = 0
circle.Name = "Mover"
Instance.new("UICorner", circle).CornerRadius = UDim.new(1, 0)

toggle.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        _G.AutoBau = not _G.AutoBau
        toggle.BackgroundColor3 = _G.AutoBau and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(100, 100, 100)
        circle:TweenPosition(
            _G.AutoBau and UDim2.new(1, -24, 0, 1) or UDim2.new(0, 1, 0, 1),
            Enum.EasingDirection.Out,
            Enum.EasingStyle.Sine,
            0.2,
            true
        )
    end
end)

-- Função de Auto Baú (teleporta suavemente)
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
