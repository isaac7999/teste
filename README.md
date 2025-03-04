local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = game.Workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui

local Panel = Instance.new("Frame")
Panel.Size = UDim2.new(0.3, 0, 0.4, 0)
Panel.Position = UDim2.new(0.35, 0, 0.3, 0)
Panel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Panel.BorderSizePixel = 2
Panel.Active = true  -- Permite arrastar
Panel.Parent = ScreenGui

local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Size = UDim2.new(0.1, 0, 0.1, 0)
MinimizeButton.Position = UDim2.new(0.9, 0, 0, 0)
MinimizeButton.Text = "-"
MinimizeButton.TextScaled = true
MinimizeButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
MinimizeButton.Parent = Panel

local ScrollFrame = Instance.new("ScrollingFrame")
ScrollFrame.Size = UDim2.new(1, 0, 0.9, 0)
ScrollFrame.Position = UDim2.new(0, 0, 0.1, 0)
ScrollFrame.CanvasSize = UDim2.new(0, 0, 2, 0)
ScrollFrame.ScrollBarThickness = 5
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.Parent = Panel

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ScrollFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder

-- Sistema de Minimizar
local Toggle = true
MinimizeButton.MouseButton1Click:Connect(function()
    Toggle = not Toggle
    Panel.Visible = Toggle
    MinimizeButton.Visible = true
end)

-- Sistema de Spectate
local function SpectatePlayer(targetPlayer)
    if Camera.CameraSubject == targetPlayer.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = LocalPlayer.Character:FindFirstChild("Humanoid")
    else
        if targetPlayer.Character then
            Camera.CameraSubject = targetPlayer.Character:FindFirstChild("Humanoid")
        end
    end
end

-- Atualizar Lista de Players
local function UpdatePlayers()
    for _, child in pairs(ScrollFrame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local PlayerButton = Instance.new("TextButton")
            PlayerButton.Size = UDim2.new(1, 0, 0.1, 0)
            PlayerButton.Text = player.Name
            PlayerButton.TextScaled = true
            PlayerButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            PlayerButton.Parent = ScrollFrame

            PlayerButton.MouseButton1Click:Connect(function()
                SpectatePlayer(player)
            end)
        end
    end
end

Players.PlayerAdded:Connect(UpdatePlayers)
Players.PlayerRemoving:Connect(UpdatePlayers)
UpdatePlayers()

-- Sistema de Arrastar Painel
local dragging, dragInput, startPos, startInputPos

Panel.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        startPos = Panel.Position
        startInputPos = input.Position
    end
end)

Panel.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - startInputPos
        Panel.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

Panel.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)
