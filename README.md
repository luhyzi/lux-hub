local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()
local VirtualUser = game:GetService("VirtualUser")

-- Settings & Data
local Settings = {
    ESP = false, NameTags = false, Tracers = false, Fullbright = false, FOV = 70, Hitbox = false, NoFog = false, Crosshair = false, CamLock = false,
    Noclip = false, Speed = false, SpeedVal = 100, Fly = false, Invisible = false, InfiniteJump = false, HighJump = false, HighJumpVal = 50,
    ClickTP = false, SpinBot = false, AntiAFK = false, BHop = false, NoFall = false, Spider = false,
    AutoClick = false, -- NOVO
    ChatSpam = false, ChatMsg = "Lux Hub on Top!",
    
    -- System Configs
    ShowNotifs = true,
    FPSBoost = false,
    AutoExecute = false,
    Translate = false
}

-- Target Variables
local SelectedPlayer = nil
local Spectating = false

-- Dicionário de Tradução
local Translations = {
    -- Tabs
    ["Main"] = "Principal",
    ["Player"] = "Jogador",
    ["Misc"] = "Outros",
    ["Status"] = "Status",
    ["Server"] = "Servidor",
    ["Settings"] = "Ajustes",
    
    -- Main
    ["ESP"] = "ESP Universal",
    ["NameTags"] = "Nomes/Vida", -- NOVO
    ["Tracers"] = "Linhas (Tracers)", -- NOVO
    ["Fullbright"] = "Luz Infinita",
    ["FOV"] = "Campo de Visão",
    ["No Fog"] = "Sem Neblina",
    ["Cam Lock"] = "Travar Câmera",
    ["Hitbox"] = "Hitbox Expandida",
    ["Crosshair"] = "Mira",
    
    -- Player
    ["Speed"] = "Velocidade",
    ["On Speed"] = "Ativar Velocidade",
    ["Jump"] = "Pulo",
    ["On Jump"] = "Ativar Pulo",
    ["Fly"] = "Voar",
    ["Noclip"] = "Atravessar Paredes",
    ["Inf Jump"] = "Pulo Infinito",
    ["No Fall"] = "Sem Dano Queda",
    ["SpinBot"] = "Girar (Spin)",
    ["Click TP"] = "TP ao Clicar",
    ["Spider"] = "Homem Aranha",
    ["Auto Clicker"] = "Auto Clicker", -- NOVO
    ["Target Name..."] = "Nome do Jogador...", -- NOVO
    ["Teleport to"] = "Teleportar", -- NOVO
    ["Spectate"] = "Assistir (Spectate)", -- NOVO
    ["Stop Spec"] = "Parar de Assistir", -- NOVO
    
    -- Misc
    ["Inf Yield"] = "Infinite Yield",
    ["Dex Files"] = "Dex Explorer",
    ["Chat Msg..."] = "Msg Chat...",
    ["Spam Chat"] = "Spam Chat",
    
    -- Settings
    ["Show Notifs"] = "Notificações",
    ["FPS Boost"] = "Aumentar FPS",
    ["Anti-AFK"] = "Anti-AFK",
    ["Auto Exec"] = "Auto Executar",
    ["Translate PT-BR"] = "Traduzir PT-BR",
    ["Unload Script"] = "Fechar Script",
    
    -- Server
    ["Rejoin"] = "Reentrar",
    ["Server Hop"] = "Trocar Server"
}

-- Armazena referências dos textos para tradução em tempo real
local TextObjects = {} 

local Colors = {
	MainBG = Color3.fromRGB(15, 15, 15),
	ItemBG = Color3.fromRGB(30, 30, 30),
	Text = Color3.fromRGB(240, 240, 240),
	
    ToggleOff = Color3.fromRGB(70, 70, 70),
    ToggleOn = Color3.fromRGB(0, 100, 40),
    Knob = Color3.fromRGB(230, 230, 230),
    
    TabUnselected = Color3.fromRGB(35, 35, 35),
    TextDim = Color3.fromRGB(150, 150, 150),
    TabSelected = Color3.fromRGB(255, 255, 255),
    TextSelected = Color3.fromRGB(0, 0, 0),
	
    CloseBtn = Color3.fromRGB(40, 40, 40)
}

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_v2.9_Ultimate"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true 
if gethui then ScreenGui.Parent = gethui() elseif game:GetService("CoreGui") then ScreenGui.Parent = game:GetService("CoreGui") else ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui") end

-- SISTEMA DE NOTIFICAÇÃO
local NotifContainer = Instance.new("Frame", ScreenGui)
NotifContainer.Name = "Notifications"
NotifContainer.Size = UDim2.new(0, 300, 1, -20)
NotifContainer.Position = UDim2.new(1, -310, 0, 10)
NotifContainer.BackgroundTransparency = 1
NotifContainer.ZIndex = 100

local NotifList = Instance.new("UIListLayout", NotifContainer)
NotifList.Padding = UDim.new(0, 5)
NotifList.VerticalAlignment = Enum.VerticalAlignment.Bottom
NotifList.HorizontalAlignment = Enum.HorizontalAlignment.Right

