-- Garantir que o script rode mesmo se o executor for simples
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- Proteção simples para evitar erros de execução
if not LocalPlayer then return end

local Settings = { ESP = false, SpeedActive = false, SpeedValue = 16 }

-- --- INTERFACE ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHubLuizFinal"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 999
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 500, 0, 300)
Background.Position = UDim2.new(0.5, -250, 0.5, -150)
Background.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Background.Visible = false
Background.Parent = ScreenGui
Instance.new("UICorner", Background).CornerRadius = UDim.new(0, 15)

-- ARRASTAR (SIMPLIFICADO)
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

-- DESIGN INTERNO (LUX HUB E v 1.3 LUIZ)
local SideBar = Instance.new("Frame")
SideBar.Size = UDim2.new(0, 130, 0, 260); SideBar.Position = UDim2.new(0, 10, 0, 20)
SideBar.BackgroundColor3 = Color3.fromRGB(25, 25, 25); SideBar.Parent = Background
Instance.new("UICorner", SideBar)

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"; Title.Font = "GothamBold"; Title.TextSize = 20; Title.TextColor3 = Color3.new(1,1,1)
Title.Size = UDim2.new(1, 0, 0, 50); Title.BackgroundTransparency = 1; Title.Parent = SideBar

local MainContent = Instance.new("Frame")
MainContent.Size = UDim2.new(0, 340, 0, 260); MainContent.Position = UDim2.new(0, 150, 0, 20)
MainContent.BackgroundColor3 = Color3.fromRGB(25, 25, 25); MainContent.Parent = Background
Instance.new("UICorner", MainContent)

local Credits = Instance.new("TextLabel")
Credits.Text = "v 1.3 luiz"; Credits.Font = "GothamBold"; Credits.TextSize = 13
Credits.TextColor3 = Color3.fromRGB(150, 150, 150); Credits.Position = UDim2.new(1, -90, 0, 15)
Credits.Size = UDim2.new(0, 80, 0, 20); Credits.BackgroundTransparency = 1; Credits.Parent = MainContent

-- BOTÃO DE ABRIR (LOGO)
local OpenLogo = Instance.new("ImageButton")
OpenLogo.Name = "OpenBtn"
OpenLogo.Image = "rbxassetid://139243074283722"
OpenLogo.Size = UDim2.new(0, 60, 0, 60); OpenLogo.Position = UDim2.new(0, 10, 0.5, -30)
OpenLogo.BackgroundColor3 = Color3.fromRGB(30, 30, 30); OpenLogo.ZIndex = 1000; OpenLogo.Parent = ScreenGui
Instance.new("UICorner", OpenLogo).CornerRadius = UDim.new(1, 0)

OpenLogo.MouseButton1Click:Connect(function()
    Background.Visible = not Background.Visible
end)

-- FUNÇÃO TOGGLE
local function AddToggle(name, pos, callback)
    local TFrame = Instance.new("Frame")
    TFrame.Size = UDim2.new(0.9, 0, 0, 45); TFrame.Position = pos; TFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15); TFrame.Parent = MainContent
    Instance.new("UICorner", TFrame)
    local Lbl = Instance.new("TextLabel")
    Lbl.Text = name; Lbl.TextColor3 = Color3.new(1,1,1); Lbl.Position = UDim2.new(0, 10, 0, 0); Lbl.Size = UDim2.new(0.5, 0, 1, 0); Lbl.BackgroundTransparency = 1; Lbl.TextXAlignment = "Left"; Lbl.Parent = TFrame
    local Btn = Instance.new("TextButton")
    Btn.Size = UDim2.new(0, 40, 0, 20); Btn.Position = UDim2.new(1, -50, 0.5, -10); Btn.BackgroundColor3 = Color3.new(0,0,0); Btn.Text = ""; Btn.Parent = TFrame
    Instance.new("UICorner", Btn).CornerRadius = UDim.new(1, 0)
    local state = false
    Btn.MouseButton1Click:Connect(function()
        state = not state
        Btn.BackgroundColor3 = state and Color3.new(0, 0.8, 0) or Color3.new(0, 0, 0)
        callback(state)
    end)
end

AddToggle("Esp", UDim2.new(0.05, 0, 0, 50), function(v) Settings.ESP = v end)
AddToggle("Velocidade Ativa", UDim2.new(0.05, 0, 0, 105), function(v) Settings.SpeedActive = v end)

-- VELOCIDADE INPUT
local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0, 100, 0, 40); SpeedInput.Position = UDim2.new(0.05, 0, 0, 165)
SpeedInput.BackgroundColor3 = Color3.fromRGB(15, 15, 15); SpeedInput.TextColor3 = Color3.new(1,1,1); SpeedInput.Text = "100"; SpeedInput.Parent = MainContent
Instance.new("UICorner", SpeedInput)

local Pronto = Instance.new("TextButton")
Pronto.Text = "Pronto"; Pronto.Size = UDim2.new(0, 150, 0, 40); Pronto.Position = UDim2.new(0.4, 0, 0, 165)
Pronto.BackgroundColor3 = Color3.fromRGB(40, 40, 40); Pronto.TextColor3 = Color3.new(1,1,1); Pronto.Parent = MainContent
Instance.new("UICorner", Pronto)
Pronto.MouseButton1Click:Connect(function() Settings.SpeedValue = tonumber(SpeedInput.Text) or 16 end)

-- LÓGICA DE LOOP
RunService.Heartbeat:Connect(function()
    if Settings.ESP then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and not p.Character:FindFirstChild("LuxESP") then
                local h = Instance.new("Highlight", p.Character); h.Name = "LuxESP"; h.FillColor = Color3.new(1, 0, 0)
            end
        end
    else
        for _, p in pairs(Players:GetPlayers()) do if p.Character and p.Character:FindFirstChild("LuxESP") then p.Character.LuxESP:Destroy() end end
    end
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = Settings.SpeedActive and Settings.SpeedValue or 16
    end
end)

warn("LUX HUB CARREGADO!")
