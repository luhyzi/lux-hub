local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- CONFIGURAÇÕES
local Settings = { ESP = false, SpeedActive = false, SpeedValue = 16 }

-- --- INTERFACE PRINCIPAL ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_V13_Restored"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 520, 0, 320)
Background.Position = UDim2.new(0.5, -260, 0.5, -160)
Background.BackgroundColor3 = Color3.fromRGB(45, 45, 45) -- Cinza original
Background.Visible = false
Background.Parent = ScreenGui
Instance.new("UICorner", Background).CornerRadius = UDim.new(0, 20)

-- SISTEMA DE ARRASTAR (PELO FUNDO)
local dragging, dragStart, startPos
Background.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Background.Position
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        Background.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
end)

-- SIDEBAR (LUX HUB)
local SideBar = Instance.new("Frame")
SideBar.Size = UDim2.new(0, 140, 0, 280); SideBar.Position = UDim2.new(0, 15, 0, 20)
SideBar.BackgroundColor3 = Color3.fromRGB(35, 35, 35); SideBar.Parent = Background
Instance.new("UICorner", SideBar).CornerRadius = UDim.new(0, 15)

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"; Title.Font = "GothamBold"; Title.TextSize = 22; Title.TextColor3 = Color3.new(1,1,1)
Title.Size = UDim2.new(1, 0, 0, 50); Title.BackgroundTransparency = 1; Title.Parent = SideBar

-- CONTEÚDO (PAINEL DAS FUNÇÕES)
local MainContent = Instance.new("Frame")
MainContent.Size = UDim2.new(0, 335, 0, 280); MainContent.Position = UDim2.new(0, 170, 0, 20)
MainContent.BackgroundColor3 = Color3.fromRGB(35, 35, 35); MainContent.Parent = Background
Instance.new("UICorner", MainContent).CornerRadius = UDim.new(0, 15)

-- VERSÃO (v 1.3 luiz)
local Credits = Instance.new("TextLabel")
Credits.Text = "v 1.3 luiz"; Credits.Font = "GothamBold"; Credits.TextSize = 13
Credits.TextColor3 = Color3.fromRGB(180, 180, 180); Credits.Position = UDim2.new(1, -95, 0, 15)
Credits.Size = UDim2.new(0, 85, 0, 20); Credits.BackgroundTransparency = 1; Credits.Parent = MainContent

-- BOTÃO DE ABRIR (LOGO BRILHOSA)
local OpenLogo = Instance.new("ImageButton")
OpenLogo.Image = "rbxassetid://139243074283722"
OpenLogo.Size = UDim2.new(0, 60, 0, 60); OpenLogo.Position = UDim2.new(0, 20, 0.5, -30)
OpenLogo.BackgroundColor3 = Color3.fromRGB(30, 30, 30); OpenLogo.ZIndex = 10; OpenLogo.Parent = ScreenGui
Instance.new("UICorner", OpenLogo).CornerRadius = UDim.new(1, 0)

local Glow = Instance.new("ImageLabel")
Glow.Image = "rbxassetid://50288246"; Glow.ImageColor3 = Color3.new(1,1,1)
Glow.Size = UDim2.new(1.5, 0, 1.5, 0); Glow.Position = UDim2.new(-0.25, 0, -0.25, 0)
Glow.BackgroundTransparency = 1; Glow.Parent = OpenLogo
TweenService:Create(Glow, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {ImageTransparency = 0.8}):Play()

OpenLogo.MouseButton1Click:Connect(function()
    Background.Visible = not Background.Visible
end)

-- FUNÇÃO TOGGLE (ALAVANCA QUE DESLIZA)
local function AddToggle(name, pos, callback)
    local TFrame = Instance.new("Frame")
    TFrame.Size = UDim2.new(0.9, 0, 0, 45); TFrame.Position = pos
    TFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25); TFrame.Parent = MainContent
    Instance.new("UICorner", TFrame).CornerRadius = UDim.new(0, 10)
    
    local Lbl = Instance.new("TextLabel")
    Lbl.Text = name; Lbl.Font = "Gotham"; Lbl.TextColor3 = Color3.new(1,1,1)
    Lbl.Position = UDim2.new(0, 15, 0, 0); Lbl.Size = UDim2.new(0.5, 0, 1, 0)
    Lbl.BackgroundTransparency = 1; Lbl.TextXAlignment = "Left"; Lbl.Parent = TFrame

    local Btn = Instance.new("TextButton")
    Btn.Size = UDim2.new(0, 45, 0, 22); Btn.Position = UDim2.new(1, -60, 0.5, -11)
    Btn.BackgroundColor3 = Color3.new(0,0,0); Btn.Text = ""; Btn.Parent = TFrame
    Instance.new("UICorner", Btn).CornerRadius = UDim.new(1, 0)
    
    local Circle = Instance.new("Frame")
    Circle.Size = UDim2.new(0, 18, 0, 18); Circle.Position = UDim2.new(0, 2, 0.5, -9)
    Circle.BackgroundColor3 = Color3.new(1,1,1); Circle.Parent = Btn
    Instance.new("UICorner", Circle).CornerRadius = UDim.new(1, 0)
    
    local state = false
    Btn.MouseButton1Click:Connect(function()
        state = not state
        TweenService:Create(Circle, TweenInfo.new(0.2), {Position = state and UDim2.new(1, -20, 0.5, -9) or UDim2.new(0, 2, 0.5, -9)}):Play()
        TweenService:Create(Btn, TweenInfo.new(0.2), {BackgroundColor3 = state and Color3.fromRGB(0, 170, 255) or Color3.new(0,0,0)}):Play()
        callback(state)
    end)
end

-- FUNÇÕES DO SCRIPT
AddToggle("Esp", UDim2.new(0.05, 0, 0, 50), function(v) Settings.ESP = v end)
AddToggle("Speed Active", UDim2.new(0.05, 0, 0, 105), function(v) Settings.SpeedActive = v end)

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0, 100, 0, 40); SpeedInput.Position = UDim2.new(0.05, 0, 0, 165)
SpeedInput.BackgroundColor3 = Color3.fromRGB(20, 20, 20); SpeedInput.TextColor3 = Color3.new(1,1,1)
SpeedInput.Text = "100"; SpeedInput.Font = "GothamBold"; SpeedInput.Parent = MainContent
Instance.new("UICorner", SpeedInput)

local Pronto = Instance.new("TextButton")
Pronto.Text = "Pronto"; Pronto.Size = UDim2.new(0, 150, 0, 40); Pronto.Position = UDim2.new(0.4, 0, 0, 165)
Pronto.BackgroundColor3 = Color3.fromRGB(25, 25, 25); Pronto.TextColor3 = Color3.new(1,1,1)
Pronto.Font = "GothamBold"; Pronto.Parent = MainContent
Instance.new("UICorner", Pronto)
Pronto.MouseButton1Click:Connect