local function SendNotification(text)
    if not Settings.ShowNotifs then return end
    
    local N = Instance.new("Frame", NotifContainer)
    N.Size = UDim2.new(0, 0, 0, 30)
    N.BackgroundColor3 = Colors.ItemBG
    N.BorderSizePixel = 0
    N.ClipsDescendants = true
    Instance.new("UICorner", N).CornerRadius = UDim.new(0, 6)
    
    local L = Instance.new("TextLabel", N)
    L.Text = text
    L.Font = Enum.Font.GothamBold
    L.TextSize = 14
    L.TextColor3 = Colors.Text
    L.Size = UDim2.new(1, 0, 1, 0)
    L.BackgroundTransparency = 1
    
    TweenService:Create(N, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0, 200, 0, 30)}):Play()
    
    task.spawn(function()
        task.wait(2.5)
        TweenService:Create(L, TweenInfo.new(0.3), {TextTransparency = 1}):Play()
        TweenService:Create(N, TweenInfo.new(0.3), {Size = UDim2.new(0, 0, 0, 30)}):Play()
        task.wait(0.3)
        N:Destroy()
    end)
end

-- FUNÇÃO DE TRADUÇÃO
local function UpdateLanguage()
    for obj, originalText in pairs(TextObjects) do
        if Settings.Translate then
            if Translations[originalText] then
                obj.Text = Translations[originalText]
            end
        else
            obj.Text = originalText
        end
    end
end

-- GET PLAYER FUNCTION (Para TP e Spectate)
local function GetPlayerFromShortName(str)
    if not str then return nil end
    str = string.lower(str)
    for _, p in pairs(Players:GetPlayers()) do
        if string.sub(string.lower(p.Name), 1, #str) == str or string.sub(string.lower(p.DisplayName), 1, #str) == str then
            return p
        end
    end
    return nil
end

-- DRAG LOGIC
local LastPosition = UDim2.new(0.5, -250, 0.5, -165)
local function MakeDraggable(trigger, objectToMove)
	objectToMove = objectToMove or trigger
	local dragging, dragInput, dragStart, startPos
	trigger.InputBegan:Connect(function(input) 
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then 
			dragging = true; dragStart = input.Position; startPos = objectToMove.Position; input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end) 
		end 
	end)
	trigger.InputChanged:Connect(function(input) 
		if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end 
	end)
	UserInputService.InputChanged:Connect(function(input) 
		if input == dragInput and dragging then 
			local delta = input.Position - dragStart
			objectToMove.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
			if objectToMove.Name == "MainFrame" then LastPosition = objectToMove.Position end
		end 
	end)
end

-- BLUR
local GlobalBlur = Instance.new("BlurEffect", Lighting); GlobalBlur.Size = 0

-- OPEN BUTTON
local OpenBtn = Instance.new("TextButton", ScreenGui)
OpenBtn.Size = UDim2.new(0, 50, 0, 50)
OpenBtn.Position = UDim2.new(0, 10, 0.2, 0)
OpenBtn.BackgroundColor3 = Colors.MainBG
OpenBtn.Text = "LX"
OpenBtn.Font = Enum.Font.GothamBlack
OpenBtn.TextSize = 24
OpenBtn.TextColor3 = Color3.new(1,1,1)
OpenBtn.Visible = false 
OpenBtn.ZIndex = 50
Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1,0)
MakeDraggable(OpenBtn)

-- MAIN FRAME
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 500, 0, 330)
MainFrame.Position = UDim2.new(0.5, -250, 1.5, 0)
MainFrame.BackgroundColor3 = Colors.MainBG
MainFrame.Visible = false
MainFrame.ZIndex = 5
MainFrame.ClipsDescendants = true 
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 24)

-- TOGGLE MENU
local isOpen = false
local function ToggleMenu()
    if isOpen then
        TweenService:Create(GlobalBlur, TweenInfo.new(0.5), {Size=0}):Play()
        MainFrame:TweenPosition(UDim2.new(LastPosition.X.Scale, LastPosition.X.Offset, 1.5, 0), Enum.EasingDirection.In, Enum.EasingStyle.Quart, 0.5, true, function() MainFrame.Visible = false end)
        isOpen = false
    else
        MainFrame.Visible = true
        MainFrame.Position = UDim2.new(LastPosition.X.Scale, LastPosition.X.Offset, 1.5, 0)
        TweenService:Create(GlobalBlur, TweenInfo.new(0.5), {Size=24}):Play()
        MainFrame:TweenPosition(LastPosition, Enum.EasingDirection.Out, Enum.EasingStyle.Quart, 0.5, true)
        isOpen = true
    end
end
OpenBtn.MouseButton1Click:Connect(ToggleMenu)

-- HEADER
local Header = Instance.new("Frame", MainFrame)
Header.Size = UDim2.new(1, 0, 0, 50)
Header.BackgroundTransparency = 1 
Header.ZIndex = 6
MakeDraggable(Header, MainFrame)

