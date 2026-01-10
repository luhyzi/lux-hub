-- SERVIÇOS
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")

-- VARIÁVEIS DO JOGADOR
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES
local Settings = {
	ESP = false,
	Noclip = false,
	TargetPlayer = nil
}

-- CRIAR A INTERFACE (GUI)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHubUI"
-- Proteção básica contra detecção de GUI (se estiver usando executor)
if syn and syn.protect_gui then
    syn.protect_gui(ScreenGui)
    ScreenGui.Parent = CoreGui
elseif gethui then
    ScreenGui.Parent = gethui()
else
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end

-- --- ESTILIZAÇÃO (DESIGN) ---
-- Cores baseadas na sua imagem
local DarkBg = Color3.fromRGB(45, 45, 45)
local DarkerBg = Color3.fromRGB(30, 30, 30)
local AccentColor = Color3.fromRGB(60, 60, 60)
local TextColor = Color3.fromRGB(240, 240, 240)

-- Main Frame (Janela Principal)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 500, 0, 300)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.new(0,0,0) -- Fundo preto da borda
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false -- Começa invisível
MainFrame.Parent = ScreenGui

local UICornerMain = Instance.new("UICorner")
UICornerMain.CornerRadius = UDim.new(0, 20)
UICornerMain.Parent = MainFrame

-- Barra Lateral (Esquerda)
local SideBar = Instance.new("Frame")
SideBar.Name = "SideBar"
SideBar.Size = UDim2.new(0, 150, 1, -10)
SideBar.Position = UDim2.new(0, 5, 0, 5)
SideBar.BackgroundColor3 = DarkBg
SideBar.BorderSizePixel = 0
SideBar.Parent = MainFrame

local UICornerSide = Instance.new("UICorner")
UICornerSide.CornerRadius = UDim.new(0, 15)
UICornerSide.Parent = SideBar

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 24
Title.TextColor3 = Color3.fromRGB(200, 200, 200)
Title.Size = UDim2.new(1, 0, 0, 50)
Title.BackgroundTransparency = 1
Title.Parent = SideBar

local TabButton = Instance.new("TextButton")
TabButton.Text = "Man"
TabButton.Font = Enum.Font.GothamBold
TabButton.TextColor3 = TextColor
TabButton.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
TabButton.Size = UDim2.new(0.8, 0, 0, 30)
TabButton.Position = UDim2.new(0.1, 0, 0, 60)
TabButton.Parent = SideBar
local UICornerTab = Instance.new("UICorner"); UICornerTab.CornerRadius = UDim.new(0, 8); UICornerTab.Parent = TabButton

-- Área de Conteúdo (Direita)
local ContentFrame = Instance.new("Frame")
ContentFrame.Name = "Content"
ContentFrame.Size = UDim2.new(1, -165, 1, -10)
ContentFrame.Position = UDim2.new(0, 160, 0, 5)
ContentFrame.BackgroundColor3 = DarkBg
ContentFrame.BorderSizePixel = 0
ContentFrame.Parent = MainFrame

local UICornerContent = Instance.new("UICorner")
UICornerContent.CornerRadius = UDim.new(0, 15)
UICornerContent.Parent = ContentFrame

-- Botão de Fechar (X)
local CloseBtn = Instance.new("TextButton")
CloseBtn.Text = "X"
CloseBtn.TextColor3 = TextColor
CloseBtn.BackgroundTransparency = 1
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 5)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Parent = ContentFrame
CloseBtn.MouseButton1Click:Connect(function()
	MainFrame.Visible = false
end)

