# Aninha123-- ==============================
-- 🌙 ANINHA HUB
-- ==============================

-- Serviços do Roblox
local Players = game:GetService("Players")
local PlayerGui = Players.LocalPlayer:WaitForChild("PlayerGui")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local StarterPlayer = game:GetService("StarterPlayer")

local Player = Players.LocalPlayer
local Character = Player.Character or Player.CharacterAdded:Wait()
local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
local Humanoid = Character:WaitForChild("Humanoid")

-- Variáveis
local isFlying = false
local flyingSpeed = 32
local speed = 16
local jumpPower = 50
local guiOpen = true
local dragToggle = false
local dragStart = nil
local startPos = nil

-- Criar Tela Principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AninhaHub"
ScreenGui.Parent = PlayerGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 280, 0, 320)
MainFrame.Position = UDim2.new(0.02, 0, 0.5, -160)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
MainFrame.BorderSizePixel = 0
MainFrame.CornerRadius = UDim.new(0, 12)
MainFrame.Parent = ScreenGui

-- Sombra
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MainFrame

-- Título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.BackgroundColor3 = Color3.fromRGB(45, 45, 70)
TitleBar.CornerRadius = UDim.new(0, 12)
TitleBar.Parent = MainFrame

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Text = "🌙 Aninha Hub"
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextColor3 = Color3.fromRGB(255, 200, 255)
TitleLabel.TextSize = 16
TitleLabel.Size = UDim2.new(1, -40, 1, 0)
TitleLabel.Position = UDim2.new(0, 10, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Parent = TitleBar

-- Botão de fechar/minimizar
local MinimizeBtn = Instance.new("TextButton")
MinimizeBtn.Text = "−"
MinimizeBtn.Font = Enum.Font.GothamBold
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 200, 255)
MinimizeBtn.TextSize = 18
MinimizeBtn.Size = UDim2.new(0, 30, 0, 30)
MinimizeBtn.Position = UDim2.new(1, -35, 0, 5)
MinimizeBtn.BackgroundTransparency = 1
MinimizeBtn.Parent = TitleBar

-- Conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Size = UDim2.new(1, -20, 1, -50)
ContentFrame.Position = UDim2.new(0, 10, 0, 45)
ContentFrame.BackgroundTransparency = 1
ContentFrame.Parent = MainFrame

-- Função para criar botão
local function createButton(name, yPos, callback)
	local btn = Instance.new("TextButton")
	btn.Text = name
	btn.Font = Enum.Font.Gotham
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.TextSize = 14
	btn.Size = UDim2.new(1, 0, 0, 35)
	btn.Position = UDim2.new(0, 0, 0, yPos)
	btn.BackgroundColor3 = Color3.fromRGB(55, 55, 85)
	btn.CornerRadius = UDim.new(0, 8)
	btn.Parent = ContentFrame
	btn.MouseButton1Click:Connect(callback)
	return btn
end

-- Função para criar slider
local function createSlider(name, yPos, min, max, default, callback)
	local container = Instance.new("Frame")
	container.Size = UDim2.new(1, 0, 0, 50)
	container.Position = UDim2.new(0, 0, 0, yPos)
	container.BackgroundTransparency = 1
	container.Parent = ContentFrame

	local label = Instance.new("TextLabel")
	label.Text = name .. ": " .. default
	label.Font = Enum.Font.Gotham
	label.TextColor3 = Color3.fromRGB(230, 230, 255)
	label.TextSize = 13
	label.Size = UDim2.new(1, 0, 0, 20)
	label.Position = UDim2.new(0, 0, 0, 0)
	label.BackgroundTransparency = 1
	label.TextXAlignment = Enum.TextXAlignment.Left
	label.Parent = container

	local sliderBg = Instance.new("Frame")
	sliderBg.Size = UDim2.new(1, 0, 0, 10)
	sliderBg.Position = UDim2.new(0, 0, 0, 25)
	sliderBg.BackgroundColor3 = Color3.fromRGB(80, 80, 110)
	sliderBg.CornerRadius = UDim.new(0, 4)
	sliderBg.Parent = container

	local sliderFill = Instance.new("Frame")
	sliderFill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
	sliderFill.BackgroundColor3 = Color3.fromRGB(200, 150, 255)
	sliderFill.CornerRadius = UDim.new(0, 4)
	sliderFill.Parent = sliderBg

	local isDragging = false
	sliderBg.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			isDragging = true
		end
	end)
	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			isDragging = false
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if isDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
			local pos = UserInputService:GetMouseLocation()
			local absPos = sliderBg.AbsolutePosition
			local size = sliderBg.AbsoluteSize.X
			local percent = math.clamp((pos.X - absPos.X) / size, 0, 1)
			local value = math.floor(min + (max - min) * percent)
			sliderFill.Size = UDim2.new(percent, 0, 1, 0)
			label.Text = name .. ": " .. value
			callback(value)
		end
	end)

	return container
