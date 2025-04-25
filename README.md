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

-- Aba Auto Farm no canto superior esquerdo do painel
local autoFarmFrame = Instance.new("Frame", main)
autoFarmFrame.Size = UDim2.new(0, 220, 0, 110)
autoFarmFrame.Position = UDim2.new(0, 10, 0, 10)
autoFarmFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
autoFarmFrame.Visible = false

-- Título da aba
local autoFarmTitle = Instance.new("TextLabel", autoFarmFrame)
autoFarmTitle.Size = UDim2.new(1, 0, 0, 30)
autoFarmTitle.Text = "Auto Farm Nível"
autoFarmTitle.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
autoFarmTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
autoFarmTitle.Font = Enum.Font.GothamBold
autoFarmTitle.TextScaled = true

-- Botão para mostrar/esconder a aba
local toggleAutoFarmBtn = Instance.new("TextButton", main)
toggleAutoFarmBtn.Size = UDim2.new(0, 120, 0, 30)
toggleAutoFarmBtn.Position = UDim2.new(0, 10, 0, 60)
toggleAutoFarmBtn.BackgroundColor3 = Color3.fromRGB(120, 0, 180)
toggleAutoFarmBtn.Text = "Auto Farm"
toggleAutoFarmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleAutoFarmBtn.Font = Enum.Font.Gotham
toggleAutoFarmBtn.TextScaled = true

toggleAutoFarmBtn.MouseButton1Click:Connect(function()
    autoFarmFrame.Visible = not autoFarmFrame.Visible
end)

-- Botão deslizante de ativar/desativar Auto Farm
local toggleFarm = Instance.new("TextButton", autoFarmFrame)
toggleFarm.Size = UDim2.new(0, 200, 0, 40)
toggleFarm.Position = UDim2.new(0, 10, 0, 60)
toggleFarm.BackgroundColor3 = Color3.fromRGB(170, 0, 0)
toggleFarm.Text = "Auto Farm [DESLIGADO]"
toggleFarm.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleFarm.Font = Enum.Font.Gotham
toggleFarm.TextScaled = true

-- Lógica do Auto Farm
local autoFarmAtivo = false

local function iniciarAutoFarm()
    local player = game.Players.LocalPlayer
    local enemiesFolder = workspace:WaitForChild("Enemies")
    local quests = workspace:FindFirstChild("QuestNPCs")

    spawn(function()
        while autoFarmAtivo do
            local level = player:FindFirstChild("Data") and player.Data:FindFirstChild("Level") and player.Data.Level.Value or 1
            local questName, enemyName

            -- Determinar missão e inimigo baseado no nível (exemplo simplificado)
            if level < 10 then
                questName = "BanditQuest1"
                enemyName = "Bandit"
            elseif level < 30 then
                questName = "BanditQuest2"
                enemyName = "Brute"
            elseif level < 60 then
                questName = "MonkeyQuest"
                enemyName = "Monkey"
            elseif level < 90 then
                questName = "GorillaQuest"
                enemyName = "Gorilla"
            else
                questName = "DefaultQuest"
                enemyName = "DefaultEnemy"
            end

            -- Aceitar missão
            if quests and quests:FindFirstChild(questName) then
                local npc = quests[questName]
                if npc:FindFirstChild("Head") and npc.Head:FindFirstChild("ClickDetector") then
                    fireclickdetector(npc.Head.ClickDetector)
                    wait(1)
                end
            end

            -- Procurar e atacar inimigos
            for _, npc in pairs(enemiesFolder:GetChildren()) do
                if npc.Name == enemyName and npc:FindFirstChild("HumanoidRootPart") and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 then
                    local character = player.Character or player.CharacterAdded:Wait()
                    local root = character:WaitForChild("HumanoidRootPart")
                    local tool = character:FindFirstChildOfClass("Tool")

                    repeat
                        root.CFrame = npc.HumanoidRootPart.CFrame * CFrame.new(0, 0, 2)
                        if tool then
                            tool:Activate() -- Ataca com espada ou estilo de luta
                        end
                        wait(0.25)
                    until npc.Humanoid.Health <= 0 or not autoFarmAtivo
                end
            end

            wait(1)
        end
    end)
end

-- Alternar o estado do Auto Farm
toggleFarm.MouseButton1Click:Connect(function()
    autoFarmAtivo = not autoFarmAtivo
    if autoFarmAtivo then
        toggleFarm.Text = "Auto Farm [LIGADO]"
        toggleFarm.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
        iniciarAutoFarm()
    else
        toggleFarm.Text = "Auto Farm [DESLIGADO]"
        toggleFarm.BackgroundColor3 = Color3.fromRGB(170, 0, 0)
    end
end)
