local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local Lighting = game:GetService("Lighting")
local Stats = game:GetService("Stats")
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
	CamLock = false,
	Target = nil
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
	Accent = Color3.fromRGB(160, 100, 255),
    TargetPurple = Color3.fromRGB(130, 0, 255), -- Cor do alvo monitorado
	Green = Color3.fromRGB(0, 255, 100),
	Yellow = Color3.fromRGB(255, 170, 0),
	Red = Color3.fromRGB(255, 50, 50)
}

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_v1.9.6"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- FUNÇÃO ARRASTAR
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

-- ==========================================
-- EFEITO BLUR (LOADING)
-- ==========================================
local LoadingBlur = Instance.new("BlurEffect"); LoadingBlur.Name = "LuxLoadingBlur"; LoadingBlur.Size = 0; LoadingBlur.Parent = Lighting
TweenService:Create(LoadingBlur, TweenInfo.new(0.5), {Size = 24}):Play()

local LoadingContainer = Instance.new("Frame"); LoadingContainer.Size = UDim2.new(1, 0, 1, 0); LoadingContainer.BackgroundTransparency = 1; LoadingContainer.Parent = ScreenGui
local LoadTitle = Instance.new("TextLabel"); LoadTitle.Text = "Loading..."; LoadTitle.Font = Enum.Font.GothamBold; LoadTitle.TextSize = 32; LoadTitle.TextColor3 = Colors.TextSelected
LoadTitle.Size = UDim2.new(0, 200, 0, 50); LoadTitle.Position = UDim2.new(0.5, -100, 0.45, -25); LoadTitle.BackgroundTransparency = 1; LoadTitle.Parent = LoadingContainer
local BarBG = Instance.new("Frame"); BarBG.Size = UDim2.new(0, 300, 0, 10); BarBG.Position = UDim2.new(0.5, -150, 0.55, 0); BarBG.BackgroundColor3 = Color3.fromRGB(40, 20, 70); BarBG.BorderSizePixel = 0; BarBG.Parent = LoadingContainer; Instance.new("UICorner", BarBG).CornerRadius = UDim.new(1, 0)
local BarFill = Instance.new("Frame"); BarFill.Size = UDim2.new(0, 0, 1, 0); BarFill.BackgroundColor3 = Color3.fromRGB(200, 200, 200); BarFill.BorderSizePixel = 0; BarFill.Parent = BarBG; Instance.new("UICorner", BarFill).CornerRadius = UDim.new(1, 0)

-- ==========================================
-- BOTÃO LX
-- ==========================================
local OpenBtn = Instance.new("TextButton")
OpenBtn.Name = "OpenButton"
OpenBtn.Size = UDim2.new(0, 55, 0, 55)
OpenBtn.Position = UDim2.new(0, 5, 0.25, 0)
OpenBtn.BackgroundColor3 = Colors.MainBG
OpenBtn.Text = "LX"; OpenBtn.Font = Enum.Font.GothamBlack; OpenBtn.TextSize = 28; OpenBtn.TextColor3 = Color3.fromRGB(255, 255, 255); OpenBtn.Visible = false; OpenBtn.Parent = ScreenGui
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1, 0); MakeDraggable(OpenBtn)

-- ==========================================
-- JANELA PRINCIPAL
-- ==========================================
local MainFrame = Instance.new("Frame"); MainFrame.Size = UDim2.new(0, 480, 0, 280); MainFrame.Position = UDim2.new(0.5, -240, 1.5, 0); MainFrame.BackgroundColor3 = Colors.MainBG; MainFrame.Visible = false; MainFrame.ClipsDescendants = true; MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 20)
local MenuBlur = nil; local isOpen = false; local isAnimating = false

OpenBtn.MouseButton1Click:Connect(function()
	if isAnimating then return end; isAnimating = true
	if isOpen then
		if MenuBlur then local blurOut = TweenService:Create(MenuBlur, TweenInfo.new(0.5), {Size = 0}); blurOut:Play(); blurOut.Completed:Connect(function() if MenuBlur then MenuBlur:Destroy(); MenuBlur = nil end end) end
		local closeTween = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {Position = UDim2.new(MainFrame.Position.X.Scale, MainFrame.Position.X.Offset, 1.5, 0)})
		closeTween:Play(); closeTween.Completed:Connect(function() MainFrame.Visible = false; isAnimating = false end)
	else
		if not MenuBlur then MenuBlur = Instance.new("BlurEffect"); MenuBlur.Name = "LuxMenuBlur"; MenuBlur.Size = 0; MenuBlur.Parent = Lighting end; TweenService:Create(MenuBlur, TweenInfo.new(0.5), {Size = 24}):Play()
		MainFrame.Visible = true; MainFrame.Position = UDim2.new(0.5, -240, 1.5, 0)
		local openTween = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = UDim2.new(0.5, -240, 0.4, -150)})
		openTween:Play(); openTween.Completed:Connect(function() isAnimating = false end)
	end; isOpen = not isOpen
