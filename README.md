-- MT SCRIPT v2
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- Variáveis de controle
local pulo = 50
local velocidade = 16
local espAtivo = false
local menuAberto = true

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MTScriptGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Painel Principal
local painel = Instance.new("Frame")
painel.Name = "Painel"
painel.Size = UDim2.new(0, 250, 0, 300)
painel.Position = UDim2.new(0, 10, 0, 10)
painel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
painel.BorderSizePixel = 0
painel.Parent = screenGui

-- Título
local titulo = Instance.new("TextLabel")
titulo.Name = "Titulo"
titulo.Size = UDim2.new(1, 0, 0, 35)
titulo.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
titulo.TextColor3 = Color3.fromRGB(255, 0, 0)
titulo.TextSize = 18
titulo.Font = Enum.Font.GothamBold
titulo.Text = "MT SCRIPT"
titulo.BorderSizePixel = 0
titulo.Parent = painel

-- Botão Fechar
local btnFechar = Instance.new("TextButton")
btnFechar.Name = "BtnFechar"
btnFechar.Size = UDim2.new(0, 30, 0, 35)
btnFechar.Position = UDim2.new(1, -35, 0, 0)
btnFechar.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
btnFechar.TextColor3 = Color3.fromRGB(255, 255, 255)
btnFechar.TextSize = 16
btnFechar.Font = Enum.Font.GothamBold
btnFechar.Text = "-"
btnFechar.BorderSizePixel = 0
btnFechar.Parent = painel

-- ScrollingFrame
local scroll = Instance.new("ScrollingFrame")
scroll.Name = "Scroll"
scroll.Size = UDim2.new(1, 0, 1, -35)
scroll.Position = UDim2.new(0, 0, 0, 35)
scroll.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 8
scroll.Parent = painel

-- Label Super Pulo
local labelPulo = Instance.new("TextLabel")
labelPulo.Size = UDim2.new(1, 0, 0, 25)
labelPulo.Position = UDim2.new(0, 10, 0, 10)
labelPulo.BackgroundTransparency = 1
labelPulo.TextColor3 = Color3.fromRGB(255, 255, 255)
labelPulo.TextSize = 14
labelPulo.Font = Enum.Font.Gotham
labelPulo.Text = "Super Pulo: " .. pulo
labelPulo.Parent = scroll

-- Slider Pulo
local sliderPulo = Instance.new("TextButton")
sliderPulo.Name = "SliderPulo"
sliderPulo.Size = UDim2.new(0, 200, 0, 15)
sliderPulo.Position = UDim2.new(0, 10, 0, 40)
sliderPulo.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
sliderPulo.BorderSizePixel = 0
sliderPulo.Text = ""
sliderPulo.Parent = scroll

local sliderPuloFill = Instance.new("Frame")
sliderPuloFill.Name = "Fill"
sliderPuloFill.Size = UDim2.new((pulo - 50) / 150, 0, 1, 0)
sliderPuloFill.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
sliderPuloFill.BorderSizePixel = 0
sliderPuloFill.Parent = sliderPulo

-- Label Super Velocidade
local labelVel = Instance.new("TextLabel")
labelVel.Size = UDim2.new(1, 0, 0, 25)
labelVel.Position = UDim2.new(0, 10, 0, 65)
labelVel.BackgroundTransparency = 1
labelVel.TextColor3 = Color3.fromRGB(255, 255, 255)
labelVel.TextSize = 14
labelVel.Font = Enum.Font.Gotham
labelVel.Text = "Super Velocidade: " .. velocidade
labelVel.Parent = scroll

-- Slider Velocidade
local sliderVel = Instance.new("TextButton")
sliderVel.Name = "SliderVel"
sliderVel.Size = UDim2.new(0, 200, 0, 15)
sliderVel.Position = UDim2.new(0, 10, 0, 95)
sliderVel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
sliderVel.BorderSizePixel = 0
sliderVel.Text = ""
sliderVel.Parent = scroll

local sliderVelFill = Instance.new("Frame")
sliderVelFill.Name = "Fill"
sliderVelFill.Size = UDim2.new((velocidade - 16) / 84, 0, 1, 0)
sliderVelFill.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
sliderVelFill.BorderSizePixel = 0
sliderVelFill.Parent = sliderVel

