-- =========================================================
-- DELTA EXECUTOR - SILENT AIM + CUSTOM CROSSHAIR & WATERMARK
-- Credits: A_1g x ابوعابد🙌
-- =========================================================

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local Workspace = game:GetService("Workspace")
local player = Players.LocalPlayer
local mouse = player:GetMouse()
local camera = Workspace.CurrentCamera

local parentContainer = (gethui and gethui()) or CoreGui or player:WaitForChild("PlayerGui")

if parentContainer:FindFirstChild("AbuAbedDeltaCrosshair") then
	parentContainer.AbuAbedDeltaCrosshair:Destroy()
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AbuAbedDeltaCrosshair"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = parentContainer

-- 1. شريط الحقوق والشعار
local watermark = Instance.new("TextLabel")
watermark.Name = "CreditsWatermark"
watermark.Size = UDim2.new(0, 220, 0, 32)
watermark.AnchorPoint = Vector2.new(0.5, 0)
watermark.Position = UDim2.new(0.5, 0, 0, 45)
watermark.BackgroundColor3 = Color3.fromRGB(18, 18, 28)
watermark.BackgroundTransparency = 0.2
watermark.Text = "A_1g x ابوعابد🙌"
watermark.TextColor3 = Color3.fromRGB(0, 255, 220)
watermark.TextSize = 16
watermark.Font = Enum.Font.GothamBold
watermark.Parent = screenGui

local uiCornerWM = Instance.new("UICorner")
uiCornerWM.CornerRadius = UDim.new(0, 8)
uiCornerWM.Parent = watermark

local uiStrokeWM = Instance.new("UIStroke")
uiStrokeWM.Color = Color3.fromRGB(0, 255, 220)
uiStrokeWM.Thickness = 1.5
uiStrokeWM.Parent = watermark

-- 2. دائرة النطاق للسايلنت ايم (FOV Circle)
local silentAimFOV = 130 -- نطاق الاستهداف بالبكسل

local fovCircle = Instance.new("Frame")
fovCircle.Name = "FOVCircle"
fovCircle.AnchorPoint = Vector2.new(0.5, 0.5)
fovCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
fovCircle.Size = UDim2.new(0, silentAimFOV * 2, 0, silentAimFOV * 2)
fovCircle.BackgroundTransparency = 1
fovCircle.Visible = false
fovCircle.Parent = screenGui

local fovCorner = Instance.new("UICorner")
fovCorner.CornerRadius = UDim.new(1, 0)
fovCorner.Parent = fovCircle

local fovStroke = Instance.new("UIStroke")
fovStroke.Color = Color3.fromRGB(255, 0, 100)
fovStroke.Thickness = 1.5
fovStroke.Transparency = 0.4
fovStroke.Parent = fovCircle

-- 3. حاوية المنتصف للكروسهير
local center = Instance.new("Frame")
center.Size = UDim2.new(0, 0, 0, 0)
center.Position = UDim2.new(0.5, 0, 0.5, 0)
center.BackgroundTransparency = 1
center.Parent = screenGui

local currentColor = Color3.fromRGB(0, 255, 150)
local currentScale = 1.0

-- عناصر الكروسهير
local dot = Instance.new("Frame")
dot.AnchorPoint = Vector2.new(0.5, 0.5)
dot.BackgroundColor3 = currentColor
dot.BorderSizePixel = 0
dot.Parent = center
local dotCorner = Instance.new("UICorner")
dotCorner.CornerRadius = UDim.new(1, 0)
dotCorner.Parent = dot

local top = Instance.new("Frame")
top.AnchorPoint = Vector2.new(0.5, 1)
top.BackgroundColor3 = currentColor
top.BorderSizePixel = 0
top.Parent = center

local bottom = Instance.new("Frame")
bottom.AnchorPoint = Vector2.new(0.5, 0)
bottom.BackgroundColor3 = currentColor
bottom.BorderSizePixel = 0
bottom.Parent = center

local left = Instance.new("Frame")
left.AnchorPoint = Vector2.new(1, 0.5)
left.BackgroundColor3 = currentColor
left.BorderSizePixel = 0
left.Parent = center