local Title = Instance.new("TextLabel", Header)
Title.Text = "Lux Hub"
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 22
Title.TextColor3 = Colors.Text
Title.Size = UDim2.new(0, 100, 1, 0)
Title.Position = UDim2.new(0, 20, 0, 0)
Title.BackgroundTransparency = 1
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 7

-- VERSÃO (v2.9)
local VerLabel = Instance.new("TextLabel", Header)
VerLabel.Text = "v2.9"
VerLabel.Font = Enum.Font.GothamBold
VerLabel.TextSize = 12
VerLabel.TextColor3 = Colors.TextDim
VerLabel.Size = UDim2.new(0, 50, 1, 0)
VerLabel.Position = UDim2.new(0, 110, 0, 2)
VerLabel.BackgroundTransparency = 1
VerLabel.TextXAlignment = Enum.TextXAlignment.Left
VerLabel.ZIndex = 7

local CloseBtn = Instance.new("TextButton", Header)
CloseBtn.Size = UDim2.new(0, 32, 0, 32)
CloseBtn.Position = UDim2.new(1, -45, 0.5, -16)
CloseBtn.BackgroundColor3 = Colors.CloseBtn
CloseBtn.Text = "X"
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 18
CloseBtn.TextColor3 = Color3.new(1,1,1)
CloseBtn.ZIndex = 7
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(1, 0)
CloseBtn.MouseButton1Click:Connect(function() ToggleMenu() end)

-- NAVBAR (SCROLL HORIZONTAL)
local NavBar = Instance.new("Frame", MainFrame)
NavBar.Size = UDim2.new(1, 0, 0, 55)
NavBar.Position = UDim2.new(0, 0, 1, -55)
NavBar.BackgroundTransparency = 1 
NavBar.ZIndex = 6

local TabScroller = Instance.new("ScrollingFrame", NavBar)
TabScroller.Size = UDim2.new(1, -20, 1, 0)
TabScroller.Position = UDim2.new(0, 10, 0, 0)
TabScroller.BackgroundTransparency = 1
TabScroller.ScrollBarThickness = 2 
TabScroller.ScrollBarImageColor3 = Colors.Knob
TabScroller.CanvasSize = UDim2.new(0, 0, 0, 0)
TabScroller.AutomaticCanvasSize = Enum.AutomaticSize.X 
TabScroller.ZIndex = 7
TabScroller.ScrollingDirection = Enum.ScrollingDirection.X 

local TabListLayout = Instance.new("UIListLayout", TabScroller)
TabListLayout.FillDirection = Enum.FillDirection.Horizontal
TabListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left 
TabListLayout.VerticalAlignment = Enum.VerticalAlignment.Center
TabListLayout.Padding = UDim.new(0, 8)

-- CONTENT AREA
local ContentFrame = Instance.new("Frame", MainFrame)
ContentFrame.Size = UDim2.new(1, -20, 1, -110)
ContentFrame.Position = UDim2.new(0, 10, 0, 55)
ContentFrame.BackgroundTransparency = 1
ContentFrame.ZIndex = 6

-- PAGES
local function CreatePage(useList)
    local P = Instance.new("ScrollingFrame", ContentFrame)
    P.Size = UDim2.new(1, 0, 1, 0)
    P.BackgroundTransparency = 1
    P.ScrollBarThickness = 2
    P.Visible = false
    P.AutomaticCanvasSize = Enum.AutomaticSize.Y
    P.CanvasSize = UDim2.new(0,0,0,0)
    P.ZIndex = 6
    
    local Layout
    if useList then
        Layout = Instance.new("UIListLayout", P)
        Layout.Padding = UDim.new(0, 8)
    else
        Layout = Instance.new("UIGridLayout", P)
        Layout.CellSize = UDim2.new(0.48, 0, 0, 42)
        Layout.CellPadding = UDim2.new(0.02, 0, 0, 10)
        Layout.SortOrder = Enum.SortOrder.LayoutOrder
        Layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    end
    
    local Pad = Instance.new("UIPadding", P); Pad.PaddingTop = UDim.new(0, 5); Pad.PaddingBottom = UDim.new(0, 5)
    return P
end

local MainPage = CreatePage(false)
local PlayerPage = CreatePage(false)
local MiscPage = CreatePage(false)
local StatusPage = CreatePage(false)
local ServerPage = CreatePage(false)
local SettingsPage = CreatePage(false)

MainPage.Visible = true

-- TABS LOGIC
local Tabs = {}
local CurrentPage = MainPage

local function SwitchTo(btn, page)
    if CurrentPage == page then return end
    for _, b in pairs(Tabs) do 
        TweenService:Create(b, TweenInfo.new(0.2), {BackgroundColor3 = Colors.TabUnselected, TextColor3 = Colors.TextDim}):Play()
    end
    TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = Colors.TabSelected, TextColor3 = Colors.TextSelected}):Play()
    CurrentPage.Visible = false; page.Visible = true; CurrentPage = page
end

