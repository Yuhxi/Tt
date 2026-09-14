-- Delta Executor Universal Script (Aimbot + Custom Crosshair + Lalo Salamanca BG + Fixed UI)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local parentGui = gethui and gethui() or CoreGui

-- 1. تنظيف شامل لجميع الواجهات والسكربتات القديمة (سواء في CoreGui أو PlayerGui)
local function cleanup()
    local targets = {parentGui, LocalPlayer:FindFirstChild("PlayerGui")}
    for _, folder in pairs(targets) do
        if folder then
            for _, child in pairs(folder:GetChildren()) do
                if child:IsA("ScreenGui") and (
                    child.Name:find("Delta") or 
                    child.Name:find("Crosshair") or 
                    child.Name:find("Custom")
                ) then
                    child:Destroy()
                end
            end
        end
    end
end
cleanup()

-- جدول الإعدادات
local Settings = {
    AimbotEnabled = false,
    AimbotFOV = 120,
    ShowFOV = true,
    CrosshairSize = 15, -- الحجم المسموح من 1 إلى 100
    CrosshairShape = "Cross (+)", -- "Cross (+)", "Dot (•)", "Cross + Dot"
    CrosshairColor = Color3.fromRGB(0, 255, 170)
}

-- تحميل صورة الخلفية (لالو سالامانكا) وتخزينها محلياً لدلتا
local bgAssetId = ""
local fileName = "LaloBackground.png"

if writefile and getcustomasset then
    if not isfile(fileName) then
        pcall(function()
            writefile(fileName, game:HttpGet("https://raw.githubusercontent.com/Yuhxi/Tt/main/lalo.png"))
        end)
    end
    pcall(function()
        bgAssetId = getcustomasset(fileName)
    end)
end

-- إنشاء الشاشة الرئيسية
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "DeltaMasterGui_V2"
screenGui.ResetOnSpawn = false
screenGui.Parent = parentGui

-- 2. شعار التوقيع العلوي
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
watermark.ZIndex = 10
watermark.Parent = screenGui

Instance.new("UICorner", watermark).CornerRadius = UDim.new(0, 8)
local wmStroke = Instance.new("UIStroke", watermark)
wmStroke.Color = Color3.fromRGB(0, 255, 170)
wmStroke.Thickness = 1.5

-- 3. زر الفتح والإغلاق العائم (⚙️)
local toggleBtn = Instance.new("TextButton")
toggleBtn.Name = "OpenToggle"
toggleBtn.Position = UDim2.new(0.03, 0, 0.2, 0)
toggleBtn.Size = UDim2.new(0, 45, 0, 45)
toggleBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
toggleBtn.Text = "⚙️"
toggleBtn.TextSize = 22
toggleBtn.ZIndex = 10
toggleBtn.Parent = screenGui
Instance.new("UICorner", toggleBtn).CornerRadius = UDim.new(0, 22)
local tgStroke = Instance.new("UIStroke", toggleBtn)
tgStroke.Color = Color3.fromRGB(0, 255, 170)

-- 4. الكروس هير (+) في منتصف الشاشة بالدقة الحسابية الصحيحة
local centerFrame = Instance.new("Frame")
centerFrame.Name = "CrosshairCenter"
centerFrame.AnchorPoint = Vector2.new(0.5, 0.5)
centerFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
centerFrame.Size = UDim2.new(0, 0, 0, 0)
centerFrame.BackgroundTransparency = 1
centerFrame.ZIndex = 5
centerFrame.Parent = screenGui

local topLine = Instance.new("Frame", centerFrame)
local botLine = Instance.new("Frame", centerFrame)
local leftLine = Instance.new("Frame", centerFrame)
local rightLine = Instance.new("Frame", centerFrame)
local dotFrame = Instance.new("Frame", centerFrame)

local function updateCrosshair()
    local sz = Settings.CrosshairSize
    local th = 2
    local gap = 3
    local col = Settings.CrosshairColor
    local shape = Settings.CrosshairShape
    
    local showCross = (shape == "Cross (+)" or shape == "Cross + Dot")
    local showDot = (shape == "Dot (•)" or shape == "Cross + Dot")
    
    topLine.Visible = showCross
    topLine.AnchorPoint = Vector2.new(0.5, 1)
    topLine.Size = UDim2.new(0, th, 0, sz)
    topLine.Position = UDim2.new(0.5, 0, 0.5, -gap)
    topLine.BackgroundColor3 = col
    topLine.BorderSizePixel = 0

    botLine.Visible = showCross
    botLine.AnchorPoint = Vector2.new(0.5, 0)
    botLine.Size = UDim2.new(0, th, 0, sz)
    botLine.Position = UDim2.new(0.5, 0, 0.5, gap)
    botLine.BackgroundColor3 = col
    botLine.BorderSizePixel = 0

    leftLine.Visible = showCross
    leftLine.AnchorPoint = Vector2.new(1, 0.5)
    leftLine.Size = UDim2.new(0, sz, 0, th)
    leftLine.Position = UDim2.new(0.5, -gap, 0.5, 0)
    leftLine.BackgroundColor3 = col
    leftLine.BorderSizePixel = 0

    rightLine.Visible = showCross
    rightLine.AnchorPoint = Vector2.new(0, 0.5)
    rightLine.Size = UDim2.new(0, sz, 0, th)
    rightLine.Position = UDim2.new(0.5, gap, 0.5, 0)
    rightLine.BackgroundColor3 = col
    rightLine.BorderSizePixel = 0

    dotFrame.Visible = showDot
    local dSz = math.clamp(math.floor(sz / 2.5), 3, 14)
    dotFrame.AnchorPoint = Vector2.new(0.5, 0.5)
    dotFrame.Size = UDim2.new(0, dSz, 0, dSz)
    dotFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
    dotFrame.BackgroundColor3 = col
    dotFrame.BorderSizePixel = 0
    
    local dCorner = dotFrame:FindFirstChildOfClass("UICorner") or Instance.new("UICorner", dotFrame)
    dCorner.CornerRadius = UDim.new(1, 0)
