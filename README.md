local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- CONFIGURAÇÕES
local Settings = {
	ESP = false,
	Noclip = false,
	Speed = false,
	SpeedVal = 100,
	Fly = false,
	Invisible = false,
	TargetPlayer = nil
}

-- CORES (ROXO TEMA)
local Colors = {
	MainBG = Color3.fromRGB(60, 30, 100),       -- Roxo Fundo
	Sidebar = Color3.fromRGB(45, 20, 80),       -- Roxo Barra Lateral
	ItemBG = Color3.fromRGB(50, 25, 90),        -- Fundo dos botões
	ToggleOff = Color3.fromRGB(30, 30, 30),     -- Cinza Desativado
	ToggleOn = Color3.fromRGB(255, 255, 255),   -- Branco Ativado
	Text = Color3.fromRGB(200, 200, 200),       -- Texto Cinza Claro (Padrão)
	TextSelected = Color3.fromRGB(255, 255, 255), -- Texto Branco (Selecionado)
	Accent = Color3.fromRGB(160, 100, 255)
}

-- GUI PRINCIPAL
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_V1_Final"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- ==========================================
-- 1. TELA DE LOADING (AGORA MAIOR)
-- ==========================================
local LoadingFrame = Instance.new("Frame")
LoadingFrame.Size = UDim2.new(0, 350, 0, 220) -- Aumentei o tamanho
LoadingFrame.Position = UDim2.new(0.5, -175, 0.5, -110)
LoadingFrame.BackgroundColor3 = Colors.MainBG
LoadingFrame.Parent = ScreenGui
Instance.new("UICorner", LoadingFrame).CornerRadius = UDim.new(0, 20)

local LoadTitle = Instance.new("TextLabel")
LoadTitle.Text = "Loading..."
LoadTitle.Font = Enum.Font.GothamBold
LoadTitle.TextSize = 24
LoadTitle.TextColor3 = Colors.TextSelected
LoadTitle.Size = UDim2.new(1, 0, 0, 50)
LoadTitle.Position = UDim2.new(0, 0, 0.2, 0)
LoadTitle.BackgroundTransparency = 1
LoadTitle.Parent = LoadingFrame

local BarBG = Instance.new("Frame")
BarBG.Size = UDim2.new(0.8, 0, 0, 10)
BarBG.Position = UDim2.new(0.1, 0, 0.6, 0)
BarBG.BackgroundColor3 = Color3.fromRGB(30, 15, 60)
BarBG.Parent = LoadingFrame
Instance.new("UICorner", BarBG).CornerRadius = UDim.new(1, 0)

local BarFill = Instance.new("Frame")
BarFill.Size = UDim2.new(0, 0, 1, 0)
BarFill.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
BarFill.Parent = BarBG
Instance.new("UICorner", BarFill).CornerRadius = UDim.new(1, 0)

-- ==========================================
-- 2. BOTÃO REDONDO (ABRIR MENU)
-- ==========================================
local OpenBtn = Instance.new("ImageButton")
OpenBtn.Name = "OpenButton"
OpenBtn.Size = UDim2.new(0, 55, 0, 55)
OpenBtn.Position = UDim2.new(0, 20, 0.5, -27)
OpenBtn.BackgroundColor3 = Colors.MainBG
OpenBtn.Image = "rbxassetid://139243074283722" -- Sua Logo
OpenBtn.Visible = false -- Só aparece depois do loading
OpenBtn.Parent = ScreenGui
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1, 0)

-- Efeito de Brilho no Botão
local Glow = Instance.new("ImageLabel")
Glow.BackgroundTransparency = 1
Glow.Image = "rbxassetid://50288246"
Glow.Size = UDim2.new(1.6, 0, 1.6, 0)
Glow.Position = UDim2.new(-0.3, 0, -0.3, 0)
Glow.Parent = OpenBtn

-- ==========================================
-- 3. JANELA PRINCIPAL
-- ==========================================
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 480, 0, 300)
MainFrame.Position = UDim2.new(0.5, -240, 0.5, -150)
MainFrame.BackgroundColor3 = Colors.MainBG
MainFrame.Visible = false
MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 20)

