local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService") -- Para animações suaves
local LocalPlayer = Players.LocalPlayer

-- CONFIGURAÇÕES INICIAIS
local Settings = { 
    ESP = false, 
    Noclip = false, 
    Fly = false,
    SpeedActive = false,
    SpeedValue = 16
}

-- --- CRIAÇÃO DA INTERFACE ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_V12_Animated"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Fundo Preto (Borda grossa da imagem)
local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 520, 0, 320)
Background.Position = UDim2.new(0.5, -260, 0.5, -160)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.Parent = ScreenGui
Instance.new("UICorner", Background).CornerRadius = UDim.new(0, 25)
Background.Visible = false -- Começa invisível para a animação

-- JANELA ESQUERDA (SIDEBAR)
local SideBar = Instance.new("Frame")
SideBar.Size = UDim2.new(0, 140, 0, 300)
SideBar.Position = UDim2.new(0, 10, 0, 10)
SideBar.BackgroundColor3 = Color3.fromRGB(45, 45, 45) -- Um pouco mais escuro para contraste
SideBar.Parent = Background
Instance.new("UICorner", SideBar).CornerRadius = UDim.new(0, 20)

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"; Title.Font = "GothamBold"; Title.TextSize = 26; Title.TextColor3 = Color3.fromRGB(220, 220, 220)
Title.Position = UDim2.new(0, 15, 0, 20); Title.Size = UDim2.new(0, 110, 0, 40); Title.BackgroundTransparency = 1; Title.Parent = SideBar

local ManTabBtn = Instance.new("TextButton")
ManTabBtn.Text = "Man"; ManTabBtn.Font = "GothamBold"; ManTabBtn.TextSize = 18; ManTabBtn.TextColor3 = Color3.new(1,1,1)
ManTabBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30); ManTabBtn.Size = UDim2.new(0, 110, 0, 40)
ManTabBtn.Position = UDim2.new(0, 15, 0, 80); ManTabBtn.Parent = SideBar
Instance.new("UICorner", ManTabBtn).CornerRadius = UDim.new(0, 10)

-- JANELA DIREITA (CONTEÚDO)
local MainContent = Instance.new("Frame")
MainContent.Size = UDim2.new(0, 350, 0, 300)
MainContent.Position = UDim2.new(0, 160, 0, 10)
MainContent.BackgroundColor3 = Color3.fromRGB(45, 45, 45) -- Um pouco mais escuro
MainContent.Parent = Background
Instance.new("UICorner", MainContent).CornerRadius = UDim.new(0, 20)

local Ver = Instance.new("TextLabel")
Ver.Text = "v 1.2"; Ver.Font = "Gotham"; Ver.TextSize = 14; Ver.TextColor3 = Color3.new(1,1,1)
Ver.Position = UDim2.new(1, -75, 0, 10); Ver.BackgroundTransparency = 1; Ver.Parent = MainContent

local Close = Instance.new("TextButton")
Close.Text = "X"; Close.Font = "GothamBold"; Close.TextSize = 18; Close.TextColor3 = Color3.new(1,1,1)
Close.Position = UDim2.new(1, -35, 0, 10); Close.BackgroundTransparency = 1; Close.Parent = MainContent
Close.MouseButton1Click:Connect(function() Background.Visible = false end)

-- --- FUNÇÃO PARA CRIAR ALAVANCAS (TOGGLES) ---
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
        Circle:TweenPosition(state and UDim2.new(1, -20, 0.5, -9) or UDim2.new(0, 2, 0.5, -9), "Out", "Sine", 0.1, true)
        callback(state)
    end)
end

-- --- LÓGICAS (ESP, NOCLIP, FLY, SPEED) ---
RunService.Heartbeat:Connect(function()
    -- ESP
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            local hl = plr.Character:FindFirstChild("LuxESP")
            if Settings.ESP then
                if not hl then
                    hl = Instance.new("Highlight", plr.Character); hl.Name = "LuxESP"; hl.FillColor = Color3.fromRGB(255,0,0); hl.OutlineColor = Color3.new(1,1,1)
                end
            elseif hl then hl:Destroy() end
        end
    end
    -- SPEED
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedActive and Settings.SpeedValue or 16
    end
end)

