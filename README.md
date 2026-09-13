-- =========================================================
-- DELTA EXECUTOR - PERFECT CENTER CROSSHAIR + SIZES & WATERMARK
-- Credits: A_1g x ابوعابد🙌
-- =========================================================

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local player = Players.LocalPlayer

local parentContainer = (gethui and gethui()) or CoreGui or player:WaitForChild("PlayerGui")

if parentContainer:FindFirstChild("AbuAbedDeltaCrosshair") then
	parentContainer.AbuAbedDeltaCrosshair:Destroy()
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AbuAbedDeltaCrosshair"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true -- إغلاق الفارق العلوي لضبط المنتصف بدقة
screenGui.Parent = parentContainer

-- 1. شريط الحقوق والشعار
local watermark = Instance.new("TextLabel")
watermark.Name = "CreditsWatermark"
watermark.Size = UDim2.new(0, 220, 0, 32)
watermark.AnchorPoint = Vector2.new(0.5, 0)
watermark.Position = UDim2.new(0.5, 0, 0, 45)
watermark.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
watermark.BackgroundTransparency = 0.25
watermark.Text = "A_1g x ابوعابد🙌"
watermark.TextColor3 = Color3.fromRGB(0, 255, 200)
watermark.TextSize = 16
watermark.Font = Enum.Font.GothamBold
watermark.Parent = screenGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 8)
uiCorner.Parent = watermark

local uiStroke = Instance.new("UIStroke")
uiStroke.Color = Color3.fromRGB(0, 255, 200)
uiStroke.Thickness = 1.5
uiStroke.Parent = watermark

-- 2. حاوية المنتصف الدقيقة
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

-- 3. دالة تحديث الأحجام
local function updateSize()
	local s = currentScale
	dot.Size = UDim2.new(0, 6 * s, 0, 6 * s)
	dot.Position = UDim2.new(0, 0, 0, 0)

	local gap = 4 * s
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

-- 4. التحكم في الأشكال والألوان والأحجام
local shapes = {"Cross", "Dot", "Circle", "CrossDot"}
local currentShapeIndex = 1

local function applyShape(shape)
	dot.Visible = (shape == "Dot" or shape == "CrossDot")
	top.Visible = (shape == "Cross" or shape == "CrossDot")
	bottom.Visible = (shape == "Cross" or shape == "CrossDot")
	left.Visible = (shape == "Cross" or shape == "CrossDot")
	right.Visible = (shape == "Cross" or shape == "CrossDot")
	circle.Visible = (shape == "Circle")
end

local colors = {
	Color3.fromRGB(0, 255, 150),
	Color3.fromRGB(255, 0, 0),
	Color3.fromRGB(0, 170, 255),
	Color3.fromRGB(255, 255, 0),
	Color3.fromRGB(255, 0, 255),
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

applyShape("Cross")
applyColor(colors[1])
updateSize()

-- 5. قائمة التحكم الجانبية
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0, 130, 0, 125)
menu.Position = UDim2.new(0, 10, 0.35, 0)
menu.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
menu.BackgroundTransparency = 0.3
menu.Parent = screenGui

local menuCorner = Instance.new("UICorner")
menuCorner.CornerRadius = UDim.new(0, 8)
menuCorner.Parent = menu

-- زر الشكل
local shapeBtn = Instance.new("TextButton")
shapeBtn.Size = UDim2.new(0.9, 0, 0.27, 0)
shapeBtn.Position = UDim2.new(0.05, 0, 0.05, 0)
shapeBtn.Text = "Shape: Cross"
shapeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
shapeBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
shapeBtn.TextScaled = true
shapeBtn.Parent = menu
local b1 = Instance.new("UICorner"); b1.CornerRadius = UDim.new(0, 6); b1.Parent = shapeBtn

shapeBtn.MouseButton1Click:Connect(function()
	currentShapeIndex = (currentShapeIndex % #shapes) + 1
	local newShape = shapes[currentShapeIndex]
	shapeBtn.Text = "Shape: " .. newShape
	applyShape(newShape)
end)

-- زر الحجم
local sizeBtn = Instance.new("TextButton")
sizeBtn.Size = UDim2.new(0.9, 0, 0.27, 0)
sizeBtn.Position = UDim2.new(0.05, 0, 0.36, 0)
sizeBtn.Text = "Size: Medium"
sizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
sizeBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
sizeBtn.TextScaled = true
sizeBtn.Parent = menu
local b2 = Instance.new("UICorner"); b2.CornerRadius = UDim.new(0, 6); b2.Parent = sizeBtn

sizeBtn.MouseButton1Click:Connect(function()
	currentSizeIndex = (currentSizeIndex % #sizes) + 1
	local sz = sizes[currentSizeIndex]
	sizeBtn.Text = "Size: " .. sz.name
	currentScale = sz.scale
	updateSize()
end)

-- زر اللون
local colorBtn = Instance.new("TextButton")
colorBtn.Size = UDim2.new(0.9, 0, 0.27, 0)
colorBtn.Position = UDim2.new(0.05, 0, 0.67, 0)
colorBtn.Text = "Color 🎨"
colorBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
colorBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
colorBtn.TextScaled = true
colorBtn.Parent = menu
local b3 = Instance.new("UICorner"); b3.CornerRadius = UDim.new(0, 6); b3.Parent = colorBtn

colorBtn.MouseButton1Click:Connect(function()
	currentColorIndex = (currentColorIndex % #colors) + 1
	applyColor(colors[currentColorIndex])
end)
