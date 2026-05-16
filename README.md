local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- Variáveis de controle
local superPuloAtivo = false
local superVelocidadeAtivo = false
local espAtivo = false
local puloForce = 100
local velocidadeForce = 20
local painelAberto = true

-- Criar ScreenGui principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "MTScript"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Painel principal (Container)
local painel = Instance.new("Frame")
painel.Name = "Painel"
painel.Size = UDim2.new(0, 250, 0, 400)
painel.Position = UDim2.new(0, 10, 0, 10)
painel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
painel.BorderSizePixel = 0
painel.Parent = screenGui

-- Título
local titulo = Instance.new("TextLabel")
titulo.Name = "Titulo"
titulo.Size = UDim2.new(1, 0, 0, 40)
titulo.Position = UDim2.new(0, 0, 0, 0)
titulo.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
titulo.TextColor3 = Color3.fromRGB(255, 100, 100)
titulo.TextSize = 18
titulo.Font = Enum.Font.GothamBold
titulo.Text = "MT SCRIPT"
titulo.BorderSizePixel = 0
titulo.Parent = painel

-- Botão Fechar (-)
local btnFechar = Instance.new("TextButton")
btnFechar.Name = "BtnFechar"
btnFechar.Size = UDim2.new(0, 40, 0, 40)
btnFechar.Position = UDim2.new(1, -45, 0, 0)
btnFechar.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
btnFechar.TextColor3 = Color3.fromRGB(255, 255, 255)
btnFechar.TextSize = 20
btnFechar.Font = Enum.Font.GothamBold
btnFechar.Text = "-"
btnFechar.BorderSizePixel = 0
btnFechar.Parent = titulo

-- ScrollingFrame para conteúdo
local scroll = Instance.new("ScrollingFrame")
scroll.Name = "Scroll"
scroll.Size = UDim2.new(1, 0, 1, -40)
scroll.Position = UDim2.new(0, 0, 0, 40)
scroll.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 8
scroll.Parent = painel

-- ===== SUPER PULO =====
local lblPulo = Instance.new("TextLabel")
lblPulo.Size = UDim2.new(1, -10, 0, 20)
lblPulo.Position = UDim2.new(0, 5, 0, 10)
lblPulo.BackgroundTransparency = 1
lblPulo.TextColor3 = Color3.fromRGB(255, 255, 255)
lblPulo.TextSize = 14
lblPulo.Font = Enum.Font.Gotham
lblPulo.Text = "Super Pulo: [OFF]"
lblPulo.TextXAlignment = Enum.TextXAlignment.Left
lblPulo.Parent = scroll

local btnPulo = Instance.new("TextButton")
btnPulo.Size = UDim2.new(0, 50, 0, 25)
btnPulo.Position = UDim2.new(1, -60, 0, 10)
btnPulo.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
btnPulo.TextColor3 = Color3.fromRGB(255, 255, 255)
btnPulo.TextSize = 12
btnPulo.Font = Enum.Font.Gotham
btnPulo.Text = "OFF"
btnPulo.BorderSizePixel = 0
btnPulo.Parent = scroll

local sliderPulo = Instance.new("TextBox")
sliderPulo.Size = UDim2.new(1, -10, 0, 25)
sliderPulo.Position = UDim2.new(0, 5, 0, 40)
sliderPulo.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
sliderPulo.TextColor3 = Color3.fromRGB(255, 255, 255)
sliderPulo.TextSize = 12
sliderPulo.Font = Enum.Font.Gotham
sliderPulo.Text = tostring(puloForce)
sliderPulo.PlaceholderText = "50-200"
sliderPulo.BorderSizePixel = 0
sliderPulo.Parent = scroll

-- ===== SUPER VELOCIDADE =====
local lblVel = Instance.new("TextLabel")
lblVel.Size = UDim2.new(1, -10, 0, 20)
lblVel.Position = UDim2.new(0, 5, 0, 80)
lblVel.BackgroundTransparency = 1
lblVel.TextColor3 = Color3.fromRGB(255, 255, 255)
lblVel.TextSize = 14
lblVel.Font = Enum.Font.Gotham
lblVel.Text = "Super Velocidade: [OFF]"
lblVel.TextXAlignment = Enum.TextXAlignment.Left
lblVel.Parent = scroll

