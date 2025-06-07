local player = game.Players.LocalPlayer
local gui = player:WaitForChild("PlayerGui")

-- Lista de raridades desejadas
local raridadesAlvo = {
    ["divine"] = true,
    ["prismatic"] = true
}

-- Controle do Auto-Buy
local autoBuyAtivado = false

-- Criar botão na tela
local tela = Instance.new("ScreenGui", gui)
tela.Name = "AutoBuyUI"

local botao = Instance.new("TextButton", tela)
botao.Size = UDim2.new(0, 180, 0, 50)
botao.Position = UDim2.new(0, 20, 0, 100)
botao.BackgroundColor3 = Color3.fromRGB(100, 200, 100)
botao.Text = "Auto-Buy OFF"
botao.Font = Enum.Font.SourceSansBold
botao.TextScaled = true

-- Função principal
local function observarLoja()
    local loja = gui:WaitForChild("SEEDS", 10) -- agora com o nome correto da loja
    if not loja then
        warn("Loja 'SEEDS' não encontrada")
        return
    end

    while autoBuyAtivado do
        for _, item in pairs(loja:GetDescendants()) do
            if item:IsA("TextLabel") or item:IsA("TextButton") then
                local texto = item.Text:lower()

                -- Checar se é raridade desejada
                for raridadeDesejada in pairs(raridadesAlvo) do
                    if texto == raridadeDesejada then
                        local parentItem = item.Parent

                        -- Verifica se tem "NO STOCK" no mesmo item
                        local temEstoque = true
                        for _, filho in pairs(parentItem:GetChildren()) do
                            if filho:IsA("TextLabel") and filho.Text:lower():find("no stock") then
                                temEstoque = false
                                break
                            end
                        end

                        if temEstoque then
                            print("Comprando semente rara:", texto)
                            -- Tenta clicar no botão da raridade ou algo dentro do item
                            if item:IsA("TextButton") then
                                item:Activate()
                            elseif parentItem:FindFirstChildWhichIsA("TextButton") then
                                parentItem:FindFirstChildWhichIsA("TextButton"):Activate()
                            end
                        end
                    end
                end
            end
        end
        wait(1.5)
    end
end

-- Alternar botão
botao.MouseButton1Click:Connect(function()
    autoBuyAtivado = not autoBuyAtivado
    botao.Text = autoBuyAtivado and "Auto-Buy ON" or "Auto-Buy OFF"
    botao.BackgroundColor3 = autoBuyAtivado and Color3.fromRGB(200, 100, 100) or Color3.fromRGB(100, 200, 100)

    if autoBuyAtivado then
        task.spawn(observarLoja)
    end
end)
