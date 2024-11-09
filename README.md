-- Aim Assist with Keybind (LocalScript)
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local camera = game.Workspace.CurrentCamera

-- Create the GUI crosshair
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

local crosshair = Instance.new("ImageLabel")
crosshair.Size = UDim2.new(0, 50, 0, 50)
crosshair.Position = UDim2.new(0.5, -25, 0.5, -25)
crosshair.BackgroundTransparency = 1
crosshair.Image = "rbxassetid://6031071050"  -- Crosshair image asset
crosshair.Parent = screenGui

-- Keybind configuration
local keybind = Enum.KeyCode.E  -- Change this to your desired key
local isKeyPressed = false

-- Function to find the nearest NPC or player
local function findTarget()
    local closestTarget = nil
    local shortestDistance = math.huge
    for _, obj in pairs(workspace:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChild("HumanoidRootPart") then
            local dist = (camera.CFrame.Position - obj.HumanoidRootPart.Position).Magnitude
            if dist < shortestDistance and dist < 100 then  -- Adjust distance for aim assist
                closestTarget = obj
                shortestDistance = dist
            end
        end
    end
    return closestTarget
end

-- Function to aim at the target
local function aimAtTarget(target)
    if target then
        local targetPosition = target.HumanoidRootPart.Position
        local direction = (targetPosition - camera.CFrame.Position).unit
        camera.CFrame = CFrame.new(camera.CFrame.Position, targetPosition)
    end
end

-- Function to toggle aiming on/off when key is pressed
mouse.KeyDown:Connect(function(key)
    if Enum.KeyCode[key] == keybind then  -- Check if the pressed key matches the keybind
        if isKeyPressed then
            -- Aim at the nearest target
            local target = findTarget()
            aimAtTarget(target)
        end
        isKeyPressed = not isKeyPressed  -- Toggle the keybind activation state
    end
end)

-- Optional: Change the crosshair visibility based on whether aim assist is active
game:GetService("RunService").Heartbeat:Connect(function()
    if isKeyPressed then
        crosshair.Visible = true
    else
        crosshair.Visible = false
    end
end)
