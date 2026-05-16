local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- Variáveis
local speedMultiplier = 1
local jumpMultiplier = 1
local isMinimized = false
local isDraggingPanel = false
local dragStartPos = nil
local panelStartPos = nil

-- BodyVelocity para velocidade
local bodyVelocity = Instance.new("BodyVelocity")
bodyVelocity.MaxForce = Vector3.new(math.huge, 0, math.huge)
bodyVelocity.Velocity = Vector3.new(0, 0, 0)
bodyVelocity.Parent = rootPart

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MTScript"
screenGui.ResetOnSpawn = false

local playerGui = player:FindFirstChild("PlayerGui")
if not playerGui then
    playerGui = Instance.new("Folder")
    playerGui.Name = "PlayerGui"
    playerGui.Parent = player
end
screenGui.Parent = playerGui

-- Frame Principal (Painel Arrastável)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 320, 0, 300)
mainFrame.Position = UDim2.new(0, 10, 0, 10)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.BorderColor3 = Color3.fromRGB(255, 0, 0)
mainFrame.BorderSizePixel = 3
mainFrame.Parent = screenGui

-- Título (Area arrastável)
local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Size = UDim2.new(1, 0, 0, 45)
titleBar.Position = UDim2.new(0, 0, 0, 0)
titleBar.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Name = "Title"
title.Size = UDim2.new(0.7, 0, 1, 0)
title.Position = UDim2.new(0, 10, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(0, 0, 0)
title.TextSize = 16
title.Font = Enum.Font.GothamBold
title.Text = "🔴 MT SCRIPT 🔴"
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = titleBar

-- Botão Minimizar
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Name = "MinimizeBtn"
minimizeBtn.Size = UDim2.new(0, 35, 0, 35)
minimizeBtn.Position = UDim2.new(1, -75, 0, 5)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
minimizeBtn.BorderColor3 = Color3.fromRGB(255, 255, 255)
minimizeBtn.BorderSizePixel = 2
minimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeBtn.TextSize = 16
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Text = "−"
minimizeBtn.Parent = titleBar

-- Botão Fechar
local closeBtn = Instance.new("TextButton")
closeBtn.Name = "CloseBtn"
closeBtn.Size = UDim2.new(0, 35, 0, 35)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
closeBtn.BorderColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.BorderSizePixel = 2
closeBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
closeBtn.TextSize = 16
closeBtn.Font = Enum.Font.GothamBold
closeBtn.Text = "✕"
closeBtn.Parent = titleBar

-- Container do Conteúdo
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Size = UDim2.new(1, 0, 1, -45)
contentFrame.Position = UDim2.new(0, 0, 0, 45)
contentFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
contentFrame.BorderSizePixel = 0
contentFrame.Parent = mainFrame

-- Label Velocidade
local speedLabel = Instance.new("TextLabel")
speedLabel.Name = "SpeedLabel"
speedLabel.Size = UDim2.new(1, -20, 0, 25)
speedLabel.Position = UDim2.new(0, 10, 0, 10)
speedLabel.BackgroundTransparency = 1
speedLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
speedLabel.TextSize = 14
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextXAlignment = Enum.TextXAlignment.Left
speedLabel.Text = "🚀 Velocidade: 1x"
speedLabel.Parent = contentFrame

-- Slider Velocidade
local speedSlider = Instance.new("Frame")
speedSlider.Name = "SpeedSlider"
speedSlider.Size = UDim2.new(1, -20, 0, 15)
speedSlider.Position = UDim2.new(0, 10, 0, 40)
speedSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
speedSlider.BorderColor3 = Color3.fromRGB(255, 0, 0)
speedSlider.BorderSizePixel = 2
speedSlider.Parent = contentFrame

local speedButton = Instance.new("TextButton")
speedButton.Name = "SpeedButton"
speedButton.Size = UDim2.new(0, 15, 0, 15)
speedButton.Position = UDim2.new(0, 0, 0, 0)
speedButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
speedButton.BorderSizePixel = 0
speedButton.Text = ""
speedButton.Parent = speedSlider

-- Label Pulo
local jumpLabel = Instance.new("TextLabel")
jumpLabel.Name = "JumpLabel"
jumpLabel.Size = UDim2.new(1, -20, 0, 25)
jumpLabel.Position = UDim2.new(0, 10, 0, 80)
jumpLabel.BackgroundTransparency = 1
jumpLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpLabel.TextSize = 14
jumpLabel.Font = Enum.Font.Gotham
jumpLabel.TextXAlignment = Enum.TextXAlignment.Left
jumpLabel.Text = "⬆️ Pulo: 1x"
jumpLabel.Parent = contentFrame

-- Slider Pulo
local jumpSlider = Instance.new("Frame")
jumpSlider.Name = "JumpSlider"
jumpSlider.Size = UDim2.new(1, -20, 0, 15)
jumpSlider.Position = UDim2.new(0, 10, 0, 110)
jumpSlider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
jumpSlider.BorderColor3 = Color3.fromRGB(255, 0, 0)
jumpSlider.BorderSizePixel = 2
jumpSlider.Parent = contentFrame

local jumpButton = Instance.new("TextButton")
jumpButton.Name = "JumpButton"
jumpButton.Size = UDim2.new(0, 15, 0, 15)
jumpButton.Position = UDim2.new(0, 0, 0, 0)
jumpButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
jumpButton.BorderSizePixel = 0
jumpButton.Text = ""
jumpButton.Parent = jumpSlider

-- Label de Posição
local positionLabel = Instance.new("TextLabel")
positionLabel.Name = "PositionLabel"
positionLabel.Size = UDim2.new(1, -20, 0, 25)
positionLabel.Position = UDim2.new(0, 10, 0, 150)
positionLabel.BackgroundTransparency = 1
positionLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
positionLabel.TextSize = 12
positionLabel.Font = Enum.Font.Gotham
positionLabel.TextXAlignment = Enum.TextXAlignment.Left
positionLabel.Text = "📍 Arraste o painel para mover"
positionLabel.Parent = contentFrame

-- Status
local statusLabel = Instance.new("TextLabel")
statusLabel.Name = "StatusLabel"
statusLabel.Size = UDim2.new(1, -20, 0, 30)
statusLabel.Position = UDim2.new(0, 10, 1, -40)
statusLabel.BackgroundTransparency = 1
statusLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
statusLabel.TextSize = 11
statusLabel.Font = Enum.Font.Gotham
statusLabel.Text = "✓ Ativo | Pressione K para minimizar"
statusLabel.Parent = contentFrame

-- Funções de atualização
local function updateSpeed()
    local percentage = (speedMultiplier - 1) / 5
    speedButton.Position = UDim2.new(percentage, -7.5, 0, 0)
    speedLabel.Text = "🚀 Velocidade: " .. string.format("%.1f", speedMultiplier) .. "x"
end

local function updateJump()
    local percentage = (jumpMultiplier - 1) / 4
    jumpButton.Position = UDim2.new(percentage, -7.5, 0, 0)
    jumpLabel.Text = "⬆️ Pulo: " .. string.format("%.1f", jumpMultiplier) .. "x"
end

-- Eventos dos Sliders
local draggingSpeed = false
local draggingJump = false

speedButton.MouseButton1Down:Connect(function()
    draggingSpeed = true
end)

jumpButton.MouseButton1Down:Connect(function()
    draggingJump = true
end)

UserInputService.InputEnded:Connect(function(input, gameProcessed)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        draggingSpeed = false
        draggingJump = false
        isDraggingPanel = false
    end
end)

UserInputService.InputChanged:Connect(function(input, gameProcessed)
    if draggingSpeed then
        local sliderPos = speedSlider.AbsolutePosition.X
        local sliderSize = speedSlider.AbsoluteSize.X
        local mousePos = UserInputService:GetMouseLocation().X
        local percentage = math.clamp((mousePos - sliderPos) / sliderSize, 0, 1)
        speedMultiplier = 1 + (percentage * 5)
        updateSpeed()
    elseif draggingJump then
        local sliderPos = jumpSlider.AbsolutePosition.X
        local sliderSize = jumpSlider.AbsoluteSize.X
        local mousePos = UserInputService:GetMouseLocation().X
        local percentage = math.clamp((mousePos - sliderPos) / sliderSize, 0, 1)
        jumpMultiplier = 1 + (percentage * 4)
        updateJump()
    elseif isDraggingPanel then
        local mousePos = UserInputService:GetMouseLocation()
        local delta = Vector2.new(
            mousePos.X - dragStartPos.X,
            mousePos.Y - dragStartPos.Y
        )
        mainFrame.Position = UDim2.new(
            0,
            panelStartPos.X + delta.X,
            0,
            panelStartPos.Y + delta.Y
        )
    end
end)

-- Arrastar Painel
titleBar.MouseButton1Down:Connect(function()
    isDraggingPanel = true
    dragStartPos = UserInputService:GetMouseLocation()
    panelStartPos = Vector2.new(mainFrame.AbsolutePosition.X, mainFrame.AbsolutePosition.Y)
end)

-- Botão Minimizar
minimizeBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        mainFrame.Size = UDim2.new(0, 320, 0, 45)
        minimizeBtn.Text = "+"
    else
        mainFrame.Size = UDim2.new(0, 320, 0, 300)
        minimizeBtn.Text = "−"
    end
end)