end)

-- Sidebar
local Sidebar = Instance.new("Frame"); Sidebar.Size = UDim2.new(0, 140, 1, 0); Sidebar.BackgroundColor3 = Colors.Sidebar; Sidebar.Parent = MainFrame; Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 20)
MakeDraggable(Sidebar, MainFrame)
local Title = Instance.new("TextLabel"); Title.Text = "Lux Hub"; Title.Font = Enum.Font.GothamBold; Title.TextSize = 24; Title.TextColor3 = Colors.TextSelected; Title.Size = UDim2.new(1, 0, 0, 30); Title.Position = UDim2.new(0, 0, 0, 20); Title.BackgroundTransparency = 1; Title.TextXAlignment = Enum.TextXAlignment.Center; Title.Parent = Sidebar
local SubVer = Instance.new("TextLabel"); SubVer.Text = "V1.9.6"; SubVer.Font = Enum.Font.Gotham; SubVer.TextSize = 14; SubVer.TextColor3 = Color3.fromRGB(180,180,180); SubVer.Size = UDim2.new(1, 0, 0, 20); SubVer.Position = UDim2.new(0, 0, 0, 45); SubVer.BackgroundTransparency = 1; SubVer.TextXAlignment = Enum.TextXAlignment.Center; SubVer.Parent = Sidebar

-- Containers
local PageContainer = Instance.new("Frame"); PageContainer.Size = UDim2.new(1, -150, 1, -20); PageContainer.Position = UDim2.new(0, 150, 0, 10); PageContainer.BackgroundTransparency = 1; PageContainer.Parent = MainFrame
local MainPage = Instance.new("ScrollingFrame"); MainPage.Size = UDim2.new(1, 0, 1, 0); MainPage.BackgroundTransparency = 1; MainPage.ScrollBarThickness = 2; MainPage.Parent = PageContainer; local UIList = Instance.new("UIListLayout"); UIList.Padding = UDim.new(0, 8); UIList.Parent = MainPage
local StatusPage = Instance.new("Frame"); StatusPage.Size = UDim2.new(1, 0, 1, 0); StatusPage.BackgroundTransparency = 1; StatusPage.Visible = false; StatusPage.Parent = PageContainer
local ServerPage = Instance.new("Frame"); ServerPage.Size = UDim2.new(1, 0, 1, 0); ServerPage.BackgroundTransparency = 1; ServerPage.Visible = false; ServerPage.Parent = PageContainer

-- Abas Sidebar
local TabContainer = Instance.new("Frame"); TabContainer.Position = UDim2.new(0, 10, 0, 80); TabContainer.Size = UDim2.new(1, -20, 1, -90); TabContainer.BackgroundTransparency = 1; TabContainer.Parent = Sidebar; local TabList = Instance.new("UIListLayout"); TabList.Padding = UDim.new(0, 8); TabList.Parent = TabContainer

local function CreateTab(name, pageToShow)
	local Btn = Instance.new("TextButton"); Btn.Size = UDim2.new(1, 0, 0, 35); Btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255); Btn.BackgroundTransparency = 0.9
	Btn.Text = name; Btn.Font = Enum.Font.GothamBold; Btn.TextSize = 17; Btn.TextColor3 = Colors.Text; Btn.Parent = TabContainer; Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 8)
	Btn.MouseButton1Click:Connect(function() 
		MainPage.Visible = false; StatusPage.Visible = false; ServerPage.Visible = false; pageToShow.Visible = true 
	end)
end
CreateTab("Main", MainPage); CreateTab("Status", StatusPage); CreateTab("Server", ServerPage)

-- ==========================================
-- ITENS ABA MAIN
-- ==========================================
local function AddToggle(text, callback)
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.Parent = MainPage; Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = text; L.Font = Enum.Font.GothamBold; L.TextSize = 17; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.6, 0, 1, 0); L.BackgroundTransparency = 1; L.Parent = F
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.Parent = F; Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	local s = false; B.MouseButton1Click:Connect(function() s = not s; TweenService:Create(B, TweenInfo.new(0.3), {BackgroundColor3 = s and Colors.ToggleOn or Colors.ToggleOff}):Play(); callback(s) end)
end

-- FUNÇÃO CAM LOCK (BUSCA PLAYER MAIS PRÓXIMO)
local function GetClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (LocalPlayer.Character.HumanoidRootPart.Position - p.Character.HumanoidRootPart.Position).Magnitude
            if dist < shortestDistance then
                shortestDistance = dist
                closestPlayer = p
            end
        end
    end
    return closestPlayer
end

-- LÓGICA FPS BOOST
local function ApplySmoothTexture(v)
    if v then
        for _, obj in pairs(game:GetDescendants()) do
            if obj:IsA("BasePart") then obj.Material = Enum.Material.SmoothPlastic elseif obj:IsA("Texture") or obj:IsA("Decal") then obj:Destroy() end
        end
        Lighting.GlobalShadows = false
    else Lighting.GlobalShadows = true end
