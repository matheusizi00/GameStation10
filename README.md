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
mainFrame.Size = UDim2.new(0, 300, 0, 260)
mainFrame.Position = UDim2.new(0, 20, 0, 20)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.BorderColor3 = Color3.fromRGB(255, 0, 0)
mainFrame.BorderSizePixel = 3
mainFrame.Parent = screenGui

-- Título (Arrastável)
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
title.TextColor3 = Color3.fromRGB(0, 0, 0)
title.TextSize = 16
title.Font = Enum.Font.GothamBold
title.Text = "🔴 MT SCRIPT 🔴"
title.Parent = mainFrame

-- Variáveis para arrastar painel
local panelDragging = false
local panelDragStartPos = nil
local panelStartPos = nil

title.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        panelDragging = true
        panelDragStartPos = input.Position
        panelStartPos = mainFrame.Position
    end
end)

title.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch then
        panelDragging = false
    end
end)

title.InputChanged:Connect(function(input)
    if panelDragging and input.UserInputType == Enum.UserInputType.Touch then
        local delta = input.Position - panelDragStartPos
        mainFrame.Position = UDim2.new(
            panelStartPos.X.Scale,
            panelStartPos.X.Offset + delta.X,
            panelStartPos.Y.Scale,
            panelStartPos.Y.Offset + delta.Y
        )
    end
end)

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 35, 0, 35)
minimizeBtn.Position = UDim2.new(1, -40, 0.5, -17)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
minimizeBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
minimizeBtn.TextSize = 16
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Text = "−"
minimizeBtn.Parent = title

minimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        mainFrame.Size = UDim2.new(0, 300, 0, 40)
        minimizeBtn.Text = "+"
    else
        mainFrame.Size = UDim2.new(0, 300, 0, 260)
        minimizeBtn.Text = "−"
    end
end)

-- Label Velocidade
local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(1, -20, 0, 20)
speedLabel.Position = UDim2.new(0, 10, 0, 50)
speedLabel.BackgroundTransparency = 1
speedLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
speedLabel.TextSize = 13
speedLabel.Font = Enum.Font.GothamBold
speedLabel.TextXAlignment = Enum.TextXAlignment.Left
speedLabel.Text = "🚀 Velocidade: 1.0x"
speedLabel.Parent = mainFrame

-- Slider Velocidade
local speedSlider = Instance.new("Frame")
speedSlider.Size = UDim2.new(0, 280, 0, 12)
speedSlider.Position = UDim2.new(0, 10, 0, 75)
speedSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
speedSlider.BorderColor3 = Color3.fromRGB(255, 0, 0)
speedSlider.BorderSizePixel = 2
speedSlider.Parent = mainFrame

-- Botão Speed
local speedButton = Instance.new("TextButton")
speedButton.Size = UDim2.new(0, 18, 0, 18)
speedButton.Position = UDim2.new(0, -3, 0.5, -9)
speedButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
speedButton.BorderColor3 = Color3.fromRGB(255, 255, 255)
speedButton.BorderSizePixel = 2
speedButton.Text = ""
speedButton.ZIndex = 100
speedButton.Parent = speedSlider

-- Variável para arrastar speed
local dragSpeedBtn = false

speedButton.MouseButton1Down:Connect(function()
    dragSpeedBtn = true
end)

speedButton.MouseButton1Up:Connect(function()
    dragSpeedBtn = false
end)

-- Label Pulo
local jumpLabel = Instance.new("TextLabel")
jumpLabel.Size = UDim2.new(1, -20, 0, 20)
jumpLabel.Position = UDim2.new(0, 10, 0, 105)
jumpLabel.BackgroundTransparency = 1
jumpLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpLabel.TextSize = 13
jumpLabel.Font = Enum.Font.GothamBold
jumpLabel.TextXAlignment = Enum.TextXAlignment.Left
jumpLabel.Text = "⬆️ Pulo: 1.0x"
jumpLabel.Parent = mainFrame

-- Slider Pulo
local jumpSlider = Instance.new("Frame")
jumpSlider.Size = UDim2.new(0, 280, 0, 12)
jumpSlider.Position = UDim2.new(0, 10, 0, 130)
jumpSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
jumpSlider.BorderColor3 = Color3.fromRGB(255, 0, 0)
jumpSlider.BorderSizePixel = 2
jumpSlider.Parent = mainFrame

-- Botão Jump
local jumpButton = Instance.new("TextButton")
jumpButton.Size = UDim2.new(0, 18, 0, 18)
jumpButton.Position = UDim2.new(0, -3, 0.5, -9)
jumpButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
jumpButton.BorderColor3 = Color3.fromRGB(255, 255, 255)
jumpButton.BorderSizePixel = 2
jumpButton.Text = ""
jumpButton.ZIndex = 100
jumpButton.Parent = jumpSlider

-- Variável para arrastar jump
local dragJumpBtn = false

jumpButton.MouseButton1Down:Connect(function()
    dragJumpBtn = true
end)

jumpButton.MouseButton1Up:Connect(function()
    dragJumpBtn = false
end)

-- Update Speed e Jump
RunService.RenderStepped:Connect(function()
    -- VELOCIDADE
    if dragSpeedBtn then
        local mouseX = UserInputService:GetMouseLocation().X
        local sliderX = speedSlider.AbsolutePosition.X
        local sliderW = speedSlider.AbsoluteSize.X
        local percent = math.clamp((mouseX - sliderX) / sliderW, 0, 1)
        
        speedMultiplier = 1 + (percent * 5)
        speedButton.Position = UDim2.new(percent, -9, 0.5, -9)
        speedLabel.Text = "🚀 Velocidade: " .. string.format("%.1f", speedMultiplier) .. "x"
    end
    
    -- PULO
    if dragJumpBtn then
        local mouseX = UserInputService:GetMouseLocation().X
        local sliderX = jumpSlider.AbsolutePosition.X
        local sliderW = jumpSlider.AbsoluteSize.X
        local percent = math.clamp((mouseX - sliderX) / sliderW, 0, 1)
        
        jumpMultiplier = 1 + (percent * 4)
        jumpButton.Position = UDim2.new(percent, -9, 0.5, -9)
        jumpLabel.Text = "⬆️ Pulo: " .. string.format("%.1f", jumpMultiplier) .. "x"
    end
    
    -- APLICAR VELOCIDADE E PULO NO JOGO
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

-- Info
local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, -20, 0, 60)
infoLabel.Position = UDim2.new(0, 10, 0, 160)
infoLabel.BackgroundTransparency = 1
infoLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
infoLabel.TextSize = 11
infoLabel.Font = Enum.Font.Gotham
infoLabel.TextWrapped = true
infoLabel.Text = "✓ Toque e arraste o título\n✓ Toque e arraste os botões\n✓ Pressione K para minimizar"
infoLabel.Parent = mainFrame

-- Atalho K
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.K then
        isMinimized = not isMinimized
        if isMinimized then
            mainFrame.Size = UDim2.new(0, 300, 0, 40)
            minimizeBtn.Text = "+"
        else
            mainFrame.Size = UDim2.new(0, 300, 0, 260)
            minimizeBtn.Text = "−"
        end
    end
end)

print("✅ MT SCRIPT CARREGADO!")
print("👆 Arraste os botões de velocidade e pulo!")
