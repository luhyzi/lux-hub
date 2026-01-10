-- SERVIÇOS
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- CONFIGURAÇÕES
local Settings = { 
    ESP = false, 
    Noclip = false, 
    Speed = 16, 
    Fly = false 
}

-- --- INTERFACE (DESIGN MANTIDO) ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_V1.1_Fix"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 520, 0, 320)
Background.Position = UDim2.new(0.5, -260, 0.5, -160)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.Parent = ScreenGui
local UICornerBg = Instance.new("UICorner"); UICornerBg.CornerRadius = UDim.new(0, 25); UICornerBg.Parent = Background

local LeftFrame = Instance.new("Frame")
LeftFrame.Size = UDim2.new(0, 140, 0, 300)
LeftFrame.Position = UDim2.new(0, 10, 0, 10)
LeftFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
LeftFrame.Parent = Background
Instance.new("UICorner", LeftFrame).CornerRadius = UDim.new(0, 20)

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"; Title.Font = "GothamBold"; Title.TextSize = 26; Title.TextColor3 = Color3.fromRGB(200, 200, 200)
Title.Position = UDim2.new(0, 15, 0, 20); Title.Size = UDim2.new(0, 110, 0, 40); Title.BackgroundTransparency = 1; Title.Parent = LeftFrame

local RightFrame = Instance.new("Frame")
RightFrame.Size = UDim2.new(0, 350, 0, 300)
RightFrame.Position = UDim2.new(0, 160, 0, 10)
RightFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
RightFrame.Parent = Background
Instance.new("UICorner", RightFrame).CornerRadius = UDim.new(0, 20)

local Ver = Instance.new("TextLabel")
Ver.Text = "v 1.1"; Ver.TextColor3 = Color3.new(1,1,1); Ver.Position = UDim2.new(1, -75, 0, 10); Ver.BackgroundTransparency = 1; Ver.Parent = RightFrame

local Close = Instance.new("TextButton")
Close.Text = "X"; Close.TextColor3 = Color3.new(1,1,1); Close.Position = UDim2.new(1, -35, 0, 10); Close.BackgroundTransparency = 1; Close.Parent = RightFrame
Close.MouseButton1Click:Connect(function() Background.Visible = false end)

-- --- FUNÇÃO TOGGLE (ESTILO VISUAL) ---
local function AddToggle(name, pos, callback)
	local TFrame = Instance.new("Frame")
	TFrame.Size = UDim2.new(0, 300, 0, 35); TFrame.Position = pos; TFrame.BackgroundColor3 = Color3.fromRGB(40,40,40); TFrame.Parent = RightFrame
	Instance.new("UICorner", TFrame).CornerRadius = UDim.new(0, 10)
	
	local Lbl = Instance.new("TextLabel")
	Lbl.Text = name; Lbl.TextColor3 = Color3.new(1,1,1); Lbl.Position = UDim2.new(0, 15, 0, 0); Lbl.Size = UDim2.new(0.4, 0, 1, 0); Lbl.BackgroundTransparency = 1; Lbl.Parent = TFrame; Lbl.TextXAlignment = "Left"
	
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

-- --- LÓGICA TÉCNICA (CORE) ---

-- 1. ESP Corrigido
RunService.RenderStepped:Connect(function()
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            local highlight = plr.Character:FindFirstChild("LuxESP")
            if Settings.ESP then
                if not highlight then
                    highlight = Instance.new("Highlight")
                    highlight.Name = "LuxESP"
                    highlight.FillColor = Color3.fromRGB(255, 0, 0)
                    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
                    highlight.Parent = plr.Character
                end
            elseif highlight then
                highlight:Destroy()
            end
        end
    end
end)

-- 2. Noclip Estrito
RunService.Stepped:Connect(function()
    if Settings.Noclip and LocalPlayer.Character then
        for _, v in pairs(LocalPlayer.Character:GetDescendants()) do
            if v:IsA("BasePart") then v.CanCollide = false end
        end
    end
end)

-- 3. Fly (Voo)
local bodyVel, bodyGyro
RunService.RenderStepped:Connect(function()
    if Settings.Fly and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local root = LocalPlayer.Character.HumanoidRootPart
        if not bodyVel then
            bodyVel = Instance.new("BodyVelocity", root)
            bodyVel.MaxForce = Vector3.new(1e6, 1e6, 1e6)
            bodyGyro = Instance.new("BodyGyro", root)
            bodyGyro.MaxTorque = Vector3.new(1e6, 1e6, 1e6)
            bodyGyro.CFrame = root.CFrame
        end
        bodyVel.Velocity = workspace.CurrentCamera.CFrame.LookVector * (LocalPlayer.Character.Humanoid.MoveDirection.Magnitude > 0 and 50 or 0)
        bodyGyro.CFrame = workspace.CurrentCamera.CFrame
    else
        if bodyVel then bodyVel:Destroy() bodyVel = nil end
        if bodyGyro then bodyGyro:Destroy() bodyGyro = nil end
    end
end)

-- --- APLICAÇÃO NA INTERFACE ---
AddToggle("Esp", UDim2.new(0.05, 0, 0, 40), function(v) Settings.ESP = v end)
AddToggle("Noclip", UDim2.new(0.05, 0, 0, 85), function(v) Settings.Noclip = v end)
AddToggle("Fly", UDim2.new(0.05, 0, 0, 130), function(v) Settings.Fly = v end)

-- Speed (Usando o botão "Pronto" para aplicar)
local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0.9, 0, 0, 35); SpeedInput.Position = UDim2.new(0.05, 0, 0, 175); SpeedInput.BackgroundColor3 = Color3.fromRGB(40,40,40)
SpeedInput.TextColor3 = Color3.new(1,1,1); SpeedInput.Text = "Velocidade (16)"; SpeedInput.Parent = RightFrame
Instance.new("UICorner", SpeedInput)

local ApplyBtn = Instance.new("TextButton")
ApplyBtn.Text = "Pronto"; ApplyBtn.Size = UDim2.new(0.4, 0, 0, 35); ApplyBtn.Position = UDim2.new(0.05, 0, 0, 220)
ApplyBtn.BackgroundColor3 = Color3.fromRGB(40,40,40); ApplyBtn.TextColor3 = Color3.new(1,1,1); ApplyBtn.Parent = RightFrame
Instance.new("UICorner", ApplyBtn).CornerRadius = UDim.new(0, 15)

ApplyBtn.MouseButton1Click:Connect(function()
    local val = tonumber(SpeedInput.Text) or 16
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = val
    end
end)

-- Botão Abrir
local Open = Instance.new("TextButton")
Open.Text = "Lux hub"; Open.Size = UDim2.new(0, 100, 0, 30); Open.Position = UDim2.new(0, 10, 0.4, 0); Open.Parent = ScreenGui
Open.MouseButton1Click:Connect(function() Background.Visible = not Background.Visible end)