-- Arrastar (Drag System)
local dragging, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true; dragStart = input.Position; startPos = MainFrame.Position
	end
end)
UserInputService.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)
UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)

-- Lógica do Botão Redondo (Abrir/Fechar)
OpenBtn.MouseButton1Click:Connect(function()
	MainFrame.Visible = not MainFrame.Visible
end)

-- Sidebar
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 140, 1, 0)
Sidebar.BackgroundColor3 = Colors.Sidebar
Sidebar.Parent = MainFrame
Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 20)
-- Retângulo para cobrir canto direito da sidebar
local Fix = Instance.new("Frame"); Fix.Size = UDim2.new(0,15,1,0); Fix.Position = UDim2.new(1,-15,0,0); Fix.BackgroundColor3 = Colors.Sidebar; Fix.BorderSizePixel=0; Fix.Parent=Sidebar

-- Título Centralizado
local Title = Instance.new("TextLabel")
Title.Text = "Lux Hub"; Title.Font = Enum.Font.GothamBold; Title.TextSize = 22
Title.TextColor3 = Colors.TextSelected
Title.Size = UDim2.new(1, 0, 0, 30) -- Ocupa toda a largura da sidebar
Title.Position = UDim2.new(0, 0, 0, 20)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Center -- CENTRALIZADO
Title.Parent = Sidebar

-- Versão Centralizada em Baixo
local SubVer = Instance.new("TextLabel")
SubVer.Text = "V1.0"; SubVer.Font = Enum.Font.Gotham; SubVer.TextSize = 12
SubVer.TextColor3 = Color3.fromRGB(180,180,180)
SubVer.Size = UDim2.new(1, 0, 0, 20)
SubVer.Position = UDim2.new(0, 0, 0, 45) -- Em baixo do título
SubVer.BackgroundTransparency = 1
SubVer.TextXAlignment = Enum.TextXAlignment.Center -- CENTRALIZADO
SubVer.Parent = Sidebar

-- Containers das Páginas
local PageContainer = Instance.new("Frame")
PageContainer.Size = UDim2.new(1, -150, 1, -20); PageContainer.Position = UDim2.new(0, 150, 0, 10)
PageContainer.BackgroundTransparency = 1; PageContainer.Parent = MainFrame

local MainPage = Instance.new("ScrollingFrame")
MainPage.Size = UDim2.new(1, 0, 1, 0); MainPage.BackgroundTransparency = 1; MainPage.ScrollBarThickness = 2; MainPage.Parent = PageContainer
local UIList = Instance.new("UIListLayout"); UIList.Padding = UDim.new(0, 8); UIList.Parent = MainPage

local ServerPage = Instance.new("Frame")
ServerPage.Size = UDim2.new(1, 0, 1, 0); ServerPage.BackgroundTransparency = 1; ServerPage.Visible = false; ServerPage.Parent = PageContainer

-- Abas (Menu Lateral)
local TabContainer = Instance.new("Frame"); TabContainer.Position = UDim2.new(0, 10, 0, 80); TabContainer.Size = UDim2.new(1, -20, 1, -90); TabContainer.BackgroundTransparency = 1; TabContainer.Parent = Sidebar
local TabList = Instance.new("UIListLayout"); TabList.Padding = UDim.new(0, 8); TabList.Parent = TabContainer

local function CreateTab(name, pageToShow)
	local Btn = Instance.new("TextButton")
	Btn.Size = UDim2.new(1, 0, 0, 35)
	Btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
	Btn.BackgroundTransparency = 0.9
	Btn.Text = name; Btn.Font = Enum.Font.GothamBold; Btn.TextColor3 = Colors.Text; Btn.Parent = TabContainer
	Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 8)
	Btn.MouseButton1Click:Connect(function()
		MainPage.Visible = false; ServerPage.Visible = false
		pageToShow.Visible = true
	end)
