-- SERVIÇOS
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer

-- CONFIGURAÇÕES
local Settings = {
	Noclip = false,
	TargetPlayer = nil
}

-- --- INTERFACE ---
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LuxHub_Fix"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 500, 0, 300)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -150)
MainFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = false
MainFrame.Parent = ScreenGui
local UICornerM = Instance.new("UICorner"); UICornerM.CornerRadius = UDim.new(0, 20); UICornerM.Parent = MainFrame

-- Título e Versão
local Title = Instance.new("TextLabel")
Title.Text = "Lux hub"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 24
Title.TextColor3 = Color3.fromRGB(220, 220, 220)
Title.Position = UDim2.new(0, 20, 0, 10)
Title.Size = UDim2.new(0, 100, 0, 40)
Title.BackgroundTransparency = 1
Title.Parent = MainFrame

local VersionLabel = Instance.new("TextLabel")
VersionLabel.Text = "v 1.1"
VersionLabel.Font = Enum.Font.Gotham
VersionLabel.TextSize = 14
VersionLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
VersionLabel.Position = UDim2.new(1, -75, 0, 10)
VersionLabel.Size = UDim2.new(0, 40, 0, 30)
VersionLabel.BackgroundTransparency = 1
VersionLabel.Parent = MainFrame

local CloseBtn = Instance.new("TextButton")
CloseBtn.Text = "X"
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Position = UDim2.new(1, -35, 0, 10)
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.BackgroundTransparency = 1
CloseBtn.Parent = MainFrame
CloseBtn.MouseButton1Click:Connect(function() MainFrame.Visible = false end)

-- --- FUNÇÕES ---

-- 1. Lógica do Noclip (Corrigida para não travar)
RunService.Stepped:Connect(function()
	if Settings.Noclip then
		if LocalPlayer.Character then
			for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
				if part:IsA("BasePart") then
					part.CanCollide = false
				end
			end
		end
	end
end)

local NoclipBtn = Instance.new("TextButton")
NoclipBtn.Text = "Noclip: OFF"
NoclipBtn.Position = UDim2.new(0.3, 0, 0.2, 0)
NoclipBtn.Size = UDim2.new(0, 200, 0, 40)
NoclipBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
NoclipBtn.TextColor3 = Color3.new(1,1,1)
NoclipBtn.Parent = MainFrame
local UICornerN = Instance.new("UICorner"); UICornerN.Parent = NoclipBtn

NoclipBtn.MouseButton1Click:Connect(function()
	Settings.Noclip = not Settings.Noclip
	NoclipBtn.Text = Settings.Noclip and "Noclip: ON" or "Noclip: OFF"
	NoclipBtn.BackgroundColor3 = Settings.Noclip and Color3.fromRGB(80, 80, 80) or Color3.fromRGB(30, 30, 30)
end)

-- 2. Dropdown de Players
local DropBtn = Instance.new("TextButton")
DropBtn.Text = "Selecionar Player v"
DropBtn.Position = UDim2.new(0.3, 0, 0.4, 0)
DropBtn.Size = UDim2.new(0, 200, 0, 40)
DropBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
DropBtn.TextColor3 = Color3.new(1,1,1)
DropBtn.Parent = MainFrame
local UICornerD = Instance.new("UICorner"); UICornerD.Parent = DropBtn

local PlayerList = Instance.new("ScrollingFrame")
PlayerList.Size = UDim2.new(0, 200, 0, 100)
PlayerList.Position = UDim2.new(0.3, 0, 0.55, 0)
PlayerList.Visible = false
PlayerList.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
PlayerList.Parent = MainFrame
local UIList = Instance.new("UIListLayout"); UIList.Parent = PlayerList

DropBtn.MouseButton1Click:Connect(function()
	PlayerList.Visible = not PlayerList.Visible
	for _, child in pairs(PlayerList:GetChildren()) do if child:IsA("TextButton") then child:Destroy() end end
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer then
			local b = Instance.new("TextButton")
			b.Size = UDim2.new(1, 0, 0, 30)
			b.Text = p.Name
			b.Parent = PlayerList
			b.MouseButton1Click:Connect(function()
				Settings.TargetPlayer = p
				DropBtn.Text = p.Name
				PlayerList.Visible = false
			end)
		end
	end
end)

-- 3. Teleport (Pronto)
local ProntoBtn = Instance.new("TextButton")
ProntoBtn.Text = "Pronto"
ProntoBtn.Position = UDim2.new(0.4, 0, 0.8, 0)
ProntoBtn.Size = UDim2.new(0, 100, 0, 40)
ProntoBtn.BackgroundColor3 = Color3.fromRGB(20, 100, 20)
ProntoBtn.TextColor3 = Color3.new(1,1,1)
ProntoBtn.Parent = MainFrame

ProntoBtn.MouseButton1Click:Connect(function()
	if Settings.TargetPlayer and Settings.TargetPlayer.Character then
		local char = LocalPlayer.Character
		local tchar = Settings.TargetPlayer.Character
		if char and tchar:FindFirstChild("HumanoidRootPart") then
			char.HumanoidRootPart.CFrame = tchar.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
		end
	end
end)

-- 4. Abrir Hub
local OpenBtn = Instance.new("TextButton")
OpenBtn.Text = "ABRIR LUX"
OpenBtn.Size = UDim2.new(0, 100, 0, 30)
OpenBtn.Position = UDim2.new(0, 10, 0.5, 0)
OpenBtn.Parent = ScreenGui

OpenBtn.MouseButton1Click:Connect(function()
	MainFrame.Visible = not MainFrame.Visible
end)
