local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- CONFIGURAÇÕES
local Settings = { 
    ESP = false, 
    Noclip = false, 
    SpeedActive = false,
    SpeedValue = 16
}

-- --- INTERFACE PRINCIPAL ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_V13_Glow"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 520, 0, 320)
Background.Position = UDim2.new(0.5, -260, 0.5, -160)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.Parent = ScreenGui
Background.Visible = false
Background.ClipsDescendants = true
Instance.new("UICorner", Background).CornerRadius = UDim.new(0, 25)

-- FUNÇÃO DRAG
local function MakeDraggable(gui)
    local dragging, dragInput, dragStart, startPos
    gui.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = gui.Position
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
end
MakeDraggable(Background)

-- --- BOTÃO DE ABRIR COM BRILHO (GLOW) ---
local OpenContainer = Instance.new("Frame") -- Container para o botão e o brilho
OpenContainer.Size = UDim2.new(0, 60, 0, 60)
OpenContainer.Position = UDim2.new(0, 20, 0.5, -30)
OpenContainer.BackgroundTransparency = 1
OpenContainer.Visible = false
OpenContainer.Parent = ScreenGui

-- O Brilho (Glow)
local Glow = Instance.new("ImageLabel")
Glow.Name = "GlowEffect"
Glow.BackgroundTransparency = 1
Glow.Image = "rbxassetid://50288246" -- Asset de sombra circular para efeito de brilho
Glow.ImageColor3 = Color3.fromRGB(255, 255, 255) -- Cor do brilho (Branco)
Glow.Size = UDim2.new(1.5, 0, 1.5, 0)
Glow.Position = UDim2.new(-0.25, 0, -0.25, 0)
Glow.ImageTransparency = 0.5
Glow.ZIndex = 1
Glow.Parent = OpenContainer

-- A Logo
local OpenLogo = Instance.new("ImageButton")
OpenLogo.Name = "Logo"
OpenLogo.Image = "rbxassetid://139243074283722"
OpenLogo.Size = UDim2.new(1, 0, 1, 0)
OpenLogo.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
OpenLogo.ZIndex = 2
OpenLogo.Parent = OpenContainer
Instance.new("UICorner", OpenLogo).CornerRadius = UDim.new(1, 0)

-- Animação do Brilho Pulsante
local glowInfo = TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true)
TweenService:Create(Glow, glowInfo, {ImageTransparency = 0.8, Size = UDim2.new(1.8, 0, 1.8, 0), Position = UDim2.new(-0.4, 0, -0.4, 0)}):Play()

OpenLogo.MouseButton1Click:Connect(function()
    Background.Visible = not Background.Visible
    if Background.Visible then
        Background.Size = UDim2.new(0, 0, 0, 0)
        TweenService:Create(Background, TweenInfo.new(0.3, Enum.EasingStyle.BackOut), {Size = UDim2.new(0, 520, 0, 320)}):Play()
    end
end)

-- --- SIDEBAR E CONTEÚDO (DESIGN v1.3) ---
local SideBar = Instance.new("Frame")
SideBar.Size = UDim2.new(0, 140, 0, 300); SideBar.Position = UDim2.new(0, 10, 0, 10)
SideBar.BackgroundColor3 = Color3.fromRGB(45, 45, 45); SideBar.Parent = Background
Instance.new("UICorner", SideBar).CornerRadius = UDim.new(0, 20)

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"; Title.Font = "GothamBold"; Title.TextSize = 26; Title.TextColor3 = Color3.fromRGB(220, 220, 220)
Title.Position = UDim2.new(0, 15, 0, 20); Title.Size = UDim2.new(0, 110, 0, 40); Title.BackgroundTransparency = 1; Title.Parent = SideBar

local ManTabBtn = Instance.new("TextButton")
ManTabBtn.Text = "Man"; ManTabBtn.Font = "GothamBold"; ManTabBtn.TextColor3 = Color3.new(1,1,1)
ManTabBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30); ManTabBtn.Size = UDim2.new(0, 110, 0, 40)
ManTabBtn.Position = UDim2.new(0, 15, 0, 80); ManTabBtn.Parent = SideBar
Instance.new("UICorner", ManTabBtn).CornerRadius = UDim.new(0, 10)

local MainContent = Instance.new("Frame")
MainContent.Size = UDim2.new(0, 350, 0, 300); MainContent.Position = UDim2.new(0, 160, 0, 10)
MainContent.BackgroundColor3 = Color3.fromRGB(45, 45, 45); MainContent.Parent = Background
Instance.new("UICorner", MainContent).CornerRadius = UDim.new(0, 20)

local Ver = Instance.new("TextLabel")
Ver.Text = "v 1.3"; Ver.Font = "Gotham"; Ver.TextSize = 14; Ver.Position = UDim2.new(1, -75, 0, 10); Ver.BackgroundTransparency = 1; Ver.TextColor3 = Color3.new(1,1,1); Ver.Parent = MainContent

local Close = Instance.new("TextButton")
Close.Text = "X"; Close.Font = "GothamBold"; Close.Position = UDim2.new(1, -35, 0, 10); Close.BackgroundTransparency = 1; Close.TextColor3 = Color3.new(1,1,1); Close.Parent = MainContent
Close.MouseButton1Click:Connect(function()
    TweenService:Create(Background, TweenInfo.new(0.2), {Size = UDim2.new(0,0,0,0)}):Play()
    task.wait(0.2)
    Background.Visible = false
end)

