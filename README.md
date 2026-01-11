local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- CONFIGURAÇÕES
local Settings = {
	ESP = false,
	Noclip = false,
	Speed = false,
	SpeedVal = 100,
	Fly = false,
	Invisible = false
}

-- CORES
local Colors = {
	MainBG = Color3.fromRGB(60, 30, 100),
	Sidebar = Color3.fromRGB(45, 20, 80),
	ItemBG = Color3.fromRGB(50, 25, 90),
	ToggleOff = Color3.fromRGB(30, 30, 30),
	ToggleOn = Color3.fromRGB(255, 255, 255),
	Text = Color3.fromRGB(200, 200, 200),
	TextSelected = Color3.fromRGB(255, 255, 255),
	Accent = Color3.fromRGB(160, 100, 255)
}

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_LX_Edition"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- FUNÇÃO ARRASTAR
local function MakeDraggable(gui)
	local dragging, dragInput, dragStart, startPos
	gui.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			dragging = true; dragStart = input.Position; startPos = gui.Position
		end
	end)
	gui.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end
	end)
	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
			gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
		end
	end)
	UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)
end

-- ==========================================
-- EFEITO BLUR
-- ==========================================
local BlurEffect = Instance.new("BlurEffect")
BlurEffect.Size = 0
BlurEffect.Parent = Lighting
TweenService:Create(BlurEffect, TweenInfo.new(0.5), {Size = 24}):Play()

-- ==========================================
-- TELA DE LOADING
-- ==========================================
local LoadingContainer = Instance.new("Frame")
LoadingContainer.Size = UDim2.new(1, 0, 1, 0); LoadingContainer.BackgroundTransparency = 1; LoadingContainer.Parent = ScreenGui

local LoadTitle = Instance.new("TextLabel")
LoadTitle.Text = "Loading..."; LoadTitle.Font = Enum.Font.GothamBold; LoadTitle.TextSize = 32; LoadTitle.TextColor3 = Colors.TextSelected
LoadTitle.Size = UDim2.new(0, 200, 0, 50); LoadTitle.Position = UDim2.new(0.5, -100, 0.45, -25); LoadTitle.BackgroundTransparency = 1; LoadTitle.Parent = LoadingContainer

local BarBG = Instance.new("Frame")
BarBG.Size = UDim2.new(0, 300, 0, 10); BarBG.Position = UDim2.new(0.5, -150, 0.55, 0); BarBG.BackgroundColor3 = Color3.fromRGB(40, 20, 70); BarBG.BorderSizePixel = 0; BarBG.Parent = LoadingContainer
Instance.new("UICorner", BarBG).CornerRadius = UDim.new(1, 0)

local BarFill = Instance.new("Frame")
BarFill.Size = UDim2.new(0, 0, 1, 0); BarFill.BackgroundColor3 = Color3.fromRGB(200, 200, 200); BarFill.BorderSizePixel = 0; BarFill.Parent = BarBG
Instance.new("UICorner", BarFill).CornerRadius = UDim.new(1, 0)

-- ==========================================
-- BOTÃO REDONDO "LX" (ALTERADO AQUI)
-- ==========================================
local OpenBtn = Instance.new("TextButton") -- Mudei para TextButton para facilitar
OpenBtn.Name = "OpenButton"
OpenBtn.Size = UDim2.new(0, 55, 0, 55)
OpenBtn.Position = UDim2.new(0, 20, 0.15, 0)
OpenBtn.BackgroundColor3 = Colors.MainBG

-- Configuração do Texto LX
OpenBtn.Text = "LX"
OpenBtn.Font = Enum.Font.GothamBlack -- Fonte bem grossa e visível
OpenBtn.TextSize = 28
OpenBtn.TextColor3 = Color3.fromRGB(255, 255, 255) -- Branco Brilhante

OpenBtn.Visible = false 
OpenBtn.Parent = ScreenGui
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1, 0)
MakeDraggable(OpenBtn)

-- Mantive o Glow para dar o efeito "brilhante"
local Glow = Instance.new("ImageLabel")
Glow.BackgroundTransparency = 1; Glow.Image = "rbxassetid://50288246"
Glow.Size = UDim2.new(1.6, 0, 1.6, 0); Glow.Position = UDim2.new(-0.3, 0, -0.3, 0); Glow.Parent = OpenBtn

-- ==========================================
-- JANELA PRINCIPAL
-- ==========================================
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 480, 0, 280); MainFrame.Position = UDim2.new(0.5, -240, 1.5, 0); MainFrame.BackgroundColor3 = Colors.MainBG; MainFrame.Visible = false; MainFrame.ClipsDescendants = true; MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 20); MakeDraggable(MainFrame)

