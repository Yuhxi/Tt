-- Delta Executor Universal Script (All-In-One)
-- Features: Aimbot + FOV Circle + Custom Shapes & Sizes + Centered UI + Watermark
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local parentGui = gethui and gethui() or CoreGui

-- تنظيف أي نسخ سابقة للسكربت
if parentGui:FindFirstChild("DeltaMasterGui") then
    parentGui.DeltaMasterGui:Destroy()
end

-- جدول الإعدادات
local Settings = {
    AimbotEnabled = false,
    AimbotFOV = 120,
    ShowFOV = true,
    CrosshairSize = 12,
    CrosshairShape = "Cross", -- "Cross", "Dot", "Cross+Dot"
    CrosshairColor = Color3.fromRGB(0, 255, 170),
    CrosshairVisible = true
}

-- الشاشة الرئيسية
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "DeltaMasterGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = parentGui

-- 1. شعار التوقيع (A_1g x ابوعابد🙌)
local watermark = Instance.new("TextLabel")
watermark.Name = "Watermark"
watermark.AnchorPoint = Vector2.new(0.5, 0)
watermark.Position = UDim2.new(0.5, 0, 0.02, 0)
watermark.Size = UDim2.new(0, 240, 0, 35)
watermark.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
watermark.BackgroundTransparency = 0.2
watermark.Text = "A_1g x ابوعابد🙌"
watermark.TextColor3 = Color3.fromRGB(0, 255, 170)
watermark.TextSize = 18
watermark.Font = Enum.Font.SourceSansBold
watermark.Parent = screenGui

Instance.new("UICorner", watermark).CornerRadius = UDim.new(0, 8)
local wmStroke = Instance.new("UIStroke", watermark)
wmStroke.Color = Color3.fromRGB(0, 255, 170)
wmStroke.Thickness = 1.5

-- 2. زر عائم لفتح وإغلاق القائمة
local toggleBtn = Instance.new("TextButton")
toggleBtn.Name = "OpenToggle"
toggleBtn.Position = UDim2.new(0.03, 0, 0.2, 0)
toggleBtn.Size = UDim2.new(0, 45, 0, 45)
toggleBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
toggleBtn.Text = "⚙️"
toggleBtn.TextSize = 22
toggleBtn.Parent = screenGui
Instance.new("UICorner", toggleBtn).CornerRadius = UDim.new(0, 22)
local tgStroke = Instance.new("UIStroke", toggleBtn)
tgStroke.Color = Color3.fromRGB(0, 255, 170)

-- 3. حاوية الكروس هير في منتصف الشاشة
local crosshairFrame = Instance.new("Frame")
crosshairFrame.Name = "Crosshair"
crosshairFrame.AnchorPoint = Vector2.new(0.5, 0.5)
crosshairFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
crosshairFrame.Size = UDim2.new(0, 0, 0, 0)
crosshairFrame.BackgroundTransparency = 1
crosshairFrame.Parent = screenGui

local topL = Instance.new("Frame", crosshairFrame)
local botL = Instance.new("Frame", crosshairFrame)
local leftL = Instance.new("Frame", crosshairFrame)
local rightL = Instance.new("Frame", crosshairFrame)
local dotL = Instance.new("Frame", crosshairFrame)

