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

-- Serviços
local TweenService = game:GetService("TweenService")
local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local humRoot = char:WaitForChild("HumanoidRootPart")

-- Variáveis
local autofarm = false
local mobName = "Bandit" -- << Nome do mob que você quer farmar

-- Função para pegar missão
local function getQuest()
    pcall(function()
        if not player.PlayerGui:FindFirstChild("Quest") then
            for _, npc in pairs(game:GetService("Workspace").NPCs:GetChildren()) do
                if npc:FindFirstChild("Head") and (npc.Head.Position - humRoot.Position).Magnitude < 100 then
                    humRoot.CFrame = npc.Head.CFrame * CFrame.new(0, 3, 0)
                    task.wait(1)

                    -- Simula click no ProximityPrompt
                    fireproximityprompt(npc:FindFirstChildOfClass("ProximityPrompt"))
                    task.wait(1)

                    -- Seleciona a primeira opção da quest
                    local questGui = player.PlayerGui:WaitForChild("QuestSelect", 3)
                    if questGui then
                        local button = questGui:FindFirstChild("Container"):FindFirstChild("QuestButton1")
                        if button then
                            button.Size = UDim2.new(0, 0, 0, 0) -- Bypassa para não travar
                            button.Visible = true
                            fireclickdetector(button:FindFirstChildWhichIsA("ClickDetector"))
                        end
                    end
                end
            end
        end
    end)
end

-- Função para mover o player até o inimigo suavemente
local function toTarget(targetPos)
    local tween = TweenService:Create(humRoot, TweenInfo.new((humRoot.Position - targetPos).Magnitude/300, Enum.EasingStyle.Linear), {CFrame = CFrame.new(targetPos)})
    tween:Play()
    tween.Completed:Wait()
end

-- Função para atacar inimigos
local function attackEnemy(enemy)
    pcall(function()
        if not enemy:FindFirstChild("Humanoid") then return end
        repeat
            -- Equipar arma
            for _, tool in pairs(player.Backpack:GetChildren()) do
                if tool:IsA("Tool") then
                    tool.Parent = player.Character
                    tool:Activate()
                end
            end

            -- Mover até inimigo
            if (humRoot.Position - enemy.HumanoidRootPart.Position).Magnitude > 5 then
                toTarget(enemy.HumanoidRootPart.Position + Vector3.new(0, 3, 0))
            end

            task.wait(0.2)
        until enemy.Humanoid.Health <= 0 or not autofarm
    end)
end

-- Sistema principal do AutoFarm
spawn(function()
    while task.wait(0.2) do
        if autofarm then
            getQuest()

            local closest
            local shortest = math.huge
            for _, enemy in pairs(game:GetService("Workspace").Enemies:GetChildren()) do
                if enemy.Name == mobName and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                    local distance = (enemy.HumanoidRootPart.Position - humRoot.Position).Magnitude
                    if distance < shortest then
                        shortest = distance
                        closest = enemy
                    end
                end
            end

            if closest then
                attackEnemy(closest)
            end
        end
    end
end)

-- Botão exemplo para ativar/desativar o AutoFarm
local toggleButton = Instance.new("TextButton", main)
toggleButton.Size = UDim2.new(0.8, 0, 0, 50)
toggleButton.Position = UDim2.new(0.1, 0, 0, 130)
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
toggleButton.Text = "Ativar AutoFarm"
toggleButton.TextColor3 = Color3.new(1,1,1)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextScaled = true
toggleButton.BorderSizePixel = 0

toggleButton.MouseButton1Click:Connect(function()
    autofarm = not autofarm
    toggleButton.Text = autofarm and "Desativar AutoFarm" or "Ativar AutoFarm"
end)
