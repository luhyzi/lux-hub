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

-- VARIÁVEL PARA O SERVER TIME
local JoinTime = tick()

-- CONFIGURAÇÕES
local Settings = {
	ESP = false,
	Noclip = false,
	Speed = false,
	SpeedVal = 100,
	Fly = false,
	Invisible = false,
	CamLock = false,
    InfiniteJump = false,
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
    TargetPurple = Color3.fromRGB(130, 0, 255),
	Green = Color3.fromRGB(0, 255, 100),
	Yellow = Color3.fromRGB(255, 170, 0),
	Red = Color3.fromRGB(255, 50, 50),
    CloseBtn = Color3.fromRGB(80, 0, 120),
    Overlay = Color3.fromRGB(0, 0, 0)
}

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_v2.2_MobileFix"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.DisplayOrder = 10000 
ScreenGui.IgnoreGuiInset = true 

if gethui then
    ScreenGui.Parent = gethui()
elseif game:GetService("CoreGui") then
    pcall(function() ScreenGui.Parent = game:GetService("CoreGui") end)
end
if not ScreenGui.Parent then
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end

-- ==========================================
-- VARIÁVEL PARA LEMBRAR POSIÇÃO
-- ==========================================
local LastPosition = UDim2.new(0.5, -240, 0.5, -140)

-- ==========================================
-- FUNÇÃO ARRASTAR
-- ==========================================
local function MakeDraggable(trigger, objectToMove)
	objectToMove = objectToMove or trigger
	local dragging = false
	local dragInput, dragStart, startPos
	
	trigger.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			dragging = true
			dragStart = input.Position
			startPos = objectToMove.Position
			
			input.Changed:Connect(function()
				if input.UserInputState == Enum.UserInputState.End then
					dragging = false
				end
			end)
		end
	end)

	trigger.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
			dragInput = input
		end
	end)

	UserInputService.InputChanged:Connect(function(input)
		if input == dragInput and dragging then
			local delta = input.Position - dragStart
            local newPos = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
			objectToMove.Position = newPos
            if objectToMove.Name == "MainFrame" then LastPosition = newPos end
		end
	end)
end

-- ==========================================
-- LOADING EFEITOS
-- ==========================================
local LoadingBlur = Instance.new("BlurEffect"); LoadingBlur.Name = "LuxLoadingBlur"; LoadingBlur.Size = 0; LoadingBlur.Parent = Lighting
TweenService:Create(LoadingBlur, TweenInfo.new(0.8), {Size = 56}):Play()

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
OpenBtn.Text = "LX"; OpenBtn.Font = Enum.Font.GothamBlack; OpenBtn.TextSize = 28; OpenBtn.TextColor3 = Color3.fromRGB(255, 255, 255); OpenBtn.Visible = false; OpenBtn.ZIndex = 10; OpenBtn.Parent = ScreenGui
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1, 0); MakeDraggable(OpenBtn)

-- ==========================================
-- JANELA PRINCIPAL
-- ==========================================
local MainFrame = Instance.new("Frame"); MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 480, 0, 280)
MainFrame.Position = UDim2.new(0.5, -240, 1.5, 0)
MainFrame.BackgroundColor3 = Colors.MainBG; MainFrame.Visible = false; MainFrame.ClipsDescendants = true; MainFrame.ZIndex = 5; MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 20)
local MenuBlur = nil; local isOpen = false; local isAnimating = false

