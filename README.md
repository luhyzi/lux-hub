-- SERVIÇOS
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- CONFIGURAÇÕES
local Settings = { ESP = false, Noclip = false, TargetPlayer = nil }

-- --- CRIAÇÃO DA INTERFACE ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_Final"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Fundo Preto (Borda grossa da imagem)
local Background = Instance.new("Frame")
Background.Size = UDim2.new(0, 520, 0, 320)
Background.Position = UDim2.new(0.5, -260, 0.5, -160)
Background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Background.Parent = ScreenGui
local UICornerBg = Instance.new("UICorner"); UICornerBg.CornerRadius = UDim.new(0, 25); UICornerBg.Parent = Background

-- JANELA ESQUERDA (Side)
local LeftFrame = Instance.new("Frame")
LeftFrame.Size = UDim2.new(0, 140, 0, 300)
LeftFrame.Position = UDim2.new(0, 10, 0, 10)
LeftFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
LeftFrame.Parent = Background
local UICornerL = Instance.new("UICorner"); UICornerL.CornerRadius = UDim.new(0, 20); UICornerL.Parent = LeftFrame

local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 26
Title.TextColor3 = Color3.fromRGB(200, 200, 200)
Title.Position = UDim2.new(0, 15, 0, 20)
Title.Size = UDim2.new(0, 110, 0, 40)
Title.BackgroundTransparency = 1
Title.Parent = LeftFrame

local ManBtn = Instance.new("TextButton")
ManBtn.Text = "Man"
ManBtn.Font = Enum.Font.GothamBold
ManBtn.TextColor3 = Color3.new(0.8,0.8,0.8)
ManBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ManBtn.Size = UDim2.new(0, 110, 0, 35)
ManBtn.Position = UDim2.new(0, 15, 0, 80)
ManBtn.Parent = LeftFrame
local UICornerM = Instance.new("UICorner"); UICornerM.CornerRadius = UDim.new(0, 10); UICornerM.Parent = ManBtn

-- JANELA DIREITA (Main)
local RightFrame = Instance.new("Frame")
RightFrame.Size = UDim2.new(0, 350, 0, 300)
RightFrame.Position = UDim2.new(0, 160, 0, 10)
RightFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
RightFrame.Parent = Background
local UICornerR = Instance.new("UICorner"); UICornerR.CornerRadius = UDim.new(0, 20); UICornerR.Parent = RightFrame

-- Versão e Fechar
local Ver = Instance.new("TextLabel")
Ver.Text = "v 1.1"
Ver.TextColor3 = Color3.new(1,1,1); Ver.Position = UDim2.new(1, -75, 0, 10); Ver.BackgroundTransparency = 1; Ver.Parent = RightFrame

local Close = Instance.new("TextButton")
Close.Text = "X"; Close.TextColor3 = Color3.new(1,1,1); Close.Position = UDim2.new(1, -35, 0, 10); Close.BackgroundTransparency = 1; Close.Parent = RightFrame
Close.MouseButton1Click:Connect(function() Background.Visible = false end)

-- FUNÇÃO CRIAR TOGGLE (Igual à imagem)
local function AddToggle(name, pos, callback)
	local TFrame = Instance.new("Frame")
	TFrame.Size = UDim2.new(0.9, 0, 0, 35); TFrame.Position = pos; TFrame.BackgroundColor3 = Color3.fromRGB(40,40,40); TFrame.Parent = RightFrame
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

-- ADICIONANDO FUNÇÕES
AddToggle("Esp", UDim2.new(0.05, 0, 0, 40), function(v) Settings.ESP = v end) -- Lógica de ESP aqui
AddToggle("Noclip", UDim2.new(0.05, 0, 0, 85), function(v) Settings.Noclip = v end)

-- DROPDOWN TP (PLAYER)
local TpFrame = Instance.new("Frame")
TpFrame.Size = UDim2.new(0.9, 0, 0, 35); TpFrame.Position = UDim2.new(0.05, 0, 0, 130); TpFrame.BackgroundColor3 = Color3.fromRGB(40,40,40); TpFrame.Parent = RightFrame
Instance.new("UICorner", TpFrame).CornerRadius = UDim.new(0, 10)

local TpLbl = Instance.new("TextLabel")
TpLbl.Text = "Tp Player"; TpLbl.TextColor3 = Color3.new(1,1,1); TpLbl.Position = UDim2.new(0, 15, 0, 0); TpLbl.Size = UDim2.new(0.4, 0, 1, 0); TpLbl.BackgroundTransparency = 1; TpLbl.Parent = TpFrame; TpLbl.TextXAlignment = "Left"

local Arrow = Instance.new("TextLabel")
Arrow.Text = "v"; Arrow.TextColor3 = Color3.new(1,1,1); Arrow.Position = UDim2.new(1, -30, 0, 0); Arrow.Size = UDim2.new(0, 20, 1, 0); Arrow.BackgroundTransparency = 1; Arrow.Parent = TpFrame

local List = Instance.new("ScrollingFrame")
List.Size = UDim2.new(0.5, 0, 0, 120); List.Position = UDim2.new(0.45, 0, 0, 170); List.BackgroundColor3 = Color3.fromRGB(50,50,50); List.Visible = false; List.Parent = RightFrame; List.ZIndex = 10
Instance.new("UIListLayout", List)

TpFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		List.Visible = not List.Visible
		for _, c in pairs(List:GetChildren()) do if c:IsA("TextButton") then c:Destroy() end end
		for _, p in pairs(Players:GetPlayers()) do
			if p ~= LocalPlayer then
				local b = Instance.new("TextButton")
				b.Size = UDim2.new(1,0,0,25); b.Text = p.Name; b.Parent = List
				b.MouseButton1Click:Connect(function() Settings.TargetPlayer = p; TpLbl.Text = p.Name; List.Visible = false end)
			end
		end
	end
end)

-- BOTÃO PRONTO
local Pronto = Instance.new("TextButton")
Pronto.Text = "Pronto"; Pronto.Font = "GothamBold"; Pronto.TextColor3 = Color3.new(1,1,1); Pronto.BackgroundColor3 = Color3.fromRGB(40,40,40); Pronto.Position = UDim2.new(0.05, 0, 0, 175); Pronto.Size = UDim2.new(0.4, 0, 0, 35); Pronto.Parent = RightFrame
Instance.new("UICorner", Pronto).CornerRadius = UDim.new(0, 15)

Pronto.MouseButton1Click:Connect(function()
	if Settings.TargetPlayer and Settings.TargetPlayer.Character then
		LocalPlayer.Character.HumanoidRootPart.CFrame = Settings.TargetPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0,0,3)
	end
end)

-- LÓGICA NOCLIP
RunService.Stepped:Connect(function()
	if Settings.Noclip and LocalPlayer.Character then
		for _, v in pairs(LocalPlayer.Character:GetDescendants()) do
			if v:IsA("BasePart") then v.CanCollide = false end
		end
	end
end)

-- ABRIR HUB
local Open = Instance.new("TextButton")
Open.Text = "Lux hub"; Open.Size = UDim2.new(0, 80, 0, 30); Open.Position = UDim2.new(0, 10, 0.4, 0); Open.Parent = ScreenGui
Open.MouseButton1Click:Connect(function() Background.Visible = not Background.Visible end)