local function AddTab(name, page)
    local Btn = Instance.new("TextButton", TabScroller)
    Btn.Size = UDim2.new(0, 75, 0, 32)
    Btn.BackgroundColor3 = Colors.TabUnselected 
    Btn.Text = name
    Btn.Font = Enum.Font.GothamBold
    Btn.TextSize = 13
    Btn.TextColor3 = Colors.TextDim
    Btn.ZIndex = 8
    Btn.ClipsDescendants = true 
    Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 10)
    
    Btn.MouseButton1Click:Connect(function() SwitchTo(Btn, page) end)
    table.insert(Tabs, Btn)
    
    -- Registrar para tradução
    TextObjects[Btn] = name
    
    return Btn
end

local MainTab = AddTab("Main", MainPage)
local PlayerTab = AddTab("Player", PlayerPage)
local MiscTab = AddTab("Misc", MiscPage)
local StatusTab = AddTab("Status", StatusPage)
local ServerTab = AddTab("Server", ServerPage)
local SettingsTab = AddTab("Settings", SettingsPage)

MainTab.BackgroundColor3 = Colors.TabSelected
MainTab.TextColor3 = Colors.TextSelected

-- UI COMPONENTS
local function AddToggle(text, parent, callback)
	local F = Instance.new("Frame", parent)
    F.BackgroundColor3 = Colors.ItemBG
    F.ZIndex = 6
    Instance.new("UICorner", F).CornerRadius = UDim.new(0, 10)
    
	local L = Instance.new("TextLabel", F)
    L.Text = text; L.Font = Enum.Font.GothamBold; L.TextSize = 14; L.TextColor3 = Colors.Text; L.TextXAlignment = Enum.TextXAlignment.Left; L.Position = UDim2.new(0, 12, 0, 0); L.Size = UDim2.new(0.65, 0, 1, 0); L.BackgroundTransparency = 1; L.ZIndex = 7
    TextObjects[L] = text -- Registrar Tradução

    local SwitchBG = Instance.new("TextButton", F)
    SwitchBG.Text = ""; SwitchBG.Size = UDim2.new(0, 42, 0, 22); SwitchBG.Position = UDim2.new(1, -52, 0.5, -11); SwitchBG.BackgroundColor3 = Colors.ToggleOff; SwitchBG.ZIndex = 7; Instance.new("UICorner", SwitchBG).CornerRadius = UDim.new(1, 0)
    
    local Knob = Instance.new("Frame", SwitchBG)
    Knob.Size = UDim2.new(0, 18, 0, 18); Knob.Position = UDim2.new(0, 2, 0.5, -9); Knob.BackgroundColor3 = Colors.Knob; Knob.ZIndex = 8; Instance.new("UICorner", Knob).CornerRadius = UDim.new(1, 0)
    
	local enabled = false
	SwitchBG.MouseButton1Click:Connect(function() 
        enabled = not enabled
        if enabled then
            TweenService:Create(SwitchBG, TweenInfo.new(0.2), {BackgroundColor3 = Colors.ToggleOn}):Play()
            TweenService:Create(Knob, TweenInfo.new(0.2), {Position = UDim2.new(1, -20, 0.5, -9)}):Play()
            SendNotification(L.Text .. " [ON]")
        else
            TweenService:Create(SwitchBG, TweenInfo.new(0.2), {BackgroundColor3 = Colors.ToggleOff}):Play()
            TweenService:Create(Knob, TweenInfo.new(0.2), {Position = UDim2.new(0, 2, 0.5, -9)}):Play()
            SendNotification(L.Text .. " [OFF]")
        end
        callback(enabled) 
    end)
end

local function AddSlider(text, parent, min, max, callback, default)
    local current=default or min
    local F = Instance.new("Frame", parent); F.BackgroundColor3=Colors.ItemBG; F.ZIndex=6; Instance.new("UICorner", F).CornerRadius=UDim.new(0,10)
    
    local L = Instance.new("TextLabel", F); L.Text=text; L.Font=Enum.Font.GothamBold; L.TextSize=14; L.TextColor3=Colors.Text; L.TextXAlignment=0; L.Position=UDim2.new(0,10,0,0); L.Size=UDim2.new(0.35,0,1,0); L.BackgroundTransparency=1; L.ZIndex=7
    TextObjects[L] = text -- Registrar Tradução
    
    local M = Instance.new("TextButton", F); M.Text="-"; M.Size=UDim2.new(0,36,0,36); M.Position=UDim2.new(0.40,0,0.5,-18); M.BackgroundColor3=Colors.MainBG; M.TextColor3=Colors.Text; M.ZIndex=7; Instance.new("UICorner", M).CornerRadius=UDim.new(1,0)
    
    local V = Instance.new("TextBox", F); V.Text=tostring(current); V.Size=UDim2.new(0,40,1,0); V.Position=UDim2.new(0.58,0,0,0); V.BackgroundTransparency=1; V.TextColor3=Colors.Text; V.Font=Enum.Font.GothamBold; V.ZIndex=7; V.ClearTextOnFocus = false
    
    local P = Instance.new("TextButton", F); P.Text="+"; P.Size=UDim2.new(0,36,0,36); P.Position=UDim2.new(0.80,0,0.5,-18); P.BackgroundColor3=Colors.MainBG; P.TextColor3=Colors.Text; P.ZIndex=7; Instance.new("UICorner", P).CornerRadius=UDim.new(1,0)
    
    local function Update() current = math.clamp(current, min, max); V.Text = tostring(current); callback(current) end
    M.MouseButton1Click:Connect(function() current=current-10; if current < min then current = min end; Update() end)
    P.MouseButton1Click:Connect(function() current=current+10; if current > max then current = max end; Update() end)
    V.FocusLost:Connect(function(enter) local num = tonumber(V.Text); if num then current = num; Update() else V.Text = tostring(current) end end)
