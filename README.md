-- Divine and Prismatic Seeds Auto-Buyer
local divineSeeds = {
    ["Grape Seed"] = true,
    ["Mushroom Seed"] = true,
    ["Pepper Seed"] = true,
    ["Cacao Seed"] = true,
}

local prismaticSeeds = {
    ["Beanstalk Seed"] = true,
    ["Ember Lily Seed"] = true,
}

local autoBuyEnabled = false

-- UI toggle
local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 200, 0, 50)
button.Position = UDim2.new(0, 50, 0, 100)
button.Text = "Auto-Buy OFF"
button.BackgroundColor3 = Color3.fromRGB(200, 100, 100)
button.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui"):WaitForChild("ScreenGui") -- ajuste se necessário

button.MouseButton1Click:Connect(function()
    autoBuyEnabled = not autoBuyEnabled
    button.Text = autoBuyEnabled and "Auto-Buy ON" or "Auto-Buy OFF"
    button.BackgroundColor3 = autoBuyEnabled and Color3.fromRGB(100, 200, 100) or Color3.fromRGB(200, 100, 100)
end)

-- Auto-buy loop
task.spawn(function()
    while true do
        if autoBuyEnabled then
            local seedsShop = workspace:FindFirstChild("Seeds") or workspace:FindFirstChild("SEEDS")
            if seedsShop then
                for _, seedUI in pairs(seedsShop:GetChildren()) do
                    if seedUI:IsA("TextLabel") then
                        local seedName = seedUI.Text
                        local isDivine = divineSeeds[seedName]
                        local isPrismatic = prismaticSeeds[seedName]
                        local stock = tonumber(seedUI.Parent:FindFirstChild("Stock").Text:match("%d+"))
                        local priceText = seedUI.Parent:FindFirstChild("Price").Text
                        local price = tonumber(priceText:gsub(",", ""):match("%d+"))

                        local money = game.Players.LocalPlayer.leaderstats.Cash.Value
                        if (isDivine or isPrismatic) and stock > 0 and money >= price then
                            fireclickdetector(seedUI.Parent:FindFirstChild("ClickDetector")) -- ajusta isso se necessário
                        end
                    end
                end
            end
        end
        task.wait(3)
    end
end)
