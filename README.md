-- MT SCRIPT - Script Roblox Completo
local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- Variáveis de Controle
local velocidadeMultiplicador = 1
local pularMultiplicador = 1
local espAtivo = false
local panelAberto = true
local espMarkers = {}

-- Criar GUI Principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MTScriptGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Frame Principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 250, 0, 300)
mainFrame.Position = UDim2.new(0, 10, 0, 10)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

-- Label Título
local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "Title"
titleLabel.Size = UDim2.new(1, 0, 0, 40)
titleLabel.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
titleLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
titleLabel.TextSize = 18
titleLabel.Font = Enum.Font.GothamBold
titleLabel.Text = "MT SCRIPT"
titleLabel.BorderSizePixel = 0
titleLabel.Parent = mainFrame

-- Botão Fechar
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.TextSize = 16
closeButton.Font = Enum.Font.GothamBold
closeButton.Text = "-"
closeButton.BorderSizePixel = 0
closeButton.Parent = titleLabel

-- ScrollingFrame para opções
local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Name = "ScrollFrame"
scrollFrame.Size = UDim2.new(1, 0, 1, -40)
scrollFrame.Position = UDim2.new(0, 0, 0, 40)
scrollFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
scrollFrame.BorderSizePixel = 0
scrollFrame.ScrollBarThickness = 8
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 400)
scrollFrame.Parent = mainFrame

-- Função para criar opção com slider
local function criarOpcaoComSlider(nome, valorInicial, minimo, maximo, posicao)
    local container = Instance.new("Frame")
    container.Name = nome
    container.Size = UDim2.new(1, 0, 0, 80)
    container.Position = UDim2.new(0, 0, 0, posicao)
    container.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    container.BorderSizePixel = 0
    container.Parent = scrollFrame

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -10, 0, 20)
    label.Position = UDim2.new(0, 5, 0, 5)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextSize = 14
    label.Font = Enum.Font.Gotham
    label.Text = nome
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = container

    local valueLabel = Instance.new("TextLabel")
    valueLabel.Size = UDim2.new(0, 50, 0, 20)
    valueLabel.Position = UDim2.new(1, -55, 0, 5)
    valueLabel.BackgroundTransparency = 1
    valueLabel.TextColor3 = Color3.fromRGB(0, 255, 100)
    valueLabel.TextSize = 14
    valueLabel.Font = Enum.Font.GothamBold
    valueLabel.Text = tostring(valorInicial)
    valueLabel.Parent = container

    local slider = Instance.new("Frame")
    slider.Name = "Slider"
    slider.Size = UDim2.new(1, -10, 0, 20)
    slider.Position = UDim2.new(0, 5, 0, 30)
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