local right = Instance.new("Frame")
right.AnchorPoint = Vector2.new(0, 0.5)
right.BackgroundColor3 = currentColor
right.BorderSizePixel = 0
right.Parent = center

local circle = Instance.new("Frame")
circle.AnchorPoint = Vector2.new(0.5, 0.5)
circle.BackgroundTransparency = 1
circle.Parent = center

local circleStroke = Instance.new("UIStroke")
circleStroke.Color = currentColor
circleStroke.Parent = circle

local circleCorner = Instance.new("UICorner")
circleCorner.CornerRadius = UDim.new(1, 0)
circleCorner.Parent = circle

-- 4. دالة تحديث الأشكال والأحجام
local shapes = {"Plus (+)", "Cross", "Dot", "Circle", "CrossDot"}
local currentShapeIndex = 1

local function updateSize()
	local s = currentScale
	dot.Size = UDim2.new(0, 6 * s, 0, 6 * s)
	dot.Position = UDim2.new(0, 0, 0, 0)

	local currentShape = shapes[currentShapeIndex]
	local gap = (currentShape == "Plus (+)") and 0 or (4 * s)
	local len = 10 * s
	local thick = math.max(2, 2 * s)

	top.Size = UDim2.new(0, thick, 0, len)
	top.Position = UDim2.new(0, 0, 0, -gap)

	bottom.Size = UDim2.new(0, thick, 0, len)
	bottom.Position = UDim2.new(0, 0, 0, gap)

	left.Size = UDim2.new(0, len, 0, thick)
	left.Position = UDim2.new(0, -gap, 0, 0)

	right.Size = UDim2.new(0, len, 0, thick)
	right.Position = UDim2.new(0, gap, 0, 0)

	circle.Size = UDim2.new(0, 20 * s, 0, 20 * s)
	circle.Position = UDim2.new(0, 0, 0, 0)
	circleStroke.Thickness = math.max(1.5, 2 * s)
end

local function applyShape(shape)
	dot.Visible = (shape == "Dot" or shape == "CrossDot")
	top.Visible = (shape == "Plus (+)" or shape == "Cross" or shape == "CrossDot")
	bottom.Visible = (shape == "Plus (+)" or shape == "Cross" or shape == "CrossDot")
	left.Visible = (shape == "Plus (+)" or shape == "Cross" or shape == "CrossDot")
	right.Visible = (shape == "Plus (+)" or shape == "Cross" or shape == "CrossDot")
	circle.Visible = (shape == "Circle")
	updateSize()
end

local colors = {
	Color3.fromRGB(0, 255, 150),
	Color3.fromRGB(255, 0, 80),
	Color3.fromRGB(0, 180, 255),
	Color3.fromRGB(255, 230, 0),
	Color3.fromRGB(200, 50, 255),
	Color3.fromRGB(255, 255, 255)
}
local currentColorIndex = 1

local function applyColor(col)
	currentColor = col
	dot.BackgroundColor3 = col
	top.BackgroundColor3 = col
	bottom.BackgroundColor3 = col
	left.BackgroundColor3 = col
	right.BackgroundColor3 = col
	circleStroke.Color = col
end

local sizes = {
	{name = "Small", scale = 0.75},
	{name = "Medium", scale = 1.0},
	{name = "Large", scale = 1.4},
	{name = "XL", scale = 1.8}
}
local currentSizeIndex = 2

applyShape(shapes[1])
applyColor(colors[1])

-- 5. نظام الـ Silent Aim (استهداف مجالي)
local silentAimEnabled = false

local function getClosestEnemyInFOV()
	local closestTarget = nil
	local shortestDistance = silentAimFOV
	local viewportCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)

	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= player and p.Character and p.Character:FindFirstChild("Humanoid") and p.Character.Humanoid.Health > 0 then
			if player.Team == nil or p.Team ~= player.Team then
				local head = p.Character:FindFirstChild("Head") or p.Character:FindFirstChild("HumanoidRootPart")
				if head then
					local screenPos, onScreen = camera:WorldToViewportPoint(head.Position)
					if onScreen then
						local dist = (Vector2.new(screenPos.X, screenPos.Y) - viewportCenter).Magnitude
						if dist <= shortestDistance then
							shortestDistance = dist
							closestTarget = head
						end
					end
				end
			end
		end
	end
	return closestTarget