local btnVel = Instance.new("TextButton")
btnVel.Size = UDim2.new(0, 50, 0, 25)
btnVel.Position = UDim2.new(1, -60, 0, 80)
btnVel.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
btnVel.TextColor3 = Color3.fromRGB(255, 255, 255)
btnVel.TextSize = 12
btnVel.Font = Enum.Font.Gotham
btnVel.Text = "OFF"
btnVel.BorderSizePixel = 0
btnVel.Parent = scroll

local sliderVel = Instance.new("TextBox")
sliderVel.Size = UDim2.new(1, -10, 0, 25)
sliderVel.Position = UDim2.new(0, 5, 0, 110)
sliderVel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
sliderVel.TextColor3 = Color3.fromRGB(255, 255, 255)
sliderVel.TextSize = 12
sliderVel.Font = Enum.Font.Gotham
sliderVel.Text = tostring(velocidadeForce)
sliderVel.PlaceholderText = "16-100"
sliderVel.BorderSizePixel = 0
sliderVel.Parent = scroll

-- ===== ESP =====
local lblESP = Instance.new("TextLabel")
lblESP.Size = UDim2.new(1, -10, 0, 20)
lblESP.Position = UDim2.new(0, 5, 0, 150)
lblESP.BackgroundTransparency = 1
lblESP.TextColor3 = Color3.fromRGB(255, 255, 255)
lblESP.TextSize = 14
lblESP.Font = Enum.Font.Gotham
lblESP.Text = "ESP: [OFF]"
lblESP.TextXAlignment = Enum.TextXAlignment.Left
lblESP.Parent = scroll

local btnESP = Instance.new("TextButton")
btnESP.Size = UDim2.new(0, 50, 0, 25)
btnESP.Position = UDim2.new(1, -60, 0, 150)
btnESP.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
btnESP.TextColor3 = Color3.fromRGB(255, 255, 255)
btnESP.TextSize = 12
btnESP.Font = Enum.Font.Gotham
btnESP.Text = "OFF"
btnESP.BorderSizePixel = 0
btnESP.Parent = scroll

-- Botão Abrir/Fechar (flutuante quando fechado)
local btnAbrir = Instance.new("TextButton")
btnAbrir.Name = "BtnAbrir"
btnAbrir.Size = UDim2.new(0, 50, 0, 50)
btnAbrir.Position = UDim2.new(0, 10, 0, 10)
btnAbrir.BackgroundColor3 = Color3.fromRGB(100, 200, 100)
btnAbrir.TextColor3 = Color3.fromRGB(255, 255, 255)
btnAbrir.TextSize = 24
btnAbrir.Font = Enum.Font.GothamBold
btnAbrir.Text = "+"
btnAbrir.BorderSizePixel = 0
btnAbrir.Visible = false
btnAbrir.Parent = screenGui

-- Função para aplicar ESP
local function atualizarESP()
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= player and p.Character then
			local humanoidRootPart = p.Character:FindFirstChild("HumanoidRootPart")
			if humanoidRootPart then
				-- Remove ESP antigo se existir
				local billboardGui = humanoidRootPart:FindFirstChild("ESPGui")
				if espAtivo and not billboardGui then
					billboardGui = Instance.new("BillboardGui")
					billboardGui.Name = "ESPGui"
					billboardGui.Size = UDim2.new(4, 0, 5, 0)
					billboardGui.MaxDistance = 500
					billboardGui.Parent = humanoidRootPart

					local textLabel = Instance.new("TextLabel")
					textLabel.Size = UDim2.new(1, 0, 1, 0)
					textLabel.BackgroundTransparency = 0.3
					textLabel.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
					textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
					textLabel.TextSize = 14
					textLabel.Font = Enum.Font.Gotham
					textLabel.Text = p.Name
					textLabel.Parent = billboardGui
				elseif not espAtivo and billboardGui then
					billboardGui:Destroy()
				end
			end
		end
	end
end

-- Função para aplicar Super Pulo
RunService.RenderStepped:Connect(function()
	if superPuloAtivo and character:FindFirstChild("HumanoidRootPart") then
		local humanoid = character:FindFirstChild("Humanoid")
		if humanoid and humanoid:FindFirstChild("BodyVelocity") then
			local bv = humanoid:FindFirstChild("BodyVelocity")
			if bv.Velocity.Y < 0.1 then
				humanoid:Jump()
			end
		end
	end
end)

-- Função para aplicar Super Velocidade
local velocidade = nil