end

local function AddButton(text, parent, callback)
    local B = Instance.new("TextButton", parent); B.BackgroundColor3=Colors.ItemBG; B.Text=text; B.TextColor3=Colors.Text; B.Font=Enum.Font.GothamBold; B.TextSize=14; B.ZIndex=6; Instance.new("UICorner", B).CornerRadius=UDim.new(0,10)
    B.MouseButton1Click:Connect(callback)
    TextObjects[B] = text -- Registrar Tradução
end

local function AddTextBox(placeholder, parent, callback)
    local F = Instance.new("Frame", parent); F.BackgroundColor3=Colors.ItemBG; F.ZIndex=6; Instance.new("UICorner", F).CornerRadius=UDim.new(0,10)
    local T = Instance.new("TextBox", F); T.Size=UDim2.new(1,-10,1,0); T.Position=UDim2.new(0,10,0,0); T.BackgroundTransparency=1; T.Text=""; T.PlaceholderText=placeholder; T.TextColor3=Colors.Text; T.PlaceholderColor3=Color3.fromRGB(150,150,150); T.Font=Enum.Font.GothamBold; T.TextSize=14; T.ZIndex=7
    T.FocusLost:Connect(function(enter) if enter then callback(T.Text) end end)
    TextObjects[T] = placeholder -- Registrar Tradução (Placeholder)
end

-- --- FUNÇÕES ---

-- MAIN
AddToggle("ESP", MainPage, function(v) Settings.ESP = v end)
AddToggle("NameTags", MainPage, function(v) Settings.NameTags = v end) -- NOVO
AddToggle("Tracers", MainPage, function(v) Settings.Tracers = v end) -- NOVO
AddToggle("Fullbright", MainPage, function(v) Settings.Fullbright = v end)
AddSlider("FOV", MainPage, 70, 120, function(v) Camera.FieldOfView = v end, 70)
AddToggle("No Fog", MainPage, function(v) if v then Lighting.FogEnd=9e9 else Lighting.FogEnd=1000 end end)
AddToggle("Cam Lock", MainPage, function(v) Settings.CamLock = v end)
AddToggle("Hitbox", MainPage, function(v) Settings.Hitbox = v end)
AddToggle("Crosshair", MainPage, function(v) Settings.Crosshair=v; ScreenGui.LuxCrosshair.Visible=v end)

-- PLAYER
AddToggle("Auto Clicker", PlayerPage, function(v) Settings.AutoClick = v end) -- NOVO
AddSlider("Speed", PlayerPage, 16, 300, function(v) Settings.SpeedVal = v end, 16)
AddToggle("On Speed", PlayerPage, function(v) Settings.Speed = v end)
AddSlider("Jump", PlayerPage, 50, 300, function(v) Settings.HighJumpVal = v end, 50)
AddToggle("On Jump", PlayerPage, function(v) Settings.HighJump = v end)
AddToggle("Fly", PlayerPage, function(v) Settings.Fly = v end)
AddToggle("Noclip", PlayerPage, function(v) Settings.Noclip = v end)
AddToggle("Inf Jump", PlayerPage, function(v) Settings.InfiniteJump = v end)
AddToggle("No Fall", PlayerPage, function(v) Settings.NoFall = v end)
AddToggle("SpinBot", PlayerPage, function(v) Settings.SpinBot = v end)
AddToggle("Click TP", PlayerPage, function(v) Settings.ClickTP = v end)
AddToggle("Spider", PlayerPage, function(v) Settings.Spider = v end)

-- TARGET SYSTEM (NOVO)
AddTextBox("Target Name...", PlayerPage, function(t) 
    local target = GetPlayerFromShortName(t)
    if target then 
        SelectedPlayer = target
        SendNotification("Selected: " .. target.DisplayName)
    else
        SendNotification("Player not found")
        SelectedPlayer = nil
    end
end)

AddButton("Teleport to", PlayerPage, function()
    if SelectedPlayer and SelectedPlayer.Character and SelectedPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = SelectedPlayer.Character.HumanoidRootPart.CFrame
    else
        SendNotification("Target invalid")
    end
end)

AddButton("Spectate", PlayerPage, function()
    if SelectedPlayer and SelectedPlayer.Character and SelectedPlayer.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = SelectedPlayer.Character.Humanoid
        Spectating = true
        SendNotification("Spectating " .. SelectedPlayer.DisplayName)
    end
end)

