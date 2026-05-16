local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

local speedMultiplier = 1
local jumpMultiplier = 1
local isMinimized = false

-- BodyVelocity
local bodyVelocity = Instance.new("BodyVelocity")
bodyVelocity.MaxForce = Vector3.new(math.huge, 0, math.huge)
bodyVelocity.Parent = rootPart

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Painel Principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 300, 0, 250)
mainFrame.Position = UDim2.new(0, 20, 0, 20)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.BorderColor3 = Color3.fromRGB(255, 0, 0)
mainFrame.BorderSizePixel = 3
mainFrame.Parent = screenGui

-- Permitir arrastar
local dragging = false
local dragInput
local dragStart
local startPos

mainFrame.InputBegan:Connect(function(input, gameProcessed)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

mainFrame.InputEnded:Connect(function(input, gameProcessed)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input, gameProcessed)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 35)
title.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
title.TextColor3 = Color3.fromRGB(0, 0, 0)
title.TextSize = 16
title.Font = Enum.Font.GothamBold
title.Text = "🔴 MT SCRIPT 🔴"
title.Parent = mainFrame

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 30, 0, 30)
minimizeBtn.Position = UDim2.new(1, -35, 0.5, -15)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
minimizeBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
minimizeBtn.TextSize = 14
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Text = "−"
minimizeBtn.Parent = title

minimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        mainFrame.Size = UDim2.new(0, 300, 0, 35)
        minimizeBtn.Text = "+"
    else
        mainFrame.Size = UDim2.new(0, 300, 0, 250)
        minimizeBtn.Text = "−"
    end
end)

-- Label Velocidade
local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(1, 0, 0, 20)
speedLabel.Position = UDim2.new(0, 10, 0, 40)
speedLabel.BackgroundTransparency = 1
speedLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
speedLabel.TextSize = 12
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextXAlignment = Enum.TextXAlignment.Left
speedLabel.Text = "🚀 Velocidade: 1.0x"
speedLabel.Parent = mainFrame

-- Slider Velocidade
local speedSlider = Instance.new("Frame")
speedSlider.Size = UDim2.new(0, 280, 0, 10)
speedSlider.Position = UDim2.new(0, 10, 0, 65)
speedSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
speedSlider.BorderColor3 = Color3.fromRGB(255, 0, 0)
speedSlider.BorderSizePixel = 1
speedSlider.Parent = mainFrame

local speedFill = Instance.new("Frame")
speedFill.Size = UDim2.new(0, 0, 1, 0)
speedFill.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
speedFill.BorderSizePixel = 0
speedFill.Parent = speedSlider

speedSlider.MouseButton1Down:Connect(function()
    local mousePos = UserInputService:GetMouseLocation().X
    local sliderPos = speedSlider.AbsolutePosition.X
    local sliderSize = speedSlider.AbsoluteSize.X
    local percent = math.clamp((mousePos - sliderPos) / sliderSize, 0, 1)
    speedMultiplier = 1 + (percent * 5)
    speedFill.Size = UDim2.new(percent, 0, 1, 0)
    speedLabel.Text = "🚀 Velocidade: " .. string.format("%.1f", speedMultiplier) .. "x"
end)

-- Label Pulo
local jumpLabel = Instance.new("TextLabel")
jumpLabel.Size = UDim2.new(1, 0, 0, 20)
jumpLabel.Position = UDim2.new(0, 10, 0, 90)
jumpLabel.BackgroundTransparency = 1
jumpLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpLabel.TextSize = 12
jumpLabel.Font = Enum.Font.Gotham
jumpLabel.TextXAlignment = Enum.TextXAlignment.Left
jumpLabel.Text = "⬆️ Pulo: 1.0x"
jumpLabel.Parent = mainFrame

-- Slider Pulo
local jumpSlider = Instance.new("Frame")
jumpSlider.Size = UDim2.new(0, 280, 0, 10)
jumpSlider.Position = UDim2.new(0, 10, 0, 115)
jumpSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
jumpSlider.BorderColor3 = Color3.fromRGB(255, 0, 0)
jumpSlider.BorderSizePixel = 1
jumpSlider.Parent = mainFrame

local jumpFill = Instance.new("Frame")
jumpFill.Size = UDim2.new(0, 0, 1, 0)
jumpFill.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
jumpFill.BorderSizePixel = 0
jumpFill.Parent = jumpSlider

jumpSlider.MouseButton1Down:Connect(function()
    local mousePos = UserInputService:GetMouseLocation().X
    local sliderPos = jumpSlider.AbsolutePosition.X
    local sliderSize = jumpSlider.AbsoluteSize.X
    local percent = math.clamp((mousePos - sliderPos) / sliderSize, 0, 1)
    jumpMultiplier = 1 + (percent * 4)
    jumpFill.Size = UDim2.new(percent, 0, 1, 0)
    jumpLabel.Text = "⬆️ Pulo: " .. string.format("%.1f", jumpMultiplier) .. "x"
end)

-- Info
local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, -20, 0, 60)
infoLabel.Position = UDim2.new(0, 10, 0, 150)
infoLabel.BackgroundTransparency = 1
infoLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
infoLabel.TextSize = 11
infoLabel.Font = Enum.Font.Gotham
infoLabel.TextWrapped = true
infoLabel.Text = "📌 Arraste o painel\n🚀 Velocidade: 1-6x\n⬆️ Pulo: 1-5x\nK = Minimizar"
infoLabel.Parent = mainFrame

-- Atalho K
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.K then
        isMinimized = not isMinimized
        if isMinimized then
            mainFrame.Size = UDim2.new(0, 300, 0, 35)
            minimizeBtn.Text = "+"
        else
            mainFrame.Size = UDim2.new(0, 300, 0, 250)
            minimizeBtn.Text = "−"
        end
    end
end)

-- Loop Principal
RunService.RenderStepped:Connect(function()
    if character and character.Parent then
        humanoid.JumpHeight = 7.2 * jumpMultiplier
        local moveDir = humanoid.MoveDirection
        if moveDir.Magnitude > 0 then
            bodyVelocity.Velocity = moveDir * (16 * speedMultiplier)
        else
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        end
    end
end)

print("✓ MT SCRIPT ATIVADO!")
