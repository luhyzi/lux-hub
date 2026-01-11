local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- CONFIGURAÇÕES GERAIS
local Settings = {
	ESP = false,
	Noclip = false,
	Speed = false,
	SpeedVal = 100,
	Fly = false,
	Invisible = false,
	TargetPlayer = nil
}

-- CORES DO TEMA (Baseadas nas imagens)
local Colors = {
	MainBG = Color3.fromRGB(60, 30, 100),       -- Roxo Principal
	Sidebar = Color3.fromRGB(45, 20, 80),       -- Roxo Escuro Lateral
	ItemBG = Color3.fromRGB(50, 25, 90),        -- Fundo dos botões
	ToggleOff = Color3.fromRGB(30, 30, 30),     -- Cinza escuro
	ToggleOn = Color3.fromRGB(255, 255, 255),   -- Branco ativo
	Text = Color3.fromRGB(255, 255, 255),
	Accent = Color3.fromRGB(160, 100, 255)
}

-- --- CRIAÇÃO DA GUI ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_Purple_Final"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- ==========================================
-- 1. TELA DE LOADING (LOADING SCREEN)
-- ==========================================
local LoadingFrame = Instance.new("Frame")
LoadingFrame.Name = "LoadingFrame"
LoadingFrame.Size = UDim2.new(0, 300, 0, 180)
LoadingFrame.Position = UDim2.new(0.5, -150, 0.5, -90)
LoadingFrame.BackgroundColor3 = Colors.MainBG
LoadingFrame.Parent = ScreenGui
Instance.new("UICorner", LoadingFrame).CornerRadius = UDim.new(0, 20)

local LoadTitle = Instance.new("TextLabel")
LoadTitle.Text = "Loading"
LoadTitle.Font = Enum.Font.GothamBold
LoadTitle.TextSize = 28
LoadTitle.TextColor3 = Colors.Text
LoadTitle.Size = UDim2.new(1, 0, 0, 50)
LoadTitle.Position = UDim2.new(0, 0, 0, 30)
LoadTitle.BackgroundTransparency = 1
LoadTitle.Parent = LoadingFrame

local BarBG = Instance.new("Frame")
BarBG.Size = UDim2.new(0.8, 0, 0, 10)
BarBG.Position = UDim2.new(0.1, 0, 0.5, 0)
BarBG.BackgroundColor3 = Color3.fromRGB(40, 20, 70)
BarBG.Parent = LoadingFrame
Instance.new("UICorner", BarBG).CornerRadius = UDim.new(1, 0)

local BarFill = Instance.new("Frame")
BarFill.Size = UDim2.new(0, 0, 1, 0) -- Começa vazio
BarFill.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
BarFill.Parent = BarBG
Instance.new("UICorner", BarFill).CornerRadius = UDim.new(1, 0)

local LoadFooter = Instance.new("TextLabel")
LoadFooter.Text = "Lux Hub"
LoadFooter.Font = Enum.Font.GothamBold
LoadFooter.TextSize = 18
LoadFooter.TextColor3 = Color3.fromRGB(150, 150, 200)
LoadFooter.Size = UDim2.new(1, 0, 0, 30)
LoadFooter.Position = UDim2.new(0, 0, 0.8, 0)
LoadFooter.BackgroundTransparency = 1
LoadFooter.Parent = LoadingFrame

-- ==========================================
-- 2. JANELA PRINCIPAL (MAIN UI)
-- ==========================================
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainHub"
MainFrame.Size = UDim2.new(0, 600, 0, 400)
MainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)
MainFrame.BackgroundColor3 = Colors.MainBG
MainFrame.Visible = false -- Começa invisível (esperando loading)
MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 25)

-- Arrastar (Drag)
local dragging, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position
	end
end)
UserInputService.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)
UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
end)

-- Sidebar
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 160, 1, 0)
Sidebar.BackgroundColor3 = Colors.Sidebar
Sidebar.Parent = MainFrame
Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 25)
-- Fix para o canto direito não ser redondo na sidebar
local FixCorner = Instance.new("Frame")
FixCorner.Size = UDim2.new(0, 20, 1, 0)
FixCorner.Position = UDim2.new(1, -10, 0, 0)
FixCorner.BackgroundColor3 = Colors.Sidebar
FixCorner.BorderSizePixel = 0
FixCorner.ZIndex = 0
FixCorner.Parent = Sidebar

local Title = Instance.new("TextLabel")
Title.Text = "Lux Hub"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 24
Title.TextColor3 = Colors.Text
Title.Position = UDim2.new(0, 20, 0, 20)
Title.Size = UDim2.new(0, 100, 0, 30)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Sidebar

