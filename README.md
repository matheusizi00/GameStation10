-- ============================================
-- ROBLOX FPS GAME COM ESP E HEALTH
-- ============================================

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- ============================================
-- CONFIGURAÇÕES
-- ============================================

local CONFIG = {
    -- Movimento
    moveSpeed = 20,
    sprintSpeed = 30,
    jumpPower = 50,
    
    -- Câmera
    mouseSensitivity = 0.003,
    fov = 70,
    
    -- Arma
    fireRate = 0.1,
    bulletDamage = 25,
    bulletSpeed = 100,
    maxAmmo = 30,
    currentAmmo = 30,
    
    -- ESP
    espEnabled = true,
    espDistance = 500,
    showHealth = true,
    showNames = true,
    espColor = Color3.fromRGB(0, 255, 0),
    enemyColor = Color3.fromRGB(255, 0, 0),
}

-- ============================================
-- VARIÁVEIS GLOBAIS
-- ============================================

local camera = workspace.CurrentCamera
local mouse = player:GetMouse()
local isAiming = false
local isSprinting = false
local canFire = true
local mouseDown = false
local espPlayers = {}

-- ============================================
-- SETUP CÂMERA
-- ============================================

camera.FieldOfView = CONFIG.fov

-- ============================================
-- SISTEMA DE ESP
-- ============================================

local function createESPBox(targetPlayer)
    if targetPlayer == player then return end
    if not targetPlayer.Character then return end
    
    local targetHumanoid = targetPlayer.Character:FindFirstChild("Humanoid")
    if not targetHumanoid then return end
    
    -- Criar container para ESP
    local espContainer = Instance.new("BillboardGui")
    espContainer.Name = "ESP_" .. targetPlayer.Name
    espContainer.Size = UDim2.new(4, 0, 5, 0)
    espContainer.MaxDistance = CONFIG.espDistance
    espContainer.Adornee = targetPlayer.Character:FindFirstChild("Head")
    
    -- CAIXA DO PERSONAGEM
    local boxFrame = Instance.new("Frame")
    boxFrame.Name = "Box"
    boxFrame.Size = UDim2.new(1, 0, 1, 0)
    boxFrame.BackgroundTransparency = 1
    boxFrame.BorderSizePixel = 2
    boxFrame.BorderColor3 = CONFIG.enemyColor
    boxFrame.Parent = espContainer
    
    -- NOME DO JOGADOR
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Name = "NameLabel"
    nameLabel.Size = UDim2.new(1, 0, 0.15, 0)
    nameLabel.Position = UDim2.new(0, 0, -0.2, 0)
    nameLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    nameLabel.BackgroundTransparency = 0.5
    nameLabel.TextColor3 = CONFIG.enemyColor
    nameLabel.TextSize = 14
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.Text = targetPlayer.Name
    nameLabel.Parent = espContainer
    
    -- BARRA DE VIDA
    local healthBarBg = Instance.new("Frame")
    healthBarBg.Name = "HealthBarBg"
    healthBarBg.Size = UDim2.new(0.15, 0, 1, 0)
    healthBarBg.Position = UDim2.new(-0.25, 0, 0, 0)
    healthBarBg.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    healthBarBg.BorderSizePixel = 1
    healthBarBg.BorderColor3 = Color3.fromRGB(255, 255, 255)
    healthBarBg.Parent = espContainer
    
    local healthBar = Instance.new("Frame")
    healthBar.Name = "HealthBar"
    healthBar.Size = UDim2.new(1, 0, 1, 0)
    healthBar.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    healthBar.BorderSizePixel = 0
    healthBar.Parent = healthBarBg
    
    -- TEXTO DE VIDA
    local healthText = Instance.new("TextLabel")
    healthText.Name = "HealthText"
    healthText.Size = UDim2.new(1, 0, 1, 0)
    healthText.BackgroundTransparency = 1
    healthText.TextColor3 = Color3.fromRGB(255, 255, 255)
    healthText.TextSize = 12
    healthText.Font = Enum.Font.GothamBold
    healthText.Text = "0/100"
    healthText.Parent = healthBarBg
    
    -- DISTÂNCIA
    local distanceLabel = Instance.new("TextLabel")
    distanceLabel.Name = "DistanceLabel"
    distanceLabel.Size = UDim2.new(1, 0, 0.15, 0)
    distanceLabel.Position = UDim2.new(0, 0, 1.05, 0)
    distanceLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    distanceLabel.BackgroundTransparency = 0.5
    distanceLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
    distanceLabel.TextSize = 12
    distanceLabel.Font = Enum.Font.Gotham
    distanceLabel.Text = "0m"
    distanceLabel.Parent = espContainer
    
    espContainer.Parent = targetPlayer.Character:FindFirstChild("Head")
    
    -- Atualizar informações
    local connection
    connection = RunService.RenderStepped:Connect(function()
        if not targetPlayer.Character or not targetHumanoid or targetHumanoid.Health <= 0 then
            espContainer:Destroy()
            connection:Disconnect()
            espPlayers[targetPlayer.Name] = nil
            return
        end
        
        -- Atualizar saúde
        local maxHealth = targetHumanoid.MaxHealth
        local currentHealth = targetHumanoid.Health
        local healthPercent = currentHealth / maxHealth
        
        healthBar.Size = UDim2.new(healthPercent, 0, 1, 0)
        healthText.Text = math.floor(currentHealth) .. "/" .. math.floor(maxHealth)
        
        -- Mudar cor da barra baseado na saúde
        if healthPercent > 0.5 then
            healthBar.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        elseif healthPercent > 0.25 then
            healthBar.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
        else
            healthBar.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        end
        
        -- Atualizar distância
        local distance = (humanoidRootPart.Position - targetPlayer.Character.Head.Position).Magnitude
        distanceLabel.Text = math.floor(distance) .. "m"
        
        -- Mudar cor da caixa baseado na distância
        if distance < 50 then
            boxFrame.BorderColor3 = Color3.fromRGB(255, 0, 0)
            nameLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        elseif distance < 100 then
            boxFrame.BorderColor3 = Color3.fromRGB(255, 255, 0)
            nameLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
        else
            boxFrame.BorderColor3 = Color3.fromRGB(0, 255, 0)
            nameLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
        end
    end)
    
    espPlayers[targetPlayer.Name] = espContainer
