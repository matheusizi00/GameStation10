local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- Variáveis de Controle
local settings = {
    jumpEnabled = false,
    speedEnabled = false,
    espEnabled = false,
    jumpPower = 50,
    speedMultiplier = 16,
    panelOpen = true
}

local espMarkers = {}

-- Criar GUI Principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MT_SCRIPT_GUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Frame Principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 300, 0, 400)
mainFrame.Position = UDim2.new(0, 10, 0, 10)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

-- Título
local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "Title"
titleLabel.Size = UDim2.new(1, 0, 0, 40)
titleLabel.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
titleLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
titleLabel.TextSize = 16
titleLabel.Font = Enum.Font.GothamBold
titleLabel.Text = "🔴 MT SCRIPT"
titleLabel.BorderSizePixel = 0
titleLabel.Parent = mainFrame

-- Botão Fechar/Abrir
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleBtn"
toggleButton.Size = UDim2.new(0, 30, 0, 40)
toggleButton.Position = UDim2.new(1, -35, 0, 0)
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.TextSize = 18
toggleButton.Font = Enum.Font.GothamBold
toggleButton.Text = "-"
toggleButton.BorderSizePixel = 0
toggleButton.Parent = mainFrame

-- ScrollFrame
local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Name = "ScrollFrame"
scrollFrame.Size = UDim2.new(1, -10, 1, -50)
scrollFrame.Position = UDim2.new(0, 5, 0, 45)
scrollFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
scrollFrame.BorderSizePixel = 0
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 600)
scrollFrame.Parent = mainFrame

-- Função para criar botões ON/OFF
local function createToggleButton(parent, name, y, callback)
    local container = Instance.new("Frame")
    container.Size = UDim2.new(1, 0, 0, 50)
    container.Position = UDim2.new(0, 0, 0, y)
    container.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    container.BorderSizePixel = 0
    container.Parent = parent
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.6, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextSize = 14
    label.Font = Enum.Font.Gotham
    label.Text = name
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = container
    
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0.35, -5, 0.8, 0)
    button.Position = UDim2.new(0.6, 5, 0.1, 0)
    button.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = 12
    button.Font = Enum.Font.GothamBold
    button.Text = "OFF"
    button.BorderSizePixel = 0
    button.Parent = container
    
    button.MouseButton1Click:Connect(function()
        callback(button)
    end)
    
    return button
end

-- Função para criar sliders
local function createSlider(parent, name, y, minValue, maxValue, initialValue, callback)
    local container = Instance.new("Frame")
    container.Size = UDim2.new(1, 0, 0, 60)
    container.Position = UDim2.new(0, 0, 0, y)
    container.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    container.BorderSizePixel = 0
    container.Parent = parent
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 0, 20)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextSize = 12
    label.Font = Enum.Font.Gotham
    label.Text = name .. ": " .. initialValue
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = container
    
    local sliderBg = Instance.new("Frame")
    sliderBg.Size = UDim2.new(0.9, 0, 0, 5)
    sliderBg.Position = UDim2.new(0.05, 0, 0.4, 0)
    sliderBg.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    sliderBg.BorderSizePixel = 0
    sliderBg.Parent = container
    
    local sliderButton = Instance.new("TextButton")
    sliderButton.Size = UDim2.new(0.05, 0, 0.6, 0)
    sliderButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    sliderButton.BorderSizePixel = 0
    sliderButton.Text = ""
    sliderButton.Parent = sliderBg
    
    local dragging = false
    local value = initialValue
    
    local function updateSlider(input)
        local relativePosition = input.Position.X - sliderBg.AbsolutePosition.X
        local sliderWidth = sliderBg.AbsoluteSize.X
        local percentage = math.clamp(relativePosition / sliderWidth, 0, 1)
        value = math.floor(minValue + (maxValue - minValue) * percentage)
        
        sliderButton.Position = UDim2.new(percentage - 0.025, 0, 0.2, 0)
        label.Text = name .. ": " .. value
        callback(value)
    end
    
    sliderButton.MouseButton1Down:Connect(function()
        dragging = true
    end)
    
    UserInputService.InputEnded:Connect(function(input, gameProcessed)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input, gameProcessed)
        if dragging then
            updateSlider(input)
        end
    end)
    
    sliderBg.InputBegan:Connect(function(input, gameProcessed)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            updateSlider(input)
        end
    end)
    
    return sliderButton
end

-- Criar Controles
local jumpButton = createToggleButton(scrollFrame, "⬆️ Super Pulo", 0, function(btn)
    settings.jumpEnabled = not settings.jumpEnabled
    btn.BackgroundColor3 = settings.jumpEnabled and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(200, 0, 0)
    btn.Text = settings.jumpEnabled and "ON" or "OFF"
end)

local jumpSlider = createSlider(scrollFrame, "Pulo", 50, 50, 200, settings.jumpPower, function(value)
    settings.jumpPower = value
end)