local SubVer = Instance.new("TextLabel")
SubVer.Text = "V1.0"
SubVer.Font = Enum.Font.Gotham
SubVer.TextSize = 12
SubVer.TextColor3 = Color3.fromRGB(180,180,180)
SubVer.Position = UDim2.new(0, 22, 0, 45)
SubVer.BackgroundTransparency = 1
SubVer.TextXAlignment = Enum.TextXAlignment.Left
SubVer.Parent = Sidebar

-- Abas
local TabContainer = Instance.new("Frame")
TabContainer.Position = UDim2.new(0, 10, 0, 80)
TabContainer.Size = UDim2.new(1, -20, 1, -90)
TabContainer.BackgroundTransparency = 1
TabContainer.Parent = Sidebar
local UIList = Instance.new("UIListLayout"); UIList.Padding = UDim.new(0, 10); UIList.Parent = TabContainer

local function CreateTab(name)
	local Btn = Instance.new("TextButton")
	Btn.Size = UDim2.new(1, 0, 0, 35)
	Btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	Btn.BackgroundTransparency = 0.9
	Btn.Text = name
	Btn.Font = Enum.Font.GothamBold
	Btn.TextColor3 = Colors.Text
	Btn.Parent = TabContainer
	Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 10)
	return Btn
end

local MainTab = CreateTab("Main")
local ServerTab = CreateTab("Server")

-- Botão Fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.new(1,1,1)
CloseBtn.BackgroundTransparency = 1
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 18
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 10)
CloseBtn.Parent = MainFrame
CloseBtn.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

-- Páginas
local PageContainer = Instance.new("Frame")
PageContainer.Size = UDim2.new(1, -170, 1, -20)
PageContainer.Position = UDim2.new(0, 170, 0, 10)
PageContainer.BackgroundTransparency = 1
PageContainer.Parent = MainFrame

local MainPage = Instance.new("ScrollingFrame")
MainPage.Size = UDim2.new(1, 0, 1, 0)
MainPage.BackgroundTransparency = 1
MainPage.ScrollBarThickness = 2
MainPage.Parent = PageContainer
local MainList = Instance.new("UIListLayout"); MainList.Padding = UDim.new(0, 8); MainList.Parent = MainPage

local ServerPage = Instance.new("Frame")
ServerPage.Size = UDim2.new(1, 0, 1, 0)
ServerPage.BackgroundTransparency = 1
ServerPage.Visible = false
ServerPage.Parent = PageContainer

local PageTitle = Instance.new("TextLabel")
PageTitle.Text = "Main"
PageTitle.Font = Enum.Font.GothamBold
PageTitle.TextSize = 22
PageTitle.TextColor3 = Color3.fromRGB(200, 200, 200)
PageTitle.Size = UDim2.new(1, 0, 0, 40)
PageTitle.BackgroundTransparency = 1
PageTitle.TextXAlignment = Enum.TextXAlignment.Left
PageTitle.Parent = MainPage

-- Lógica Abas
MainTab.BackgroundColor3 = Color3.fromRGB(100, 100, 100); MainTab.BackgroundTransparency = 0.5
MainTab.MouseButton1Click:Connect(function()
	MainPage.Visible = true; ServerPage.Visible = false
	MainTab.BackgroundTransparency = 0.5; ServerTab.BackgroundTransparency = 0.9
end)
ServerTab.MouseButton1Click:Connect(function()
	MainPage.Visible = false; ServerPage.Visible = true
	MainTab.BackgroundTransparency = 0.9; ServerTab.BackgroundTransparency = 0.5
end)

-- ==========================================
-- 3. CRIAÇÃO DE ITENS (FUNÇÕES)
-- ==========================================

local function AddToggle(text, callback)
	local Frame = Instance.new("Frame")
	Frame.Size = UDim2.new(1, -10, 0, 40)
	Frame.BackgroundColor3 = Colors.ItemBG
	Frame.Parent = MainPage
	Instance.new("UICorner", Frame).CornerRadius = UDim.new(0, 10)
	
	local Lbl = Instance.new("TextLabel")
	Lbl.Text = text
	Lbl.Font = Enum.Font.GothamBold
	Lbl.TextColor3 = Colors.Text
	Lbl.TextXAlignment = Enum.TextXAlignment.Left
	Lbl.Position = UDim2.new(0, 15, 0, 0)
	Lbl.Size = UDim2.new(0.6, 0, 1, 0)
	Lbl.BackgroundTransparency = 1
	Lbl.Parent = Frame
	
	local Btn = Instance.new("TextButton")
	Btn.Text = ""
	Btn.Size = UDim2.new(0, 24, 0, 24)
	Btn.Position = UDim2.new(1, -35, 0.5, -12)
	Btn.BackgroundColor3 = Colors.ToggleOff
	Btn.Parent = Frame
	Instance.new("UICorner", Btn).CornerRadius = UDim.new(1, 0)
	
	local state = false
	Btn.MouseButton1Click:Connect(function()
		state = not state
		if state then
			Btn.BackgroundColor3 = Colors.ToggleOn
		else
			Btn.BackgroundColor3 = Colors.ToggleOff
		end
		callback(state)
	end)