RunService.RenderStepped:Connect(function()
	if superVelocidadeAtivo and character:FindFirstChild("HumanoidRootPart") then
		if not velocidade then
			velocidade = Instance.new("BodyVelocity")
			velocidade.Velocity = Vector3.new(0, 0, 0)
			velocidade.MaxForce = Vector3.new(math.huge, 0, math.huge)
			velocidade.Parent = humanoidRootPart
		end

		local camera = workspace.CurrentCamera
		local moveDirection = Vector3.new(0, 0, 0)

		if UserInputService:IsKeyDown(Enum.KeyCode.W) then
			moveDirection = moveDirection + (camera.CFrame.LookVector * Vector3.new(1, 0, 1)).Unit
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then
			moveDirection = moveDirection - camera.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then
			moveDirection = moveDirection - (camera.CFrame.LookVector * Vector3.new(1, 0, 1)).Unit
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then
			moveDirection = moveDirection + camera.CFrame.RightVector
		end

		if moveDirection.Magnitude > 0 then
			moveDirection = moveDirection.Unit
		end

		velocidade.Velocity = moveDirection * velocidadeForce
	elseif velocidade then
		velocidade:Destroy()
		velocidade = nil
	end
end)

-- Atualizar ESP periodicamente
RunService.Heartbeat:Connect(function()
	if espAtivo then
		atualizarESP()
	end
end)

-- Novos jogadores
Players.PlayerAdded:Connect(function(novoPlayer)
	if espAtivo then
		wait(0.5)
		atualizarESP()
	end
end)

-- Evento do botão Super Pulo
btnPulo.MouseButton1Click:Connect(function()
	superPuloAtivo = not superPuloAtivo
	if superPuloAtivo then
		btnPulo.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
		btnPulo.Text = "ON"
		lblPulo.Text = "Super Pulo: [ON]"
	else
		btnPulo.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
		btnPulo.Text = "OFF"
		lblPulo.Text = "Super Pulo: [OFF]"
	end
end)

-- Evento do slider Super Pulo
sliderPulo.FocusLost:Connect(function()
	local valor = tonumber(sliderPulo.Text)
	if valor and valor >= 50 and valor <= 200 then
		puloForce = valor
	else
		sliderPulo.Text = tostring(puloForce)
	end
end)

-- Evento do botão Super Velocidade
btnVel.MouseButton1Click:Connect(function()
	superVelocidadeAtivo = not superVelocidadeAtivo
	if superVelocidadeAtivo then
		btnVel.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
		btnVel.Text = "ON"
		lblVel.Text = "Super Velocidade: [ON]"
	else
		btnVel.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
		btnVel.Text = "OFF"
		lblVel.Text = "Super Velocidade: [OFF]"
		if velocidade then
			velocidade:Destroy()
			velocidade = nil
		end
	end
end)

-- Evento do slider Super Velocidade
sliderVel.FocusLost:Connect(function()
	local valor = tonumber(sliderVel.Text)
	if valor and valor >= 16 and valor <= 100 then
		velocidadeForce = valor
	else
		sliderVel.Text = tostring(velocidadeForce)
	end
end)

-- Evento do botão ESP
btnESP.MouseButton1Click:Connect(function()
	espAtivo = not espAtivo
	if espAtivo then
		btnESP.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
		btnESP.Text = "ON"
		lblESP.Text = "ESP: [ON]"
		atualizarESP()
	else
		btnESP.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
		btnESP.Text = "OFF"
		lblESP.Text = "ESP: [OFF]"
		-- Remove todos os ESPs
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= player and p.Character then
				local humanoidRootPart = p.Character:FindFirstChild("HumanoidRootPart")
				if humanoidRootPart then
					local billboardGui = humanoidRootPart:FindFirstChild("ESPGui")
					if billboardGui then
						billboardGui:Destroy()
					end
				end
			end
		end
	end
end)

-- Evento do botão Fechar
btnFechar.MouseButton1Click:Connect(function()
	painelAberto = false
	painel.Visible = false
	btnAbrir.Visible = true
end)

-- Evento do botão Abrir
btnAbrir.MouseButton1Click:Connect(function()
	painelAberto = true
	painel.Visible = true
	btnAbrir.Visible = false
end)

-- Atualizar quando respawnar
player.CharacterAdded:Connect(function(novaCharacter)
	character = novaCharacter
	humanoidRootPart = character:WaitForChild("HumanoidRootPart")
	humanoid = character:WaitForChild("Humanoid")
	velocidade = nil
end)

print("MT SCRIPT Carregado! ✅")
