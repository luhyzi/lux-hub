-- ==========================================
-- LUX HUB v2.0.1 (EMERGENCY FIX / SAFE MODE)
-- ==========================================

-- 1. NOTIFICAÇÃO DE INÍCIO (Para saber se o executor leu o script)
pcall(function()
	game:GetService("StarterGui"):SetCore("SendNotification", {
		Title = "Lux Hub Loading...";
		Text = "Aguarde, carregando...";
		Duration = 2;
	})
end)

-- 2. LIMPEZA DE VERSÕES ANTIGAS
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui", 10) -- Espera segura de 10s

if not PlayerGui then 
	warn("Lux Hub: PlayerGui não encontrado!") 
	return 
end

for _, v in pairs(PlayerGui:GetChildren()) do
	if v.Name:find("LuxHub") then v:Destroy() end
end

-- 3. SERVIÇOS
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local Stats = game:GetService("Stats")
local Camera = workspace.CurrentCamera

-- 4. GUI PRINCIPAL
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_v2.0.1_Safe"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = PlayerGui

-- 5. CORES
local Colors = {
	MainBG = Color3.fromRGB(60, 30, 100),
	Sidebar = Color3.fromRGB(45, 20, 80),
	ItemBG = Color3.fromRGB(50, 25, 90),
	ToggleOff = Color3.fromRGB(30, 30, 30),
	ToggleOn = Color3.fromRGB(255, 255, 255),
	Text = Color3.fromRGB(200, 200, 200),
	Green = Color3.fromRGB(0, 255, 100),
	Yellow = Color3.fromRGB(255, 170, 0),
	Red = Color3.fromRGB(255, 50, 50)
}

-- 6. CONFIGURAÇÕES
local Settings = {ESP = false, Noclip = false, Speed = false, SpeedVal = 100, Fly = false, Invisible = false}

-- FUNÇÃO ARRASTAR (Simplificada)
local function MakeDraggable(trigger, objectToMove)
	objectToMove = objectToMove or trigger
	local dragging, dragInput, dragStart, startPos
	trigger.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true; dragStart = input.Position; startPos = objectToMove.Position
		end
	end)
	trigger.InputChanged:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end end)
	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			objectToMove.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)
	UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)
end

-- TELA DE LOADING (Simples, sem Blur para não travar)
local LoadingContainer = Instance.new("Frame"); LoadingContainer.Size = UDim2.new(1, 0, 1, 0); LoadingContainer.BackgroundTransparency = 1; LoadingContainer.Parent = ScreenGui
local BarBG = Instance.new("Frame"); BarBG.Size = UDim2.new(0, 300, 0, 10); BarBG.Position = UDim2.new(0.5, -150, 0.55, 0); BarBG.BackgroundColor3 = Color3.fromRGB(40, 20, 70); BarBG.BorderSizePixel = 0; BarBG.Parent = LoadingContainer
local BarFill = Instance.new("Frame"); BarFill.Size = UDim2.new(0, 0, 1, 0); BarFill.BackgroundColor3 = Color3.fromRGB(200, 200, 200); BarFill.BorderSizePixel = 0; BarFill.Parent = BarBG

-- BOTÃO LX (Seguro)
local OpenBtn = Instance.new("TextButton")
OpenBtn.Name = "OpenButton"
OpenBtn.Size = UDim2.new(0, 55, 0, 55)
OpenBtn.Position = UDim2.new(0, 5, 0.25, 0)
OpenBtn.BackgroundColor3 = Colors.MainBG
OpenBtn.Text = "LX"; OpenBtn.Font = Enum.Font.GothamBlack; OpenBtn.TextSize = 28; OpenBtn.TextColor3 = Color3.fromRGB(255, 255, 255); OpenBtn.Visible = false; OpenBtn.Parent = ScreenGui
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1, 0); MakeDraggable(OpenBtn)

-- JANELA PRINCIPAL
local MainFrame = Instance.new("Frame"); MainFrame.Size = UDim2.new(0, 480, 0, 280); MainFrame.Position = UDim2.new(0.5, -240, 1.5, 0); MainFrame.BackgroundColor3 = Colors.MainBG; MainFrame.Visible = false; MainFrame.ClipsDescendants = true; MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 20)
local isOpen = false

OpenBtn.MouseButton1Click:Connect(function()
	if isOpen then MainFrame.Visible = false else MainFrame.Visible = true end
	isOpen = not isOpen
end)

-- SIDEBAR
local Sidebar = Instance.new("Frame"); Sidebar.Size = UDim2.new(0, 140, 1, 0); Sidebar.BackgroundColor3 = Colors.Sidebar; Sidebar.Parent = MainFrame; Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 20); MakeDraggable(Sidebar, MainFrame)
local Title = Instance.new("TextLabel"); Title.Text = "Lux Hub"; Title.Font = Enum.Font.GothamBold; Title.TextSize = 24; Title.TextColor3 = Colors.ToggleOn; Title.Size = UDim2.new(1, 0, 0, 30); Title.Position = UDim2.new(0, 0, 0, 20); Title.BackgroundTransparency = 1; Title.Parent = Sidebar
local SubVer = Instance.new("TextLabel"); SubVer.Text = "v2.0.1"; SubVer.Font = Enum.Font.Gotham; SubVer.TextSize = 14; SubVer.TextColor3 = Color3.fromRGB(150,150,150); SubVer.Size = UDim2.new(1, 0, 0, 20); SubVer.Position = UDim2.new(0, 0, 0, 45); SubVer.BackgroundTransparency = 1; SubVer.Parent = Sidebar