-- Botão ESP
local btnESP = Instance.new("TextButton")
btnESP.Name = "BtnESP"
btnESP.Size = UDim2.new(0, 200, 0, 35)
btnESP.Position = UDim2.new(0, 10, 0, 120)
btnESP.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
btnESP.TextColor3 = Color3.fromRGB(255, 255, 255)
btnESP.TextSize = 14
btnESP.Font = Enum.Font.GothamBold
btnESP.Text = "ESP: OFF"
btnESP.BorderSizePixel = 0
btnESP.Parent = scroll

-- Atualizar tamanho do scroll
scroll.CanvasSize = UDim2.new(0, 0, 0, 170)

-- Função para criar ESP
local function criarESP(p)
	if p == player then return end
	if p:FindFirstChild("ESP") then return end
	
	local billboard = Instance.new("BillboardGui")
	billboard.Name = "ESP"
	billboard.Size = UDim2.new(4, 0, 5, 0)
	billboard.MaxDistance = 500
	billboard.Parent = p:FindFirstChild("Head") or p.Character:FindFirstChildOfClass("Model")
	
	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 1, 0)
	label.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
	label.BackgroundTransparency = 0.3
	label.TextColor3 = Color3.fromRGB(255, 0, 0)
	label.TextSize = 14
	label.Font = Enum.Font.GothamBold
	label.Text = p.Name
	label.BorderSizePixel = 0
	label.Parent = billboard
end

-- Função para remover ESP
local function removerESP(p)
	if p:FindFirstChild("ESP") then
		p:FindFirstChild("ESP"):Destroy()
	end
end

-- Controlar ESP
btnESP.MouseButton1Click:Connect(function()
	espAtivo = not espAtivo
	btnESP.BackgroundColor3 = espAtivo and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(100, 0, 0)
	btnESP.Text = espAtivo and "ESP: ON" or "ESP: OFF"
	
	if espAtivo then
		for _, p in pairs(Players:GetPlayers()) do
			criarESP(p)
		end
	else
		for _, p in pairs(Players:GetPlayers()) do
			removerESP(p)
		end
	end
end)

-- Evento de novos jogadores
Players.PlayerAdded:Connect(function(p)
	if espAtivo then
		p.CharacterAdded:Connect(function()
			wait(0.5)
			criarESP(p)
		end)
	end
end)

-- Controlar Pulo com Slider
sliderPulo.MouseButton1Click:Connect(function(x, y)
	local tamanho = sliderPulo.AbsoluteSize.X
	local clique = x - sliderPulo.AbsolutePosition.X
	pulo = math.max(50, math.min(200, 50 + (clique / tamanho) * 150))
	sliderPuloFill.Size = UDim2.new((pulo - 50) / 150, 0, 1, 0)
	labelPulo.Text = "Super Pulo: " .. math.floor(pulo)
end)

-- Controlar Velocidade com Slider
sliderVel.MouseButton1Click:Connect(function(x, y)
	local tamanho = sliderVel.AbsoluteSize.X
	local clique = x - sliderVel.AbsolutePosition.X
	velocidade = math.max(16, math.min(100, 16 + (clique / tamanho) * 84))
	sliderVelFill.Size = UDim2.new((velocidade - 16) / 84, 0, 1, 0)
	labelVel.Text = "Super Velocidade: " .. math.floor(velocidade)
end)

-- Botão Fechar
btnFechar.MouseButton1Click:Connect(function()
	menuAberto = not menuAberto
	painel.Visible = menuAberto
	btnFechar.Text = menuAberto and "-" or "+"
end)

-- Aplicar modificações em tempo real
RunService.RenderStepped:Connect(function()
	if character and humanoid then
		humanoid.JumpPower = pulo
		humanoid.WalkSpeed = velocidade
	end
end)

-- Recriar ao respawn
player.CharacterAdded:Connect(function(newChar)
	character = newChar
	humanoid = character:WaitForChild("Humanoid")
	rootPart = character:WaitForChild("HumanoidRootPart")
end)

print("MT SCRIPT Carregado com sucesso!")    button.Parent = container
    
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
