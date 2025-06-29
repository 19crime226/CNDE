-- Aimbot GUI pour Blox Fruits par SkyMan

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Config
local AimbotEnabled = false
local AimbotRange = 200 -- distance max en studs

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SkyManAimbotGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(0, 150, 0, 50)
ToggleButton.Position = UDim2.new(0, 20, 0, 200)
ToggleButton.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 20
ToggleButton.Text = "Aimbot: OFF"
ToggleButton.Parent = ScreenGui
ToggleButton.BorderSizePixel = 0
ToggleButton.BackgroundTransparency = 0.2

-- Fonction pour trouver l'ennemi le plus proche
local function getClosestTarget()
    local closestTarget = nil
    local shortestDistance = AimbotRange

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local char = player.Character
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChild("Humanoid")

            if hrp and hum and hum.Health > 0 then
                local dist = (hrp.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                if dist < shortestDistance then
                    shortestDistance = dist
                    closestTarget = hrp
                end
            end
        end
    end

    return closestTarget
end

-- Aimbot Loop
RunService.RenderStepped:Connect(function()
    if AimbotEnabled then
        local target = getClosestTarget()
        if target then
            local screenPos, onScreen = Camera:WorldToViewportPoint(target.Position)
            if onScreen then
                local dx = screenPos.X - Mouse.X
                local dy = screenPos.Y - Mouse.Y
                pcall(function()
                    mousemoverel(dx, dy) -- nécessite un exécuteur compatible (KRNL, Synapse X...)
                end)
            end
        end
    end
end)

-- Toggle Button
ToggleButton.MouseButton1Click:Connect(function()
    AimbotEnabled = not AimbotEnabled
    ToggleButton.Text = "Aimbot: " .. (AimbotEnabled and "ON" or "OFF")
    ToggleButton.BackgroundColor3 = AimbotEnabled and Color3.fromRGB(50, 200, 50) or Color3.fromRGB(25, 25, 25)
end)

print("✅ GUI Aimbot chargée pour Blox Fruits ! Clique sur le bouton pour activer.")