local function ToggleMenu()
    if isAnimating then return end; isAnimating = true
    if isOpen then
        -- FECHAR
        if MenuBlur then local blurOut = TweenService:Create(MenuBlur, TweenInfo.new(0.6), {Size = 0}); blurOut:Play(); blurOut.Completed:Connect(function() if MenuBlur then MenuBlur:Destroy(); MenuBlur = nil end end) end
        
        LastPosition = MainFrame.Position 
        local closeTween = TweenService:Create(MainFrame, TweenInfo.new(0.6, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {Position = UDim2.new(LastPosition.X.Scale, LastPosition.X.Offset, 1.5, 0)})
        closeTween:Play(); closeTween.Completed:Connect(function() MainFrame.Visible = false; isAnimating = false end)
        isOpen = false
    else
        -- ABRIR
        if not MenuBlur then MenuBlur = Instance.new("BlurEffect"); MenuBlur.Name = "LuxMenuBlur"; MenuBlur.Size = 0; MenuBlur.Parent = Lighting end
        TweenService:Create(MenuBlur, TweenInfo.new(0.6), {Size = 56}):Play()
        
        MainFrame.Visible = true; MainFrame.Position = UDim2.new(LastPosition.X.Scale, LastPosition.X.Offset, 1.5, 0)
        local openTween = TweenService:Create(MainFrame, TweenInfo.new(0.6, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Position = LastPosition})
        openTween:Play(); openTween.Completed:Connect(function() isAnimating = false end)
        isOpen = true
    end
end
OpenBtn.MouseButton1Click:Connect(ToggleMenu)

-- Sidebar
local Sidebar = Instance.new("Frame"); Sidebar.Size = UDim2.new(0, 130, 1, 0); Sidebar.BackgroundColor3 = Colors.Sidebar; Sidebar.ZIndex = 6; Sidebar.Parent = MainFrame; Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 20)
local Title = Instance.new("TextLabel"); Title.Text = "Lux Hub"; Title.Font = Enum.Font.GothamBold; Title.TextSize = 22; Title.TextColor3 = Colors.TextSelected; Title.Size = UDim2.new(1, 0, 0, 30); Title.Position = UDim2.new(0, 0, 0, 20); Title.BackgroundTransparency = 1; Title.TextXAlignment = Enum.TextXAlignment.Center; Title.ZIndex = 7; Title.Parent = Sidebar
-- VERSÃO V2.2
local SubVer = Instance.new("TextLabel"); SubVer.Text = "V2.2"; SubVer.Font = Enum.Font.Gotham; SubVer.TextSize = 12; SubVer.TextColor3 = Color3.fromRGB(180,180,180); SubVer.Size = UDim2.new(1, 0, 0, 20); SubVer.Position = UDim2.new(0, 0, 0, 45); SubVer.BackgroundTransparency = 1; SubVer.TextXAlignment = Enum.TextXAlignment.Center; SubVer.ZIndex = 7; SubVer.Parent = Sidebar

-- Drag Zone
local DragFrame = Instance.new("Frame"); DragFrame.Name = "TopDragZone"; DragFrame.Size = UDim2.new(1, -130, 0, 35); DragFrame.Position = UDim2.new(0, 130, 0, 0); DragFrame.BackgroundTransparency = 1; DragFrame.Active = true; DragFrame.ZIndex = 10; DragFrame.Parent = MainFrame; MakeDraggable(DragFrame, MainFrame)

-- Botão Fechar
local CloseBtn = Instance.new("TextButton"); CloseBtn.Name = "CloseButton"
CloseBtn.Size = UDim2.new(0, 30, 0, 30); CloseBtn.Position = UDim2.new(1, -40, 0, 5) 
CloseBtn.BackgroundColor3 = Colors.CloseBtn; CloseBtn.Text = "X"; CloseBtn.Font = Enum.Font.GothamBold; CloseBtn.TextSize = 16; CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255); CloseBtn.ZIndex = 10; CloseBtn.Parent = MainFrame; Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(1, 0); CloseBtn.MouseButton1Click:Connect(function() if isOpen then ToggleMenu() end end)

-- Containers (CANVASGROUP PARA FADE SUAVE)
local PageContainer = Instance.new("Frame"); PageContainer.Size = UDim2.new(1, -140, 1, -40); PageContainer.Position = UDim2.new(0, 140, 0, 40); PageContainer.BackgroundTransparency = 1; PageContainer.ZIndex = 6; PageContainer.Parent = MainFrame

-- Main Page
local MainGroup = Instance.new("CanvasGroup"); MainGroup.Size = UDim2.new(1, 0, 1, 0); MainGroup.BackgroundTransparency = 1; MainGroup.ZIndex = 6; MainGroup.Visible = true; MainGroup.Parent = PageContainer
local MainPage = Instance.new("ScrollingFrame"); MainPage.Size = UDim2.new(1, 0, 1, 0); MainPage.BackgroundTransparency = 1; MainPage.ScrollBarThickness = 2; MainPage.ZIndex = 6; MainPage.Parent = MainGroup; local UIList = Instance.new("UIListLayout"); UIList.Padding = UDim.new(0, 8); UIList.Parent = MainPage