end

local function AddSpeedInput()
	local Frame = Instance.new("Frame")
	Frame.Size = UDim2.new(1, -10, 0, 40)
	Frame.BackgroundColor3 = Colors.ItemBG
	Frame.Parent = MainPage
	Instance.new("UICorner", Frame).CornerRadius = UDim.new(0, 10)
	
	local Lbl = Instance.new("TextLabel")
	Lbl.Text = "Speed"
	Lbl.Font = Enum.Font.GothamBold
	Lbl.TextColor3 = Colors.Text
	Lbl.TextXAlignment = Enum.TextXAlignment.Left
	Lbl.Position = UDim2.new(0, 15, 0, 0)
	Lbl.Size = UDim2.new(0.3, 0, 1, 0)
	Lbl.BackgroundTransparency = 1
	Lbl.Parent = Frame
	
	local Input = Instance.new("TextBox")
	Input.Text = "100"
	Input.BackgroundColor3 = Colors.Sidebar
	Input.TextColor3 = Colors.Text
	Input.Size = UDim2.new(0, 60, 0, 24)
	Input.Position = UDim2.new(0.6, 0, 0.5, -12)
	Input.Parent = Frame
	Instance.new("UICorner", Input).CornerRadius = UDim.new(0, 8)
	
	Input.FocusLost:Connect(function()
		Settings.SpeedVal = tonumber(Input.Text) or 100
	end)

	local Btn = Instance.new("TextButton")
	Btn.Text = ""
	Btn.Size = UDim2.new(0, 24, 0, 24)
	Btn.Position = UDim2.new(1, -35, 0.5, -12)
	Btn.BackgroundColor3 = Colors.ToggleOff
	Btn.Parent = Frame
	Instance.new("UICorner", Btn).CornerRadius = UDim.new(1, 0)
	
	Btn.MouseButton1Click:Connect(function()
		Settings.Speed = not Settings.Speed
		Btn.BackgroundColor3 = Settings.Speed and Colors.ToggleOn or Colors.ToggleOff
	end)
end

local function AddTPSection()
	local Container = Instance.new("Frame")
	Container.Size = UDim2.new(1, -10, 0, 160)
	Container.BackgroundTransparency = 1
	Container.Parent = MainPage
	
	-- Lista (Esquerda)
	local ListFrame = Instance.new("ScrollingFrame")
	ListFrame.Size = UDim2.new(0.4, 0, 1, 0)
	ListFrame.BackgroundColor3 = Colors.ItemBG
	ListFrame.Parent = Container
	Instance.new("UICorner", ListFrame).CornerRadius = UDim.new(0, 10)
	local Layout = Instance.new("UIListLayout"); Layout.Parent = ListFrame
	
	-- Área de Ação (Direita)
	local ActionFrame = Instance.new("Frame")
	ActionFrame.Size = UDim2.new(0.55, 0, 0, 50)
	ActionFrame.Position = UDim2.new(0.45, 0, 0, 0)
	ActionFrame.BackgroundColor3 = Colors.ItemBG
	ActionFrame.Parent = Container
	Instance.new("UICorner", ActionFrame).CornerRadius = UDim.new(0, 10)
	
	local TpLbl = Instance.new("TextLabel")
	TpLbl.Text = "TP"
	TpLbl.Font = Enum.Font.GothamBold; TpLbl.TextColor3 = Colors.Text
	TpLbl.Position = UDim2.new(0, 10, 0, 0); TpLbl.Size = UDim2.new(0.3, 0, 1, 0)
	TpLbl.BackgroundTransparency = 1; TpLbl.Parent = ActionFrame
	
	local ReadyBtn = Instance.new("TextButton")
	ReadyBtn.Text = "Ready"
	ReadyBtn.BackgroundColor3 = Colors.Sidebar
	ReadyBtn.TextColor3 = Colors.Text
	ReadyBtn.Size = UDim2.new(0, 60, 0, 30)
	ReadyBtn.Position = UDim2.new(1, -70, 0.5, -15)
	ReadyBtn.Parent = ActionFrame
	Instance.new("UICorner", ReadyBtn).CornerRadius = UDim.new(0, 8)
	
	-- Lógica TP
	local function UpdateList()
		for _, v in pairs(ListFrame:GetChildren()) do if v:IsA("TextButton") then v:Destroy() end end
		for _, plr in pairs(Players:GetPlayers()) do
			if plr ~= LocalPlayer then
				local b = Instance.new("TextButton")
				b.Size = UDim2.new(1, 0, 0, 30)
				b.Text = plr.Name
				b.TextColor3 = Colors.Text
				b.BackgroundTransparency = 1
				b.Parent = ListFrame
				b.MouseButton1Click:Connect(function()
					Settings.TargetPlayer = plr
					ReadyBtn.Text = "Go!"
				end)
			end
		end
	end
	UpdateList()
	-- Atualizar lista ao clicar
	ListFrame.MouseEnter:Connect(UpdateList)
	
	ReadyBtn.MouseButton1Click:Connect(function()
		if Settings.TargetPlayer and Settings.TargetPlayer.Character and Settings.TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
			LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.TargetPlayer.Character.HumanoidRootPart.CFrame
		else
			ReadyBtn.Text = "Select!"
		end
	end)