-- --- FUNÇÃO TOGGLE ---
local function AddToggle(name, pos, callback)
    local TFrame = Instance.new("Frame")
    TFrame.Size = UDim2.new(0.9, 0, 0, 35); TFrame.Position = pos; TFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30); TFrame.Parent = MainContent
    Instance.new("UICorner", TFrame).CornerRadius = UDim.new(0, 10)
    local Lbl = Instance.new("TextLabel")
    Lbl.Text = name; Lbl.TextColor3 = Color3.new(1,1,1); Lbl.Position = UDim2.new(0, 15, 0, 0); Lbl.Size = UDim2.new(0.4, 0, 1, 0); Lbl.BackgroundTransparency = 1; Lbl.Parent = TFrame; Lbl.TextXAlignment = "Left"; Lbl.Font = "Gotham"
    local Btn = Instance.new("TextButton")
    Btn.Size = UDim2.new(0, 45, 0, 22); Btn.Position = UDim2.new(1, -55, 0.5, -11); Btn.BackgroundColor3 = Color3.new(0,0,0); Btn.Text = ""; Btn.Parent = TFrame
    Instance.new("UICorner", Btn).CornerRadius = UDim.new(1, 0)
    local Circle = Instance.new("Frame")
    Circle.Size = UDim2.new(0, 18, 0, 18); Circle.Position = UDim2.new(0, 2, 0.5, -9); Circle.BackgroundColor3 = Color3.new(1,1,1); Circle.Parent = Btn
    Instance.new("UICorner", Circle).CornerRadius = UDim.new(1, 0)
    local state = false
    Btn.MouseButton1Click:Connect(function()
        state = not state
        TweenService:Create(Circle, TweenInfo.new(0.2), {Position = state and UDim2.new(1, -20, 0.5, -9) or UDim2.new(0, 2, 0.5, -9)}):Play()
        callback(state)
    end)
end

-- LÓGICAS
RunService.Heartbeat:Connect(function()
    if Settings.ESP then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character and not plr.Character:FindFirstChild("LuxESP") then
                local hl = Instance.new("Highlight", plr.Character); hl.Name = "LuxESP"; hl.FillColor = Color3.fromRGB(255,0,0)
            end
        end
    else
        for _, plr in pairs(Players:GetPlayers()) do if plr.Character and plr.Character:FindFirstChild("LuxESP") then plr.Character.LuxESP:Destroy() end end
    end
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedActive and Settings.SpeedValue or 16
    end
end)

RunService.Stepped:Connect(function()
    if Settings.Noclip and LocalPlayer.Character then
        for _, v in pairs(LocalPlayer.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end
    end
end)

-- CONTROLES
AddToggle("Esp", UDim2.new(0.05, 0, 0, 40), function(v) Settings.ESP = v end)
AddToggle("Noclip", UDim2.new(0.05, 0, 0, 85), function(v) Settings.Noclip = v end)
AddToggle("Speed Active", UDim2.new(0.05, 0, 0, 130), function(v) Settings.SpeedActive = v end)

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0.4, 0, 0, 35); SpeedInput.Position = UDim2.new(0.05, 0, 0, 175)
SpeedInput.BackgroundColor3 = Color3.fromRGB(30,30,30); SpeedInput.TextColor3 = Color3.new(1,1,1); SpeedInput.Text = "100"; SpeedInput.Parent = MainContent
Instance.new("UICorner", SpeedInput)

local ProntoBtn = Instance.new("TextButton")
ProntoBtn.Text = "Pronto"; ProntoBtn.Size = UDim2.new(0.4, 0, 0, 35); ProntoBtn.Position = UDim2.new(0.5, 0, 0, 175)
ProntoBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35); ProntoBtn.TextColor3 = Color3.new(1,1,1); ProntoBtn.Parent = MainContent
Instance.new("UICorner", ProntoBtn).CornerRadius = UDim.new(0, 15)
ProntoBtn.MouseButton1Click:Connect(function() Settings.SpeedValue = tonumber(SpeedInput.Text) or 16 end)

-- INTRODUÇÃO
local function RunIntro()
    local Blur = Instance.new("BlurEffect", workspace.CurrentCamera); Blur.Size = 0
    local IntroText = Instance.new("TextLabel", ScreenGui)
    IntroText.Text = "Lux hub"; IntroText.Font = "GothamBold"; IntroText.TextSize = 80; IntroText.TextColor3 = Color3.new(1,1,1); IntroText.BackgroundTransparency = 1; IntroText.Size = UDim2.new(1,0,1,0); IntroText.Position = UDim2.new(0,0,1,0)
    TweenService:Create(Blur, TweenInfo.new(1.5), {Size = 25}):Play()
    TweenService:Create(IntroText, TweenInfo.new(1.5), {Position = UDim2.new(0,0,0,0)}):Play()
    task.wait(2.5)
    TweenService:Create(Blur, TweenInfo.new(0.5), {Size = 0}):Play()
    TweenService:Create(IntroText, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
    task.wait(0.5); Blur:Destroy(); IntroText:Destroy(); OpenContainer.Visible = true
end
task.spawn(RunIntro)