AddButton("Stop Spec", PlayerPage, function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = LocalPlayer.Character.Humanoid
        Spectating = false
    end
end)


-- MISC
AddButton("Inf Yield", MiscPage, function() loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))() end)
AddButton("Dex Files", MiscPage, function() loadstring(game:HttpGet("https://raw.githubusercontent.com/Babyhamsta/RBLX_Scripts/main/Universal/BypassedDarkDexV3.lua", true))() end)
AddTextBox("Chat Msg...", MiscPage, function(t) Settings.ChatMsg = t end)
AddToggle("Spam Chat", MiscPage, function(v) Settings.ChatSpam = v end)

-- SETTINGS
AddToggle("Show Notifs", SettingsPage, function(v) Settings.ShowNotifs = v end)
AddToggle("FPS Boost", SettingsPage, function(v) 
    Settings.FPSBoost = v
    if v then 
        for _,o in pairs(game:GetDescendants()) do 
            if o:IsA("BasePart") then o.Material=Enum.Material.SmoothPlastic 
            elseif o:IsA("Texture") then o:Destroy() end 
        end 
    end 
end)
AddToggle("Anti-AFK", SettingsPage, function(v) Settings.AntiAFK = v end)

-- AUTO EXECUTE
AddToggle("Auto Exec", SettingsPage, function(v) 
    Settings.AutoExecute = v
    if v then
        if writefile then
            pcall(function() 
                writefile("LuxHub_AutoExec.lua", "loadstring(game:HttpGet('URL_DO_SEU_SCRIPT'))()")
                SendNotification("Auto Exec Saved")
            end)
        else
            SendNotification("Executor not supported")
        end
    else
        if delfile then
            pcall(function() delfile("LuxHub_AutoExec.lua") end)
            SendNotification("Auto Exec Removed")
        end
    end
end)

-- TRADUTOR
AddToggle("Translate PT-BR", SettingsPage, function(v) 
    Settings.Translate = v
    UpdateLanguage()
end)

AddButton("Unload Script", SettingsPage, function() ScreenGui:Destroy() end)

-- SERVER
AddButton("Rejoin", ServerPage, function() TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer) end)
AddButton("Server Hop", ServerPage, function() 
    local sfUrl="https://games.roblox.com/v1/games/%s/servers/Public?sortOrder=Asc&limit=100"
    local req=game:HttpGet(string.format(sfUrl, game.PlaceId))
    local body=HttpService:JSONDecode(req)
    if body and body.data then for i,v in next,body.data do if type(v)=="table" and tonumber(v.playing) and tonumber(v.maxPlayers) and v.playing<v.maxPlayers and v.id~=game.JobId then TeleportService:TeleportToPlaceInstance(game.PlaceId, v.id, LocalPlayer) break end end end 
end)

-- STATUS
local function AddStat(text) 
    local F = Instance.new("Frame", StatusPage); F.BackgroundColor3=Colors.ItemBG; F.ZIndex=6; Instance.new("UICorner", F).CornerRadius=UDim.new(0,10)
    local L = Instance.new("TextLabel", F); L.Size=UDim2.new(1,-10,1,0); L.Position=UDim2.new(0,10,0,0); L.Text=text; L.Font=Enum.Font.GothamBold; L.TextSize=13; L.TextColor3=Colors.Text; L.BackgroundTransparency=1; L.TextXAlignment=0; L.ZIndex=7; 
    return L 
end
local s_Ply = AddStat("User: " .. LocalPlayer.Name)
local s_HP = AddStat("HP: ...")
local s_Reg = AddStat("Region: ...")
local s_FPS = AddStat("FPS: ...")
local s_Ping = AddStat("Ping: ...")
local s_Time = AddStat("Time: ...")
local s_Date = AddStat("Date: ...")
local s_SrvTime = AddStat("S.Time: ...")
local s_Exec = AddStat("Exec: ...")
local s_Pos = AddStat("Pos: ...")

-- DATA UPDATER LOOP
task.spawn(function()
    local execName = "Unknown"
    if identifyexecutor then execName = identifyexecutor() end
    s_Exec.Text = "Exec: " .. execName

    pcall(function()
        local data = HttpService:JSONDecode(game:HttpGet("http://ip-api.com/json/"))
        s_Reg.Text = "Region: " .. (data.countryCode or "N/A")
    end)

    while true do
        if StatusPage.Visible then
            s_Time.Text = "Time: " .. os.date("%H:%M:%S")
            s_Date.Text = "Date: " .. os.date("%d/%m")
            
            local t = workspace.DistributedGameTime
            local h = math.floor(t/3600); local m = math.floor((t%3600)/60); local s = math.floor(t%60)
            s_SrvTime.Text = string.format("S.Time: %02d:%02d:%02d", h, m, s)
            
            local fps = math.floor(workspace:GetRealPhysicsFPS())
            s_FPS.Text = "FPS: " .. fps
            
            local ping = 0
            pcall(function() ping = math.floor(game:GetService("Stats").Network.ServerStatsItem["Data Ping"]:GetValueString():match("%d+")) end)
            s_Ping.Text = "Ping: " .. ping .. "ms"
            
            if LocalPlayer.Character then
                local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
                local root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if hum then s_HP.Text = "HP: " .. math.floor(hum.Health) end
                if root then s_Pos.Text = string.format("%d, %d, %d", root.Position.X, root.Position.Y, root.Position.Z) end
            end
        end
        task.wait(1)
    end
end)