local isOpen = false; local isAnimating = false
OpenBtn.InputBegan:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then isDraggingBtn = false end end)
OpenBtn.InputChanged:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseMovement then isDraggingBtn = true end end)

OpenBtn.MouseButton1Click:Connect(function()
	if isAnimating then return end; isAnimating = true
	if isOpen then
		local closePos = UDim2.new(MainFrame.Position.X.Scale, MainFrame.Position.X.Offset, 1.5, 0)
		local closeTween = TweenService:Create(MainFrame, TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.In), {Position = closePos})
		closeTween:Play(); closeTween.Completed:Connect(function() MainFrame.Visible = false; isAnimating = false end)
	else
		MainFrame.Visible = true; MainFrame.Position = UDim2.new(0.5, -240, 1.5, 0) 
		local openTween = TweenService:Create(MainFrame, TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Position = UDim2.new(0.5, -240, 0.4, -150)})
		openTween:Play(); openTween.Completed:Connect(function() isAnimating = false end)
	end
	isOpen = not isOpen
end)

-- Sidebar & Title
local Sidebar = Instance.new("Frame"); Sidebar.Size = UDim2.new(0, 140, 1, 0); Sidebar.BackgroundColor3 = Colors.Sidebar; Sidebar.Parent = MainFrame; Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 20)
local Fix = Instance.new("Frame"); Fix.Size = UDim2.new(0,15,1,0); Fix.Position = UDim2.new(1,-15,0,0); Fix.BackgroundColor3 = Colors.Sidebar; Fix.BorderSizePixel=0; Fix.Parent=Sidebar
local Title = Instance.new("TextLabel"); Title.Text = "Lux Hub"; Title.Font = Enum.Font.GothamBold; Title.TextSize = 22; Title.TextColor3 = Colors.TextSelected; Title.Size = UDim2.new(1, 0, 0, 30); Title.Position = UDim2.new(0, 0, 0, 20); Title.BackgroundTransparency = 1; Title.TextXAlignment = Enum.TextXAlignment.Center; Title.Parent = Sidebar
local SubVer = Instance.new("TextLabel"); SubVer.Text = "V2.1"; SubVer.Font = Enum.Font.Gotham; SubVer.TextSize = 12; SubVer.TextColor3 = Color3.fromRGB(180,180,180); SubVer.Size = UDim2.new(1, 0, 0, 20); SubVer.Position = UDim2.new(0, 0, 0, 45); SubVer.BackgroundTransparency = 1; SubVer.TextXAlignment = Enum.TextXAlignment.Center; SubVer.Parent = Sidebar

-- Containers & Tabs
local PageContainer = Instance.new("Frame"); PageContainer.Size = UDim2.new(1, -150, 1, -20); PageContainer.Position = UDim2.new(0, 150, 0, 10); PageContainer.BackgroundTransparency = 1; PageContainer.Parent = MainFrame
local MainPage = Instance.new("ScrollingFrame"); MainPage.Size = UDim2.new(1, 0, 1, 0); MainPage.BackgroundTransparency = 1; MainPage.ScrollBarThickness = 2; MainPage.Parent = PageContainer; local UIList = Instance.new("UIListLayout"); UIList.Padding = UDim.new(0, 8); UIList.Parent = MainPage
local ServerPage = Instance.new("Frame"); ServerPage.Size = UDim2.new(1, 0, 1, 0); ServerPage.BackgroundTransparency = 1; ServerPage.Visible = false; ServerPage.Parent = PageContainer
local TabContainer = Instance.new("Frame"); TabContainer.Position = UDim2.new(0, 10, 0, 80); TabContainer.Size = UDim2.new(1, -20, 1, -90); TabContainer.BackgroundTransparency = 1; TabContainer.Parent = Sidebar; local TabList = Instance.new("UIListLayout"); TabList.Padding = UDim.new(0, 8); TabList.Parent = TabContainer

local function CreateTab(name, pageToShow)
	local Btn = Instance.new("TextButton"); Btn.Size = UDim2.new(1, 0, 0, 35); Btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255); Btn.BackgroundTransparency = 0.9
	Btn.Text = name; Btn.Font = Enum.Font.GothamBold; Btn.TextColor3 = Colors.Text; Btn.Parent = TabContainer; Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 8)
	Btn.MouseButton1Click:Connect(function() MainPage.Visible = false; ServerPage.Visible = false; pageToShow.Visible = true end)
end
CreateTab("Main", MainPage); CreateTab("Server", ServerPage)