end

-- Server Hop Button
local ServerTitle = Instance.new("TextLabel")
ServerTitle.Text = "Server"
ServerTitle.Font = Enum.Font.GothamBold; ServerTitle.TextSize = 22; ServerTitle.TextColor3 = Color3.new(1,1,1)
ServerTitle.Size = UDim2.new(1, 0, 0, 40); ServerTitle.BackgroundTransparency = 1; ServerTitle.Parent = ServerPage

local HopBtn = Instance.new("TextButton")
HopBtn.Text = "Server Hop"
HopBtn.Size = UDim2.new(0, 150, 0, 40)
HopBtn.Position = UDim2.new(0, 0, 0, 50)
HopBtn.BackgroundColor3 = Colors.ItemBG
HopBtn.TextColor3 = Colors.Text
HopBtn.Font = Enum.Font.GothamBold
HopBtn.Parent = ServerPage
Instance.new("UICorner", HopBtn).CornerRadius = UDim.new(0, 20)

HopBtn.MouseButton1Click:Connect(function()
    -- Server Hop Logic
    local PlaceId = game.PlaceId
    local Servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
    for _, s in pairs(Servers.data) do
        if s.playing ~= s.maxPlayers then
            TeleportService:TeleportToPlaceInstance(PlaceId, s.id, LocalPlayer)
        end
    end
end)


-- ==========================================
-- 4. ADICIONANDO FUNÇÕES
-- ==========================================

AddToggle("Esp", function(v) Settings.ESP = v end)
AddToggle("No Clip", function(v) Settings.Noclip = v end)
AddSpeedInput()
AddToggle("Fly", function(v) Settings.Fly = v end)
AddToggle("Invisible", function(v) 
	Settings.Invisible = v
	if LocalPlayer.Character then
		for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
			if part:IsA("BasePart") or part:IsA("Decal") then
				part.Transparency = v and 1 or 0
			end
		end
	end
end)
AddTPSection()

-- ==========================================
-- 5. LÓGICA DE EXECUÇÃO (LOOPS)
-- ==========================================

-- LOADING ANIMATION
local tweenInfo = TweenInfo.new(3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
local tween = TweenService:Create(BarFill, tweenInfo, {Size = UDim2.new(1, 0, 1, 0)})
tween:Play()

tween.Completed:Connect(function()
	task.wait(0.5)
	LoadingFrame:Destroy()
	MainFrame.Visible = true
end)

-- ESP LOOP
RunService.RenderStepped:Connect(function()
	if Settings.ESP then
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer and p.Character then
				if not p.Character:FindFirstChild("LuxHighlight") then
					local h = Instance.new("Highlight")
					h.Name = "LuxHighlight"
					h.FillColor = Color3.fromRGB(150, 0, 255)
					h.OutlineColor = Color3.new(1,1,1)
					h.Parent = p.Character
				end
			end
		end
	else
		for _, p in pairs(Players:GetPlayers()) do
			if p.Character and p.Character:FindFirstChild("LuxHighlight") then
				p.Character.LuxHighlight:Destroy()
			end
		end
	end
end)

-- NOCLIP LOOP
RunService.Stepped:Connect(function()
	if Settings.Noclip and LocalPlayer.Character then
		for _, v in pairs(LocalPlayer.Character:GetDescendants()) do
			if v:IsA("BasePart") then v.CanCollide = false end
		end
	end
end)

-- SPEED LOOP
RunService.Heartbeat:Connect(function()
	if Settings.Speed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
		LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedVal
	end
end)

-- FLY LOOP
local bv, bg
RunService.RenderStepped:Connect(function()
	if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		local root = LocalPlayer.Character.HumanoidRootPart
		if not bv then
			bv = Instance.new("BodyVelocity", root)
			bv.MaxForce = Vector3.new(1e9, 1e9, 1e9)
			bg = Instance.new("BodyGyro", root)
			bg.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
		end
		bg.CFrame = Camera.CFrame
		bv.Velocity = Camera.CFrame.LookVector * 50
	else
		if bv then bv:Destroy(); bv = nil end
		if bg then bg:Destroy(); bg = nil end
	end
end)