local speedButton = createToggleButton(scrollFrame, "⚡ Super Velocidade", 120, function(btn)
    settings.speedEnabled = not settings.speedEnabled
    btn.BackgroundColor3 = settings.speedEnabled and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(200, 0, 0)
    btn.Text = settings.speedEnabled and "ON" or "OFF"
end)

local speedSlider = createSlider(scrollFrame, "Velocidade", 170, 16, 100, settings.speedMultiplier, function(value)
    settings.speedMultiplier = value
end)

local espButton = createToggleButton(scrollFrame, "👁️ ESP (Ver Atrás Parede)", 240, function(btn)
    settings.espEnabled = not settings.espEnabled
    btn.BackgroundColor3 = settings.espEnabled and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(200, 0, 0)
    btn.Text = settings.espEnabled and "ON" or "OFF"
end)

-- Lógica de Toggle do Painel
toggleButton.MouseButton1Click:Connect(function()
    if settings.panelOpen then
        mainFrame:TweenSize(UDim2.new(0, 50, 0, 50), Enum.EasingDirection.InOut, Enum.EasingStyle.Quad, 0.3, true)
        toggleButton.Text = "+"
        scrollFrame.Visible = false
        titleLabel.Visible = false
        settings.panelOpen = false
    else
        mainFrame:TweenSize(UDim2.new(0, 300, 0, 400), Enum.EasingDirection.InOut, Enum.EasingStyle.Quad, 0.3, true)
        toggleButton.Text = "-"
        scrollFrame.Visible = true
        titleLabel.Visible = true
        settings.panelOpen = true
    end
end)

-- Lógica do Pulo
local function applyJump()
    if settings.jumpEnabled then
        humanoid.JumpPower = settings.jumpPower
    else
        humanoid.JumpPower = 50
    end
end

-- Lógica da Velocidade
local bodyVelocity
local function applySpeed()
    if settings.speedEnabled then
        if not bodyVelocity then
            bodyVelocity = Instance.new("BodyVelocity")
            bodyVelocity.Parent = humanoidRootPart
            bodyVelocity.MaxForce = Vector3.new(math.huge, 0, math.huge)
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        end
        
        local moveDirection = Vector3.new(0, 0, 0)
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then
            moveDirection = moveDirection + (Camera.CFrame.LookVector * Vector3.new(1, 0, 1)).Unit
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then
            moveDirection = moveDirection - (Camera.CFrame.LookVector * Vector3.new(1, 0, 1)).Unit
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then
            moveDirection = moveDirection - Camera.CFrame.RightVector
        end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then
            moveDirection = moveDirection + Camera.CFrame.RightVector
        end
        
        if moveDirection.Magnitude > 0 then
            moveDirection = moveDirection.Unit
        end
        
        bodyVelocity.Velocity = moveDirection * settings.speedMultiplier
    else
        if bodyVelocity then
            bodyVelocity:Destroy()
            bodyVelocity = nil
        end
    end
end

-- Lógica do ESP
local function createESPMarker(targetPlayer)
    if targetPlayer == player then return end
    if espMarkers[targetPlayer] then return end
    
    local targetCharacter = targetPlayer.Character
    if not targetCharacter then return end
    
    local targetHumanoidRootPart = targetCharacter:FindFirstChild("HumanoidRootPart")
    if not targetHumanoidRootPart then return end
    
    -- Criar BillboardGui
    local billboard = Instance.new("BillboardGui")
    billboard.Size = UDim2.new(4, 0, 2, 0)
    billboard.MaxDistance = 500
    billboard.Parent = targetHumanoidRootPart
    
    -- Criar TextLabel transparente
    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 0.5
    textLabel.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.TextSize = 14
    textLabel.Font = Enum.Font.GothamBold
    textLabel.Text = targetPlayer.Name
    textLabel.BorderSizePixel = 0
    textLabel.Parent = billboard
    
    espMarkers[targetPlayer] = billboard
end

local function removeESPMarker(targetPlayer)
    if espMarkers[targetPlayer] then
        espMarkers[targetPlayer]:Destroy()
        espMarkers[targetPlayer] = nil
    end
end

local function updateESP()
    if settings.espEnabled then
        for _, targetPlayer in pairs(Players:GetPlayers()) do
            if targetPlayer ~= player then
                createESPMarker(targetPlayer)
            end
        end
    else
        for targetPlayer, _ in pairs(espMarkers) do
            removeESPMarker(targetPlayer)
        end
    end
end

-- Detectar novos jogadores
Players.PlayerAdded:Connect(function(newPlayer)
    if settings.espEnabled then
        task.wait(0.5)
        createESPMarker(newPlayer)
    end
end)

Players.PlayerRemoving:Connect(function(leftPlayer)
    removeESPMarker(leftPlayer)
end)

-- Loop Principal
RunService.RenderStepped:Connect(function()
    if character and humanoidRootPart and humanoid then
        applyJump()
        applySpeed()
        updateESP()
    end
end)

-- Recriar script ao morrer
humanoid.Died:Connect(function()
    wait(0.1)
    character = player.Character or player.CharacterAdded:Wait()
    humanoidRootPart = character:WaitForChild("HumanoidRootPart")
    humanoid = character:WaitForChild("Humanoid")
    
    if bodyVelocity then
        bodyVelocity:Destroy()
        bodyVelocity = nil
    end
end)

