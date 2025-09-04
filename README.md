local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera

local ESP = false
local AIMBOT = false
local AIMBOT_FOV = 150
local AIMBOT_SMOOTH = 0.2

local function ESPCreate(char)
    if not char:FindFirstChild("Highlight") then
        local h = Instance.new("Highlight")
        h.Adornee = char
        h.FillColor = Color3.fromRGB(255,0,0)
        h.OutlineColor = Color3.fromRGB(255,255,255)
        h.Enabled = ESP
        h.Parent = char
    else
        char.Highlight.Enabled = ESP
    end
end

local function toggleESP(state)
    ESP = state
    for _,p in ipairs(Players:GetPlayers()) do
        if p~=localPlayer and p.Character then
            ESPCreate(p.Character)
        end
    end
end

local function closestTarget()
    local closest = nil
    local shortest = AIMBOT_FOV
    for _,p in ipairs(Players:GetPlayers()) do
        if p~=localPlayer and p.Character and p.Character:FindFirstChild("Head") then
            local headPos = camera:WorldToViewportPoint(p.Character.Head.Position)
            local mousePos = UserInputService:GetMouseLocation()
            local dist = (Vector2.new(headPos.X, headPos.Y)-mousePos).Magnitude
            if dist<shortest then
                shortest = dist
                closest = p
            end
        end
    end
    return closest
end

RunService.RenderStepped:Connect(function()
    if AIMBOT then
        local target = closestTarget()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            local tPos = camera:WorldToViewportPoint(target.Character.Head.Position)
            local mouse = UserInputService:GetMouseLocation()
            local aimPos = Vector2.new(mouse.X, mouse.Y):Lerp(Vector2.new(tPos.X, tPos.Y), AIMBOT_SMOOTH)
            mousemoveabs(aimPos.X, aimPos.Y)
        end
    end
end)

local gui = Instance.new("ScreenGui", localPlayer:WaitForChild("PlayerGui"))
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0,200,0,150)
frame.Position = UDim2.new(0.05,0,0.3,0)
frame.BackgroundColor3 = Color3.fromRGB(40,40,40)

local btnESP = Instance.new("TextButton", frame)
btnESP.Size = UDim2.new(1,-10,0,40)
btnESP.Position = UDim2.new(0,5,0,10)
btnESP.Text = "ESP: OFF"

local btnAim = Instance.new("TextButton", frame)
btnAim.Size = UDim2.new(1,-10,0,40)
btnAim.Position = UDim2.new(0,5,0,60)
btnAim.Text = "Aimbot: OFF"

btnESP.MouseButton1Click:Connect(function()
    ESP = not ESP
    btnESP.Text = "ESP: "..(ESP and "ON" or "OFF")
    toggleESP(ESP)
end)

btnAim.MouseButton1Click:Connect(function()
    AIMBOT = not AIMBOT
    btnAim.Text = "Aimbot: "..(AIMBOT and "ON" or "OFF")
end)