-- Status Page
local StatusGroup = Instance.new("CanvasGroup"); StatusGroup.Size = UDim2.new(1, 0, 1, 0); StatusGroup.BackgroundTransparency = 1; StatusGroup.ZIndex = 6; StatusGroup.Visible = false; StatusGroup.Parent = PageContainer

-- Server Page
local ServerGroup = Instance.new("CanvasGroup"); ServerGroup.Size = UDim2.new(1, 0, 1, 0); ServerGroup.BackgroundTransparency = 1; ServerGroup.ZIndex = 6; ServerGroup.Visible = false; ServerGroup.Parent = PageContainer

-- ==========================================
-- SISTEMA DE ABAS SUAVE
-- ==========================================
local TabContainer = Instance.new("Frame"); TabContainer.Position = UDim2.new(0, 5, 0, 80); TabContainer.Size = UDim2.new(1, -10, 1, -90); TabContainer.BackgroundTransparency = 1; TabContainer.ZIndex = 7; TabContainer.Parent = Sidebar; local TabList = Instance.new("UIListLayout"); TabList.Padding = UDim.new(0, 8); TabList.Parent = TabContainer

local TabButtons = {}
local CurrentPage = MainGroup
local IsSwitching = false

local function SwitchTab(btn, newPage)
    if CurrentPage == newPage or IsSwitching then return end
    IsSwitching = true
    
    if MenuBlur then 
        local blurSpike = TweenService:Create(MenuBlur, TweenInfo.new(0.2, Enum.EasingStyle.Sine, Enum.EasingDirection.Out, 0, true), {Size = 70})
        blurSpike:Play()
    end

    for _, b in pairs(TabButtons) do 
        TweenService:Create(b, TweenInfo.new(0.3), {BackgroundTransparency = 0.9, TextColor3 = Colors.Text}):Play()
    end
    TweenService:Create(btn, TweenInfo.new(0.3), {BackgroundTransparency = 0.6, TextColor3 = Colors.TextSelected}):Play()

    local tweenOut = TweenService:Create(CurrentPage, TweenInfo.new(0.25), {GroupTransparency = 1})
    tweenOut:Play()
    tweenOut.Completed:Wait()
    CurrentPage.Visible = false

    newPage.Visible = true
    newPage.GroupTransparency = 1
    local tweenIn = TweenService:Create(newPage, TweenInfo.new(0.3), {GroupTransparency = 0})
    tweenIn:Play()

    CurrentPage = newPage
    IsSwitching = false
end

local function CreateTab(name, pageToShow)
	local Btn = Instance.new("TextButton"); Btn.Size = UDim2.new(1, 0, 0, 35); Btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255); Btn.BackgroundTransparency = 0.9; Btn.Text = name; Btn.Font = Enum.Font.GothamBold; Btn.TextSize = 16; Btn.TextColor3 = Colors.Text; Btn.ZIndex = 8; Btn.Parent = TabContainer; Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 8)
    TabButtons[name] = Btn
	Btn.MouseButton1Click:Connect(function() SwitchTab(Btn, pageToShow) end)
end

CreateTab("Main", MainGroup)
CreateTab("Status", StatusGroup)
CreateTab("Server", ServerGroup)

TabButtons["Main"].BackgroundTransparency = 0.6; TabButtons["Main"].TextColor3 = Colors.TextSelected

