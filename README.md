-- Serviços do Jogo
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui = game:GetService("CoreGui")

local player = Players.LocalPlayer

-- Conexões exatas do script que você enviou
local chestSpawn = ReplicatedStorage:WaitForChild("ChestSpawn")
local getChestTarget = chestSpawn:WaitForChild("GetChestTarget")
local chestActiveFlag = chestSpawn:WaitForChild("ChestActiveFlag")

-- ==========================================
-- INTERFACE INTERATIVA (GUI)
-- ==========================================
local protectUI = gethui and gethui() or CoreGui

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TPBauOficialGUI"
screenGui.Parent = protectUI

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 180, 0, 110)
mainFrame.Position = UDim2.new(0.5, -90, 0.4, -55)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true -- Permite arrastar a janelinha pela tela
mainFrame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "TP BAÚ OFICIAL"
title.TextColor3 = Color3.fromRGB(255, 215, 0) -- Cor Dourada
title.Font = Enum.Font.GothamBold
title.TextSize = 13
title.Parent = mainFrame

local statusText = Instance.new("TextLabel")
statusText.Size = UDim2.new(1, 0, 0, 25)
statusText.Position = UDim2.new(0, 0, 0, 30)
statusText.BackgroundTransparency = 1
statusText.Text = "Status: Aguardando..."
statusText.TextColor3 = Color3.fromRGB(200, 200, 200)
statusText.Font = Enum.Font.GothamMedium
statusText.TextSize = 11
statusText.Parent = mainFrame

local tpButton = Instance.new("TextButton")
tpButton.Size = UDim2.new(0, 150, 0, 35)
tpButton.Position = UDim2.new(0.5, -75, 0, 60)
tpButton.BackgroundColor3 = Color3.fromRGB(0, 140, 60)
tpButton.Text = "TELEPORTAR AO BAÚ"
tpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
tpButton.Font = Enum.Font.GothamBold
tpButton.TextSize = 11
tpButton.Parent = mainFrame

local tpCorner = Instance.new("UICorner")
tpCorner.CornerRadius = UDim.new(0, 6)
tpCorner.Parent = tpButton

-- ==========================================
-- LÓGICA DE VERIFICAÇÃO E TELEPORTE
-- ==========================================

tpButton.MouseButton1Click:Connect(function()
    statusText.Text = "Verificando servidor..."
    statusText.TextColor3 = Color3.fromRGB(255, 255, 100)

    -- 1. Verifica se o jogo diz que há um baú ativo no momento
    if not chestActiveFlag.Value then
        statusText.Text = "Erro: Nenhum baú ativo no jogo!"
        statusText.TextColor3 = Color3.fromRGB(255, 100, 100)
        return
    end

    -- 2. Invoca o servidor exatamente da forma que a bússola faz (v_u_8:InvokeServer())
    local success, response = pcall(function()
        return getChestTarget:InvokeServer()
    end)

    -- 3. Se o servidor responder com a tabela correta contendo a posição (Vector3)
    if success and type(response) == "table" and response.active and typeof(response.pos) == "Vector3" then
        local targetPos = response.pos
        local character = player.Character
        local hrp = character and character:FindFirstChild("HumanoidRootPart")

        if hrp then
            -- Teleporta o personagem para as coordenadas oficiais + 5 studs de altura (segurança)
            hrp.CFrame = CFrame.new(targetPos + Vector3.new(0, 5, 0))
            statusText.Text = "Teleportado com sucesso!"
            statusText.TextColor3 = Color3.fromRGB(100, 255, 100)
        else
            statusText.Text = "Erro: Personagem inválido."
            statusText.TextColor3 = Color3.fromRGB(255, 100, 100)
        end
    else
        -- Se cair aqui, o servidor recusou o envio dos dados
        statusText.Text = "Erro: Segure a Bússola na mão!"
        statusText.TextColor3 = Color3.fromRGB(255, 100, 100)
    end
end)