-- Botão Fechar
closeBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Atalho K para minimizar
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.K then
        isMinimized = not isMinimized
        if isMinimized then
            mainFrame.Size = UDim2.new(0, 320, 0, 45)
            minimizeBtn.Text = "+"
        else
            mainFrame.Size = UDim2.new(0, 320, 0, 300)
            minimizeBtn.Text = "−"
        end
    end
end)

-- Loop Principal - VELOCIDADE E PULO
RunService.RenderStepped:Connect(function()
    if not character or not character.Parent then return end
    
    -- Super Pulo
    humanoid.JumpHeight = 7.2 * jumpMultiplier
    
    -- Super Velocidade
    local moveDir = humanoid.MoveDirection
    if moveDir.Magnitude > 0 then
        bodyVelocity.Velocity = moveDir * (16 * speedMultiplier)
    else
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    end
end)

-- Inicializar sliders
updateSpeed()
updateJump()

print("✓ MT SCRIPT CARREGADO COM SUCESSO!")
print("📍 Controles:")
print("   🚀 Arraste o slider de velocidade (1x a 6x)")
print("   ⬆️ Arraste o slider de pulo (1x a 5x)")
print("   📌 Arraste a barra vermelha para mover o painel")
print("   K = Minimizar/Abrir painel")
print("   ✕ = Fechar painel")
