-- Serviços do Roblox
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- 1. Interface Principal (ScreenGui)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MenuXRayBolinhaPreta"
ScreenGui.Parent = playerGui
ScreenGui.ResetOnSpawn = false

-- 2. Criar a Bolinha (Botão de Abrir/Fechar) - FUNDO PRETO
local OpenButton = Instance.new("TextButton")
OpenButton.Name = "ToggleBall"
OpenButton.Size = UDim2.new(0, 50, 0, 50)
OpenButton.Position = UDim2.new(0.1, 0, 0.5, 0)
OpenButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- FUNDO PRETO
OpenButton.Text = "X-RAY"
OpenButton.TextColor3 = Color3.new(1,1,1) -- TEXTO BRANCO
OpenButton.Font = Enum.Font.GothamBold
OpenButton.TextSize = 10
OpenButton.Parent = ScreenGui
OpenButton.ZIndex = 10

local ballCorner = Instance.new("UICorner")
ballCorner.CornerRadius = UDim.new(1, 0)
ballCorner.Parent = OpenButton

local ballStroke = Instance.new("UIStroke")
ballStroke.Thickness = 2
ballStroke.Color = Color3.new(1,1,1) -- CONTORNO BRANCO
ballStroke.Parent = OpenButton

-- 3. Painel Principal (Começa fechado)
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 320, 0, 350)
MainFrame.Position = UDim2.new(0.5, -160, 0.5, -175)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.Visible = false
MainFrame.Active = true 
MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame)

-- Alternar Menu ao clicar na Bolinha
OpenButton.MouseButton1Click:Connect(function()
	MainFrame.Visible = not MainFrame.Visible
end)

-- Barra de Título
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TitleBar.Parent = MainFrame
Instance.new("UICorner", TitleBar)

local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(1, 0, 1, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "X-RAY CONTROL PANEL"
TitleText.TextColor3 = Color3.new(1,1,1)
TitleText.Font = Enum.Font.GothamBold
TitleText.Parent = TitleBar

-- Área de Rolagem (Scroll)
local Scroll = Instance.new("ScrollingFrame")
Scroll.Size = UDim2.new(1, -10, 1, -50)
Scroll.Position = UDim2.new(0, 5, 0, 45)
Scroll.BackgroundTransparency = 1
Scroll.CanvasSize = UDim2.new(0, 0, 0, 380) 
Scroll.ScrollBarThickness = 8
Scroll.Parent = MainFrame

-- Função de Slider
local function createSlider(name, yPos, min, max)
	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 0, 20)
	label.Position = UDim2.new(0, 0, 0, yPos)
	label.Text = name .. ": 0"
	label.TextColor3 = Color3.new(1,1,1)
	label.BackgroundTransparency = 1
	label.Font = Enum.Font.GothamBold
	label.Parent = Scroll

	local sliderFrame = Instance.new("Frame")
	sliderFrame.Size = UDim2.new(0.8, 0, 0, 6)
	sliderFrame.Position = UDim2.new(0.1, 0, 0, yPos + 25)
	sliderFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
	sliderFrame.Parent = Scroll
	Instance.new("UICorner", sliderFrame)

	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(0, 18, 0, 18)
	btn.Position = UDim2.new(0.5, -9, 0.5, -9)
	btn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
	btn.Text = ""
	btn.Parent = sliderFrame
	Instance.new("UICorner", btn).CornerRadius = UDim.new(1, 0)

	local value = 0
	local dragging = false

	btn.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = true end
	end)
	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
	end)

	RunService.RenderStepped:Connect(function()
		if dragging then
			local mousePos = UserInputService:GetMouseLocation().X
			local framePos = sliderFrame.AbsolutePosition.X
			local frameWidth = sliderFrame.AbsoluteSize.X
			local percentage = math.clamp((mousePos - framePos) / frameWidth, 0, 1)
			btn.Position = UDim2.new(percentage, -9, 0.5, -9)
			value = min + (max - min) * percentage
			label.Text = name .. ": " .. math.floor(value)
		end
	end)
	return function() return value end
end

-- Itens do Menu
local inputSize = Instance.new("TextBox")
inputSize.Size = UDim2.new(0.8, 0, 0, 35)
inputSize.Position = UDim2.new(0.1, 0, 0, 10)
inputSize.PlaceholderText = "Tamanho (20)"
inputSize.Text = "20"
inputSize.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
inputSize.TextColor3 = Color3.new(1,1,1)
inputSize.Parent = Scroll
Instance.new("UICorner", inputSize)

local getX = createSlider("LADO (X)", 60, -50, 50)
local getY = createSlider("ALTURA (Y)", 120, -50, 50)
local getZ = createSlider("FRENTE (Z)", 180, -50, 50)

local ActiveBtn = Instance.new("TextButton")
ActiveBtn.Size = UDim2.new(0.8, 0, 0, 40)
ActiveBtn.Position = UDim2.new(0.1, 0, 0, 250)
ActiveBtn.Text = "ATIVAR / ATUALIZAR"
ActiveBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 100)
ActiveBtn.TextColor3 = Color3.new(1,1,1)
ActiveBtn.Parent = Scroll
Instance.new("UICorner", ActiveBtn)

local DeactiveBtn = Instance.new("TextButton")
DeactiveBtn.Size = UDim2.new(0.8, 0, 0, 40)
DeactiveBtn.Position = UDim2.new(0.1, 0, 0, 305)
DeactiveBtn.Text = "DESATIVAR TUDO"
DeactiveBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
DeactiveBtn.TextColor3 = Color3.new(1,1,1)
DeactiveBtn.Parent = Scroll
Instance.new("UICorner", DeactiveBtn)

-- 4. Lógica do X-Ray
local oQuadrado = nil
local originalTransparencies = {}
local connection = nil

local function restoreMap()
	for part, originalValue in pairs(originalTransparencies) do
		if part and part.Parent then part.Transparency = originalValue end
	end
	originalTransparencies = {}
end

ActiveBtn.MouseButton1Click:Connect(function()
	local char = player.Character or player.CharacterAdded:Wait()
	local root = char:FindFirstChild("HumanoidRootPart")
	if not oQuadrado or not oQuadrado.Parent then
		oQuadrado = Instance.new("Part")
		oQuadrado.CanCollide = false
		oQuadrado.Anchored = true
		oQuadrado.Transparency = 0.8
		oQuadrado.Parent = workspace
		Instance.new("SelectionBox", oQuadrado).Adornee = oQuadrado
	end
	if connection then connection:Disconnect() end
	connection = RunService.Heartbeat:Connect(function()
		if not oQuadrado or not root then return end
		local sVal = (tonumber(inputSize.Text) or 20) * 5
		oQuadrado.Size = Vector3.new(sVal, sVal, sVal)
		oQuadrado.CFrame = root.CFrame * CFrame.new(getX(), getY(), getZ())
		local params = OverlapParams.new()
		params.FilterType = Enum.RaycastFilterType.Exclude
		params.FilterDescendantsInstances = {char, oQuadrado}
		local parts = workspace:GetPartBoundsInBox(oQuadrado.CFrame, oQuadrado.Size, params)
		local current = {}
		for _, p in pairs(parts) do
			if p:IsA("BasePart") then
				current[p] = true
				if not originalTransparencies[p] then originalTransparencies[p] = p.Transparency end
				p.Transparency = 0.6
			end
		end
		for p, orig in pairs(originalTransparencies) do
			if not current[p] then p.Transparency = orig originalTransparencies[p] = nil end
		end
	end)
end)

DeactiveBtn.MouseButton1Click:Connect(function()
	if connection then connection:Disconnect() connection = nil end
	restoreMap()
	if oQuadrado then oQuadrado:Destroy() oQuadrado = nil end
end)

-- 5. Função de Arrastar (Para o Botão e para o Menu)
local function makeDraggable(obj, dragHandle)
	local dragging, dragInput, dragStart, startPos
	dragHandle.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true dragStart = input.Position startPos = obj.Position
			input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end)
		end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			local delta = input.Position - dragStart
			obj.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)
end

makeDraggable(MainFrame, TitleBar)
makeDraggable(OpenButton, OpenButton)
