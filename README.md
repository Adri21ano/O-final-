local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "OMaestroGUI"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 400, 0, 250)
frame.Position = UDim2.new(0.5, -200, 0.5, -125)
frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
frame.BorderSizePixel = 0
frame.AnchorPoint = Vector2.new(0.5, 0.5)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 50)
title.BackgroundTransparency = 1
title.Text = "O MAESTRO"
title.TextColor3 = Color3.fromRGB(138, 43, 226) -- Roxo bonito
title.Font = Enum.Font.GothamBold
title.TextScaled = true

local line = Instance.new("Frame", frame)
line.Size = UDim2.new(1, -40, 0, 2)
line.Position = UDim2.new(0, 20, 0, 55)
line.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
line.BorderSizePixel = 0

local icon = Instance.new("TextButton", gui)
icon.Size = UDim2.new(0, 50, 0, 50)
icon.Position = UDim2.new(0, 10, 1, -60)
icon.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
icon.Text = "7"
icon.TextColor3 = Color3.fromRGB(255, 255, 255)
icon.Font = Enum.Font.GothamBold
icon.TextScaled = true
icon.BorderSizePixel = 0
icon.AutoButtonColor = true

icon.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
end)
