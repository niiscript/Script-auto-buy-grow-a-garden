-- Divine and Prismatic seed names
local divineSeeds = {
    Grape = true,
    Mushroom = true,
    Pepper = true,
    Cacao = true
}

local prismaticSeeds = {
    ["Beanstalk"] = true,
    ["Ember Lily"] = true
}

-- Toggle variable
local autoBuyEnabled = true

-- Create button
local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "AutoBuyGUI"

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 200, 0, 50)
button.Position = UDim2.new(0, 50, 0, 100)
button.Text = "Auto-Buy ON"
button.BackgroundColor3 = Color3.fromRGB(200, 100, 100)
button.TextScaled = true
button.Parent = gui

button.MouseButton1Click:Connect(function()
    autoBuyEnabled = not autoBuyEnabled
    button.Text = autoBuyEnabled and "Auto-Buy ON" or "Auto-Buy OFF"
    button.BackgroundColor3 = autoBuyEnabled and Color3.fromRGB(200, 100, 100) or Color3.fromRGB(100, 100, 100)
end)

-- Buying function
task.spawn(function()
    while true do
        if autoBuyEnabled then
            local seedsShop = workspace:FindFirstChild("SEEDS")
            if seedsShop then
                for _, seed in pairs(seedsShop:GetChildren()) do
                    if seed:IsA("Model") then
                        local name = seed.Name
                        local stock = seed:FindFirstChild("Stock")
                        local price = seed:FindFirstChild("Price")
                        local rarity = seed:FindFirstChild("Rarity")
                        local buyButton = seed:FindFirstChild("Buy")

                        if stock and price and rarity and buyButton then
                            local stockAmount = tonumber(stock.Value:match("%d+")) or 0
                            local priceAmount = tonumber(price.Value:match("%d+")) or math.huge
                            local playerMoney = player.leaderstats and player.leaderstats:FindFirstChild("Money") and player.leaderstats.Money.Value or 0
                            local rarityText = rarity.Value

                            local isWanted =
                                divineSeeds[name] and rarityText == "Divine" or
                                prismaticSeeds[name] and rarityText == "Prismatica"

                            if stockAmount > 0 and isWanted and playerMoney >= priceAmount then
                                fireclickdetector(buyButton:FindFirstChildOfClass("ClickDetector"))
                                print("Bought seed:", name)
                            end
                        end
                    end
                end
            end
        end
        task.wait(2.5)
    end
end)
