local Players       = game:GetService("Players")
local TweenService  = game:GetService("TweenService")

local player    = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local shop = workspace:FindFirstChild("SEEDS")
if not shop then
    warn("Shop model 'SEEDS' not found in workspace")
    return
end

local filteredFruits = {
    "Grape",
    "Mushroom",
    "Pepper",
    "Cacao",
    "Beanstalk",
    "EmberLily"
}

local fixedPrices = {
    Grape     = 850000,
    Mushroom  = 150000,
    Pepper    = 1000000,
    Cacao     = 2500000,
    Beanstalk = 10000000,
    EmberLily = 15000000
}

local rarities = {
    Grape     = "Divine",
    Mushroom  = "Divine",
    Pepper    = "Divine",
    Cacao     = "Divine",
    Beanstalk = "Prismatic",
    EmberLily = "Prismatic"
}

local hue = 0
local function getRainbowColor()
    hue = (hue + 0.01) % 1
    return Color3.fromHSV(hue, 1, 1)
end

local function formatTime(seconds)
    local minutes = math.floor(seconds / 60)
    local secs    = seconds % 60
    return string.format("%02d:%02d", minutes, secs)
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name         = "niiscript"
screenGui.ResetOnSpawn = false
screenGui.Parent       = playerGui

local toggleButton = Instance.new("TextButton")
toggleButton.Name             = "niiscript"
toggleButton.Size             = UDim2.new(0, 32, 0, 32)
toggleButton.Position         = UDim2.new(0, 10, 0, 50)
toggleButton.BackgroundColor3 = Color3.fromRGB(120, 0, 180)
toggleButton.BorderSizePixel  = 0
toggleButton.Font             = Enum.Font.SourceSansBold
toggleButton.Text             = "≡"
toggleButton.TextSize         = 24
toggleButton.TextColor3       = Color3.new(1, 1, 1)
toggleButton.Parent           = screenGui

local clickSound = Instance.new("Sound")
clickSound.SoundId = "rbxassetid://9118823104"
clickSound.Volume  = 1
clickSound.Parent  = toggleButton

local frame = Instance.new("Frame")
frame.Size             = UDim2.new(0, 260, 0, #filteredFruits * 30 + 60)
frame.Position         = UDim2.new(0, 50, 0, 50)
frame.BackgroundColor3 = Color3.fromRGB(40, 0, 60)
frame.BorderSizePixel  = 0
frame.Visible          = false
frame.Parent           = screenGui

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Padding       = UDim.new(0, 5)
UIListLayout.FillDirection = Enum.FillDirection.Vertical
UIListLayout.SortOrder     = Enum.SortOrder.LayoutOrder
UIListLayout.Parent        = frame

local labels = {}
for _, fruit in ipairs(filteredFruits) do
    local lbl = Instance.new("TextLabel")
    lbl.Size                 = UDim2.new(1, -10, 0, 25)
    lbl.BackgroundTransparency = 1
    lbl.RichText             = true
    lbl.Font                 = Enum.Font.SourceSansBold
    lbl.TextSize             = 18
    lbl.TextXAlignment       = Enum.TextXAlignment.Left
    lbl.Text                 = fruit .. " - Checking..."
    lbl.Parent               = frame
    labels[fruit]            = lbl
end

local timerLabel = Instance.new("TextLabel")
timerLabel.Size                 = UDim2.new(1, -10, 0, 25)
timerLabel.BackgroundTransparency = 1
timerLabel.Font                 = Enum.Font.SourceSansItalic
timerLabel.TextSize             = 16
timerLabel.TextXAlignment       = Enum.TextXAlignment.Left
timerLabel.TextColor3           = Color3.fromRGB(255, 215, 100)
timerLabel.Text                 = "Restock in: --"
timerLabel.Parent               = frame

local isOpen = false
toggleButton.MouseButton1Click:Connect(function()
    isOpen = not isOpen
    frame.Visible = isOpen
    clickSound:Play()
    local targetColor = isOpen and Color3.fromRGB(150, 0, 255) or Color3.fromRGB(120, 0, 180)
    TweenService:Create(toggleButton, TweenInfo.new(0.25), {BackgroundColor3 = targetColor}):Play()
end)

while true do
    local inStock = {}
    for _, item in pairs(shop:GetChildren()) do
        if table.find(filteredFruits, item.Name) then
            inStock[item.Name] = true
        end
    end

    for _, fruit in ipairs(filteredFruits) do
        local rarity = rarities[fruit]
        local tagText
        if rarity == "Divine" then
            tagText = '<font color="rgb(255,170,0)">[Divine]</font>'
        else
            local c = getRainbowColor()
            tagText = string.format('<font color="rgb(%d,%d,%d)">[Prismatic]</font>',
                math.floor(c.R*255), math.floor(c.G*255), math.floor(c.B*255))
        end

        if inStock[fruit] then
            local price = fixedPrices[fruit]
            labels[fruit].Text = string.format(
                '%s %s - <font color="rgb(100,255,100)">In Stock - Price: %s</font>',
                fruit, tagText, tostring(price)
            )
        else
            labels[fruit].Text = string.format(
                '%s %s - <font color="rgb(255,90,90)">Out of Stock</font>',
                fruit, tagText
            )
        end
    end

    local restockVal = shop:FindFirstChild("RestockTime")
    if restockVal and restockVal:IsA("IntValue") then
        timerLabel.Text = "Restock in: " .. formatTime(restockVal.Value)
    else
        timerLabel.Text = "Restock in: --"
    end

    wait(1)
end
