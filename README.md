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
ScreenGui.Name = "LuxHub_V13_Final_Fixed"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 100 -- Garante que fique acima de tudo
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 520, 0, 320)
Background.Position = UDim2.new(0.5, -260, 0.5, -160)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.Parent = ScreenGui
Background.Visible = false -- Começa invisível
Instance.new("UICorner", Background).CornerRadius = UDim.new(0, 25)

-- --- BARRA DE ARRASTE (ALÇA) ---
local DragBar = Instance.new("Frame")
DragBar.Name = "DragBar"
DragBar.Size = UDim2.new(0, 150, 0, 15)
DragBar.Position = UDim2.new(0.5, -75, 0, 10)
DragBar.BackgroundColor3 = Color3.fromRGB(120, 120, 120)
DragBar.BackgroundTransparency = 0.3
DragBar.Parent = Background
Instance.new("UICorner", DragBar).CornerRadius = UDim.new(1, 0)

-- FUNÇÃO DRAG MELHORADA
local function MakeDraggable(gui, dragPart)
    local dragging, dragInput, dragStart, startPos
    dragPart.InputBegan:Connect(function(input)
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
MakeDraggable(Background, DragBar)

-- --- BOTÃO DA LOGO BRILHANTE (TOTALMENTE FUNCIONAL) ---
local OpenLogo = Instance.new("ImageButton")
OpenLogo.Name = "LuxButton"
OpenLogo.Image = "rbxassetid://139243074283722"
OpenLogo.Size = UDim2.new(0, 65, 0, 65)
OpenLogo.Position = UDim2.new(0, 30, 0.5, -32)
OpenLogo.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
OpenLogo.ZIndex = 100
OpenLogo.Visible = false -- Só aparece após a intro
OpenLogo.Parent = ScreenGui
Instance.new("UICorner", OpenLogo).CornerRadius = UDim.new(1, 0)

local Glow = Instance.new("ImageLabel")
Glow.Image = "rbxassetid://50288246"
Glow.ImageColor3 = Color3.fromRGB(255, 255, 255)
Glow.BackgroundTransparency = 1
Glow.Size = UDim2.new(1.6, 0, 1.6, 0)
Glow.Position = UDim2.new(-0.3, 0, -0.3, 0)
Glow.ZIndex = 99
Glow.Parent = OpenLogo

-- Animação do Brilho
TweenService:Create(Glow, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {ImageTransparency = 0.6}):Play()

-- LÓGICA DE CLIQUE DIRETO (SEM ERRO)
OpenLogo.MouseButton1Click:Connect(function()
    Background.Visible = not Background.Visible
end)

-- --- DESIGN INTERNO ---
local MainContent = Instance.new("Frame")
MainContent.Size = UDim2.new(0, 350, 0, 260); MainContent.Position = UDim2.new(0, 160, 0, 45)
MainContent.BackgroundColor3 = Color3.fromRGB(45, 45, 45); MainContent.Parent = Background
Instance.new("UICorner", MainContent).CornerRadius = UDim.new(0, 20)

local SideBar = Instance.new("Frame")
SideBar.Size = UDim2.new(0, 140, 0, 260); SideBar.Position = UDim2.new(0, 10, 0, 45)
SideBar.BackgroundColor3 = Color3.fromRGB(45, 45, 45); SideBar.Parent = Background
Instance.new("UICorner", SideBar).CornerRadius = UDim.new(0, 20)

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"; Title.Font = "GothamBold"; Title.TextSize = 22; Title.TextColor3 = Color3.new(1,1,1)
Title.Position = UDim2.new(0, 15, 0, 15); Title.BackgroundTransparency = 1; Title.Parent = SideBar

local Close = Instance.new("TextButton")
Close.Text = "X"; Close.Font = "GothamBold"; Close.TextColor3 = Color3.new(1,1,1); Close.Position = UDim2.new(1, -35, 0, 10); Close.BackgroundTransparency = 1; Close.Parent = MainContent
Close.MouseButton1Click:Connect(function() Background.Visible = false end)

-- --- FUNÇÃO TOGGLE ---
local function AddToggle(name, pos, callback)
    local TFrame = Instance.new("Frame")
    TFrame.Size = UDim2.new(0.9, 0, 0, 40); TFrame.Position = pos; TFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35); TFrame.Parent = MainContent
    Instance.new("UICorner", TFrame).CornerRadius = UDim.new(0, 10)
    
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
    
    local Lbl = Instance.new("TextLabel")
    Lbl.Text = name; Lbl.TextColor3 = Color3.new(1,1,1); Lbl.Position = UDim2.new(0, 15, 0, 0); Lbl.Size = UDim2.new(0.5, 0, 1, 0); Lbl.BackgroundTransparency = 1; Lbl.Parent = TFrame; Lbl.TextXAlignment = "Left"; Lbl.Font = "Gotham"
end

-- LOGICAS
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

-- COMPONENTES
AddToggle("Esp", UDim2.new(0.05, 0, 0, 20), function(v) Settings.ESP = v end)
AddToggle("Speed Active", UDim2.new(0.05, 0, 0, 70), function(v) Settings.SpeedActive = v end)

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0.4, 0, 0, 35); SpeedInput.Position = UDim2.new(0.05, 0, 0, 120)
SpeedInput.BackgroundColor3 = Color3.fromRGB(30,30,30); SpeedInput.TextColor3 = Color3.new(1,1,1); SpeedInput.Text = "100"; SpeedInput.Parent = MainContent
Instance.new("UICorner", SpeedInput)

local ProntoBtn = Instance.new("TextButton")
ProntoBtn.Text = "Pronto"; ProntoBtn.Size = UDim2.new(0.4, 0, 0, 35); ProntoBtn.Position = UDim2.new(0.5, 0, 0, 120)
ProntoBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35); ProntoBtn.TextColor3 = Color3.new(1,1,1); ProntoBtn.Parent = MainContent
Instance.new("UICorner", ProntoBtn)
ProntoBtn.MouseButton1Click:Connect(function() Settings.SpeedValue = tonumber(SpeedInput.Text) or 16 end)

-- --- ANIMAÇÃO DE INTRODUÇÃO (3 SEGUNDOS) ---
local function RunIntro()
    local Blur = Instance.new("BlurEffect", workspace.CurrentCamera); Blur.Size = 0
    local IntroText = Instance.new("TextLabel", ScreenGui)
    IntroText.Text = "Lux hub"; IntroText.Font = "GothamBold"; IntroText.TextSize = 80; IntroText.TextColor3 = Color3.new(1,1,1); IntroText.BackgroundTransparency = 1; IntroText.Size = UDim2.new(1,0,1,0); IntroText.Position = UDim2.new(0,0,1,0)
    
    TweenService:Create(Blur, TweenInfo.new(1.5), {Size = 25}):Play()
    TweenService:Create(IntroText, TweenInfo.new(1.5), {Position = UDim2.new(0,0,0,0)}):Play()
    
    task.wait(2.5) -- Duração solicitada
    
    TweenService:Create(Blur, TweenInfo.new(0.5), {Size = 0}):Play()
    TweenService:Create(IntroText, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
    
    task.wait(0.5)
    Blur:Destroy(); IntroText:Destroy()
    OpenLogo.Visible = true -- Libera o botão da logo
end

task.spawn(RunIntro)