local function updateCrosshair()
    local sz = Settings.CrosshairSize
    local th = 2
    local gap = 4
    local col = Settings.CrosshairColor
    local shape = Settings.CrosshairShape
    
    topL.Visible = (shape == "Cross" or shape == "Cross+Dot")
    botL.Visible = (shape == "Cross" or shape == "Cross+Dot")
    leftL.Visible = (shape == "Cross" or shape == "Cross+Dot")
    rightL.Visible = (shape == "Cross" or shape == "Cross+Dot")
    dotL.Visible = (shape == "Dot" or shape == "Cross+Dot")

    -- رسم أجزاء Cross (+)
    topL.Size = UDim2.new(0, th, 0, sz)
    topL.Position = UDim2.new(0.5, -th/2, 0.5, -(gap + sz))
    topL.BackgroundColor3 = col
    topL.BorderSizePixel = 0

    botL.Size = UDim2.new(0, th, 0, sz)
    botL.Position = UDim2.new(0.5, -th/2, 0.5, gap)
    botL.BackgroundColor3 = col
    botL.BorderSizePixel = 0

    leftL.Size = UDim2.new(0, sz, 0, th)
    leftL.Position = UDim2.new(0.5, -(gap + sz), 0.5, -th/2)
    leftL.BackgroundColor3 = col
    leftL.BorderSizePixel = 0

    rightL.Size = UDim2.new(0, sz, 0, th)
    rightL.Position = UDim2.new(0.5, gap, 0.5, -th/2)
    rightL.BackgroundColor3 = col
    rightL.BorderSizePixel = 0

    -- رسم النقطة (Dot)
    local dotSize = math.clamp(math.floor(sz / 2.5), 4, 20)
    dotL.Size = UDim2.new(0, dotSize, 0, dotSize)
    dotL.Position = UDim2.new(0.5, -dotSize/2, 0.5, -dotSize/2)
    dotL.BackgroundColor3 = col
    dotL.BorderSizePixel = 0
    
    local dotCorner = dotL:FindFirstChildOfClass("UICorner") or Instance.new("UICorner", dotL)
    dotCorner.CornerRadius = UDim.new(1, 0)
end
updateCrosshair()

-- 4. دائرة نطاق الإيم بوت (FOV Circle)
local fovCircle = Instance.new("Frame")
fovCircle.Name = "FOVCircle"
fovCircle.AnchorPoint = Vector2.new(0.5, 0.5)
fovCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
fovCircle.BackgroundTransparency = 1
fovCircle.Parent = screenGui

Instance.new("UICorner", fovCircle).CornerRadius = UDim.new(1, 0)
local fovStroke = Instance.new("UIStroke", fovCircle)
fovStroke.Color = Color3.fromRGB(0, 255, 170)
fovStroke.Thickness = 1.5
fovStroke.Transparency = 0.3

local function updateFOV()
    fovCircle.Size = UDim2.new(0, Settings.AimbotFOV * 2, 0, Settings.AimbotFOV * 2)
    fovCircle.Visible = Settings.ShowFOV and Settings.AimbotEnabled
end
updateFOV()

-- 5. القائمة الرئيسية في منتصف الشاشة
local mainPanel = Instance.new("Frame")
mainPanel.Name = "MainPanel"
mainPanel.AnchorPoint = Vector2.new(0.5, 0.5)
mainPanel.Position = UDim2.new(0.5, 0, 0.5, 0)
mainPanel.Size = UDim2.new(0, 270, 0, 320)
mainPanel.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
mainPanel.BackgroundTransparency = 0.1
mainPanel.Parent = screenGui

Instance.new("UICorner", mainPanel).CornerRadius = UDim.new(0, 12)
local panelStroke = Instance.new("UIStroke", mainPanel)
panelStroke.Color = Color3.fromRGB(0, 255, 170)
panelStroke.Thickness = 1.5

toggleBtn.MouseButton1Click:Connect(function()
    mainPanel.Visible = not mainPanel.Visible
end)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 35)
title.Text = "لوحة التحكم الشاملة"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18
title.BackgroundTransparency = 1
title.Parent = mainPanel

local function createButton(text, pos, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 32)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.Parent = mainPanel
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    btn.MouseButton1Click:Connect(function() callback(btn) end)
    return btn
end

-- زر الإيم بوت
local aimBtn = createButton("Aimbot: OFF", UDim2.new(0.05, 0, 0.13, 0), function(btn)
    Settings.AimbotEnabled = not Settings.AimbotEnabled
    btn.Text = Settings.AimbotEnabled and "Aimbot: ON" or "Aimbot: OFF"
    btn.BackgroundColor3 = Settings.AimbotEnabled and Color3.fromRGB(0, 180, 90) or Color3.fromRGB(180, 50, 50)
    updateFOV()
end)
aimBtn.BackgroundColor3 = Color3.fromRGB(180, 50, 50)