end

AddToggle("Esp", function(v) Settings.ESP = v end)
AddToggle("No Clip", function(v) Settings.Noclip = v end)
AddToggle("Fly", function(v) Settings.Fly = v end)
AddToggle("FPS Boost", function(v) ApplySmoothTexture(v) end)
AddToggle("Cam Lock", function(v) Settings.CamLock = v end)
AddToggle("Invisible", function(v) Settings.Invisible = v; if LocalPlayer.Character then for _,p in pairs(LocalPlayer.Character:GetDescendants()) do if p:IsA("BasePart") or p:IsA("Decal") then p.Transparency = v and 1 or 0 end end end end)

-- ==========================================
-- LOOPS E LÓGICA FINAL
-- ==========================================
RunService.RenderStepped:Connect(function()
    -- LÓGICA CAM LOCK E TARGET ROXO
    if Settings.CamLock then
        Settings.Target = GetClosestPlayer()
        if Settings.Target and Settings.Target.Character and Settings.Target.Character:FindFirstChild("HumanoidRootPart") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, Settings.Target.Character.HumanoidRootPart.Position)
        end
    else
        Settings.Target = nil
    end

	if Settings.ESP or Settings.CamLock then
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer and p.Character then
				local h = p.Character:FindFirstChild("LuxHighlight") or Instance.new("Highlight", p.Character)
				h.Name = "LuxHighlight"
                h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                
                -- LÓGICA DE COR DINÂMICA
                if Settings.CamLock and Settings.Target == p then
                    h.Enabled = true -- Sempre ligado se for o alvo do Cam Lock
                    h.FillColor = Colors.TargetPurple
                    h.FillTransparency = 0.2
                    h.OutlineColor = Color3.new(1, 1, 1)
                elseif Settings.ESP then
                    h.Enabled = true
                    h.FillColor = Colors.Accent
                    h.FillTransparency = 0.5
                    h.OutlineColor = Color3.new(1, 1, 1)
                else
                    h.Enabled = false
                end
			end
		end
	else
		for _, p in pairs(Players:GetPlayers()) do if p.Character and p.Character:FindFirstChild("LuxHighlight") then p.Character.LuxHighlight:Destroy() end end
	end
end)

RunService.Stepped:Connect(function() if (Settings.Noclip or Settings.Fly) and LocalPlayer.Character then for _,v in pairs(LocalPlayer.Character:GetDescendants()) do if v:A("BasePart") then v.CanCollide = false end end end end)

-- LÓGICA FLY
RunService.RenderStepped:Connect(function()
	if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
		local root = LocalPlayer.Character.HumanoidRootPart; local hum = LocalPlayer.Character.Humanoid
		if not root:FindFirstChild("LuxFlyVelocity") then
			local bv = Instance.new("BodyVelocity"); bv.Name = "LuxFlyVelocity"; bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge); bv.Velocity = Vector3.new(0, 0, 0); bv.Parent = root
			local bg = Instance.new("BodyGyro"); bg.Name = "LuxFlyGyro"; bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9); bg.P = 10000; bg.D = 100; bg.CFrame = root.CFrame; bg.Parent = root
		end
		local bv = root:FindFirstChild("LuxFlyVelocity"); local bg = root:FindFirstChild("LuxFlyGyro"); hum.PlatformStand = true
		local moveDir = Vector3.new(0, 0, 0)
		if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + Camera.CFrame.LookVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - Camera.CFrame.LookVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - Camera.CFrame.RightVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + Camera.CFrame.RightVector end
		if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
		if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDir = moveDir - Vector3.new(0, 1, 0) end
		if moveDir.Magnitude == 0 and hum.MoveDirection.Magnitude > 0 then moveDir = Camera.CFrame.LookVector end
		bv.Velocity = moveDir * Settings.SpeedVal; bg.CFrame = Camera.CFrame
	else
		if LocalPlayer.Character then
			local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
			if root then 
                if root:FindFirstChild("LuxFlyVelocity") then root.LuxFlyVelocity:Destroy() end
                if root:FindFirstChild("LuxFlyGyro") then root.LuxFlyGyro:Destroy() end 
            end
			local hum = LocalPlayer.Character:FindFirstChild("Humanoid"); if hum then hum.PlatformStand = false end
		end
	end
end)

-- Loading Finish
local tw = TweenService:Create(BarFill, TweenInfo.new(3), {Size = UDim2.new(1, 0, 1, 0)}); tw:Play()
tw.Completed:Connect(function() task.wait(0.5); local blurOff = TweenService:Create(LoadingBlur, TweenInfo.new(1), {Size = 0}); blurOff:Play()
	blurOff.Completed:Connect(function() LoadingBlur:Destroy(); LoadingContainer:Destroy(); OpenBtn.Visible = true end)
end)