-- Funções
local function AddToggle(text, callback)
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.Parent = MainPage; Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = text; L.Font = Enum.Font.GothamBold; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.6, 0, 1, 0); L.BackgroundTransparency = 1; L.Parent = F
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.Parent = F; Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	local s = false; B.MouseButton1Click:Connect(function() s = not s; local colorInfo = TweenInfo.new(0.3); TweenService:Create(B, colorInfo, {BackgroundColor3 = s and Colors.ToggleOn or Colors.ToggleOff}):Play(); callback(s) end)
end

local function AddSpeed()
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.Parent = MainPage; Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = "Speed"; L.Font = Enum.Font.GothamBold; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.3, 0, 1, 0); L.BackgroundTransparency = 1; L.Parent = F
	local I = Instance.new("TextBox"); I.Text = "100"; I.Size = UDim2.new(0, 60, 0, 24); I.Position = UDim2.new(0.6, 0, 0.5, -12); I.BackgroundColor3 = Colors.Sidebar; I.TextColor3 = Colors.TextSelected; I.Parent = F; Instance.new("UICorner", I).CornerRadius = UDim.new(0, 6)
	I.FocusLost:Connect(function() Settings.SpeedVal = tonumber(I.Text) or 100 end)
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.Parent = F; Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	local s = false; B.MouseButton1Click:Connect(function() s = not s; local colorInfo = TweenInfo.new(0.3); TweenService:Create(B, colorInfo, {BackgroundColor3 = s and Colors.ToggleOn or Colors.ToggleOff}):Play(); Settings.Speed = s; if not s and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then LocalPlayer.Character.Humanoid.WalkSpeed = 16 end end)
end

local Hop = Instance.new("TextButton"); Hop.Text = "Server Hop"; Hop.Size = UDim2.new(0, 140, 0, 40); Hop.Position = UDim2.new(0, 10, 0, 10); Hop.BackgroundColor3 = Colors.ItemBG; Hop.TextColor3 = Colors.Text; Hop.Font = Enum.Font.GothamBold; Hop.Parent = ServerPage; Instance.new("UICorner", Hop).CornerRadius = UDim.new(0, 10)
Hop.MouseButton1Click:Connect(function() local Servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100")); for _, s in pairs(Servers.data) do if s.playing ~= s.maxPlayers then TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id, LocalPlayer) end end end)

AddToggle("Esp", function(v) Settings.ESP = v end); AddToggle("No Clip", function(v) Settings.Noclip = v end); AddSpeed(); AddToggle("Fly", function(v) Settings.Fly = v end)
AddToggle("Invisible", function(v) Settings.Invisible = v; if LocalPlayer.Character then for _,p in pairs(LocalPlayer.Character:GetDescendants()) do if p:IsA("BasePart") or p:IsA("Decal") then p.Transparency = v and 1 or 0 end end end end)

-- ==========================================
-- ANIMATION LOOP (BLUR & LOADING)
-- ==========================================
local tw = TweenService:Create(BarFill, TweenInfo.new(3), {Size = UDim2.new(1, 0, 1, 0)}); tw:Play()
tw.Completed:Connect(function() task.wait(0.5); local blurOff = TweenService:Create(BlurEffect, TweenInfo.new(1), {Size = 0}); blurOff:Play()
	blurOff.Completed:Connect(function() BlurEffect:Destroy(); LoadingContainer:Destroy(); OpenBtn.Visible = true end)
end)

-- Funções Loop
RunService.RenderStepped:Connect(function() if Settings.ESP then for _, p in pairs(Players:GetPlayers()) do if p ~= LocalPlayer and p.Character then if not p.Character:FindFirstChild("LuxHighlight") then local h = Instance.new("Highlight"); h.Name = "LuxHighlight"; h.FillColor = Color3.fromRGB(160, 80, 255); h.OutlineColor = Color3.new(1,1,1); h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop; h.Parent = p.Character end end end else for _, p in pairs(Players:GetPlayers()) do if p.Character and p.Character:FindFirstChild("LuxHighlight") then p.Character.LuxHighlight:Destroy() end end end end)
RunService.Stepped:Connect(function() if Settings.Noclip and LocalPlayer.Character then for _,v in pairs(LocalPlayer.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end end end)
RunService.Heartbeat:Connect(function() if Settings.Speed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedVal end end)
local bv, bg
RunService.RenderStepped:Connect(function() if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then local r = LocalPlayer.Character.HumanoidRootPart; if not bv then bv = Instance.new("BodyVelocity", r); bv.MaxForce = Vector3.new(1e9,1e9,1e9); bg = Instance.new("BodyGyro", r); bg.MaxTorque = Vector3.new(1e9,1e9,1e9) end; bg.CFrame = Camera.CFrame; bv.Velocity = Camera.CFrame.LookVector * 50 else if bv then bv:Destroy(); bv = nil end; if bg then bg:Destroy(); bg = nil end end end)