end
CreateTab("Main", MainPage)
CreateTab("Server", ServerPage)

-- ==========================================
-- 4. FUNÇÕES E COMPONENTES
-- ==========================================

-- Toggle Padrão
local function AddToggle(text, callback)
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.Parent = MainPage
	Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = text; L.Font = Enum.Font.GothamBold; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.6, 0, 1, 0); L.BackgroundTransparency = 1; L.Parent = F
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.Parent = F
	Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	local s = false
	B.MouseButton1Click:Connect(function() s = not s; B.BackgroundColor3 = s and Colors.ToggleOn or Colors.ToggleOff; callback(s) end)
end

-- Speed Input
local function AddSpeed()
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.Parent = MainPage
	Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = "Speed"; L.Font = Enum.Font.GothamBold; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.3, 0, 1, 0); L.BackgroundTransparency = 1; L.Parent = F
	local I = Instance.new("TextBox"); I.Text = "100"; I.Size = UDim2.new(0, 60, 0, 24); I.Position = UDim2.new(0.6, 0, 0.5, -12); I.BackgroundColor3 = Colors.Sidebar; I.TextColor3 = Colors.TextSelected; I.Parent = F
	Instance.new("UICorner", I).CornerRadius = UDim.new(0, 6)
	I.FocusLost:Connect(function() Settings.SpeedVal = tonumber(I.Text) or 100 end)
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.Parent = F
	Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	B.MouseButton1Click:Connect(function() Settings.Speed = not Settings.Speed; B.BackgroundColor3 = Settings.Speed and Colors.ToggleOn or Colors.ToggleOff end)
end

-- TP Section (CORRIGIDO E MELHORADO)
local function AddTP()
	local C = Instance.new("Frame"); C.Size = UDim2.new(1, -10, 0, 150); C.BackgroundTransparency = 1; C.Parent = MainPage
	
	-- Lista de Jogadores
	local List = Instance.new("ScrollingFrame"); List.Size = UDim2.new(0.5, 0, 1, 0); List.BackgroundColor3 = Colors.ItemBG; List.Parent = C
	Instance.new("UICorner", List).CornerRadius = UDim.new(0, 10); local LL = Instance.new("UIListLayout"); LL.Parent = List
	
	-- Painel de Ação (Ready)
	local Action = Instance.new("Frame"); Action.Size = UDim2.new(0.45, 0, 0, 50); Action.Position = UDim2.new(0.55, 0, 0, 0); Action.BackgroundColor3 = Colors.ItemBG; Action.Parent = C
	Instance.new("UICorner", Action).CornerRadius = UDim.new(0, 10)
	
	-- Botão Ready
	local Ready = Instance.new("TextButton"); Ready.Text = "Ready"; Ready.Font = Enum.Font.GothamBold; Ready.Size = UDim2.new(0, 80, 0, 30); Ready.Position = UDim2.new(0.5, -40, 0.5, -15); Ready.BackgroundColor3 = Colors.Sidebar; Ready.TextColor3 = Colors.Text; Ready.Parent = Action
	Instance.new("UICorner", Ready).CornerRadius = UDim.new(0, 8)

	-- Função de Atualizar Lista
	local function Refresh()
		for _,v in pairs(List:GetChildren()) do if v:IsA("TextButton") then v:Destroy() end end
		for _,p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer then
				local b = Instance.new("TextButton")
				b.Size = UDim2.new(1,0,0,30)
				b.Text = p.Name
				b.TextColor3 = Colors.Text -- Começa Cinza
				b.BackgroundColor3 = Colors.ItemBG
				b.BorderSizePixel = 0
				b.Parent = List
				
				-- Ao Clicar no Jogador
				b.MouseButton1Click:Connect(function()
					-- Reseta a cor de todos os outros
					for _, other in pairs(List:GetChildren()) do
						if other:IsA("TextButton") then other.TextColor3 = Colors.Text end
					end
					-- Deixa o selecionado BRANCO
					b.TextColor3 = Colors.TextSelected 
					Settings.TargetPlayer = p
					Ready.Text = "Ready" -- Reseta texto do botão
				end)
			end
		end
	end
	
	List.MouseEnter:Connect(Refresh) -- Atualiza ao passar o mouse
	Refresh()
	
	-- Ação do Botão Ready
	Ready.MouseButton1Click:Connect(function()
		if Settings.TargetPlayer and Settings.TargetPlayer.Character and Settings.TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
			if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
				-- TELEPORTA
				LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.TargetPlayer.Character.HumanoidRootPart.CFrame
			end
		else
			Ready.Text = "Select!" -- Avisa se não selecionou ninguém
		end
	end)