end
updateCrosshair()

-- 5. دائرة نطاق الإيم بوت (FOV Circle)
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

-- 6. القائمة الرئيسية ذات الخلفية المصورة (لالو سالامانكا)
local mainPanel = Instance.new("Frame")
mainPanel.Name = "MainPanel"
mainPanel.AnchorPoint = Vector2.new(0.5, 0.5)
mainPanel.Position = UDim2.new(0.5, 0, 0.5, 0)
mainPanel.Size = UDim2.new(0, 280, 0, 330)
mainPanel.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
mainPanel.ClipsDescendants = true
mainPanel.ZIndex = 8
mainPanel.Parent = screenGui

Instance.new("UICorner", mainPanel).CornerRadius = UDim.new(0, 14)
local panelStroke = Instance.new("UIStroke", mainPanel)
panelStroke.Color = Color3.fromRGB(0, 255, 170)
panelStroke.Thickness = 1.5

-- إضافة صورة خلفية لالو سالامانكا
local bgImage = Instance.new("ImageLabel")
bgImage.Name = "LaloBG"
bgImage.Size = UDim2.new(1, 0, 1, 0)
bgImage.Position = UDim2.new(0, 0, 0, 0)
bgImage.BackgroundTransparency = 1
bgImage.Image = bgAssetId ~= "" and bgAssetId or "rbxassetid://10870932204"
bgImage.ImageTransparency = 0.35
bgImage.ScaleType = Enum.ScaleType.Crop
bgImage.ZIndex = 1
bgImage.Parent = mainPanel

-- طبقة تظليل ليكون الكلام مريح ومقروء
local overlay = Instance.new("Frame")
overlay.Size = UDim2.new(1, 0, 1, 0)
overlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
overlay.BackgroundTransparency = 0.4
overlay.ZIndex = 2
overlay.Parent = mainPanel

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
title.ZIndex = 3
title.Parent = mainPanel

local function createButton(text, pos, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 32)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    btn.BackgroundTransparency = 0.2
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.ZIndex = 3
    btn.Parent = mainPanel
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    
    local btnStroke = Instance.new("UIStroke", btn)
    btnStroke.Color = Color3.fromRGB(60, 60, 60)
    btnStroke.Thickness = 1
    
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
local shapes = {"Cross (+)", "Dot (•)", "Cross + Dot"}
local shapeIndex = 1
local shapeBtn = createButton("الشكل: Cross (+)", UDim2.new(0.05, 0, 0.41, 0), function(btn)
    shapeIndex = (shapeIndex % #shapes) + 1
    Settings.CrosshairShape = shapes[shapeIndex]
    btn.Text = "الشكل: " .. Settings.CrosshairShape
    updateCrosshair()
end)

-- مربع تحديد الحجم (من 1 إلى 100)
local sizeLabel = Instance.new("TextLabel")
sizeLabel.Size = UDim2.new(0.55, 0, 0, 32)
sizeLabel.Position = UDim2.new(0.05, 0, 0.55, 0)
sizeLabel.Text = "الحجم (من 1 - 100):"
sizeLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
sizeLabel.Font = Enum.Font.SourceSans
sizeLabel.TextSize = 13
sizeLabel.BackgroundTransparency = 1
sizeLabel.ZIndex = 3
sizeLabel.Parent = mainPanel

local sizeBox = Instance.new("TextBox")
sizeBox.Size = UDim2.new(0.3, 0, 0, 32)
sizeBox.Position = UDim2.new(0.63, 0, 0.55, 0)
sizeBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
sizeBox.BackgroundTransparency = 0.2
sizeBox.Text = tostring(Settings.CrosshairSize)
sizeBox.TextColor3 = Color3.fromRGB(0, 255, 170)
sizeBox.Font = Enum.Font.SourceSansBold
sizeBox.TextSize = 15
sizeBox.ZIndex = 3
sizeBox.Parent = mainPanel
Instance.new("UICorner", sizeBox).CornerRadius = UDim.new(0, 6)

sizeBox.FocusLost:Connect(function()
    local val = tonumber(sizeBox.Text)
    if val then
        val = math.clamp(math.floor(val), 1, 100)
        Settings.CrosshairSize = val
        sizeBox.Text = tostring(val)
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

-- 7. محرك الإيم بوت (Aimbot Logic)
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
