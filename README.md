Script de teleport   -- Criando o painel GUI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local MinimizeButton = Instance.new("TextButton")
local ScrollingFrame = Instance.new("ScrollingFrame")
local Logo = Instance.new("TextLabel")
local NotificationFrame = Instance.new("Frame")
local NotificationText = Instance.new("TextLabel")

-- Configurações do painel
ScreenGui.Name = "MeuPainel"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -175)
MainFrame.Size = UDim2.new(0, 300, 0, 350)

UICorner.CornerRadius = UDim.new(0, 15)
UICorner.Parent = MainFrame

ScrollingFrame.Parent = MainFrame
ScrollingFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ScrollingFrame.Position = UDim2.new(0, 0, 0, 50)
ScrollingFrame.Size = UDim2.new(1, 0, 1, -50)
ScrollingFrame.CanvasSize = UDim2.new(0, 0, 2, 0)
ScrollingFrame.ScrollBarThickness = 10
ScrollingFrame.ScrollBarImageColor3 = Color3.fromRGB(50, 50, 50)

-- Configurações do logo
Logo.Parent = MainFrame
Logo.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Logo.BackgroundTransparency = 1
Logo.Position = UDim2.new(0.5, -50, 0, 5)
Logo.Size = UDim2.new(0, 100, 0, 40)
Logo.Font = Enum.Font.SourceSansBold
Logo.Text = "Meu Painel"
Logo.TextColor3 = Color3.fromRGB(255, 0, 0)
Logo.TextScaled = true

-- Configurações da notificação de entrada de STAFF
NotificationFrame.Parent = ScreenGui
NotificationFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
NotificationFrame.BackgroundTransparency = 0.5
NotificationFrame.Position = UDim2.new(0.5, -150, 0, 100)
NotificationFrame.Size = UDim2.new(0, 300, 0, 50)
NotificationFrame.Visible = false

NotificationText.Parent = NotificationFrame
NotificationText.Size = UDim2.new(1, 0, 1, 0)
NotificationText.Font = Enum.Font.SourceSansBold
NotificationText.Text = ""
NotificationText.TextColor3 = Color3.fromRGB(255, 255, 255)
NotificationText.TextScaled = true

local function showNotification(text)
    NotificationText.Text = text
    NotificationFrame.Visible = true
    wait(5)
    NotificationFrame.Visible = false
end

-- Função para minimizar/maximizar o painel
local panelVisible = true
MinimizeButton.Parent = MainFrame
MinimizeButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MinimizeButton.Position = UDim2.new(0, 0, 0, 0)
MinimizeButton.Size = UDim2.new(0, 100, 0, 40)
MinimizeButton.Text = "Minimizar"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)

MinimizeButton.MouseButton1Click:Connect(function()
    panelVisible = not panelVisible
    if panelVisible then
        MainFrame.Size = UDim2.new(0, 300, 0, 350)
        ScrollingFrame.Visible = true
    else
        MainFrame.Size = UDim2.new(0, 100, 0, 40)
        ScrollingFrame.Visible = false
    end
    MinimizeButton.Text = panelVisible and "Minimizar" or "Maximizar"
end)

-- Função para criar botões de opção
function createOptionButton(name, position, callback)
    local Button = Instance.new("TextButton")
    Button.Parent = ScrollingFrame
    Button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    Button.Size = UDim2.new(0, 280, 0, 50)
    Button.Position = UDim2.new(0.5, -140, 0, position)
    Button.Text = name
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextScaled = true
    Button.MouseButton1Click:Connect(callback)
    return Button
end

-- Função para ativar/desativar o Modo Deus
local godModeEnabled = false
local function toggleGodMode(button)
    godModeEnabled = not godModeEnabled
    local player = game.Players.LocalPlayer
    local character = player.Character
    if character then
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if godModeEnabled then
            humanoid.Health = humanoid.MaxHealth
            humanoid:GetPropertyChangedSignal("Health"):Connect(function()
                if godModeEnabled then
                    humanoid.Health = humanoid.MaxHealth
                end
            end)
        end
    end
    button.Text = godModeEnabled and "Modo Deus (Ativado)" or "Modo Deus (Desativado)"
end

-- Função para ativar/desativar Speed 70
local speedEnabled = false
local function toggleSpeed(button)
    speedEnabled = not speedEnabled
    local player = game.Players.LocalPlayer
    local character = player.Character
    if character then
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if speedEnabled then
            humanoid.WalkSpeed = 70
        else
            humanoid.WalkSpeed = 16 -- Velocidade padrão
        end
    end
    button.Text = speedEnabled and "Speed 70 (Ativado)" or "Speed 70 (Desativado)"
end

-- Função para ativar/desativar ESP Player
local espEnabled = false
local espConnections = {}

local function createEsp(player)
    local highlight = Instance.new("Highlight")
    highlight.Name = "ESP"
    highlight.FillColor = Color3.fromRGB(255, 0, 0)
    highlight.FillTransparency = 0.5
    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
    highlight.OutlineTransparency = 0
    highlight.Parent = player.Character
end

local function toggleESP(button)
    espEnabled = not espEnabled
    if espEnabled then
        for _, player in ipairs(game.Players:GetPlayers()) do
            if player ~= game.Players.LocalPlayer then
                createEsp(player)
            end
        end
        table.insert(espConnections, game.Players.PlayerAdded:Connect(createEsp))
        table.insert(espConnections, game.Players.PlayerRemoving:Connect(function(player)
            local highlight = player.Character:FindFirstChild("ESP")
            if highlight then
                highlight:Destroy()
            end
        end))
    else
        for _, player in ipairs(game.Players:GetPlayers()) do
            local highlight = player.Character:FindFirstChild("ESP")
            if highlight then
                highlight:Destroy()
            end
        end
        for _, connection in ipairs(espConnections) do
            connection:Disconnect()
        end
        espConnections = {}
    end
    button.Text = espEnabled and "ESP Player (Ativado)" or "ESP Player (Desativado)"
