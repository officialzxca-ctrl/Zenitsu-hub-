--// ZENITSU HUB (MENU VERSION)
-- Infinite Jump + Anti Die + Secret Brainrot ESP

---------------- SERVICES ----------------
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

---------------- PLAYER ----------------
local lp = Players.LocalPlayer
local char, hum, root

---------------- STATES ----------------
local infiniteJump = true
local secretESP = true
local MAX_FALL_SPEED = -50

---------------- SECRET BRAINROT KEYS ----------------
local SECRET_KEYWORDS = {"secret","brainrot","rare"}

---------------- CHARACTER SETUP ----------------
local function setupCharacter(c)
	char = c
	hum = c:WaitForChild("Humanoid")
	root = c:WaitForChild("HumanoidRootPart")

	pcall(function()
		hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown,false)
		hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll,false)
		hum:SetStateEnabled(Enum.HumanoidStateType.Physics,false)
	end)

	hum.BreakJointsOnDeath = false
	hum.RequiresNeck = false
end

if lp.Character then setupCharacter(lp.Character) end
lp.CharacterAdded:Connect(setupCharacter)

---------------- INFINITE JUMP ----------------
UIS.JumpRequest:Connect(function()
	if infiniteJump and hum and hum.Health > 0 then
		hum:ChangeState(Enum.HumanoidStateType.Jumping)
	end
end)

---------------- ANTI DIE ----------------
RunService.Heartbeat:Connect(function()
	if hum and root and hum.Health > 0 then
		if root.Velocity.Y < MAX_FALL_SPEED then
			root.Velocity = Vector3.new(root.Velocity.X, MAX_FALL_SPEED, root.Velocity.Z)
		end
		if hum:GetState() == Enum.HumanoidStateType.Freefall then
			hum:ChangeState(Enum.HumanoidStateType.Running)
		end
	end
end)

---------------- SECRET BRAINROT ESP ----------------
local function isSecret(obj)
	local n = string.lower(obj.Name)
	for _,k in ipairs(SECRET_KEYWORDS) do
		if string.find(n,k) then return true end
	end
	return false
end

local function updateESP()
	if not secretESP then return end
	for _,v in ipairs(workspace:GetDescendants()) do
		if (v:IsA("Model") or v:IsA("BasePart")) and isSecret(v) then
			if not v:FindFirstChild("ZenSecretESP") then
				local h = Instance.new("Highlight")
				h.Name = "ZenSecretESP"
				h.FillColor = Color3.fromRGB(255,145,0)
				h.OutlineColor = Color3.new(1,1,1)
				h.FillTransparency = 0.35
				h.Parent = v
			end
		end
	end
end

task.spawn(function()
	while true do
		updateESP()
		task.wait(2)
	end
end)

---------------- GUI ----------------
local gui = Instance.new("ScreenGui", lp.PlayerGui)
gui.Name = "ZenitsuHub"

local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0,220,0,160)
main.Position = UDim2.new(0.02,0,0.35,0)
main.BackgroundColor3 = Color3.fromRGB(20,20,20)
main.Active = true
main.Draggable = true

local stroke = Instance.new("UIStroke", main)
stroke.Color = Color3.fromRGB(255,145,0)
stroke.Thickness = 2

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1,0,0,28)
title.Text = "⚡ Zenitsu Hub"
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255,145,0)
title.Font = Enum.Font.GothamBold
title.TextSize = 14

-- CLOSE BUTTON
local close = Instance.new("TextButton", main)
close.Size = UDim2.new(0,24,0,24)
close.Position = UDim2.new(1,-28,0,2)
close.Text = "X"
close.Font = Enum.Font.GothamBold
close.TextSize = 12
close.BackgroundColor3 = Color3.fromRGB(255,70,70)
close.TextColor3 = Color3.new(1,1,1)

close.MouseButton1Click:Connect(function()
	main.Visible = false
end)

---------------- TOGGLE BUTTONS ----------------
local function toggleBtn(text, posY, callback)
	local b = Instance.new("TextButton", main)
	b.Size = UDim2.new(0.9,0,0,28)
	b.Position = UDim2.new(0.05,0,0,posY)
	b.Text = text.." : ON"
	b.Font = Enum.Font.Gotham
	b.TextSize = 12
	b.BackgroundColor3 = Color3.fromRGB(35,35,35)
	b.TextColor3 = Color3.new(1,1,1)

	local on = true
	b.MouseButton1Click:Connect(function()
		on = not on
		b.Text = text.." : "..(on and "ON" or "OFF")
		callback(on)
	end)
end

toggleBtn("Infinite Jump", 0.3, function(v)
	infiniteJump = v
end)

toggleBtn("Secret Brainrot ESP", 0.5, function(v)
	secretESP = v
end)

---------------- MENU TOGGLE ----------------
UIS.InputBegan:Connect(function(input,gp)
	if gp then return end
	if input.KeyCode == Enum.KeyCode.RightShift then
		main.Visible = not main.Visible
	end
end)
