-- Início
local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "OMaestro"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

--===[ FUNÇÃO DE ARRASTAR ELEMENTOS ]===--
local function makeDraggable(frame)
    local dragging, dragInput, dragStart, startPos = false

    local function update(input)
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging, dragStart, startPos = true, input.Position, frame.Position
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

--===[ FRAME PRINCIPAL ]===--
local main = Instance.new("Frame")
main.Size = UDim2.new(0, 380, 0, 300)
main.Position = UDim2.new(0.5, -190, 0.5, -150)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Visible = true
main.Parent = gui
makeDraggable(main)

--===[ TÍTULO DO SCRIPTER ]===--
local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "O MAESTRO"
title.BackgroundTransparency = 0
title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(170, 0, 255)
title.TextScaled = true

--===[ BOTÃO PARA ESCONDER/MOSTRAR ]===--
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

icon.MouseButton1Click:Connect(function()
    main.Visible = not main.Visible
end)

--===[ ÁREA PARA EXPANSÕES (ex: botões, abas, opções) ]===--
--[[
Exemplo de botão para futura expansão:

local botao = Instance.new("TextButton", main)
botao.Size = UDim2.new(0, 100, 0, 30)
botao.Position = UDim2.new(0, 20, 0, 60)
botao.Text = "Auto Farm"
botao.BackgroundColor3 = Color3.fromRGB(60, 0, 100)
botao.TextColor3 = Color3.fromRGB(255, 255, 255)
botao.Font = Enum.Font.Gotham
botao.TextScaled = true
botao.MouseButton1Click:Connect(function()
    print("Auto Farm Ativado")
end)
--]]

-- Auto Farm Toggle
local AutoFarm = false

-- Botão: Ativar Auto Farm
local btnAtivar = Instance.new("TextButton", main)
btnAtivar.Size = UDim2.new(0, 160, 0, 35)
btnAtivar.Position = UDim2.new(0, 20, 0, 60)
btnAtivar.Text = "Ativar Auto Farm"
btnAtivar.BackgroundColor3 = Color3.fromRGB(90, 0, 160)
btnAtivar.TextColor3 = Color3.fromRGB(255, 255, 255)
btnAtivar.Font = Enum.Font.GothamBold
btnAtivar.TextScaled = true
btnAtivar.BorderSizePixel = 0

-- Botão: Desativar Auto Farm
local btnDesativar = Instance.new("TextButton", main)
btnDesativar.Size = UDim2.new(0, 160, 0, 35)
btnDesativar.Position = UDim2.new(0, 200, 0, 60)
btnDesativar.Text = "Desativar Auto Farm"
btnDesativar.BackgroundColor3 = Color3.fromRGB(140, 0, 60)
btnDesativar.TextColor3 = Color3.fromRGB(255, 255, 255)
btnDesativar.Font = Enum.Font.GothamBold
btnDesativar.TextScaled = true
btnDesativar.BorderSizePixel = 0

-- Função de ativar haki (buso)
local function ativarHaki()
    pcall(function()
        game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("Buso")
    end)
end

-- Loop de Auto Farm
spawn(function()
    while true do
        wait(0.5)
        if AutoFarm then
            pcall(function()
                ativarHaki()
                local player = game.Players.LocalPlayer
                local char = player.Character or player.CharacterAdded:Wait()
                for i, v in pairs(game.Workspace.Enemies:GetChildren()) do
                    if v:FindFirstChild("Humanoid") and v:FindFirstChild("HumanoidRootPart") and v.Humanoid.Health > 0 then
                        repeat wait()
                            char.HumanoidRootPart.CFrame = v.HumanoidRootPart.CFrame * CFrame.new(0, 10, 0)
                            game:GetService("VirtualInputManager"):SendKeyEvent(true, "Z", false, game)
                        until v.Humanoid.Health <= 0 or not AutoFarm
                        break
                    end
                end
            end)
        end
    end
end)

-- Ações dos botões
btnAtivar.MouseButton1Click:Connect(function()
    AutoFarm = true
end)

btnDesativar.MouseButton1Click:Connect(function()
    AutoFarm = false
end)

-- Variáveis de Controle
local autofarmActive = false
local autofarmLoop

-- Botão para ativar/desativar o AutoFarm
local farmButton = Instance.new("TextButton", main)
farmButton.Size = UDim2.new(0.8, 0, 0, 50)
farmButton.Position = UDim2.new(0.1, 0, 0, 60)
farmButton.BackgroundColor3 = Color3.fromRGB(170, 0, 255)
farmButton.BorderSizePixel = 0
farmButton.Text = "Ativar AutoFarm"
farmButton.Font = Enum.Font.GothamBold
farmButton.TextColor3 = Color3.new(1, 1, 1)
farmButton.TextScaled = true

-- Função de AutoFarm
local function startAutoFarm()
    if autofarmLoop then return end -- Prevê múltiplos loops

    autofarmLoop = task.spawn(function()
        while autofarmActive do
            pcall(function()
                -- Lógica de encontrar o inimigo mais próximo
                local closestEnemy
                local shortestDistance = math.huge
                for _, enemy in pairs(game:GetService("Workspace").Enemies:GetChildren()) do
                    if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                        local distance = (enemy.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
                        if distance < shortestDistance then
                            closestEnemy = enemy
                            shortestDistance = distance
                        end
                    end
                end

                -- Se encontrou um inimigo, move até ele e ataca
                if closestEnemy then
                    player.Character.HumanoidRootPart.CFrame = closestEnemy.HumanoidRootPart.CFrame * CFrame.new(0, 3, 0)
                    -- Simular ataque
                    for _, tool in pairs(player.Backpack:GetChildren()) do
                        if tool:IsA("Tool") then
                            tool.Parent = player.Character
                            tool:Activate()
                        end
                    end
                end
            end)
            task.wait(0.5)
        end
        autofarmLoop = nil
    end)
end

-- Ligar/Desligar AutoFarm
farmButton.MouseButton1Click:Connect(function()
    autofarmActive = not autofarmActive
    farmButton.Text = autofarmActive and "Desativar AutoFarm" or "Ativar AutoFarm"
    if autofarmActive then
        startAutoFarm()
    end
end)