UserInputService.JumpRequest:Connect(function()
    if Settings.InfiniteJump and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- ADVANCED ESP & TRACERS LOOP
local function UpdateVisuals()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") and p.Character:FindFirstChild("Humanoid") then
            -- ESP BOX/HIGHLIGHT
            if Settings.ESP then
                if not p.Character:FindFirstChild("LuxHighlight") then
                    local hl = Instance.new("Highlight", p.Character); hl.Name = "LuxHighlight"; hl.FillColor = Color3.fromRGB(255, 0, 0); hl.OutlineColor = Color3.fromRGB(255, 255, 255); hl.FillTransparency = 0.5
                end
            else
                if p.Character:FindFirstChild("LuxHighlight") then p.Character.LuxHighlight:Destroy() end
            end

            -- NAMETAGS
            local Head = p.Character:FindFirstChild("Head")
            if Head then
                if Settings.NameTags then
                    local dist = math.floor((LocalPlayer.Character.HumanoidRootPart.Position - Head.Position).Magnitude)
                    local hp = math.floor(p.Character.Humanoid.Health)
                    
                    local bg = Head:FindFirstChild("LuxTag")
                    if not bg then
                        bg = Instance.new("BillboardGui", Head)
                        bg.Name = "LuxTag"; bg.Size = UDim2.new(0, 100, 0, 50); bg.StudsOffset = Vector3.new(0, 2, 0); bg.AlwaysOnTop = true
                        local t = Instance.new("TextLabel", bg); t.Size = UDim2.new(1,0,1,0); t.BackgroundTransparency = 1; t.TextColor3 = Color3.new(1,1,1); t.TextStrokeTransparency = 0; t.Font=Enum.Font.Bold; t.TextSize=14
                    end
                    bg.TextLabel.Text = p.DisplayName .. "\n[" .. dist .. "m] [" .. hp .. " HP]"
                    if hp < 30 then bg.TextLabel.TextColor3 = Color3.fromRGB(255,0,0) else bg.TextLabel.TextColor3 = Color3.fromRGB(255,255,255) end
                else
                    if Head:FindFirstChild("LuxTag") then Head.LuxTag:Destroy() end
                end
            end
        end
    end
    
    -- TRACERS (LINES)
    if Settings.Tracers then
        if ScreenGui:FindFirstChild("TracerContainer") then ScreenGui.TracerContainer:ClearAllChildren() else local tc = Instance.new("Frame", ScreenGui); tc.Name="TracerContainer"; tc.Size=UDim2.new(1,0,1,0); tc.BackgroundTransparency=1; tc.ZIndex=1 end
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = p.Character.HumanoidRootPart
                local vec, vis = Camera:WorldToViewportPoint(hrp.Position)
                if vis then
                    local line = Instance.new("Frame", ScreenGui.TracerContainer); line.BackgroundColor3=Color3.new(1,0,0); line.BorderSizePixel=0
                    local start = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                    local target = Vector2.new(vec.X, vec.Y)
                    local length = (target - start).Magnitude
                    line.Size = UDim2.new(0, length, 0, 1)
                    line.Position = UDim2.new(0, start.X, 0, start.Y)
                    line.Rotation = math.deg(math.atan2(target.Y - start.Y, target.X - start.X))
                    line.AnchorPoint = Vector2.new(0, 0.5)
                end
            end
        end
    else
        if ScreenGui:FindFirstChild("TracerContainer") then ScreenGui.TracerContainer:Destroy() end
    end
end
RunService.RenderStepped:Connect(UpdateVisuals)

RunService.Stepped:Connect(function()
    local Char = LocalPlayer.Character
    if Char then
        local Hum = Char:FindFirstChild("Humanoid")
        local Root = Char:FindFirstChild("HumanoidRootPart")
        
        if Settings.Noclip then
            for _, part in pairs(Char:GetDescendants()) do
                if part:IsA("BasePart") and part.CanCollide then part.CanCollide = false end
            end
        end

        if Hum and Root then
            if Settings.Speed then Hum.WalkSpeed = Settings.SpeedVal end
            if Settings.HighJump then Hum.JumpPower = Settings.HighJumpVal; Hum.UseJumpPower = true end
            if Settings.Fullbright then Lighting.Brightness=2; Lighting.ClockTime=14; Lighting.GlobalShadows=false end

            if Settings.Fly and Root then
                local bv = Root:FindFirstChild("LuxFlyVelo") or Instance.new("BodyVelocity", Root)
                bv.Name = "LuxFlyVelo"; bv.MaxForce = Vector3.new(1e5,1e5,1e5)
                local bg = Root:FindFirstChild("LuxFlyGyro") or Instance.new("BodyGyro", Root)
                bg.Name = "LuxFlyGyro"; bg.MaxTorque = Vector3.new(1e5,1e5,1e5); bg.P = 10000; bg.D = 100
                Hum.PlatformStand = true
                bg.CFrame = Camera.CFrame
                local dir = Hum.MoveDirection
                if dir.Magnitude > 0.1 then
                   bv.Velocity = (Camera.CFrame.LookVector * (dir.Z * -1) + Camera.CFrame.RightVector * dir.X) * Settings.SpeedVal
                else
                   bv.Velocity = Vector3.zero
                end
            else
                if Root:FindFirstChild("LuxFlyVelo") then Root.LuxFlyVelo:Destroy() end
                if Root:FindFirstChild("LuxFlyGyro") then Root.LuxFlyGyro:Destroy() end
                if Hum.PlatformStand and Settings.Fly == false then Hum.PlatformStand = false end
            end

            if Settings.CamLock then 
                local target = nil; local maxDist = math.huge
                for _, p in pairs(Players:GetPlayers()) do
                    if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                        local dist = (LocalPlayer.Character.HumanoidRootPart.Position - p.Character.HumanoidRootPart.Position).Magnitude
                        if dist < maxDist then maxDist = dist; target = p end
                    end
                end
                if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                    Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
                    if Root then Hum.AutoRotate = false; local tPos = target.Character.HumanoidRootPart.Position; Root.CFrame = CFrame.lookAt(Root.Position, Vector3.new(tPos.X, Root.Position.Y, tPos.Z)) end
                end
            else
                if not Spectating and Hum then Hum.AutoRotate = true end
            end
        end
    end
    
    if Settings.Hitbox then for _, p in pairs(Players:GetPlayers()) do if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then p.Character.HumanoidRootPart.Size = Vector3.new(20,20,20); p.Character.HumanoidRootPart.Transparency=0.5; p.Character.HumanoidRootPart.CanCollide=false end end end
    
    if Settings.AntiAFK and tick() % 60 < 1 then VirtualUser:Button2Down(Vector2.new(0,0)); VirtualUser:Button2Up(Vector2.new(0,0)) end
    
    -- AUTO CLICKER LOGIC
    if Settings.AutoClick then
        VirtualUser:Button1Down(Vector2.new(0,0))
        VirtualUser:Button1Up(Vector2.new(0,0))
    end
end)

RunService.RenderStepped:Connect(function()
    if Settings.ChatSpam and tick() % 2 < 0.1 then pcall(function() game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest:FireServer(Settings.ChatMsg, "All") end) end
    if Settings.Spider and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local Root = LocalPlayer.Character.HumanoidRootPart; local ray = Ray.new(Root.Position, Root.CFrame.LookVector * 2); local hit, _ = workspace:FindPartOnRay(ray, LocalPlayer.Character); if hit then Root.Velocity = Vector3.new(Root.Velocity.X, 40, Root.Velocity.Z) end
    end
end)

Mouse.Button1Down:Connect(function() if Settings.ClickTP and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(Mouse.Hit.p + Vector3.new(0, 3, 0)) end end)

local function PlayIntro()
    local IntroLabel = Instance.new("TextLabel", ScreenGui); IntroLabel.Text="LX"; IntroLabel.Font=Enum.Font.GothamBlack; IntroLabel.TextSize=120; IntroLabel.TextColor3=Color3.new(1,1,1); IntroLabel.Size=UDim2.new(1,0,1,0); IntroLabel.Position=UDim2.new(0,0,0,0); IntroLabel.BackgroundTransparency=1; IntroLabel.ZIndex=100; IntroLabel.TextTransparency=1
    local IntroGlow = Instance.new("ImageLabel", ScreenGui); IntroGlow.Name="IntroGlow"; IntroGlow.Size=UDim2.new(1,0,1,0); IntroGlow.BackgroundTransparency=1; IntroGlow.Image="rbxassetid://4576475446"; IntroGlow.ImageColor3=Color3.fromRGB(120,0,255); IntroGlow.ImageTransparency=1; IntroGlow.ZIndex=99
    TweenService:Create(GlobalBlur, TweenInfo.new(1), {Size=24}):Play(); TweenService:Create(IntroLabel, TweenInfo.new(1), {TextTransparency=0}):Play(); TweenService:Create(IntroGlow, TweenInfo.new(1), {ImageTransparency=0}):Play(); task.wait(2)
    TweenService:Create(IntroLabel, TweenInfo.new(0.5), {TextTransparency=1}):Play(); TweenService:Create(IntroGlow, TweenInfo.new(0.5), {ImageTransparency=1}):Play(); task.wait(0.5); TweenService:Create(GlobalBlur, TweenInfo.new(0.5), {Size=0}):Play(); IntroLabel:Destroy(); IntroGlow:Destroy(); OpenBtn.Visible=true; ToggleMenu()
end
PlayIntro()