-- FUNÇÃO PARA CRIAR TOGGLES (ESP e Noclip)
local function CreateToggle(name, position, callback)
	local ToggleFrame = Instance.new("Frame")
	ToggleFrame.Size = UDim2.new(0.9, 0, 0, 40)
	ToggleFrame.Position = position
	ToggleFrame.BackgroundColor3 = DarkerBg
	ToggleFrame.Parent = ContentFrame
	local UICorner = Instance.new("UICorner"); UICorner.CornerRadius = UDim.new(0, 10); UICorner.Parent = ToggleFrame
	
	local Label = Instance.new("TextLabel")
	Label.Text = name
	Label.Font = Enum.Font.GothamBold
	Label.TextColor3 = TextColor
	Label.TextXAlignment = Enum.TextXAlignment.Left
	Label.Position = UDim2.new(0, 15, 0, 0)
	Label.Size = UDim2.new(0.6, 0, 1, 0)
	Label.BackgroundTransparency = 1
	Label.Parent = ToggleFrame
	
	local SwitchInfo = {State = false}
	
	local SwitchBtn = Instance.new("TextButton")
	SwitchBtn.Text = ""
	SwitchBtn.Size = UDim2.new(0, 50, 0, 24)
	SwitchBtn.Position = UDim2.new(1, -60, 0.5, -12)
	SwitchBtn.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
	SwitchBtn.Parent = ToggleFrame
	local UIStroke = Instance.new("UIStroke"); UIStroke.Parent = SwitchBtn; UIStroke.Color = Color3.fromRGB(60,60,60); UIStroke.Thickness = 1
	local UICornerSw = Instance.new("UICorner"); UICornerSw.CornerRadius = UDim.new(1, 0); UICornerSw.Parent = SwitchBtn
	
	local Circle = Instance.new("Frame")
	Circle.Size = UDim2.new(0, 20, 0, 20)
	Circle.Position = UDim2.new(0, 2, 0.5, -10)
	Circle.BackgroundColor3 = Color3.fromRGB(150, 150, 150)
	Circle.Parent = SwitchBtn
	local UICornerCi = Instance.new("UICorner"); UICornerCi.CornerRadius = UDim.new(1, 0); UICornerCi.Parent = Circle
	
	SwitchBtn.MouseButton1Click:Connect(function()
		SwitchInfo.State = not SwitchInfo.State
		if SwitchInfo.State then
			Circle:TweenPosition(UDim2.new(0, 28, 0.5, -10), "Out", "Sine", 0.2, true)
			Circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
		else
			Circle:TweenPosition(UDim2.new(0, 2, 0.5, -10), "Out", "Sine", 0.2, true)
			Circle.BackgroundColor3 = Color3.fromRGB(150, 150, 150)
		end
		callback(SwitchInfo.State)
	end)
end

-- --- FUNCIONALIDADES ---

-- 1. ESP (Wallhack/Highlight)
local ESP_Highlights = {}
CreateToggle("Esp", UDim2.new(0.05, 0, 0, 50), function(state)
	Settings.ESP = state
	if state then
		-- Ativar
		RunService:BindToRenderStep("ESP_Loop", Enum.RenderPriority.Camera.Value, function()
			for _, plr in pairs(Players:GetPlayers()) do
				if plr ~= LocalPlayer and plr.Character then
					if not plr.Character:FindFirstChild("LuxHighlight") then
						local hl = Instance.new("Highlight")
						hl.Name = "LuxHighlight"
						hl.Adornee = plr.Character
						hl.FillColor = Color3.fromRGB(255, 0, 0)
						hl.FillTransparency = 0.5
						hl.OutlineColor = Color3.fromRGB(255, 255, 255)
						hl.Parent = plr.Character
					end
				end
			end
		end)
	else
		-- Desativar
		RunService:UnbindFromRenderStep("ESP_Loop")
		for _, plr in pairs(Players:GetPlayers()) do
			if plr.Character and plr.Character:FindFirstChild("LuxHighlight") then
				plr.Character.LuxHighlight:Destroy()
			end
		end
	end
end)

-- 2. NOCLIP (Atravessar Parede, mas tentar manter chão)
CreateToggle("Noclip", UDim2.new(0.05, 0, 0, 100), function(state)
	Settings.Noclip = state
	if state then
		RunService:BindToRenderStep("Noclip_Loop", Enum.RenderPriority.Camera.Value, function()
			if LocalPlayer.Character then
				for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
					if part:IsA("BasePart") and part.CanCollide == true then
						part.CanCollide = false
					end
				end
			end
		end)
	else
		RunService:UnbindFromRenderStep("Noclip_Loop")
		if LocalPlayer.Character then
            -- Tenta restaurar colisão (humanoide cuida disso geralmente ao andar)
            LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Physics)
		end
	end
end)

-- 3. DROP DOWN (TP Player)
local DropFrame = Instance.new("Frame")
DropFrame.Size = UDim2.new(0.9, 0, 0, 40)
DropFrame.Position = UDim2.new(0.05, 0, 0, 150)
DropFrame.BackgroundColor3 = DarkerBg
DropFrame.Parent = ContentFrame
local UICornerDrop = Instance.new("UICorner"); UICornerDrop.CornerRadius = UDim.new(0, 10); UICornerDrop.Parent = DropFrame