-- PAGINAS
local PageContainer = Instance.new("Frame"); PageContainer.Size = UDim2.new(1, -150, 1, -20); PageContainer.Position = UDim2.new(0, 150, 0, 10); PageContainer.BackgroundTransparency = 1; PageContainer.Parent = MainFrame
local MainPage = Instance.new("ScrollingFrame"); MainPage.Size = UDim2.new(1, 0, 1, 0); MainPage.BackgroundTransparency = 1; MainPage.ScrollBarThickness = 2; MainPage.Parent = PageContainer; local UIList = Instance.new("UIListLayout"); UIList.Padding = UDim.new(0, 8); UIList.Parent = MainPage
local StatusPage = Instance.new("Frame"); StatusPage.Size = UDim2.new(1, 0, 1, 0); StatusPage.BackgroundTransparency = 1; StatusPage.Visible = false; StatusPage.Parent = PageContainer
local ServerPage = Instance.new("Frame"); ServerPage.Size = UDim2.new(1, 0, 1, 0); ServerPage.BackgroundTransparency = 1; ServerPage.Visible = false; ServerPage.Parent = PageContainer

-- ABAS
local TabContainer = Instance.new("Frame"); TabContainer.Position = UDim2.new(0, 10, 0, 80); TabContainer.Size = UDim2.new(1, -20, 1, -90); TabContainer.BackgroundTransparency = 1; TabContainer.Parent = Sidebar; local TabList = Instance.new("UIListLayout"); TabList.Padding = UDim.new(0, 8); TabList.Parent = TabContainer
local function CreateTab(name, pageToShow)
	local Btn = Instance.new("TextButton"); Btn.Size = UDim2.new(1, 0, 0, 35); Btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255); Btn.BackgroundTransparency = 0.9; Btn.Text = name; Btn.Font = Enum.Font.GothamBold; Btn.TextSize = 17; Btn.TextColor3 = Colors.Text; Btn.Parent = TabContainer; Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 8)
	Btn.MouseButton1Click:Connect(function() MainPage.Visible = false; StatusPage.Visible = false; ServerPage.Visible = false; pageToShow.Visible = true end)
end
CreateTab("Main", MainPage); CreateTab("Status", StatusPage); CreateTab("Server", ServerPage)

-- ================= STATUS INFO =================
local InfoList = Instance.new("UIListLayout"); InfoList.Parent = StatusPage; InfoList.Padding = UDim.new(0, 5)
local function CreateStat(text) local L = Instance.new("TextLabel"); L.Text = text; L.Size = UDim2.new(1,0,0,25); L.TextColor3 = Colors.Text; L.BackgroundTransparency = 1; L.Font = Enum.Font.GothamBold; L.TextSize = 16; L.TextXAlignment = Enum.TextXAlignment.Left; L.Parent = StatusPage; return L end

local L_Player = CreateStat("Player: " .. LocalPlayer.Name)
local L_Health = CreateStat("Health: --")
local L_Pos = CreateStat("Pos: 0,0,0")
local L_Players = CreateStat("Online: --")
local L_Time = CreateStat("Time: --")
local L_FPS = CreateStat("FPS: --")
local L_Ping = CreateStat("Ping: --")

task.spawn(function()
	while true do
		task.wait(0.5)
		if StatusPage.Visible then
			pcall(function()
				L_Time.Text = "Time: " .. os.date("%H:%M:%S")
				if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
					L_Health.Text = "Health: " .. math.floor(LocalPlayer.Character.Humanoid.Health)
				end
				if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
					local p = LocalPlayer.Character.HumanoidRootPart.Position
					L_Pos.Text = string.format("Pos: %d, %d, %d", p.X, p.Y, p.Z)
				end
				L_Players.Text = "Online: " .. #Players:GetPlayers()
				local fps = math.floor(workspace:GetRealPhysicsFPS())
				L_FPS.Text = "FPS: " .. fps
				local ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValueString():split(" ")[1])
				L_Ping.Text = "Ping: " .. ping
			end)
		end
	end
end)

-- ================= SERVER HOP =================
local Hop = Instance.new("TextButton"); Hop.Text = "Server Hop"; Hop.Size = UDim2.new(0.9, 0, 0, 40); Hop.Position = UDim2.new(0.05, 0, 0, 10); Hop.BackgroundColor3 = Colors.ItemBG; Hop.TextColor3 = Colors.Text; Hop.Parent = ServerPage; Instance.new("UICorner", Hop).CornerRadius = UDim.new(0, 10)
Hop.MouseButton1Click:Connect(function()
	pcall(function()
		local Servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
		for _, s in pairs(Servers.data) do
			if s.playing ~= s.maxPlayers then TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id, LocalPlayer) end
		end
	end)
