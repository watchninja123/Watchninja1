-- Serviços
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Debris = game:GetService("Debris")
local player = Players.LocalPlayer

-- GUI Principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CustomGUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Função de notificação
local function showNotification(text)
	local note = Instance.new("TextLabel")
	note.Size = UDim2.new(0, 200, 0, 35)
	note.Position = UDim2.new(1, -210, 1, -60)
	note.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
	note.BackgroundTransparency = 0.3
	note.Text = text
	note.TextColor3 = Color3.new(1, 1, 1)
	note.TextScaled = true
	note.Font = Enum.Font.GothamBold
	note.Parent = screenGui

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = note

	Debris:AddItem(note, 2)
end

-- Função que adiciona arraste via toque ou mouse
local function enableDrag(frame)
	local dragging, dragInput, startPos, startInputPos

	local function update(input)
		local delta = input.Position - startInputPos
		frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
								   startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end

	frame.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			startInputPos = input.Position
			startPos = frame.Position
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)

	frame.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			update(input)
		end
	end)
end

-- Painel lateral
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 180, 0, 210)
frame.Position = UDim2.new(0, 20, 0.5, -105)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Visible = false
frame.Active = true
frame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = frame

-- Toggle Button (Menu)
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 40, 0, 40)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
toggleButton.Text = "≡"
toggleButton.TextColor3 = Color3.fromRGB(255, 0, 0)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextSize = 24
toggleButton.Active = true
toggleButton.Parent = screenGui

local iconCorner = Instance.new("UICorner")
iconCorner.CornerRadius = UDim.new(1, 0)
iconCorner.Parent = toggleButton

enableDrag(toggleButton)

toggleButton.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
end)

-- Créditos
local creditLabel = Instance.new("TextLabel")
creditLabel.Size = UDim2.new(1, 0, 0, 20)
creditLabel.Position = UDim2.new(0, 0, 1, -20)
creditLabel.BackgroundTransparency = 1
creditLabel.Text = "Créditos: watchninja"
creditLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
creditLabel.Font = Enum.Font.Gotham
creditLabel.TextSize = 14
creditLabel.TextXAlignment = Enum.TextXAlignment.Center
creditLabel.Parent = frame

-- Função criar botões do painel
local function criarBotao(pai, texto, posY, callback)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, -20, 0, 30)
	btn.Position = UDim2.new(0, 10, 0, posY)
	btn.Text = texto
	btn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
	btn.TextColor3 = Color3.fromRGB(255, 0, 0)
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 16
	btn.Parent = pai

	local btnCorner = Instance.new("UICorner")
	btnCorner.CornerRadius = UDim.new(0, 6)
	btnCorner.Parent = btn

	btn.MouseButton1Click:Connect(callback)
end

-- Sub-Seções
local altura = 150
local plataformaAerea
local humanoid
local speedEnabled = false
local speedValue = 45

local function getRootPart()
	return player.Character and player.Character:FindFirstChild("HumanoidRootPart") or nil
end

local function atualizarVelocidade()
	if humanoid then
		humanoid.WalkSpeed = speedEnabled and speedValue or 16
	end
end

local function onCharacterAdded(character)
	humanoid = character:WaitForChild("Humanoid")
	atualizarVelocidade()

	humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
		atualizarVelocidade()
	end)
end

player.CharacterAdded:Connect(onCharacterAdded)
if player.Character then
	onCharacterAdded(player.Character)
else
	player.CharacterAdded:Wait()
end

-- Velocidade
criarBotao(frame, "⚡ Velocidade", 20, function()
	speedEnabled = not speedEnabled
	atualizarVelocidade()
	showNotification(speedEnabled and "Velocidade ativada!" or "Velocidade desativada!")
end)

-- BOTÃO SUBIR
local frameSubir = Instance.new("TextButton")
frameSubir.Size = UDim2.new(0, 90, 0, 30)
frameSubir.Position = UDim2.new(0, 10, 0.8, 0)
frameSubir.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frameSubir.Text = "↑ Subir"
frameSubir.TextColor3 = Color3.fromRGB(255, 0, 0)
frameSubir.Font = Enum.Font.GothamBold
frameSubir.TextSize = 16
frameSubir.Active = true
frameSubir.Parent = screenGui

local subirCorner = Instance.new("UICorner")
subirCorner.CornerRadius = UDim.new(0, 6)
subirCorner.Parent = frameSubir

enableDrag(frameSubir)

frameSubir.MouseButton1Click:Connect(function()
	local rootPart = getRootPart()
	if rootPart then
		local destino = rootPart.Position + Vector3.new(0, altura, 0)

		local result = workspace:Raycast(rootPart.Position, Vector3.new(0, altura, 0), RaycastParams.new())
		if result then
			showNotification("Algo está acima!")
		end

		if plataformaAerea then
			plataformaAerea:Destroy()
		end

		plataformaAerea = Instance.new("Part")
		plataformaAerea.Size = Vector3.new(20, 4, 20)
		plataformaAerea.Position = destino - Vector3.new(0, 2, 0)
		plataformaAerea.Anchored = true
		plataformaAerea.Transparency = 1
		plataformaAerea.CanCollide = true
		plataformaAerea.Name = "PlataformaAerea"
		plataformaAerea.Parent = workspace

		rootPart.CFrame = CFrame.new(destino, destino + rootPart.CFrame.LookVector)
		showNotification("Teleportado para cima!")
	end
end)

-- BOTÃO DESCER
local frameDescer = Instance.new("TextButton")
frameDescer.Size = UDim2.new(0, 90, 0, 30)
frameDescer.Position = UDim2.new(0, 110, 0.8, 0)
frameDescer.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frameDescer.Text = "↓ Descer"
frameDescer.TextColor3 = Color3.fromRGB(255, 0, 0)
frameDescer.Font = Enum.Font.GothamBold
frameDescer.TextSize = 16
frameDescer.Active = true
frameDescer.Parent = screenGui

local descerCorner = Instance.new("UICorner")
descerCorner.CornerRadius = UDim.new(0, 6)
descerCorner.Parent = frameDescer

enableDrag(frameDescer)

frameDescer.MouseButton1Click:Connect(function()
	local rootPart = getRootPart()
	if rootPart then
		rootPart.CFrame = rootPart.CFrame - Vector3.new(0, altura, 0)
		if plataformaAerea then
			plataformaAerea:Destroy()
			plataformaAerea = nil
		end
		showNotification("Você desceu!")
	end
end)
