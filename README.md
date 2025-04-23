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

-- Aba Auto Farm
local autoFarmTab = Instance.new("TextButton", main)
autoFarmTab.Size = UDim2.new(1, 0, 0, 50)
autoFarmTab.Position = UDim2.new(0, 0, 0, 40)
autoFarmTab.BackgroundColor3 = Color3.fromRGB(60, 0, 100)
autoFarmTab.Text = "Auto Farm"
autoFarmTab.TextColor3 = Color3.fromRGB(255, 255, 255)
autoFarmTab.Font = Enum.Font.GothamBold
autoFarmTab.TextScaled = true
autoFarmTab.BorderSizePixel = 0

-- Frame para as opções do Auto Farm (inicialmente escondido)
local autoFarmFrame = Instance.new("Frame", main)
autoFarmFrame.Size = UDim2.new(1, -20, 0, 250)
autoFarmFrame.Position = UDim2.new(0, 10, 0, 90)
autoFarmFrame.BackgroundTransparency = 0.5
autoFarmFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
autoFarmFrame.Visible = false
makeDraggable(autoFarmFrame)

-- Função para criar botões dentro da aba Auto Farm
local function createAutoFarmButtons(parent)
    local yPos = 0
    local buttonHeight = 50

    local function createBtn(txt, callback)
        local btn = Instance.new("TextButton", parent)
        btn.Size = UDim2.new(1, 0, 0, buttonHeight)
        btn.Position = UDim2.new(0, 0, 0, yPos)
        btn.BackgroundColor3 = Color3.fromRGB(60, 0, 100)
        btn.BorderSizePixel = 0
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.Font = Enum.Font.GothamBold
        btn.TextScaled = true
        btn.Text = txt

        -- Ao clicar no botão, chama o callback
        btn.MouseButton1Click:Connect(callback)

        -- Atualiza a posição para o próximo botão
        yPos = yPos + buttonHeight + 10  -- Aumenta a posição Y para o próximo botão (com 10px de distância)
    end

    -- Auto Farm Macacos
    createBtn("Auto Farm Macacos", function()
        _G.FarmMonkeys = true
        spawn(function()
            while _G.FarmMonkeys and task.wait() do
                for _, mob in pairs(workspace.Enemies:GetChildren()) do
                    if mob.Name == "Monkey" and mob:FindFirstChild("HumanoidRootPart") and mob.Humanoid.Health > 0 then
                        repeat
                            pcall(function()
                                local hrp = game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart")
                                hrp.CFrame = mob.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)
                                game:GetService("VirtualInputManager"):SendKeyEvent(true, "Z", false, game)
                                game:GetService("VirtualInputManager"):SendKeyEvent(false, "Z", false, game)
                            end)
                            task.wait()
                        until mob.Humanoid.Health <= 0 or not _G.FarmMonkeys
                    end
                end
            end
        end)
    end)

    -- Placeholder para Auto Farm Mundo 1
    createBtn("Auto Farm Mundo 1", function()
        print("Auto Farm Mundo 1 ativado!")
        -- Adicionar código para farm Mundo 1
    end)

    -- Placeholder para Auto Farm Boss
    createBtn("Auto Farm Boss", function()
        print("Auto Farm Boss ativado!")
        -- Adicionar código para farm Boss
    end)
end

-- Chama a função para criar os botões dentro da aba Auto Farm
createAutoFarmButtons(autoFarmFrame)

-- Alternar visibilidade da aba Auto Farm
autoFarmTab.MouseButton1Click:Connect(function()
    autoFarmFrame.Visible = not autoFarmFrame.Visible
end)

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