end

-- هوك الماوس للتوجيه التلقائي داخل النطاق
local oldIndex
if hookmetamethod then
	oldIndex = hookmetamethod(game, "__index", newcclosure(function(self, key)
		if not checkcaller() and silentAimEnabled and self == mouse and (key == "Hit" or key == "Target") then
			local targetHead = getClosestEnemyInFOV()
			if targetHead then
				if key == "Hit" then
					return targetHead.CFrame
				elseif key == "Target" then
					return targetHead
				end
			end
		end
		return oldIndex(self, key)
	end))
end

-- 6. قائمة التحكم الجانبية بالتصميم والألوان الجديدة
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0, 140, 0, 165)
menu.Position = UDim2.new(0, 10, 0.3, 0)
menu.BackgroundColor3 = Color3.fromRGB(15, 18, 26)
menu.BackgroundTransparency = 0.15
menu.Parent = screenGui

local menuCorner = Instance.new("UICorner")
menuCorner.CornerRadius = UDim.new(0, 10)
menuCorner.Parent = menu

local menuStroke = Instance.new("UIStroke")
menuStroke.Color = Color3.fromRGB(0, 220, 255)
menuStroke.Thickness = 1.2
menuStroke.Parent = menu

local function createStyledButton(text, pos, bgColor, strokeColor)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(0.9, 0, 0.2, 0)
	btn.Position = pos
	btn.Text = text
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.BackgroundColor3 = bgColor
	btn.TextScaled = true
	btn.Font = Enum.Font.GothamMedium
	btn.Parent = menu

	local btnCorner = Instance.new("UICorner")
	btnCorner.CornerRadius = UDim.new(0, 6)
	btnCorner.Parent = btn

	local btnStroke = Instance.new("UIStroke")
	btnStroke.Color = strokeColor
	btnStroke.Thickness = 1
	btnStroke.Parent = btn

	return btn
end

-- أزرار التحكم
local shapeBtn = createStyledButton("Shape: Plus (+)", UDim2.new(0.05, 0, 0.04, 0), Color3.fromRGB(28, 32, 48), Color3.fromRGB(0, 200, 255))
shapeBtn.MouseButton1Click:Connect(function()
	currentShapeIndex = (currentShapeIndex % #shapes) + 1
	local newShape = shapes[currentShapeIndex]
	shapeBtn.Text = "Shape: " .. newShape
	applyShape(newShape)
end)

local sizeBtn = createStyledButton("Size: Medium", UDim2.new(0.05, 0, 0.28, 0), Color3.fromRGB(28, 32, 48), Color3.fromRGB(0, 200, 255))
sizeBtn.MouseButton1Click:Connect(function()
	currentSizeIndex = (currentSizeIndex % #sizes) + 1
	local sz = sizes[currentSizeIndex]
	sizeBtn.Text = "Size: " .. sz.name
	currentScale = sz.scale
	updateSize()
end)

local colorBtn = createStyledButton("Color 🎨", UDim2.new(0.05, 0, 0.52, 0), Color3.fromRGB(28, 32, 48), Color3.fromRGB(0, 200, 255))
colorBtn.MouseButton1Click:Connect(function()
	currentColorIndex = (currentColorIndex % #colors) + 1
	applyColor(colors[currentColorIndex])
end)

local silentBtn = createStyledButton("Silent Aim: OFF", UDim2.new(0.05, 0, 0.76, 0), Color3.fromRGB(45, 20, 30), Color3.fromRGB(255, 50, 100))
silentBtn.MouseButton1Click:Connect(function()
	silentAimEnabled = not silentAimEnabled
	fovCircle.Visible = silentAimEnabled
	if silentAimEnabled then
		silentBtn.Text = "Silent Aim: ON 🎯"
		silentBtn.BackgroundColor3 = Color3.fromRGB(15, 45, 30)
		silentBtn.UIStroke.Color = Color3.fromRGB(0, 255, 150)
	else
		silentBtn.Text = "Silent Aim: OFF"
		silentBtn.BackgroundColor3 = Color3.fromRGB(45, 20, 30)
		silentBtn.UIStroke.Color = Color3.fromRGB(255, 50, 100)
	end
end)