end)

-- ================= FUNÇÕES MAIN =================
local function AddToggle(text, callback)
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.Parent = MainPage; Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = text; L.Font = Enum.Font.GothamBold; L.TextSize = 17; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.6, 0, 1, 0); L.BackgroundTransparency = 1; L.Parent = F
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.Parent = F; Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	local s = false; B.MouseButton1Click:Connect(function() s = not s; TweenService:Create(B, TweenInfo.new(0.2), {BackgroundColor3 = s and Colors.Green or Colors.ToggleOff}):Play(); callback(s) end)
end

local function AddSpeed()
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.Parent = MainPage; Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = "Speed"; L.Font = Enum.Font.GothamBold; L.TextSize = 17; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.3, 0, 1, 0); L.BackgroundTransparency = 1; L.Parent = F
	local I = Instance.new("TextBox"); I.Text = "100"; I.Size = UDim2.new(0, 60, 0, 24); I.Position = UDim2.new(0.6, 0, 0.5, -12); I.BackgroundColor3 = Colors.Sidebar; I.TextColor3 = Colors.ToggleOn; I.Parent = F; Instance.new("UICorner", I).CornerRadius = UDim.new(0, 6)
	I.FocusLost:Connect(function() Settings.SpeedVal = tonumber(I.Text) or 100 end)
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.Parent = F; Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	local s = false; B.MouseButton1Click:Connect(function() s = not s; TweenService:Create(B, TweenInfo.new(0.2), {BackgroundColor3 = s and Colors.Green or Colors.ToggleOff}):Play(); Settings.Speed = s end)
end

AddToggle("Esp", function(v) Settings.ESP = v end)
AddToggle("No Clip", function(v) Settings.Noclip = v end)
AddSpeed()
AddToggle("Fly", function(v) Settings.Fly = v end)
AddToggle("Invisible", function(v) Settings.Invisible = v; if LocalPlayer.Character then for _,p in pairs(LocalPlayer.Character:GetDescendants()) do if p:IsA("BasePart") or p:IsA("Decal") then p.Transparency = v and 1 or 0 end end end end)

-- LOADING FIM
local tw = TweenService:Create(BarFill, TweenInfo.new(2), {Size = UDim2.new(1, 0, 1, 0)}); tw:Play()
tw.Completed:Connect(function() LoadingContainer:Destroy(); OpenBtn.Visible = true; 
	game:GetService("StarterGui"):SetCore("SendNotification", {Title = "Lux Hub"; Text = "Carregado com Sucesso!"; Duration = 3;})
end)

-- LOOP GERAL (OTIMIZADO)
RunService.RenderStepped:Connect(function()
	-- ESP
	if Settings.ESP then for _, p in pairs(Players:GetPlayers()) do if p ~= LocalPlayer and p.Character and not p.Character:FindFirstChild("LuxHighlight") then local h = Instance.new("Highlight"); h.Name = "LuxHighlight"; h.FillColor = Colors.Accent; h.OutlineColor = Color3.new(1,1,1); h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop; h.Parent = p.Character end end end
	
	-- NOCLIP / FLY NOCLIP
	if (Settings.Noclip or Settings.Fly) and LocalPlayer.Character then for _,v in pairs(LocalPlayer.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end end
	
	-- SPEED
	if Settings.Speed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedVal end

	-- FLY
	if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		local root = LocalPlayer.Character.HumanoidRootPart
		local hum = LocalPlayer.Character.Humanoid
		if not root:FindFirstChild("LuxFlyVelocity") then
			local bv = Instance.new("BodyVelocity"); bv.Name = "LuxFlyVelocity"; bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge); bv.Velocity = Vector3.new(0, 0, 0); bv.Parent = root
			local bg = Instance.new("BodyGyro"); bg.Name = "LuxFlyGyro"; bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9); bg.P = 10000; bg.D = 100; bg.CFrame = root.CFrame; bg.Parent = root
		end
		hum.PlatformStand = true
		local bv = root:FindFirstChild("LuxFlyVelocity"); local bg = root:FindFirstChild("LuxFlyGyro")
		local moveDir = Vector3.new(0,0,0)
		if UserInputService:IsKeyDown(Enum.KeyCode.W) or (hum.MoveDirection.Magnitude > 0) then moveDir = moveDir + Camera.CFrame.LookVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - Camera.CFrame.LookVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - Camera.CFrame.RightVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + Camera.CFrame.RightVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0,1,0) end
		if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDir = moveDir - Vector3.new(0,1,0) end
		bv.Velocity = moveDir * (Settings.SpeedVal or 50)
		bg.CFrame = Camera.CFrame
	else
		if LocalPlayer.Character then
			local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
			if root and root:FindFirstChild("LuxFlyVelocity") then root.LuxFlyVelocity:Destroy(); root.LuxFlyGyro:Destroy() end
			local hum = LocalPlayer.Character:FindFirstChild("Humanoid"); if hum then hum.PlatformStand = false end
		end
	end
end)
