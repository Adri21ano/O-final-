local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "OMaestro"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

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

local main = Instance.new("Frame")
main.Size = UDim2.new(0, 380, 0, 300)
main.Position = UDim2.new(0.5, -190, 0.5, -150)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Visible = true
main.Parent = gui
makeDraggable(main)

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "O MAESTRO"
title.BackgroundTransparency = 0
title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(170, 0, 255)
title.TextScaled = true

local autoFarmTab = Instance.new("TextButton", main)
autoFarmTab.Size = UDim2.new(1, 0, 0, 50)
autoFarmTab.Position = UDim2.new(0, 0, 0, 40)
autoFarmTab.BackgroundColor3 = Color3.fromRGB(60, 0, 100)
autoFarmTab.Text = "Auto Farm"
autoFarmTab.TextColor3 = Color3.fromRGB(255, 255, 255)
autoFarmTab.Font = Enum.Font.GothamBold
autoFarmTab.TextScaled = true
autoFarmTab.BorderSizePixel = 0

local autoFarmFrame = Instance.new("Frame", main)
autoFarmFrame.Size = UDim2.new(1, -20, 0, 250)
autoFarmFrame.Position = UDim2.new(0, 10, 0, 90)
autoFarmFrame.BackgroundTransparency = 0.5
autoFarmFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
autoFarmFrame.Visible = false
makeDraggable(autoFarmFrame)
