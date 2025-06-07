local player = game.Players.LocalPlayer
local gui = player:WaitForChild("PlayerGui")

-- Seeds to monitor (confirmed Divine and Prismatic)
local targetSeeds = {
    ["grape"] = true,
    ["mushroom"] = true,
    ["pepper"] = true,
    ["cacao"] = true,
    ["beanstalk"] = true,
    ["ember lily"] = true
}

-- Auto-Buy toggle
local autoBuyEnabled = false

-- Create UI button
local screen = Instance.new("ScreenGui", gui)
screen.Name = "AutoBuyUI"

local button = Instance.new("TextButton", screen)
button.Size = UDim2.new(0, 180, 0, 50)
button.Position = UDim2.new(0, 20, 0, 100)
button.BackgroundColor3 = Color3.fromRGB(100, 200, 100)
button.Text = "Auto-Buy OFF"
button.Font = Enum.Font.SourceSansBold
button.TextScaled = true

-- Main function to scan the SEEDS shop
local function monitorShop()
    local shop = gui:WaitForChild("SEEDS", 10)
    if not shop then
        warn("SEEDS shop not found.")
        return
    end

    while autoBuyEnabled do
        for _, item in pairs(shop:GetDescendants()) do
            if item:IsA("TextLabel") or item:IsA("TextButton") then
                local text = item.Text:lower()

                -- Check if it matches any valid seed name
                for seedName in pairs(targetSeeds) do
                    if text:find(seedName) then
                        local parent = item.Parent
                        local inStock = true

                        for _, child in pairs(parent:GetChildren()) do
                            if child:IsA("TextLabel") and child.Text:lower():find("no stock") then
                                inStock = false
                                break
                            end
                        end

                        if inStock then
                            print("Buying seed:", seedName)
                            if item:IsA("TextButton") then
                                item:Activate()
                            elseif parent:FindFirstChildWhichIsA("TextButton") then
                                parent:FindFirstChildWhichIsA("TextButton"):Activate()
                            end
                        end
                    end
                end
            end
        end
        wait(1.5)
    end
end

-- Toggle button logic
button.MouseButton1Click:Connect(function()
    autoBuyEnabled = not autoBuyEnabled
    button.Text = autoBuyEnabled and "Auto-Buy ON" or "Auto-Buy OFF"
    button.BackgroundColor3 = autoBuyEnabled and Color3.fromRGB(200, 100, 100) or Color3.fromRGB(100, 200, 100)

    if autoBuyEnabled then
        task.spawn(monitorShop)
    end
end)
