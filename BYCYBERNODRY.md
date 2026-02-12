-- Limpeza de GUIs antigas
local oldGui = game.CoreGui:FindFirstChild("CyberMenu_Final")
if oldGui then oldGui:Destroy() end

-- Configuração Principal
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local TitleBar = Instance.new("Frame")
local TitleLabel = Instance.new("TextLabel")
local CloseBtn = Instance.new("TextButton")
local MinBtn = Instance.new("TextButton")
local Container = Instance.new("Frame")

-- Botões Restantes
local BtnRamp = Instance.new("TextButton")
local BtnSmooth = Instance.new("TextButton")
local BtnCarFly = Instance.new("TextButton")
local BtnBringCars = Instance.new("TextButton")
local Credits = Instance.new("TextLabel")

-- Propriedades da GUI
ScreenGui.Name = "CyberMenu_Final"
ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false

MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -100, 0.3, 0)
MainFrame.Size = UDim2.new(0, 220, 0, 240) -- Tamanho reduzido
MainFrame.Active = true
MainFrame.Draggable = true 

TitleBar.Name = "TitleBar"
TitleBar.Parent = MainFrame
TitleBar.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
TitleBar.Size = UDim2.new(1, 0, 0, 30)

TitleLabel.Parent = TitleBar
TitleLabel.Size = UDim2.new(1, -60, 1, 0)
TitleLabel.Position = UDim2.new(0, 10, 0, 0)
TitleLabel.Text = "Cyber Menu V8"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Font = Enum.Font.SourceSansBold

CloseBtn.Parent = TitleBar
CloseBtn.Size = UDim2.new(0, 30, 1, 0)
CloseBtn.Position = UDim2.new(1, -30, 0, 0)
CloseBtn.Text = "X"
CloseBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

MinBtn.Parent = TitleBar
MinBtn.Size = UDim2.new(0, 30, 1, 0)
MinBtn.Position = UDim2.new(1, -60, 0, 0)
MinBtn.Text = "-"
MinBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
MinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

Container.Name = "Container"
Container.Parent = MainFrame
Container.BackgroundTransparency = 1
Container.Position = UDim2.new(0, 0, 0, 35)
Container.Size = UDim2.new(1, 0, 1, -55)

local function styleButton(btn, text, pos, color)
    btn.Parent = Container
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.Position = pos
    btn.Text = text
    btn.BackgroundColor3 = color
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = btn
end

-- Posicionamento dos botões
styleButton(BtnRamp, "Teleport: Prision", UDim2.new(0.05, 0, 0, 0), Color3.fromRGB(39, 174, 96))
styleButton(BtnSmooth, "Teleport: Criminoso", UDim2.new(0.05, 0, 0, 45), Color3.fromRGB(41, 128, 185))
styleButton(BtnCarFly, "Car Fly: OFF", UDim2.new(0.05, 0, 0, 100), Color3.fromRGB(80, 80, 80))
styleButton(BtnBringCars, "PUXAR CARROS", UDim2.new(0.05, 0, 0, 145), Color3.fromRGB(142, 68, 173))

Credits.Parent = MainFrame
Credits.Size = UDim2.new(1, -10, 0, 20)
Credits.Position = UDim2.new(0, 0, 1, -20)
Credits.Text = "by: CyberNoDry"
Credits.TextColor3 = Color3.fromRGB(150, 150, 150)
Credits.BackgroundTransparency = 1
Credits.TextXAlignment = Enum.TextXAlignment.Right
Credits.Font = Enum.Font.SourceSansItalic

--- Lógica Central ---

local player = game.Players.LocalPlayer
local bringsRunning = false

-- Lógica Puxar Carros
BtnBringCars.MouseButton1Click:Connect(function()
    if bringsRunning then return end
    bringsRunning = true
    BtnBringCars.Text = "PUXANDO..."
    
    local originalPos = player.Character.HumanoidRootPart.CFrame
    local carContainer = workspace:FindFirstChild("CarContainer")
    
    if carContainer then
        for _, car in pairs(carContainer:GetChildren()) do
            local seat = car:FindFirstChildWhichIsA("VehicleSeat", true)
            if seat and seat.Occupant == nil then
                player.Character.HumanoidRootPart.CFrame = seat.CFrame
                local t = 0
                repeat task.wait(0.1); t = t + 1 until player.Character.Humanoid.SeatPart == seat or t > 15
                if player.Character.Humanoid.SeatPart == seat then
                    task.wait(0.2)
                    seat.CFrame = originalPos + Vector3.new(0, 5, 0)
                    task.wait(0.2)
                end
                player.Character.Humanoid.Jump = true
                task.wait(0.3)
            end
        end
    end
    player.Character.HumanoidRootPart.CFrame = originalPos
    BtnBringCars.Text = "PUXAR CARROS"
    bringsRunning = false
end)

-- Lógica Car Fly
local carFlyEnabled = false
BtnCarFly.MouseButton1Click:Connect(function()
    carFlyEnabled = not carFlyEnabled
    BtnCarFly.Text = carFlyEnabled and "Car Fly: ON" or "Car Fly: OFF"
    BtnCarFly.BackgroundColor3 = carFlyEnabled and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(80, 80, 80)
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if carFlyEnabled and player.Character then
        local humanoid = player.Character:FindFirstChild("Humanoid")
        local seat = humanoid and humanoid.SeatPart
        if seat and seat:IsA("VehicleSeat") then
            local bv = seat:FindFirstChild("CyberVel") or Instance.new("BodyVelocity", seat)
            local bg = seat:FindFirstChild("CyberGyro") or Instance.new("BodyGyro", seat)
            bv.Name = "CyberVel"; bg.Name = "CyberGyro"
            bv.MaxForce = Vector3.new(9e9, 9e9, 9e9); bg.maxTorque = Vector3.new(9e9, 9e9, 9e9)
            bv.Velocity = workspace.CurrentCamera.CFrame.LookVector * (seat.Throttle * 100)
            bg.CFrame = workspace.CurrentCamera.CFrame
        end
    end
end)

-- Lógica Teleportes
BtnRamp.MouseButton1Click:Connect(function()
    local t = workspace:FindFirstChild("Prison_Cellblock") and workspace.Prison_Cellblock:FindFirstChild("a_ramp") and workspace.Prison_Cellblock.a_ramp:FindFirstChild("Part")
    if t then player.Character.HumanoidRootPart.CFrame = CFrame.new(t.Position + Vector3.new(0, 3, 0)) end
end)

BtnSmooth.MouseButton1Click:Connect(function()
    local t = workspace:FindFirstChild("Smooth Block Model")
    if t then player.Character.HumanoidRootPart.CFrame = CFrame.new(t.Position + Vector3.new(0, 3, 0)) end
end)

-- Controles da Janela
CloseBtn.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)
MinBtn.MouseButton1Click:Connect(function()
    Container.Visible = not Container.Visible
    MainFrame:TweenSize(Container.Visible and UDim2.new(0, 220, 0, 240) or UDim2.new(0, 220, 0, 30), "Out", "Quad", 0.3, true)
end)