-- زر إظهار دائرة FOV
local fovBtn = createButton("دائرة FOV: مفعلة", UDim2.new(0.05, 0, 0.27, 0), function(btn)
    Settings.ShowFOV = not Settings.ShowFOV
    btn.Text = Settings.ShowFOV and "دائرة FOV: مفعلة" or "دائرة FOV: معطلة"
    updateFOV()
end)

-- زر تغيير شكل الكروس هير
local shapes = {"Cross", "Dot", "Cross+Dot"}
local shapeIndex = 1
local shapeBtn = createButton("الشكل: Cross (+)", UDim2.new(0.05, 0, 0.41, 0), function(btn)
    shapeIndex = (shapeIndex % #shapes) + 1
    Settings.CrosshairShape = shapes[shapeIndex]
    btn.Text = "الشكل: " .. Settings.CrosshairShape
    updateCrosshair()
end)

-- مربع تحديد الحجم (مثال: 7, 12, 72)
local sizeLabel = Instance.new("TextLabel")
sizeLabel.Size = UDim2.new(0.5, 0, 0, 32)
sizeLabel.Position = UDim2.new(0.05, 0, 0.55, 0)
sizeLabel.Text = "الحجم (7, 12, 72):"
sizeLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
sizeLabel.Font = Enum.Font.SourceSans
sizeLabel.TextSize = 13
sizeLabel.BackgroundTransparency = 1
sizeLabel.Parent = mainPanel

local sizeBox = Instance.new("TextBox")
sizeBox.Size = UDim2.new(0.35, 0, 0, 32)
sizeBox.Position = UDim2.new(0.6, 0, 0.55, 0)
sizeBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
sizeBox.Text = tostring(Settings.CrosshairSize)
sizeBox.TextColor3 = Color3.fromRGB(0, 255, 170)
sizeBox.Font = Enum.Font.SourceSansBold
sizeBox.TextSize = 15
sizeBox.Parent = mainPanel
Instance.new("UICorner", sizeBox).CornerRadius = UDim.new(0, 6)

sizeBox.FocusLost:Connect(function()
    local val = tonumber(sizeBox.Text)
    if val and val > 0 then
        Settings.CrosshairSize = val
        updateCrosshair()
    else
        sizeBox.Text = tostring(Settings.CrosshairSize)
    end
end)

-- تبديل الألوان
local colors = {
    {name = "أخضر نيوني", color = Color3.fromRGB(0, 255, 170)},
    {name = "أحمر", color = Color3.fromRGB(255, 50, 50)},
    {name = "أزرق", color = Color3.fromRGB(50, 150, 255)},
    {name = "أصفر", color = Color3.fromRGB(255, 230, 0)},
    {name = "أبيض", color = Color3.fromRGB(255, 255, 255)}
}
local colorIdx = 1
local colorBtn = createButton("اللون: أخضر نيوني 🎨", UDim2.new(0.05, 0, 0.69, 0), function(btn)
    colorIdx = (colorIdx % #colors) + 1
    Settings.CrosshairColor = colors[colorIdx].color
    btn.Text = "اللون: " .. colors[colorIdx].name .. " 🎨"
    updateCrosshair()
end)

-- زر إغلاق القائمة
local closeBtn = createButton("إغلاق القائمة", UDim2.new(0.05, 0, 0.83, 0), function()
    mainPanel.Visible = false
end)
closeBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

-- 6. محرك الإيم بوت (Aimbot Logic)
local function getClosestPlayer()
    local closest = nil
    local maxDist = Settings.AimbotFOV

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") and player.Character:FindFirstChildOfClass("Humanoid") and player.Character.Humanoid.Health > 0 then
            local head = player.Character.Head
            local screenPos, onScreen = Camera:WorldToViewportPoint(head.Position)
            
            if onScreen then
                local dist = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                if dist < maxDist then
                    maxDist = dist
                    closest = head
                end
            end
        end
    end
    return closest
end

RunService.RenderStepped:Connect(function()
    if Settings.AimbotEnabled then
        local targetHead = getClosestPlayer()
        if targetHead then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetHead.Position)
        end
    end
end)