end

-- 🦋 Botão de Voar
local flyBtn = createButton("🦋 Voar: DESLIGADO", 10, function()
	isFlying = not isFlying
	flyBtn.Text = isFlying and "🦋 Voar: LIGADO" or "🦋 Voar: DESLIGADO"
	flyBtn.BackgroundColor3 = isFlying and Color3.fromRGB(100, 200, 150) or Color3.fromRGB(55, 55, 85)
end)

-- 🚀 WalkSpeed (1 a 50)
createSlider("🚀 WalkSpeed", 60, 1, 50, 16, function(val)
	speed = val
	Humanoid.WalkSpeed = speed
end)

-- 🦘 JumpPower (1 a 200)
createSlider("🦘 JumpPower", 130, 1, 200, 50, function(val)
	jumpPower = val
	Humanoid.JumpPower = jumpPower
end)

-- Sistema de Voar
local function flyLoop()
	local Connections = {}
	local function updateFly()
		if not isFlying then
			for _, conn in pairs(Connections) do conn:Disconnect() end
			Connections = {}
			Humanoid.PlatformStand = false
			return
		end

		Humanoid.PlatformStand = true
		local camCF = workspace.CurrentCamera.CFrame

		local conn = RunService.RenderStepped:Connect(function()
			if not isFlying then return end
			local moveDir = Vector3.new()

			if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir += camCF.LookVector end
			if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir -= camCF.LookVector end
			if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir -= camCF.RightVector end
			if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir += camCF.RightVector end
			if UserInputService:IsKeyDown.Space then moveDir += Vector3.new(0, 1, 0) end
			if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDir -= Vector3.new(0, 1, 0) end

			moveDir = moveDir.Unit * flyingSpeed
			HumanoidRootPart.Velocity = moveDir
		end)
		table.insert(Connections, conn)
	end

	while task.wait(0.1) do
		updateFly()
	end
end
task.spawn(flyLoop)

-- Arrastar a janela
TitleBar.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragToggle = true
		dragStart = input.Position
		startPos = MainFrame.Position
	end
end)
UserInputService.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement and dragToggle then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(
			startPos.X.Scale, startPos.X.Offset + delta.X,
			startPos.Y.Scale, startPos.Y.Offset + delta.Y
		)
	end
end)
UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragToggle = false
	end
end)

-- Minimizar
local minimized = false
MinimizeBtn.MouseButton1Click:Connect(function()
	minimized = not minimized
	ContentFrame.Visible = not minimized
	MainFrame.Size = minimized and UDim2.new(0, 280, 0, 40) or UDim2.new(0, 280, 0, 320)
	MinimizeBtn.Text = minimized and "+" or "−"
end)

-- Atualizar personagem
Player.CharacterAdded:Connect(function(newChar)
	Character = newChar
	HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
	Humanoid = Character:WaitForChild("Humanoid")
	Humanoid.WalkSpeed = speed
	Humanoid.JumpPower = jumpPower
end)

print("🌙 Aninha Hub carregado com sucesso!")