end

-- Server Hop Button
local Hop = Instance.new("TextButton"); Hop.Text = "Server Hop"; Hop.Size = UDim2.new(0, 140, 0, 40); Hop.Position = UDim2.new(0, 10, 0, 10); Hop.BackgroundColor3 = Colors.ItemBG; Hop.TextColor3 = Colors.Text; Hop.Font = Enum.Font.GothamBold; Hop.Parent = ServerPage
Instance.new("UICorner", Hop).CornerRadius = UDim.new(0, 10)
Hop.MouseButton1Click:Connect(function()
	local Servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
	for _, s in pairs(Servers.data) do if s.playing ~= s.maxPlayers then TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id, LocalPlayer) end end
end)

-- Adicionando Componentes
AddToggle("Esp", function(v) Settings.ESP = v end)
AddToggle("No Clip", function(v) Settings.Noclip = v end)
AddSpeed()
AddToggle("Fly", function(v) Settings.Fly = v end)
AddToggle("Invisible", function(v) 
	Settings.Invisible = v 
	if LocalPlayer.Character then for _,p in pairs(LocalPlayer.Character:GetDescendants()) do if p:IsA("BasePart") or p:IsA("Decal") then p.Transparency = v and 1 or 0 end end end
end)
AddTP()

-- ==========================================
-- 5. LOOPS (ESP, SPEED, ETC)
-- ==========================================

-- Loading Animation
local tw = TweenService:Create(BarFill, TweenInfo.new(3), {Size = UDim2.new(1, 0, 1, 0)})
tw:Play()
tw.Completed:Connect(function()
	task.wait(0.5)
	LoadingFrame:Destroy()
	OpenBtn.Visible = true -- Mostra o botão redondo
end)

-- ESP LOOP (Highlight AlwaysOnTop)
RunService.RenderStepped:Connect(function()
	if Settings.ESP then
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer and p.Character then
				if not p.Character:FindFirstChild("LuxHighlight") then
					local h = Instance.new("Highlight")
					h.Name = "LuxHighlight"
					h.FillColor = Color3.fromRGB(160, 80, 255)
					h.OutlineColor = Color3.new(1,1,1)
					h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop -- Vê através da parede
					h.Parent = p.Character
				end
			end
		end
	else
		for _, p in pairs(Players:GetPlayers()) do if p.Character and p.Character:FindFirstChild("LuxHighlight") then p.Character.LuxHighlight:Destroy() end end
	end
end)

-- Noclip & Speed & Fly
RunService.Stepped:Connect(function()
	if Settings.Noclip and LocalPlayer.Character then for _,v in pairs(LocalPlayer.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end end
end)
RunService.Heartbeat:Connect(function()
	if Settings.Speed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedVal end
end)
local bv, bg
RunService.RenderStepped:Connect(function()
	if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		local r = LocalPlayer.Character.HumanoidRootPart
		if not bv then bv = Instance.new("BodyVelocity", r); bv.MaxForce = Vector3.new(1e9,1e9,1e9); bg = Instance.new("BodyGyro", r); bg.MaxTorque = Vector3.new(1e9,1e9,1e9) end
		bg.CFrame = Camera.CFrame; bv.Velocity = Camera.CFrame.LookVector * 50
	else
		if bv then bv:Destroy(); bv = nil end; if bg then bg:Destroy(); bg = nil end
	end
end)
