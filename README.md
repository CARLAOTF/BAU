local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer

-- Criação da Interface (GUI)
local ScreenGui = Instance.new("ScreenGui")
local success = pcall(function() ScreenGui.Parent = CoreGui end)
if not success then ScreenGui.Parent = player:WaitForChild("PlayerGui") end
ScreenGui.Name = "LuckyBlockPanel"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 250, 0, 200) -- Aumentado para caber o novo botão
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -100)
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local TopBar = Instance.new("Frame", MainFrame)
TopBar.Size = UDim2.new(1, 0, 0, 30)
TopBar.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
TopBar.BorderSizePixel = 0

local Title = Instance.new("TextLabel", TopBar)
Title.Size = UDim2.new(1, -60, 1, 0)
Title.Position = UDim2.new(0, 10, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Painel Lucky Block"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 16

local MinButton = Instance.new("TextButton", TopBar)
MinButton.Size = UDim2.new(0, 30, 1, 0)
MinButton.Position = UDim2.new(1, -60, 0, 0)
MinButton.BackgroundTransparency = 1
MinButton.Text = "-"
MinButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinButton.TextSize = 20
MinButton.Font = Enum.Font.SourceSansBold

local CloseButton = Instance.new("TextButton", TopBar)
CloseButton.Size = UDim2.new(0, 30, 1, 0)
CloseButton.Position = UDim2.new(1, -30, 0, 0)
CloseButton.BackgroundTransparency = 1
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 100, 100)
CloseButton.TextSize = 18
CloseButton.Font = Enum.Font.SourceSansBold

local ContentFrame = Instance.new("Frame", MainFrame)
ContentFrame.Size = UDim2.new(1, 0, 1, -30)
ContentFrame.Position = UDim2.new(0, 0, 0, 30)
ContentFrame.BackgroundTransparency = 1

local EspButton = Instance.new("TextButton", ContentFrame)
EspButton.Size = UDim2.new(0.9, 0, 0, 35)
EspButton.Position = UDim2.new(0.05, 0, 0, 10)
EspButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
EspButton.Text = "ESP Lucky Block: OFF"
EspButton.TextColor3 = Color3.fromRGB(255, 255, 255)
EspButton.Font = Enum.Font.SourceSansSemibold
EspButton.TextSize = 16

local TpButton = Instance.new("TextButton", ContentFrame)
TpButton.Size = UDim2.new(0.9, 0, 0, 35)
TpButton.Position = UDim2.new(0.05, 0, 0, 55)
TpButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
TpButton.Text = "Teleportar para Lucky Block"
TpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TpButton.Font = Enum.Font.SourceSansSemibold
TpButton.TextSize = 16

local AutoClickButton = Instance.new("TextButton", ContentFrame)
AutoClickButton.Size = UDim2.new(0.9, 0, 0, 35)
AutoClickButton.Position = UDim2.new(0.05, 0, 0, 100)
AutoClickButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
AutoClickButton.Text = "Auto Pegar/Clicar: OFF"
AutoClickButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoClickButton.Font = Enum.Font.SourceSansSemibold
AutoClickButton.TextSize = 16

-- Lógica de Minimizar e Fechar
local minimized = false
MinButton.MouseButton1Click:Connect(function()
    minimized = not minimized
    ContentFrame.Visible = not minimized
    if minimized then
        MainFrame.Size = UDim2.new(0, 250, 0, 30)
    else
        MainFrame.Size = UDim2.new(0, 250, 0, 200)
    end
end)

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

-- Lógica do ESP
local espAtivo = false
local espObjects = {}

local function limparESP()
    for _, obj in pairs(espObjects) do
        if obj then obj:Destroy() end
    end
    table.clear(espObjects)
end

local function atualizarESP()
    limparESP()
    if not espAtivo then return end
    
    local pasta = Workspace:FindFirstChild("LuckyBlocksActive")
    if pasta then
        for _, bloco in pairs(pasta:GetChildren()) do
            local highlight = Instance.new("Highlight")
            highlight.Adornee = bloco
            highlight.FillColor = Color3.fromRGB(255, 255, 0)
            highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
            highlight.Parent = ScreenGui
            table.insert(espObjects, highlight)

            local bgui = Instance.new("BillboardGui")
            bgui.Adornee = bloco
            bgui.Size = UDim2.new(0, 100, 0, 30)
            bgui.AlwaysOnTop = true
            
            local texto = Instance.new("TextLabel", bgui)
            texto.Size = UDim2.new(1, 0, 1, 0)
            texto.BackgroundTransparency = 1
            texto.Text = "Lucky Block"
            texto.TextColor3 = Color3.fromRGB(255, 255, 0)
            texto.TextStrokeTransparency = 0
            texto.Font = Enum.Font.SourceSansBold
            texto.TextSize = 14
            
            bgui.Parent = ScreenGui
            table.insert(espObjects, bgui)
        end
    end
end

EspButton.MouseButton1Click:Connect(function()
    espAtivo = not espAtivo
    if espAtivo then
        EspButton.Text = "ESP Lucky Block: ON"
        EspButton.TextColor3 = Color3.fromRGB(100, 255, 100)
    else
        EspButton.Text = "ESP Lucky Block: OFF"
        EspButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        limparESP()
    end
end)

task.spawn(function()
    while task.wait(1) do
        if espAtivo then atualizarESP() end
    end
end)

-- Lógica de Teleporte
TpButton.MouseButton1Click:Connect(function()
    local pasta = Workspace:FindFirstChild("LuckyBlocksActive")
    if pasta then
        local blocos = pasta:GetChildren()
        if #blocos > 0 then
            local alvo = blocos[1]
            local alvoCFrame = nil
            
            if alvo:IsA("Model") and alvo.PrimaryPart then
                alvoCFrame = alvo.PrimaryPart.CFrame
            elseif alvo:IsA("Model") and alvo:FindFirstChildWhichIsA("BasePart") then
                alvoCFrame = alvo:FindFirstChildWhichIsA("BasePart").CFrame
            elseif alvo:IsA("BasePart") then
                alvoCFrame = alvo.CFrame
            end
            
            if alvoCFrame then
                local character = player.Character or player.CharacterAdded:Wait()
                local hrp = character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    hrp.CFrame = alvoCFrame + Vector3.new(0, 3, 0)
                end
            end
        end
    end
end)

-- Lógica de Auto Pegar/Clicar
local autoClickAtivo = false

AutoClickButton.MouseButton1Click:Connect(function()
    autoClickAtivo = not autoClickAtivo
    if autoClickAtivo then
        AutoClickButton.Text = "Auto Pegar/Clicar: ON"
        AutoClickButton.TextColor3 = Color3.fromRGB(100, 255, 100)
    else
        AutoClickButton.Text = "Auto Pegar/Clicar: OFF"
        AutoClickButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
end)

-- Loop que procura e clica nos blocos
task.spawn(function()
    while task.wait(0.2) do -- Executa a cada 0.2 segundos para ser rápido mas não travar
        if autoClickAtivo then
            local pasta = Workspace:FindFirstChild("LuckyBlocksActive")
            if pasta then
                for _, bloco in pairs(pasta:GetChildren()) do
                    -- Verifica se há um ClickDetector no bloco ou nos filhos dele
                    local clickDetector = bloco:FindFirstChildWhichIsA("ClickDetector", true) 
                    
                    if clickDetector then
                        -- Simula o clique esquerdo do mouse interagindo com o bloco, passando a distância máxima para burlar limites
                        fireclickdetector(clickDetector, 50) 
                    end
                end
            end
        end
    end
end)