-- Status (Conteúdo)
local StatsInfoContainer = Instance.new("Frame"); StatsInfoContainer.Size = UDim2.new(0.95, 0, 1, 0); StatsInfoContainer.Position = UDim2.new(0, 5, 0, 0); StatsInfoContainer.BackgroundTransparency = 1; StatsInfoContainer.ZIndex = 6; StatsInfoContainer.Parent = StatusGroup
local StatsGrid = Instance.new("UIGridLayout"); StatsGrid.CellSize = UDim2.new(0.48, 0, 0, 25); StatsGrid.CellPadding = UDim2.new(0.02, 0, 0, 5); StatsGrid.SortOrder = Enum.SortOrder.LayoutOrder; StatsGrid.Parent = StatsInfoContainer
local function CreateStatLabel(text, order)
	local L = Instance.new("TextLabel"); L.Text = text; L.Font = Enum.Font.GothamBold; L.TextSize = 14; L.TextColor3 = Colors.Text; L.BackgroundTransparency = 1; L.TextXAlignment = Enum.TextXAlignment.Left; L.LayoutOrder = order; L.ZIndex = 7; L.Parent = StatsInfoContainer; return L
end
CreateStatLabel("Player: " .. LocalPlayer.Name, 1); local RegionLabel = CreateStatLabel("Region: ...", 2); local ServerTimeLabel = CreateStatLabel("S.Time: ...", 3); local TimeLabel = CreateStatLabel("Time: ...", 4); local DateLabel = CreateStatLabel("Date: ...", 5); local ExecutorLabel = CreateStatLabel("Exec: ...", 6); local HealthLabel = CreateStatLabel("HP: ...", 7); local FPSLabel = CreateStatLabel("FPS: ...", 8); local PingLabel = CreateStatLabel("Ping: ...", 9); local PlayersLabel = CreateStatLabel("Online: ...", 10); local PosLabel = CreateStatLabel("Pos: ...", 11)
task.spawn(function() pcall(function() local data = HttpService:JSONDecode(game:HttpGet("http://ip-api.com/json/")); RegionLabel.Text = "Region: " .. data.countryCode end) end)
ExecutorLabel.Text = "Exec: " .. (identifyexecutor and identifyexecutor() or "Unknown")

-- Server Hop (Conteúdo)
local Hop = Instance.new("TextButton"); Hop.Text = "Server Hop"; Hop.Font = Enum.Font.GothamBold; Hop.TextSize = 17; Hop.Size = UDim2.new(0.95, 0, 0, 40); Hop.Position = UDim2.new(0.025, 0, 0, 10); Hop.BackgroundColor3 = Colors.ItemBG; Hop.TextColor3 = Colors.Text; Hop.ZIndex = 6; Hop.Parent = ServerGroup; Instance.new("UICorner", Hop).CornerRadius = UDim.new(0, 10)
Hop.MouseButton1Click:Connect(function() local sfUrl = "https://games.roblox.com/v1/games/%s/servers/Public?sortOrder=Asc&limit=100"; local req = game:HttpGet(string.format(sfUrl, game.PlaceId)); local body = HttpService:JSONDecode(req) 
    if body and body.data then for i, v in next, body.data do if type(v) == "table" and tonumber(v.playing) and tonumber(v.maxPlayers) and v.playing < v.maxPlayers and v.id ~= game.JobId then TeleportService:TeleportToPlaceInstance(game.PlaceId, v.id, LocalPlayer) break end end end
end)

-- ==========================================
-- FUNÇÕES DO MAIN
-- ==========================================
local function AddToggle(text, callback)
	local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.ZIndex = 6; F.Parent = MainPage; Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
	local L = Instance.new("TextLabel"); L.Text = text; L.Font = Enum.Font.GothamBold; L.TextSize = 16; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.6, 0, 1, 0); L.BackgroundTransparency = 1; L.ZIndex = 7; L.Parent = F
	local B = Instance.new("TextButton"); B.Text = ""; B.Size = UDim2.new(0, 24, 0, 24); B.Position = UDim2.new(1, -35, 0.5, -12); B.BackgroundColor3 = Colors.ToggleOff; B.ZIndex = 7; B.Parent = F; Instance.new("UICorner", B).CornerRadius = UDim.new(1, 0)
	local s = false; 
    B.MouseButton1Click:Connect(function() 
        s = not s; 
        TweenService:Create(B, TweenInfo.new(0.4, Enum.EasingStyle.Quart), {BackgroundColor3 = s and Colors.ToggleOn or Colors.ToggleOff}):Play()
        callback(s) 
    end)
end