end

-- Função para notificar entrada de STAFF
local function onPlayerAdded(player)
    if player:GetRankInGroup(1) >= 250 then -- Ajuste este valor de acordo com o rank de STAFF no grupo
        showNotification("STAFF " .. player.Name .. " entrou no jogo")
    end
end

-- Conectando a função ao evento PlayerAdded
game.Players.PlayerAdded:Connect(onPlayerAdded)

-- Função para ativar/desativar o Aimbot
local aimbotEnabled = false
local aimConnection
local aimRadius = 100
local function toggleAimbot(button)
    aimbotEnabled = not aimbotEnabled
    if aimbotEnabled then
        aimConnection = game:GetService("RunService").RenderStepped:Connect(function()
            local closestPlayer = nil
            local shortestDistance = aimRadius
            local localPlayer = game.Players.LocalPlayer
            local localCharacter = localPlayer.Character
            if not localCharacter then return end

            local players = game.Players:GetPlayers()
            for _, player in ipairs(players) do
                if player ~= localPlayer and player.Character and player.Character:FindFirstChild("Head") and player.Character:FindFirstChildOfClass("Humanoid").Health > 0 then
                    local head = player.Character.Head
                    local headPosition = head.Position
                    local headViewportPosition = workspace.CurrentCamera:WorldToViewportPoint(headPosition)
                    local distance = (Vector2.new(headViewportPosition.X, headViewportPosition.Y) - Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y / 2)).magnitude

                    if distance < shortestDistance then
                        closestPlayer = player
                        shortestDistance = distance
                    end
                end
            end

            if closestPlayer then
                local head = closestPlayer.Character.Head
                local headPosition = head.Position
                workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, headPosition)
            end
        end)
    else
        if aimConnection then
            aimConnection:Disconnect()
            aimConnection = nil
        end
    end
    button.Text = aimbotEnabled and "Aimbot (Ativado)" or "Aimbot (Desativado)"
end

-- Função para ativar/desativar o Noclip
local noclipEnabled = false
local noclipConnection
local function toggleNoclip(button)
    noclipEnabled = not noclipEnabled
    local player = game.Players.LocalPlayer
    local character = player.Character
    if character then
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if noclipEnabled then
            noclipConnection = game:GetService("RunService").Stepped:Connect(function()
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end)
        else
            if noclipConnection then
                noclipConnection:Disconnect()
                noclipConnection = nil
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true
                    end
                end
            end
        end
    end
    button.Text = noclipEnabled and "Noclip (Ativado)" or "Noclip (Desativado)"
end

-- Função para teletransportar para um jogador selecionado
local function teleportToPlayer(playerName)
    local player = game.Players.LocalPlayer
    local targetPlayer = game.Players:FindFirstChild(playerName)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local targetPosition = targetPlayer.Character.HumanoidRootPart.Position
        player.Character:SetPrimaryPartCFrame(CFrame.new(targetPosition))
    end
end

-- Função para puxar (bring) um jogador para você
local function bringPlayer(playerName)
    local player = game.Players.LocalPlayer
    local targetPlayer = game.Players:FindFirstChild(playerName)
    if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local targetCharacter = targetPlayer.Character
        local playerPosition = player.Character.HumanoidRootPart.Position
        targetCharacter:SetPrimaryPartCFrame(CFrame.new(playerPosition))
    end
end

-- Função para atualizar a lista de jogadores no painel
local function updatePlayerList()
    for _, child in ipairs(ScrollingFrame:GetChildren()) do
        if child:IsA("TextButton") and (child.Name == "TeleportButton" or child.Name == "BringButton") then
            child:Destroy()
        end
    end

    local yPos = 310
    for _, player in ipairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            local teleportButton = createOptionButton("Teleportar para " .. player.Name, yPos, function()
                teleportToPlayer(player.Name)
            end)
            teleportButton.Name = "TeleportButton"
            yPos = yPos + 60

            local bringButton = createOptionButton("Bring " .. player.Name, yPos, function()
                bringPlayer(player.Name)
            end)
            bringButton.Name = "BringButton"
            yPos = yPos + 60
        end
    end
end

-- Atualiza a lista de jogadores quando um jogador entra ou sai do jogo
game.Players.PlayerAdded:Connect(updatePlayerList)
game.Players.PlayerRemoving:Connect(updatePlayerList)

-- Inicializa a lista de jogadores
updatePlayerList()

-- Criando botões no painel
local godModeButton = createOptionButton("Modo Deus (Desativado)", 10, function(button) toggleGodMode(button) end)
local speedButton = createOptionButton("Speed 70 (Desativado)", 70, function(button) toggleSpeed(button) end)
local espButton = createOptionButton("ESP Player (Desativado)", 130, function(button) toggleESP(button) end)
local aimbotButton = createOptionButton("Aimbot (Desativado)", 190, function(button) toggleAimbot(button) end)
local noclipButton = createOptionButton("Noclip (Desativado)", 250, function(button) toggleNoclip(button) end)

-- Criando botão para mostrar lista de jogadores para teleportar para você
createOptionButton("BRING PLAYER", 310, updatePlayerList)