end

-- ============================================
-- GERENCIAR ESP PARA TODOS OS PLAYERS
-- ============================================

local function setupAllPlayers()
    for _, targetPlayer in pairs(Players:GetPlayers()) do
        if targetPlayer ~= player and not espPlayers[targetPlayer.Name] then
            createESPBox(targetPlayer)
        end
    end
end

Players.PlayerAdded:Connect(function(newPlayer)
    task.wait(0.5)
    if CONFIG.espEnabled then
        createESPBox(newPlayer)
    end
end)

Players.PlayerRemoving:Connect(function(leftPlayer)
    if espPlayers[leftPlayer.Name] then
        espPlayers[leftPlayer.Name]:Destroy()
        espPlayers[leftPlayer.Name] = nil
    end
end)

setupAllPlayers()

-- ============================================
-- TOGGLE ESP
-- ============================================

local function toggleESP()
    CONFIG.espEnabled = not CONFIG.espEnabled
    
    if CONFIG.espEnabled then
        setupAllPlayers()
        print("✓ ESP ATIVADO")
    else
        for name, esp in pairs(espPlayers) do
            if esp then esp:Destroy() end
        end
        espPlayers = {}
        print("✗ ESP DESATIVADO")
    end
end

-- ============================================
-- SISTEMA DE ARMA
-- ============================================

local function createBullet(origin, direction)
    if CONFIG.currentAmmo <= 0 then
        print("Sem munição!")
        return
    end
    
    if not canFire then return end
    
    CONFIG.currentAmmo = CONFIG.currentAmmo - 1
    canFire = false
    
    -- Criar bala
    local bullet = Instance.new("Part")
    bullet.Shape = Enum.PartType.Ball
    bullet.Material = Enum.Material.Metal
    bullet.Size = Vector3.new(0.2, 0.2, 0.2)
    bullet.CanCollide = false
    bullet.CFrame = origin + direction * 5
    bullet.TopSurface = Enum.SurfaceType.Smooth
    bullet.BottomSurface = Enum.SurfaceType.Smooth
    bullet.BrickColor = BrickColor.new("Really black")
    
    local bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = direction * CONFIG.bulletSpeed
    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bodyVelocity.Parent = bullet
    
    bullet.Parent = workspace
    
    -- Sistema de colisão
    local function onBulletHit(hit)
        if hit.Parent == character then return end
        
        local targetHumanoid = hit.Parent:FindFirstChild("Humanoid")
        if targetHumanoid then
            targetHumanoid:TakeDamage(CONFIG.bulletDamage)
            print("💥 Acertou! Dano: " .. CONFIG.bulletDamage)
        end
        
        bullet:Destroy()
    end
    
    local connection
    connection = bullet.Touched:Connect(function(hit)
        onBulletHit(hit)
        if connection then
            connection:Disconnect()
        end
    end)
    
    game:GetService("Debris"):AddItem(bullet, 10)
    
    task.wait(CONFIG.fireRate)
    canFire = true