local function AddSpeedControl()
    local F = Instance.new("Frame"); F.Size = UDim2.new(1, -10, 0, 40); F.BackgroundColor3 = Colors.ItemBG; F.ZIndex = 6; F.Parent = MainPage; Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
    local L = Instance.new("TextLabel"); L.Text = "Speed"; L.Font = Enum.Font.GothamBold; L.TextSize = 16; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 15, 0, 0); L.Size = UDim2.new(0.3, 0, 1, 0); L.BackgroundTransparency = 1; L.ZIndex = 7; L.Parent = F
    local Minus = Instance.new("TextButton"); Minus.Text = "-"; Minus.Size = UDim2.new(0, 30, 0, 30); Minus.Position = UDim2.new(0.4, 0, 0.5, -15); Minus.BackgroundColor3 = Colors.Sidebar; Minus.TextColor3 = Colors.Text; Minus.ZIndex = 7; Minus.Parent = F; Instance.new("UICorner", Minus).CornerRadius = UDim.new(0, 5)
    local Val = Instance.new("TextLabel"); Val.Text = tostring(Settings.SpeedVal); Val.Size = UDim2.new(0, 40, 1, 0); Val.Position = UDim2.new(0.5, 0, 0, 0); Val.BackgroundTransparency = 1; Val.TextColor3 = Colors.TextSelected; Val.Font = Enum.Font.GothamBold; Val.ZIndex = 7; Val.Parent = F
    local Plus = Instance.new("TextButton"); Plus.Text = "+"; Plus.Size = UDim2.new(0, 30, 0, 30); Plus.Position = UDim2.new(0.65, 0, 0.5, -15); Plus.BackgroundColor3 = Colors.Sidebar; Plus.TextColor3 = Colors.Text; Plus.ZIndex = 7; Plus.Parent = F; Instance.new("UICorner", Plus).CornerRadius = UDim.new(0, 5)
    local Toggle = Instance.new("TextButton"); Toggle.Text = ""; Toggle.Size = UDim2.new(0, 24, 0, 24); Toggle.Position = UDim2.new(1, -35, 0.5, -12); Toggle.BackgroundColor3 = Colors.ToggleOff; Toggle.ZIndex = 7; Toggle.Parent = F; Instance.new("UICorner", Toggle).CornerRadius = UDim.new(1, 0)
    
    Minus.MouseButton1Click:Connect(function() Settings.SpeedVal = math.max(16, Settings.SpeedVal - 10); Val.Text = tostring(Settings.SpeedVal) end)
    Plus.MouseButton1Click:Connect(function() Settings.SpeedVal = Settings.SpeedVal + 10; Val.Text = tostring(Settings.SpeedVal) end)
    local s = false; 
    Toggle.MouseButton1Click:Connect(function() 
        s = not s; 
        Settings.Speed = s; 
        TweenService:Create(Toggle, TweenInfo.new(0.4, Enum.EasingStyle.Quart), {BackgroundColor3 = s and Colors.ToggleOn or Colors.ToggleOff}):Play() 
    end)
end

local function ApplySmoothTexture(v)
    if v then for _, obj in pairs(game:GetDescendants()) do if obj:IsA("BasePart") then obj.Material = Enum.Material.SmoothPlastic elseif obj:IsA("Texture") or obj:IsA("Decal") then obj:Destroy() end end Lighting.GlobalShadows = false else Lighting.GlobalShadows = true end
end

AddToggle("Esp", function(v) Settings.ESP = v end)
AddToggle("No Clip", function(v) Settings.Noclip = v end)
AddToggle("Fly", function(v) Settings.Fly = v end)
AddToggle("Infinite Jump", function(v) Settings.InfiniteJump = v end)
AddSpeedControl()
AddToggle("FPS Boost", function(v) ApplySmoothTexture(v) end)
AddToggle("Cam Lock", function(v) Settings.CamLock = v end)
AddToggle("Invisible", function(v) Settings.Invisible = v; if LocalPlayer.Character then for _,p in pairs(LocalPlayer.Character:GetDescendants()) do if p:IsA("BasePart") or p:IsA("Decal") then p.Transparency = v and 1 or 0 end end end end)

UserInputService.JumpRequest:Connect(function()
	if Settings.InfiniteJump and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
		LocalPlayer.Character.Humanoid:
