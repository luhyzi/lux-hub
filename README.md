local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local Lighting = game:GetService("Lighting")
local Stats = game:GetService("Stats")
local VirtualUser = game:GetService("VirtualUser")
local TextChatService = game:GetService("TextChatService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

local JoinTime = tick()

local Settings = {
    -- Main
    ESP = false, Fullbright = false, FOV = 70, Hitbox = false, NoFog = false, Crosshair = false, CamLock = false, Target = nil,
    -- Player
    Noclip = false, Speed = false, SpeedVal = 100, Fly = false, Invisible = false, InfiniteJump = false, HighJump = false, HighJumpVal = 50,
    ClickTP = false, SpinBot = false, AntiAFK = false, BHop = false, NoFall = false, TpTargetPlayer = nil,
    -- Misc
    Spider = false, ChatSpam = false, ChatMsg = "Lux Hub on Top!"
}

-- CORES DARK (V2.6)
local Colors = {
	MainBG = Color3.fromRGB(10, 10, 10),        -- Preto Quase Puro
	Sidebar = Color3.fromRGB(5, 5, 5),          -- Preto Puro
	ItemBG = Color3.fromRGB(25, 25, 25),        -- Cinza Chumbo
	ToggleOff = Color3.fromRGB(40, 40, 40),     -- Cinza Escuro
	ToggleOn = Color3.fromRGB(255, 255, 255),   -- Branco
	Text = Color3.fromRGB(240, 240, 240),       -- Texto Branco
	TextSelected = Color3.fromRGB(255, 255, 255),
	Accent = Color3.fromRGB(120, 80, 255),      -- Roxo Neon
	TargetPurple = Color3.fromRGB(130, 0, 255),
	CloseBtn = Color3.fromRGB(30, 30, 30)       -- Cinza Escuro (Combina com o script)
}

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_v2.6_DexIY"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.IgnoreGuiInset = true 
if gethui then ScreenGui.Parent = gethui() elseif game:GetService("CoreGui") then pcall(function() ScreenGui.Parent = game:GetService("CoreGui") end) end
if not ScreenGui.Parent then ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui") end

-- CROSSHAIR
local Crosshair = Instance.new("Frame", ScreenGui); Crosshair.Name="LuxCrosshair"; Crosshair.Size=UDim2.new(0,4,0,4); Crosshair.Position=UDim2.new(0.5,0,0.5,0); Crosshair.AnchorPoint=Vector2.new(0.5,0.5); Crosshair.BackgroundColor3=Color3.fromRGB(255,0,0); Crosshair.Visible=false; Crosshair.ZIndex=100; Instance.new("UICorner", Crosshair).CornerRadius=UDim.new(1,0)

-- DRAGGABLE
local LastPosition = UDim2.new(0.5, -240, 0.5, -140)
local function MakeDraggable(trigger, objectToMove)
	objectToMove = objectToMove or trigger
	local dragging, dragInput, dragStart, startPos
	trigger.InputBegan:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = true; dragStart = input.Position; startPos = objectToMove.Position; input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end) end end)
	trigger.InputChanged:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end end)
	UserInputService.InputChanged:Connect(function(input) if input == dragInput and dragging then local delta = input.Position - dragStart; objectToMove.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y); if objectToMove.Name == "MainFrame" then LastPosition = objectToMove.Position end end end)
end

local GlobalBlur = Instance.new("BlurEffect", Lighting); GlobalBlur.Size = 0

-- UI SETUP
-- BOTÃO LX CLÁSSICO
local OpenBtn = Instance.new("TextButton", ScreenGui); OpenBtn.Size=UDim2.new(0,55,0,55); OpenBtn.Position=UDim2.new(0,5,0.25,0); OpenBtn.BackgroundColor3=Colors.MainBG; OpenBtn.Text="LX"; OpenBtn.Font=Enum.Font.GothamBlack; OpenBtn.TextSize=28; OpenBtn.TextColor3=Color3.new(1,1,1); OpenBtn.Visible=false; OpenBtn.ZIndex=10; Instance.new("UICorner", OpenBtn).CornerRadius=UDim.new(1,0); MakeDraggable(OpenBtn)

local MainFrame = Instance.new("Frame", ScreenGui); MainFrame.Name="MainFrame"; MainFrame.Size=UDim2.new(0,480,0,280); MainFrame.Position=UDim2.new(0.5,-240,1.5,0); MainFrame.BackgroundColor3=Colors.MainBG; MainFrame.Visible=false; MainFrame.ClipsDescendants=true; MainFrame.ZIndex=5; Instance.new("UICorner", MainFrame).CornerRadius=UDim.new(0,20)
local isOpen=false; local isAnimating=false
local function ToggleMenu()
    if isAnimating then return end; isAnimating=true
    if isOpen then TweenService:Create(GlobalBlur, TweenInfo.new(0.6), {Size=0}):Play(); local t=TweenService:Create(MainFrame, TweenInfo.new(0.6,Enum.EasingStyle.Quart,Enum.EasingDirection.In), {Position=UDim2.new(LastPosition.X.Scale,LastPosition.X.Offset,1.5,0)}); t:Play(); t.Completed:Connect(function() MainFrame.Visible=false; isAnimating=false end); isOpen=false
    else MainFrame.Visible=true; MainFrame.Position=UDim2.new(LastPosition.X.Scale,LastPosition.X.Offset,1.5,0); TweenService:Create(GlobalBlur, TweenInfo.new(0.6), {Size=56}):Play(); local t=TweenService:Create(MainFrame, TweenInfo.new(0.6,Enum.EasingStyle.Quart,Enum.EasingDirection.Out), {Position=LastPosition}); t:Play(); t.Completed:Connect(function() isAnimating=false end); isOpen=true end
end
OpenBtn.MouseButton1Click:Connect(ToggleMenu)

local Sidebar = Instance.new("Frame", MainFrame); Sidebar.Size=UDim2.new(0,130,1,0); Sidebar.BackgroundColor3=Colors.Sidebar; Sidebar.ZIndex=6; Instance.new("UICorner", Sidebar).CornerRadius=UDim.new(0,20)
local Title = Instance.new("TextLabel", Sidebar); Title.Text="Lux Hub"; Title.Font=Enum.Font.GothamBold; Title.TextSize=22; Title.TextColor3=Colors.TextSelected; Title.Size=UDim2.new(1,0,0,30); Title.Position=UDim2.new(0,0,0,20); Title.BackgroundTransparency=1; Title.ZIndex=7
local SubVer = Instance.new("TextLabel", Sidebar); SubVer.Text="V2.6"; SubVer.Font=Enum.Font.Gotham; SubVer.TextSize=12; SubVer.TextColor3=Color3.fromRGB(150,150,150); SubVer.Size=UDim2.new(1,0,0,20); SubVer.Position=UDim2.new(0,0,0,45); SubVer.BackgroundTransparency=1; SubVer.ZIndex=7

local DragFrame = Instance.new("Frame", MainFrame); DragFrame.Size=UDim2.new(1,-130,0,35); DragFrame.Position=UDim2.new(0,130,0,0); DragFrame.BackgroundTransparency=1; DragFrame.ZIndex=10; MakeDraggable(DragFrame, MainFrame)
local PageTitle = Instance.new("TextLabel", MainFrame); PageTitle.Text="Main"; PageTitle.Font=Enum.Font.GothamBold; PageTitle.TextSize=20; PageTitle.TextColor3=Colors.TextSelected; PageTitle.Size=UDim2.new(1,-170,0,35); PageTitle.Position=UDim2.new(0,130,0,0); PageTitle.BackgroundTransparency=1; PageTitle.ZIndex=7

-- Botão FECHAR (X) com cor do tema
local CloseBtn = Instance.new("TextButton", MainFrame); CloseBtn.Size=UDim2.new(0,30,0,30); CloseBtn.Position=UDim2.new(1,-40,0,5); CloseBtn.BackgroundColor3=Colors.CloseBtn; CloseBtn.Text="X"; CloseBtn.Font=Enum.Font.GothamBold; CloseBtn.TextSize=22; CloseBtn.TextColor3=Color3.new(1,1,1); CloseBtn.ZIndex=10; Instance.new("UICorner", CloseBtn).CornerRadius=UDim.new(1,0); CloseBtn.MouseButton1Click:Connect(function() if isOpen then ToggleMenu() end end)

local PageContainer = Instance.new("Frame", MainFrame); PageContainer.Size=UDim2.new(1,-140,1,-40); PageContainer.Position=UDim2.new(0,140,0,40); PageContainer.BackgroundTransparency=1; PageContainer.ZIndex=6

-- SCROLLING FUNCTION
local function CreateScrollingPage(parent)
    local S = Instance.new("ScrollingFrame", parent); S.Size=UDim2.new(1,0,1,0); S.BackgroundTransparency=1; S.ScrollBarThickness=2; S.ZIndex=6; S.AutomaticCanvasSize=Enum.AutomaticSize.Y; S.CanvasSize=UDim2.new(0,0,0,0)
    local Pad = Instance.new("UIPadding", S); Pad.PaddingBottom=UDim.new(0,10); Pad.PaddingTop=UDim.new(0,2)
    local L = Instance.new("UIListLayout", S); L.Padding=UDim.new(0,8)
    return S
end

-- PAGES STRUCTURE
local MainGroup = Instance.new("CanvasGroup", PageContainer); MainGroup.Size=UDim2.new(1,0,1,0); MainGroup.BackgroundTransparency=1; MainGroup.ZIndex=6; local MainPage = CreateScrollingPage(MainGroup)
local PlayerGroup = Instance.new("CanvasGroup", PageContainer); PlayerGroup.Size=UDim2.new(1,0,1,0); PlayerGroup.BackgroundTransparency=1; PlayerGroup.ZIndex=6; PlayerGroup.Visible=false; local PlayerPage = CreateScrollingPage(PlayerGroup)
local MiscGroup = Instance.new("CanvasGroup", PageContainer); MiscGroup.Size=UDim2.new(1,0,1,0); MiscGroup.BackgroundTransparency=1; MiscGroup.ZIndex=6; MiscGroup.Visible=false; local MiscPage = CreateScrollingPage(MiscGroup)
local StatusGroup = Instance.new("Frame", PageContainer); StatusGroup.Size=UDim2.new(1,0,1,0); StatusGroup.BackgroundTransparency=1; StatusGroup.ZIndex=6; StatusGroup.Visible=false; local StatusPage = CreateScrollingPage(StatusGroup)
local ServerGroup = Instance.new("Frame", PageContainer); ServerGroup.Size=UDim2.new(1,0,1,0); ServerGroup.BackgroundTransparency=1; ServerGroup.ZIndex=6; ServerGroup.Visible=false; local ServerPage = CreateScrollingPage(ServerGroup)

-- SIDEBAR SCROLL (CENTERED)
local TabContainer = Instance.new("ScrollingFrame", Sidebar); TabContainer.Position = UDim2.new(0, 0, 0, 80); TabContainer.Size = UDim2.new(1, 0, 1, -90); TabContainer.BackgroundTransparency = 1; TabContainer.ZIndex = 7; TabContainer.ScrollBarThickness = 2; TabContainer.ScrollBarImageColor3 = Colors.Accent; TabContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y; TabContainer.CanvasSize = UDim2.new(0,0,0,0)
local TabListLayout = Instance.new("UIListLayout", TabContainer); TabListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center; TabListLayout.SortOrder = Enum.SortOrder.LayoutOrder; TabListLayout.Padding = UDim.new(0, 8)
local TabPadding = Instance.new("UIPadding", TabContainer); TabPadding.PaddingTop = UDim.new(0, 5); TabPadding.PaddingBottom = UDim.new(0, 5)

local TabButtons={}; local CurrentPage=MainGroup
local function SwitchTab(btn, newPage)
    if CurrentPage==newPage then return end
    PageTitle.Text=btn.Text
    TweenService:Create(GlobalBlur, TweenInfo.new(0.2), {Size=70}):Play()
    for _,b in pairs(TabButtons) do TweenService:Create(b,TweenInfo.new(0.3),{BackgroundTransparency=0.9, TextColor3=Colors.Text}):Play() end
    TweenService:Create(btn,TweenInfo.new(0.3),{BackgroundTransparency=0.6, TextColor3=Colors.TextSelected}):Play()
    CurrentPage.Visible=false; newPage.Visible=true; CurrentPage=newPage
end
local function CreateTab(name, page)
	local Btn = Instance.new("TextButton", TabContainer); Btn.Size=UDim2.new(0.85,0,0,35); Btn.BackgroundColor3=Color3.new(1,1,1); Btn.BackgroundTransparency=0.9; Btn.Text=name; Btn.Font=Enum.Font.GothamBold; Btn.TextSize=16; Btn.TextColor3=Colors.Text; Btn.ZIndex=8; Instance.new("UICorner", Btn).CornerRadius=UDim.new(0,8)
    TabButtons[name]=Btn; Btn.MouseButton1Click:Connect(function() SwitchTab(Btn, page) end)
end
CreateTab("Main", MainGroup); CreateTab("Player", PlayerGroup); CreateTab("Misc", MiscGroup); CreateTab("Status", StatusGroup); CreateTab("Server", ServerGroup)
TabButtons["Main"].BackgroundTransparency=0.6; TabButtons["Main"].TextColor3=Colors.TextSelected

-- HELPERS
local function AddToggle(text, parent, callback)
	local F = Instance.new("Frame", parent); F.Size=UDim2.new(1,-10,0,40); F.BackgroundColor3=Colors.ItemBG; F.ZIndex=6; Instance.new("UICorner", F).CornerRadius=UDim.new(0,10)
	local L = Instance.new("TextLabel", F); L.Text=text; L.Font=Enum.Font.GothamBold; L.TextSize=17; L.TextColor3=Colors.Text; L.TextXAlignment=0; L.Position=UDim2.new(0,15,0,0); L.Size=UDim2.new(0.6,0,1,0); L.BackgroundTransparency=1; L.ZIndex=7
	local B = Instance.new("TextButton", F); B.Text=""; B.Size=UDim2.new(0,24,0,24); B.Position=UDim2.new(1,-35,0.5,-12); B.BackgroundColor3=Colors.ToggleOff; B.ZIndex=7; Instance.new("UICorner", B).CornerRadius=UDim.new(1,0)
	local s=false; B.MouseButton1Click:Connect(function() s=not s; TweenService:Create(B, TweenInfo.new(0.4), {BackgroundColor3=s and Colors.ToggleOn or Colors.ToggleOff}):Play(); callback(s) end)
end

local function AddSlider(text, parent, min, max, callback, default)
    local current=default or min
    local F = Instance.new("Frame", parent); F.Size=UDim2.new(1,-10,0,40); F.BackgroundColor3=Colors.ItemBG; F.ZIndex=6; Instance.new("UICorner", F).CornerRadius=UDim.new(0,10)
    local L = Instance.new("TextLabel", F); L.Text=text; L.Font=Enum.Font.GothamBold; L.TextSize=17; L.TextColor3=Colors.Text; L.TextXAlignment=0; L.Position=UDim2.new(0,15,0,0); L.Size=UDim2.new(0.3,0,1,0); L.BackgroundTransparency=1; L.ZIndex=7
    local M = Instance.new("TextButton", F); M.Text="-"; M.Size=UDim2.new(0,30,0,30); M.Position=UDim2.new(0.4,0,0.5,-15); M.BackgroundColor3=Colors.Sidebar; M.TextColor3=Colors.Text; M.ZIndex=7; Instance.new("UICorner", M).CornerRadius=UDim.new(0,5)
    local V = Instance.new("TextLabel", F); V.Text=tostring(current); V.Size=UDim2.new(0,40,1,0); V.Position=UDim2.new(0.5,0,0,0); V.BackgroundTransparency=1; V.TextColor3=Colors.TextSelected; V.Font=Enum.Font.GothamBold; V.ZIndex=7
    local P = Instance.new("TextButton", F); P.Text="+"; P.Size=UDim2.new(0,30,0,30); P.Position=UDim2.new(0.65,0,0.5,-15); P.BackgroundColor3=Colors.Sidebar; P.TextColor3=Colors.Text; P.ZIndex=7; Instance.new("UICorner", P).CornerRadius=UDim.new(0,5)
    local function Update() V.Text=tostring(current); callback(current) end
    M.MouseButton1Click:Connect(function() current=math.max(min,current-10); Update() end)
    P.MouseButton1Click:Connect(function() current=math.min(max,current+10); Update() end)
end

local function AddTextBox(placeholder, parent, callback)
    local F = Instance.new("Frame", parent); F.Size=UDim2.new(1,-10,0,40); F.BackgroundColor3=Colors.ItemBG; F.ZIndex=6; Instance.new("UICorner", F).CornerRadius=UDim.new(0,10)
    local T = Instance.new("TextBox", F); T.Size=UDim2.new(1,-20,1,0); T.Position=UDim2.new(0,10,0,0); T.BackgroundTransparency=1; T.Text=""; T.PlaceholderText=placeholder; T.TextColor3=Colors.Text; T.PlaceholderColor3=Color3.fromRGB(150,150,150); T.Font=Enum.Font.GothamBold; T.TextSize=16; T.ZIndex=7
    T.FocusLost:Connect(function(enter) if enter then callback(T.Text) end end)
end

local function AddButton(text, parent, callback)
    local B = Instance.new("TextButton", parent); B.Size=UDim2.new(1,-10,0,40); B.BackgroundColor3=Colors.ItemBG; B.Text=text; B.TextColor3=Colors.Text; B.Font=Enum.Font.GothamBold; B.TextSize=17; B.ZIndex=6; Instance.new("UICorner", B).CornerRadius=UDim.new(0,10)
    B.MouseButton1Click:Connect(callback)
end

local function AddLabel(text, parent)
    local F = Instance.new("Frame", parent); F.Size=UDim2.new(1,-10,0,30); F.BackgroundTransparency=1; F.ZIndex=6
    local L = Instance.new("TextLabel", F); L.Size=UDim2.new(1,0,1,0); L.BackgroundTransparency=1; L.Text=text; L.TextColor3=Color3.fromRGB(150,150,150); L.Font=Enum.Font.GothamBold; L.TextSize=16; L.ZIndex=7
end

-- TP PLAYER OVERLAY
local PlayerSelectFrame = Instance.new("Frame", MainFrame); PlayerSelectFrame.Size=UDim2.new(0.8,0,0.7,0); PlayerSelectFrame.Position=UDim2.new(0.5,0,0.55,0); PlayerSelectFrame.AnchorPoint=Vector2.new(0.5,0.5); PlayerSelectFrame.BackgroundColor3=Color3.fromRGB(35,15,60); PlayerSelectFrame.ZIndex=20; PlayerSelectFrame.Visible=false; Instance.new("UICorner", PlayerSelectFrame).CornerRadius=UDim.new(0,10)
local PlayerScroll = Instance.new("ScrollingFrame", PlayerSelectFrame); PlayerScroll.Size=UDim2.new(1,-10,1,-10); PlayerScroll.Position=UDim2.new(0,5,0,5); PlayerScroll.BackgroundTransparency=1; PlayerScroll.ZIndex=21; PlayerScroll.ScrollBarThickness=2; PlayerScroll.AutomaticCanvasSize=Enum.AutomaticSize.Y; Instance.new("UIListLayout", PlayerScroll).Padding=UDim.new(0,5)
local TpBtnLabel = nil; local SpectateBtnLabel = nil

local function RefreshPlayerList()
    for _,v in pairs(PlayerScroll:GetChildren()) do if v:IsA("TextButton") then v:Destroy() end end
    for _,p in pairs(Players:GetPlayers()) do
        if p~=LocalPlayer then
            local B = Instance.new("TextButton", PlayerScroll); B.Size=UDim2.new(1,0,0,30); B.BackgroundColor3=Colors.ItemBG; B.Text=p.Name; B.TextColor3=Colors.Text; B.Font=Enum.Font.GothamBold; B.TextSize=16; B.ZIndex=22; Instance.new("UICorner", B).CornerRadius=UDim.new(0,5)
            B.MouseButton1Click:Connect(function() Settings.TpTargetPlayer=p; PlayerSelectFrame.Visible=false; if TpBtnLabel then TpBtnLabel.Text="Teleport to: "..p.Name end; if SpectateBtnLabel then SpectateBtnLabel.Text="Spectate: "..p.Name end end)
        end
    end
end

local function AddTpSystem(parent)
    local Title = Instance.new("TextLabel", parent); Title.Size = UDim2.new(1, -10, 0, 30); Title.BackgroundTransparency = 1; Title.Text = "Player Control System"; Title.Font = Enum.Font.GothamBold; Title.TextColor3 = Colors.Accent; Title.TextSize = 18; Title.TextXAlignment = Enum.TextXAlignment.Left; Title.Position = UDim2.new(0, 5, 0, 0)
    local F1 = Instance.new("Frame", parent); F1.Size=UDim2.new(1,-10,0,40); F1.BackgroundColor3=Colors.ItemBG; F1.ZIndex=6; Instance.new("UICorner", F1).CornerRadius=UDim.new(0,10); local B1 = Instance.new("TextButton", F1); B1.Size=UDim2.new(1,0,1,0); B1.BackgroundTransparency=1; B1.Text="Select Player..."; B1.Font=Enum.Font.GothamBold; B1.TextSize=17; B1.TextColor3=Colors.Text; B1.ZIndex=7; B1.MouseButton1Click:Connect(function() RefreshPlayerList(); PlayerSelectFrame.Visible=not PlayerSelectFrame.Visible end)
    local F2 = Instance.new("Frame", parent); F2.Size=UDim2.new(1,-10,0,40); F2.BackgroundColor3=Colors.ItemBG; F2.ZIndex=6; Instance.new("UICorner", F2).CornerRadius=UDim.new(0,10); TpBtnLabel = Instance.new("TextButton", F2); TpBtnLabel.Size=UDim2.new(1,0,1,0); TpBtnLabel.BackgroundTransparency=1; TpBtnLabel.Text="Teleport to Target"; TpBtnLabel.Font=Enum.Font.GothamBold; TpBtnLabel.TextSize=17; TpBtnLabel.TextColor3=Colors.Accent; TpBtnLabel.ZIndex=7; TpBtnLabel.MouseButton1Click:Connect(function() if Settings.TpTargetPlayer and Settings.TpTargetPlayer.Character and Settings.TpTargetPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then LocalPlayer.Character.HumanoidRootPart.CFrame=Settings.TpTargetPlayer.Character.HumanoidRootPart.CFrame*CFrame.new(0,0,3) end end)
    local F3 = Instance.new("Frame", parent); F3.Size=UDim2.new(1,-10,0,40); F3.BackgroundColor3=Colors.ItemBG; F3.ZIndex=6; Instance.new("UICorner", F3).CornerRadius=UDim.new(0,10); SpectateBtnLabel = Instance.new("TextButton", F3); SpectateBtnLabel.Size=UDim2.new(1,0,1,0); SpectateBtnLabel.BackgroundTransparency=1; SpectateBtnLabel.Text="Spectate Target"; SpectateBtnLabel.Font=Enum.Font.GothamBold; SpectateBtnLabel.TextSize=17; SpectateBtnLabel.TextColor3=Colors.TargetPurple; SpectateBtnLabel.ZIndex=7; SpectateBtnLabel.MouseButton1Click:Connect(function() if Camera.CameraSubject == LocalPlayer.Character.Humanoid then if Settings.TpTargetPlayer and Settings.TpTargetPlayer.Character and Settings.TpTargetPlayer.Character:FindFirstChild("Humanoid") then Camera.CameraSubject = Settings.TpTargetPlayer.Character.Humanoid; SpectateBtnLabel.Text = "Stop Spectating" end else if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then Camera.CameraSubject = LocalPlayer.Character.Humanoid; if Settings.TpTargetPlayer then SpectateBtnLabel.Text = "Spectate: " .. Settings.TpTargetPlayer.Name else SpectateBtnLabel.Text = "Spectate Target" end end end end)
end

-- CONTENT
AddToggle("Esp", MainPage, function(v) Settings.ESP=v end)
AddToggle("Cam Lock", MainPage, function(v) Settings.CamLock=v end)
AddToggle("Fullbright", MainPage, function(v) Settings.Fullbright=v end)
AddToggle("Crosshair", MainPage, function(v) Settings.Crosshair=v; Crosshair.Visible=v end)
AddSlider("FOV", MainPage, 70, 120, function(v) Settings.FOV=v; Camera.FieldOfView=v end, 70)
AddToggle("Hitbox Expander", MainPage, function(v) Settings.Hitbox=v end)
AddToggle("No Fog", MainPage, function(v) Settings.NoFog=v; if v then Lighting.FogEnd=9e9 else Lighting.FogEnd=1000 end end)
AddToggle("FPS Boost", MainPage, function(v) if v then for _,o in pairs(game:GetDescendants()) do if o:IsA("BasePart") then o.Material=Enum.Material.SmoothPlastic elseif o:IsA("Texture") then o:Destroy() end end end end)

AddSlider("Speed", PlayerPage, 16, 300, function(v) Settings.SpeedVal=v end, 100)
AddToggle("Enable Speed", PlayerPage, function(v) Settings.Speed=v end)
AddSlider("Jump Power", PlayerPage, 50, 300, function(v) Settings.HighJumpVal=v end, 50)
AddToggle("Enable High Jump", PlayerPage, function(v) Settings.HighJump=v end)
AddToggle("Fly", PlayerPage, function(v) Settings.Fly=v end)
AddToggle("Noclip", PlayerPage, function(v) Settings.Noclip=v end)
AddToggle("Infinite Jump", PlayerPage, function(v) Settings.InfiniteJump=v end)
AddToggle("Click TP (Touch/Click)", PlayerPage, function(v) Settings.ClickTP=v end)
AddToggle("SpinBot", PlayerPage, function(v) Settings.SpinBot=v end)
AddToggle("B-Hop", PlayerPage, function(v) Settings.BHop=v end)
AddToggle("No Fall Damage", PlayerPage, function(v) Settings.NoFall=v end)
AddToggle("Invisible", PlayerPage, function(v) Settings.Invisible=v; if LocalPlayer.Character then for _,p in pairs(LocalPlayer.Character:GetDescendants()) do if p:IsA("BasePart") or p:IsA("Decal") then if v then p.Transparency = 1 else if p.Name == "HumanoidRootPart" then p.Transparency = 1 else p.Transparency = 0 end end end end end end)
AddToggle("Anti-AFK", PlayerPage, function(v) Settings.AntiAFK=v; if v then LocalPlayer.Idled:Connect(function() if Settings.AntiAFK then VirtualUser:Button2Down(Vector2.new(0,0)); VirtualUser:Button2Up(Vector2.new(0,0)) end end) end end)
AddTpSystem(PlayerPage)

-- MISC PAGE CONTENT (ATUALIZADA)
AddButton("Infinite Yield (Admin)", MiscPage, function() loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))() end)
AddButton("Dex Explorer (Files)", MiscPage, function() loadstring(game:HttpGet("https://raw.githubusercontent.com/Babyhamsta/RBLX_Scripts/main/Universal/BypassedDarkDexV3.lua", true))() end)
AddToggle("Spider (Walk on Walls)", MiscPage, function(v) Settings.Spider = v end)
AddTextBox("Chat Message...", MiscPage, function(text) Settings.ChatMsg = text end)
AddToggle("Auto Chat Spammer", MiscPage, function(v) Settings.ChatSpam = v end)

-- SERVER PAGE (WITH REJOIN)
local HopBtn = Instance.new("TextButton", ServerPage); HopBtn.Size=UDim2.new(1,-10,0,45); HopBtn.BackgroundColor3=Colors.ItemBG; HopBtn.Text="Server Hop"; HopBtn.TextColor3=Colors.Text; HopBtn.Font=Enum.Font.GothamBold; HopBtn.TextSize=17; HopBtn.ZIndex=10; Instance.new("UICorner", HopBtn).CornerRadius=UDim.new(0,10)
HopBtn.MouseButton1Click:Connect(function() local sfUrl="https://games.roblox.com/v1/games/%s/servers/Public?sortOrder=Asc&limit=100"; local req=game:HttpGet(string.format(sfUrl, game.PlaceId)); local body=HttpService:JSONDecode(req); if body and body.data then for i,v in next,body.data do if type(v)=="table" and tonumber(v.playing) and tonumber(v.maxPlayers) and v.playing<v.maxPlayers and v.id~=game.JobId then TeleportService:TeleportToPlaceInstance(game.PlaceId, v.id, LocalPlayer) break end end end end)
-- REJOIN BUTTON
local RejoinBtn = Instance.new("TextButton", ServerPage); RejoinBtn.Size=UDim2.new(1,-10,0,45); RejoinBtn.BackgroundColor3=Colors.ItemBG; RejoinBtn.Text="Rejoin Server"; RejoinBtn.TextColor3=Colors.Text; RejoinBtn.Font=Enum.Font.GothamBold; RejoinBtn.TextSize=17; RejoinBtn.ZIndex=10; Instance.new("UICorner", RejoinBtn).CornerRadius=UDim.new(0,10)
RejoinBtn.MouseButton1Click:Connect(function() TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer) end)

-- STATUS REBUILT
local StatsList = Instance.new("UIListLayout", StatusPage); StatsList.Padding = UDim.new(0, 5)
local function AddStat(text) local L = Instance.new("TextLabel", StatusPage); L.Size = UDim2.new(1, 0, 0, 25); L.Text=text; L.Font=Enum.Font.GothamBold; L.TextSize=15; L.TextColor3=Colors.Text; L.BackgroundTransparency=1; L.TextXAlignment=0; L.ZIndex=10; return L end
local s_Ply = AddStat("Player: " .. LocalPlayer.Name); local s_Reg = AddStat("Region: ..."); local s_SrvTime = AddStat("Server Time: 00:00:00"); local s_Date = AddStat("Date: 00/00/00"); local s_Time = AddStat("Time: 00:00:00"); local s_Exec = AddStat("Executor: ..."); local s_HP = AddStat("Health: 100/100"); local s_Pos = AddStat("Pos: 0, 0, 0")
task.spawn(function() pcall(function() local data = HttpService:JSONDecode(game:HttpGet("http://ip-api.com/json/")); s_Reg.Text = "Region: " .. (data.countryCode or "Unknown") .. " - " .. (data.regionName or "Unknown") end); s_Exec.Text = "Executor: " .. (identifyexecutor and identifyexecutor() or "Unknown") end)

-- LOGIC LOOPS
Mouse.Button1Down:Connect(function() if Settings.ClickTP and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(Mouse.Hit.p + Vector3.new(0, 3, 0)) end end)

local SpamDelay = 0
RunService.RenderStepped:Connect(function(deltaTime)
    local Char = LocalPlayer.Character
    if Char and Char:FindFirstChild("Humanoid") and Char:FindFirstChild("HumanoidRootPart") then
        local Root = Char.HumanoidRootPart
        -- Speed/Jump Logic
        if Settings.Speed then Char.Humanoid.WalkSpeed = Settings.SpeedVal end
        if Settings.HighJump then Char.Humanoid.JumpPower = Settings.HighJumpVal; Char.Humanoid.UseJumpPower = true end
        if Settings.SpinBot then Root.CFrame = Root.CFrame * CFrame.Angles(0, math.rad(30), 0) end
        if Settings.BHop and Char.Humanoid.FloorMaterial ~= Enum.Material.Air then Char.Humanoid.Jump = true end
        
        -- Misc: Spider
        if Settings.Spider then
            local ray = Ray.new(Root.Position, Root.CFrame.LookVector * 2)
            local hit, pos = workspace:FindPartOnRay(ray, Char)
            if hit then Root.Velocity = Vector3.new(Root.Velocity.X, 40, Root.Velocity.Z) end
        end
    end

    -- Misc: Chat Spam
    if Settings.ChatSpam then
        SpamDelay = SpamDelay + deltaTime
        if SpamDelay >= 2 then
            SpamDelay = 0
            pcall(function()
                game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest:FireServer(Settings.ChatMsg, "All")
            end)
        end
    end

    if Settings.NoFall and Char and Char:FindFirstChild("Humanoid") then Char.Humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false); Char.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false) end
    if Settings.Fullbright then Lighting.Brightness=2; Lighting.ClockTime=14; Lighting.GlobalShadows=false end
    if Settings.Hitbox then for _, p in pairs(Players:GetPlayers()) do if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then p.Character.HumanoidRootPart.Size = Vector3.new(20,20,20); p.Character.HumanoidRootPart.Transparency=0.5; p.Character.HumanoidRootPart.CanCollide=false end end end
    if Settings.CamLock then local target=nil; local maxDist=math.huge; for _,p in pairs(Players:GetPlayers()) do if p~=LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then local dist=(LocalPlayer.Character.HumanoidRootPart.Position-p.Character.HumanoidRootPart.Position).Magnitude; if dist<maxDist then maxDist=dist; target=p end end end; Settings.Target=target; if Settings.Target and Settings.Target.Character then Camera.CFrame=CFrame.new(Camera.CFrame.Position, Settings.Target.Character.HumanoidRootPart.Position) end end
    
    if StatusGroup.Visible then
        local diff = tick() - JoinTime; local h = math.floor(diff/3600); local m = math.floor((diff%3600)/60); local s = math.floor(diff%60)
        s_SrvTime.Text = string.format("Server Time: %02d:%02d:%02d", h, m, s); s_Time.Text = "Time: " .. os.date("%H:%M:%S"); s_Date.Text = "Date: " .. os.date("%d/%m/%Y")
        if Char and Char:FindFirstChild("Humanoid") then s_HP.Text = "Health: " .. math.floor(Char.Humanoid.Health) .. "/" .. math.floor(Char.Humanoid.MaxHealth) end
        if Char and Char:FindFirstChild("HumanoidRootPart") then local pos=Char.HumanoidRootPart.Position; s_Pos.Text=string.format("Pos: %d, %d, %d", pos.X, pos.Y, pos.Z) end
    end
end)

RunService.RenderStepped:Connect(function()
    if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local root = LocalPlayer.Character.HumanoidRootPart; local hum = LocalPlayer.Character.Humanoid
        if not root:FindFirstChild("LuxVelo") then local bv=Instance.new("BodyVelocity", root); bv.Name="LuxVelo"; bv.MaxForce=Vector3.new(math.huge,math.huge,math.huge); local bg=Instance.new("BodyGyro", root); bg.Name="LuxGyro"; bg.MaxTorque=Vector3.new(9e9,9e9,9e9); bg.P=10000; bg.D=100 end
        hum.PlatformStand=true; local bg=root.LuxGyro; local bv=root.LuxVelo; bg.CFrame=Camera.CFrame
        local moveDir=hum.MoveDirection
        if moveDir.Magnitude>0.1 then local look=Camera.CFrame.LookVector; local flat=Vector3.new(look.X,0,look.Z).Unit; local dot=flat:Dot(moveDir); if dot>0.5 then bv.Velocity=look*Settings.SpeedVal elseif dot<-0.5 then bv.Velocity=-look*Settings.SpeedVal else bv.Velocity=moveDir*Settings.SpeedVal end else bv.Velocity=Vector3.new(0,0,0); root.AssemblyLinearVelocity=Vector3.new(0,0,0) end
    else if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then LocalPlayer.Character.Humanoid.PlatformStand=false; if LocalPlayer.Character.HumanoidRootPart:FindFirstChild("LuxVelo") then LocalPlayer.Character.HumanoidRootPart.LuxVelo:Destroy() end; if LocalPlayer.Character.HumanoidRootPart:FindFirstChild("LuxGyro") then LocalPlayer.Character.HumanoidRootPart.LuxGyro:Destroy() end end end
end)

local function PlayIntro()
    local IntroLabel = Instance.new("TextLabel", ScreenGui); IntroLabel.Text="LX"; IntroLabel.Font=Enum.Font.GothamBlack; IntroLabel.TextSize=120; IntroLabel.TextColor3=Color3.new(1,1,1); IntroLabel.Size=UDim2.new(0,300,0,150); IntroLabel.Position=UDim2.new(0.5,-150,0.5,-75); IntroLabel.TextTransparency=1; IntroLabel.BackgroundTransparency=1
    
    -- EFEITO ROXO (GLOW VIGNETTE)
    local IntroGlow = Instance.new("ImageLabel", ScreenGui); IntroGlow.Name="IntroGlow"; IntroGlow.Size=UDim2.new(1,0,1,0); IntroGlow.BackgroundTransparency=1; IntroGlow.Image="rbxassetid://4576475446"; IntroGlow.ImageColor3=Color3.fromRGB(120,0,255); IntroGlow.ImageTransparency=0; IntroGlow.ZIndex=100

    TweenService:Create(IntroLabel, TweenInfo.new(1.5), {TextTransparency=0}):Play(); TweenService:Create(GlobalBlur, TweenInfo.new(1.5), {Size=56}):Play(); task.wait(2)
    TweenService:Create(IntroLabel, TweenInfo.new(0.8), {TextTransparency=1}):Play(); TweenService:Create(IntroGlow, TweenInfo.new(0.8), {ImageTransparency=1}):Play(); task.wait(0.8); IntroLabel:Destroy(); IntroGlow:Destroy(); OpenBtn.Visible=true; ToggleMenu()
end
PlayIntro()