local DropLabel = Instance.new("TextLabel")
DropLabel.Text = "Tp Player"
DropLabel.Font = Enum.Font.GothamBold
DropLabel.TextColor3 = TextColor
DropLabel.TextXAlignment = Enum.TextXAlignment.Left
DropLabel.Position = UDim2.new(0, 15, 0, 0)
DropLabel.Size = UDim2.new(0.4, 0, 1, 0)
DropLabel.BackgroundTransparency = 1
DropLabel.Parent = DropFrame

local DropBtn = Instance.new("TextButton")
DropBtn.Text = "Selecione..."
DropBtn.Size = UDim2.new(0.5, 0, 0.8, 0)
DropBtn.Position = UDim2.new(0.45, 0, 0.1, 0)
DropBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
DropBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
DropBtn.Parent = DropFrame
local UICornerDB = Instance.new("UICorner"); UICornerDB.CornerRadius = UDim.new(0, 6); UICornerDB.Parent = DropBtn

local PlayerListFrame = Instance.new("ScrollingFrame")
PlayerListFrame.Size = UDim2.new(0.5, 0, 0, 150) -- Altura da lista
PlayerListFrame.Position = UDim2.new(0.5, 0, 0, 195) -- Embaixo do botão TP
PlayerListFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
PlayerListFrame.Visible = false
PlayerListFrame.ZIndex = 5
PlayerListFrame.Parent = ContentFrame
local UIListLayout = Instance.new("UIListLayout"); UIListLayout.Parent = PlayerListFrame

DropBtn.MouseButton1Click:Connect(function()
	PlayerListFrame.Visible = not PlayerListFrame.Visible
	-- Limpar lista antiga
	for _, child in pairs(PlayerListFrame:GetChildren()) do
		if child:IsA("TextButton") then child:Destroy() end
	end
	
	-- Povoar lista
	if PlayerListFrame.Visible then
		for _, plr in pairs(Players:GetPlayers()) do
			if plr ~= LocalPlayer then
				local pBtn = Instance.new("TextButton")
				pBtn.Text = plr.Name
				pBtn.Size = UDim2.new(1, 0, 0, 30)
				pBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
				pBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
				pBtn.Parent = PlayerListFrame
				
				pBtn.MouseButton1Click:Connect(function()
					Settings.TargetPlayer = plr
					DropBtn.Text = plr.Name
					PlayerListFrame.Visible = false
				end)
			end
		end
	end
end)

-- 4. BOTÃO "PRONTO" (Executar Teleporte)
local ProntoBtn = Instance.new("TextButton")
ProntoBtn.Text = "Pronto"
ProntoBtn.Font = Enum.Font.GothamBold
ProntoBtn.TextColor3 = TextColor
ProntoBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
ProntoBtn.Size = UDim2.new(0.4, 0, 0, 40)
ProntoBtn.Position = UDim2.new(0.05, 0, 0, 210)
ProntoBtn.Parent = ContentFrame
local UICornerPronto = Instance.new("UICorner"); UICornerPronto.CornerRadius = UDim.new(0, 20); UICornerPronto.Parent = ProntoBtn

ProntoBtn.MouseButton1Click:Connect(function()
	if Settings.TargetPlayer and Settings.TargetPlayer.Character and Settings.TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
		if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
			-- Lógica de Teleporte
			LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.TargetPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
		end
	else
		DropBtn.Text = "Jogador inválido"
		wait(1)
		DropBtn.Text = "Selecione..."
	end
end)


-- --- BOTÃO PARA ABRIR O SCRIPT (Toggle Mini) ---
local OpenFrame = Instance.new("Frame")
OpenFrame.Size = UDim2.new(0, 100, 0, 30)
OpenFrame.Position = UDim2.new(0, 10, 0.5, 0) -- Meio esquerda da tela
OpenFrame.BackgroundColor3 = DarkBg
OpenFrame.Parent = ScreenGui
local UICornerOpen = Instance.new("UICorner"); UICornerOpen.CornerRadius = UDim.new(0, 8); UICornerOpen.Parent = OpenFrame

local OpenBtn = Instance.new("TextButton")
OpenBtn.Size = UDim2.new(1, 0, 1, 0)
OpenBtn.BackgroundTransparency = 1
OpenBtn.Text = "Abrir Lux"
OpenBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenBtn.Font = Enum.Font.GothamBold
OpenBtn.Parent = OpenFrame

OpenBtn.MouseButton1Click:Connect(function()
	MainFrame.Visible = not MainFrame.Visible
end)

-- Arrastar Janela (Draggable)
local UserInputService = game:GetService("UserInputService")
local dragging, dragInput, dragStart, startPos

local function update(input)
	local delta = input.Position - dragStart
	MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position
		
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

MainFrame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		update(input)
	end
end)