RunService.Stepped:Connect(function()
    if Settings.Noclip and LocalPlayer.Character then
        for _, v in pairs(LocalPlayer.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end
    end
end)

local bv, bg
RunService.RenderStepped:Connect(function()
    if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local root = LocalPlayer.Character.HumanoidRootPart
        if not bv then
            bv = Instance.new("BodyVelocity", root); bv.MaxForce = Vector3.new(1e6,1e6,1e6)
            bg = Instance.new("BodyGyro", root); bg.MaxTorque = Vector3.new(1e6,1e6,1e6)
        end
        bg.CFrame = workspace.CurrentCamera.CFrame
        bv.Velocity = workspace.CurrentCamera.CFrame.LookVector * (LocalPlayer.Character.Humanoid.MoveDirection.Magnitude > 0 and 50 or 0)
    else
        if bv then bv:Destroy() bv = nil end
        if bg then bg:Destroy() bg = nil end
    end
end)

-- --- BOTÕES DA ABA MAN ---
AddToggle("Esp", UDim2.new(0.05, 0, 0, 40), function(v) Settings.ESP = v end)
AddToggle("Noclip", UDim2.new(0.05, 0, 0, 85), function(v) Settings.Noclip = v end)
AddToggle("Fly", UDim2.new(0.05, 0, 0, 130), function(v) Settings.Fly = v end)
AddToggle("Speed Active", UDim2.new(0.05, 0, 0, 175), function(v) Settings.SpeedActive = v end)

-- INPUT DE VELOCIDADE
local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0.4, 0, 0, 35); SpeedInput.Position = UDim2.new(0.05, 0, 0, 220)
SpeedInput.BackgroundColor3 = Color3.fromRGB(30,30,30); SpeedInput.TextColor3 = Color3.new(1,1,1)
SpeedInput.Text = "100"; SpeedInput.Font = "Gotham"; SpeedInput.Parent = MainContent
Instance.new("UICorner", SpeedInput)

local ProntoBtn = Instance.new("TextButton")
ProntoBtn.Text = "Pronto"; ProntoBtn.Font = "GothamBold"; ProntoBtn.Size = UDim2.new(0.4, 0, 0, 35)
ProntoBtn.Position = UDim2.new(0.5, 0, 0, 220); ProntoBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
ProntoBtn.TextColor3 = Color3.new(1,1,1); ProntoBtn.Parent = MainContent
Instance.new("UICorner", ProntoBtn).CornerRadius = UDim.new(0, 15)
ProntoBtn.MouseButton1Click:Connect(function() 
    Settings.SpeedValue = tonumber(SpeedInput.Text) or 16 
end)

-- BOTÃO PARA REABRIR (INVISÍVEL DURANTE A INTRO)
local OpenBtn = Instance.new("TextButton")
OpenBtn.Text = "Lux hub"; OpenBtn.Size = UDim2.new(0, 100, 0, 35); OpenBtn.Position = UDim2.new(0, 10, 0.4, 0)
OpenBtn.BackgroundColor3 = Color3.fromRGB(45,45,45); OpenBtn.TextColor3 = Color3.new(1,1,1); OpenBtn.Parent = ScreenGui
Instance.new("UICorner", OpenBtn)
OpenBtn.Visible = false -- Invisível durante a intro
OpenBtn.MouseButton1Click:Connect(function() Background.Visible = not Background.Visible end)

-- --- ANIMAÇÃO DE INTRODUÇÃO ---
local Blur = Instance.new("BlurEffect", workspace.CurrentCamera)
Blur.Size = 0 -- Começa sem desfoque

local IntroTitle = Instance.new("TextLabel")
IntroTitle.Text = "Lux hub"; IntroTitle.Font = "GothamBold"; IntroTitle.TextSize = 60; IntroTitle.TextColor3 = Color3.new(1,1,1)
IntroTitle.BackgroundTransparency = 1; IntroTitle.Size = UDim2.new(1,0,1,0)
IntroTitle.Position = UDim2.new(0,0,1,0) -- Começa fora da tela, embaixo
IntroTitle.Parent = ScreenGui
IntroTitle.ZIndex = 10 -- Garante que esteja acima de tudo

-- Propriedades da animação
local blurTweenInfo = TweenInfo.new(1.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out, 0, false, 0)
local textTweenInfo = TweenInfo.new(1.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out, 0, false, 0)

-- Animação de Desfoque (Blur)
local blurTween = TweenService:Create(Blur, blurTweenInfo, {Size = 15}) -- Desfoca até 15
blurTween:Play()

-- Animação do Texto "Lux hub" subindo
local textTween = TweenService:Create(IntroTitle, textTweenInfo, {Position = UDim2.new(0,0,0.5, -30)}) -- Sobe para o centro
textTween:Play()

-- Espera um pouco, depois reverte o desfoque e esconde o texto
task.wait(2.5) -- Tempo total da animação

local blurOutTween = TweenService:Create(Blur, TweenInfo.new(0.5), {Size = 0}) -- Remove o desfoque
local textOutTween = TweenService:Create(IntroTitle, TweenInfo.new(0.5), {TextTransparency = 1}) -- Faz o texto sumir
blurOutTween:Play()
textOutTween:Play()

task.wait(0.5) -- Espera o desfoque e o texto sumirem
Blur:Destroy()
IntroTitle:Destroy()

Background.Visible = true -- Torna a interface principal visível
OpenBtn.Visible = true -- Torna o botão de reabrir visível
