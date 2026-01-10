local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- CONFIGURAÇÕES
local Settings = { ESP = false, SpeedActive = false, SpeedValue = 16 }

-- --- INTERFACE PRINCIPAL ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_V13_Organizado"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 520, 0, 320)
Background.Position = UDim2.new(0.5, -260, 0.5, -160)
Background.BackgroundColor3 = Color3.fromRGB(10, 10, 10) -- Preto suave
Background.Visible = false
Background.Parent = ScreenGui
Instance.new("UICorner", Background).CornerRadius = UDim.new(0, 20)

-- SISTEMA DE ARRASTAR (CLICANDO NO FUNDO)
local function MakeDraggable(gui)
    local dragging, dragInput, dragStart, startPos
    gui.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = gui.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            gui.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
end
MakeDraggable(Background)

-- SIDEBAR (BARRA LATERAL)
local SideBar = Instance.new("Frame")
SideBar.Size = UDim2.new(0, 140, 0, 280)
SideBar.Position = UDim2.new(0, 15, 0, 20)
SideBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
SideBar.Parent = Background
Instance.new("UICorner", SideBar).CornerRadius = UDim.new(0, 15)

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"
Title.Font = "GothamBold"
Title.TextSize = 22
Title.TextColor3 = Color3.new(1,1,1)
Title.Size = UDim2.new(1, 0, 0, 50)
Title.BackgroundTransparency = 1
Title.Parent = SideBar

-- CONTEÚDO PRINCIPAL
local MainContent = Instance.new("Frame")
MainContent.Size = UDim2.new(0, 335, 0, 280)
MainContent.Position = UDim2.new(0, 170, 0, 20)
MainContent.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainContent.Parent = Background
Instance.new("UICorner", MainContent).CornerRadius = UDim.new(0, 15)

-- VERSÃO (ONDE ERA O X)
local VerLabel = Instance.new("TextLabel")
VerLabel.Text = "v 1.3"
VerLabel.Font = "GothamBold"
VerLabel.TextSize = 14
VerLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
VerLabel.Position = UDim2.new(1, -50, 0, 15)
VerLabel.Size = UDim2.new(0, 40, 0, 20)
VerLabel.BackgroundTransparency = 1
VerLabel.Parent = MainContent

-- BOTÃO DE ABRIR (LOGO BRILHOSA)
local OpenLogo = Instance.new("ImageButton")
OpenLogo.Name = "LuxOpen"
OpenLogo.Image = "rbxassetid://139243074283722"
OpenLogo.Size = UDim2.new(0, 60, 0, 60)
OpenLogo.Position = UDim2.new(0, 20, 0.5, -30)
OpenLogo.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
OpenLogo.Visible = false
OpenLogo.ZIndex = 10
OpenLogo.Parent = ScreenGui
Instance.new("UICorner", OpenLogo).CornerRadius = UDim.new(1, 0)

local Glow = Instance.new("ImageLabel")
Glow.Image = "rbxassetid://50288246"
Glow.ImageColor3 = Color3.new(1,1,1)
Glow.Size = UDim2.new(1.5, 0, 1.5, 0)
Glow.Position = UDim2.new(-0.25, 0, -0.25, 0)
Glow.BackgroundTransparency = 1
Glow.Parent = OpenLogo
TweenService:Create(Glow, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {ImageTransparency = 0.8}):Play()

OpenLogo.MouseButton1Click:Connect(function()
    Background.Visible = not Background.Visible
end)

-- FUNÇÃO TOGGLE (BOTAO LIGA/DESLIGA)
local function AddToggle(name, pos, callback)
    local TFrame = Instance.new("Frame")
    TFrame.Size = UDim2.new(0.9, 0, 0, 45)
    TFrame.Position = pos
    TFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    TFrame.Parent = MainContent
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
        callback(state)
    end)
end

-- COMPONENTES
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
Pronto.MouseButton1Click:Connect(function() Settings.SpeedValue = tonumber(SpeedInput.Text) or 16 end)

-- LÓGICA ESP/SPEED
RunService.Heartbeat:Connect(function()
    if Settings.ESP then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and not p.Character:FindFirstChild("LuxESP") then
                local h = Instance.new("Highlight", p.Character); h.Name = "LuxESP"; h.FillColor = Color3.new(1,0,0)
            end
        end
    else
        for _, p in pairs(Players:GetPlayers()) do if p.Character and p.Character:FindFirstChild("LuxESP") then p.Character.LuxESP:Destroy() end end
    end
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedActive and Settings.SpeedValue or 16
    end
end)

-- INTRODUÇÃO
local function RunIntro()
    local Blur = Instance.new("BlurEffect", workspace.CurrentCamera); Blur.Size = 0
    local Txt = Instance.new("TextLabel", ScreenGui)
    Txt.Text = "Lux hub"; Txt.Font = "GothamBold"; Txt.TextSize = 80; Txt.TextColor3 = Color3.new(1,1,1)
    Txt.Size = UDim2.new(1,0,1,0); Txt.BackgroundTransparency = 1; Txt.Position = UDim2.new(0,0,1,0)
    TweenService:Create(Blur, TweenInfo.new(1.5), {Size = 25}):Play()
    TweenService:Create(Txt, TweenInfo.new(1.5), {Position = UDim2.new(0,0,0,0)}):Play()
    task.wait(2.5)
    TweenService:Create(Blur, TweenInfo.new(0.5), {Size = 0}):Play()
    TweenService:Create(Txt, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
    task.wait(0.5); Blur:Destroy(); Txt:Destroy(); OpenLogo.Visible = true
end
task.spawn(RunIntro)