print("🔴 MT SCRIPT Carregado com Sucesso!")    slider.Position = UDim2.new(0, 5, 0, 30)
    slider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    slider.BorderSizePixel = 0
    slider.Parent = container

    local thumb = Instance.new("Frame")
    thumb.Name = "Thumb"
    thumb.Size = UDim2.new(0, 10, 1, 0)
    thumb.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    thumb.BorderSizePixel = 0
    thumb.Parent = slider

    local valor = valorInicial
    
    local function atualizarSlider(mouseX)
        local sliderPos = slider.AbsolutePosition.X
        local sliderSize = slider.AbsoluteSize.X
        local posicaoRelativa = math.clamp(mouseX - sliderPos, 0, sliderSize)
        local percentual = posicaoRelativa / sliderSize
        valor = math.floor(minimo + (maximo - minimo) * percentual)
        
        thumb.Position = UDim2.new(percentual, -5, 0, 0)
        valueLabel.Text = tostring(valor)
        
        return valor
    end

    thumb.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            local connection
            connection = UserInputService.InputChanged:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.Mouse then
                    atualizarSlider(input.Position.X)
                end
            end)
            
            UserInputService.InputEnded:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 then
                    connection:Disconnect()
                end
            end)
        end
    end)

    slider.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            atualizarSlider(input.Position.X)
        end
    end)

    return {valor = function() return valor end, container = container}
end

-- Criar opções
local sliderPulo = criarOpcaoComSlider("Super Pulo", 1.5, 1, 3, 0)
local sliderVelocidade = criarOpcaoComSlider("Super Velocidade", 1, 1, 3, 100)

-- Botão ESP
local espButton = Instance.new("TextButton")
espButton.Name = "ESPButton"
espButton.Size = UDim2.new(1, -10, 0, 40)
espButton.Position = UDim2.new(0, 5, 0, 200)
espButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
espButton.TextSize = 14
espButton.Font = Enum.Font.Gotham
espButton.Text = "ESP: OFF"
espButton.BorderSizePixel = 0
espButton.Parent = scrollFrame

-- Função para atualizar o canvas size do scroll
local function atualizarCanvasSize()
    scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 250)
end
atualizarCanvasSize()

-- Lógica do ESP
local function marcarJogadores()
    if not espAtivo then
        for _, marker in pairs(espMarkers) do
            if marker:FindFirstChild("TextLabel") then
                marker:Destroy()
            end
        end
        espMarkers = {}
        return
    end

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local parte = p.Character.HumanoidRootPart
            
            if not espMarkers[p] then
                local billboard = Instance.new("BillboardGui")
                billboard.Size = UDim2.new(4, 0, 2, 0)
                billboard.MaxDistance = 500
                billboard.Adornee = parte
                billboard.Parent = parte

                local textLabel = Instance.new("TextLabel")
                textLabel.Size = UDim2.new(1, 0, 1, 0)
                textLabel.BackgroundTransparency = 0
                textLabel.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
                textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
                textLabel.TextScaled = true
                textLabel.Text = p.Name
                textLabel.Parent = billboard

                espMarkers[p] = billboard
            end
        end
    end
end

-- Botão ESP Click
espButton.MouseButton1Click:Connect(function()
    espAtivo = not espAtivo
    espButton.Text = espAtivo and "ESP: ON" or "ESP: OFF"
    espButton.BackgroundColor3 = espAtivo and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(50, 50, 50)
    marcarJogadores()
end)

-- Botão Fechar/Abrir
local painelFechado = false
closeButton.MouseButton1Click:Connect(function()
    painelFechado = not painelFechado
    if painelFechado then
        mainFrame:TweenSize(UDim2.new(0, 40, 0, 40), "Out", "Quad", 0.3, true)
        closeButton.Text = "+"
    else
        mainFrame:TweenSize(UDim2.new(0, 250, 0, 300), "Out", "Quad", 0.3, true)
        closeButton.Text = "-"
    end
end)

-- Loop para aplicar modificações
RunService.RenderStepped:Connect(function()
    if character and character:FindFirstChild("Humanoid") and character:FindFirstChild("HumanoidRootPart") then
        local humanoid = character.Humanoid
        local rootPart = character.HumanoidRootPart
        
        -- Super Velocidade
        if humanoid.MoveVector.Magnitude > 0 then
            local velocidade = sliderVelocidade.valor()
            rootPart.Velocity = humanoid.MoveDirection * (16 * velocidade)
        end
        
        -- Super Pulo
        if humanoid:GetState() == Enum.HumanoidStateType.Landed then
            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            local pulo = sliderPulo.valor()
            rootPart.Velocity = rootPart.Velocity + Vector3.new(0, 50 * pulo, 0)
        end
    end
    
    -- Atualizar ESP
    if espAtivo then
        marcarJogadores()
    end
end)

-- Respawn
player.CharacterAdded:Connect(function(newChar)
    character = newChar
end)