end

-- ============================================
-- SISTEMA DE MOVIMENTO
-- ============================================

local function updateMovement()
    local moveDirection = Vector3.new(0, 0, 0)
    
    if UserInputService:IsKeyDown(Enum.KeyCode.W) then
        moveDirection = moveDirection + (humanoidRootPart.CFrame.LookVector)
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.S) then
        moveDirection = moveDirection - (humanoidRootPart.CFrame.LookVector)
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.A) then
        moveDirection = moveDirection - (humanoidRootPart.CFrame.RightVector)
    end
    if UserInputService:IsKeyDown(Enum.KeyCode.D) then
        moveDirection = moveDirection + (humanoidRootPart.CFrame.RightVector)
    end
    
    moveDirection = moveDirection.Unit
    
    if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
        isSprinting = true
        humanoid.WalkSpeed = CONFIG.sprintSpeed
    else
        isSprinting = false
        humanoid.WalkSpeed = CONFIG.moveSpeed
    end
    
    humanoid:Move(moveDirection, false)
end

-- ============================================
-- SISTEMA DE CÂMERA
-- ============================================

local lastMousePosition = mouse.Hit.Position
local camX = 0
local camY = 0

local function updateCamera()
    local mouseDelta = mouse.Hit.Position - lastMousePosition
    lastMousePosition = mouse.Hit.Position
    
    camX = camX + mouseDelta.X * CONFIG.mouseSensitivity
    camY = math.max(-89, math.min(89, camY - mouseDelta.Y * CONFIG.mouseSensitivity))
    
    local cameraRotation = CFrame.Angles(math.rad(camY), math.rad(camX), 0)
    camera.CFrame = humanoidRootPart.CFrame + humanoidRootPart.CFrame.LookVector * 10 * cameraRotation
    
    humanoidRootPart.CFrame = humanoidRootPart.CFrame * CFrame.Angles(0, math.rad(-mouseDelta.X * CONFIG.mouseSensitivity), 0)
end

-- ============================================
-- INPUTS
-- ============================================

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        mouseDown = true
        while mouseDown and canFire do
            createBullet(camera.CFrame.Position, camera.CFrame.LookVector)
            task.wait(CONFIG.fireRate)
        end
    end
    
    if input.KeyCode == Enum.KeyCode.Space then
        humanoid:Jump()
    end
    
    if input.KeyCode == Enum.KeyCode.R then
        CONFIG.currentAmmo = CONFIG.maxAmmo
        print("🔫 Munição recarregada!")
    end
    
    -- TOGGLE ESP
    if input.KeyCode == Enum.KeyCode.E then
        toggleESP()
    end
end)

UserInputService.InputEnded:Connect(function(input, gameProcessed)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        mouseDown = false
    end
end)

-- ============================================
-- LOOP PRINCIPAL
-- ============================================

RunService.RenderStepped:Connect(function()
    if character and humanoid.Health > 0 then
        updateMovement()
        updateCamera()
    end
end)

-- ============================================
-- RESPAWN
-- ============================================

humanoid.Died:Connect(function()
    player:LoadCharacter()
end)

-- ============================================
-- MENU DE INFORMAÇÕES
-- ============================================

print("\n========================================")
print("✓ Script FPS com ESP carregado!")
print("========================================")
print("\n🎮 CONTROLES:")
print("  W/A/S/D - Mover")
print("  SHIFT - Correr")
print("  SPACE - Pular")
print("  MOUSE - Mirar")
print("  CLICK - Atirar")
print("  R - Recarregar")
print("  E - Toggle ESP (Visão através de paredes)")
print("\n📊 INFORMAÇÕES DO ESP:")
print("  Verde - Longe (>100m)")
print("  Amarelo - Médio (50-100m)")
print("  Vermelho - Perto (<50m)")
print("  Barra verde/amarela/vermelha = Vida do inimigo")
print("========================================\n")
