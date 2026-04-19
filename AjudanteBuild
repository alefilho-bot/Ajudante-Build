local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local UserInputService = game:GetService("UserInputService")
local GuiService = game:GetService("GuiService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

-- Configuração do Cursor e Anel (Mantido idêntico)
local Cursor = Instance.new("Frame", ScreenGui)
Cursor.Name = "Cursor"
Cursor.Size = UDim2.new(0, 20, 0, 20)
Cursor.AnchorPoint = Vector2.new(0.5, 0.5)
Cursor.Position = UDim2.new(0.5, 0, 0.5, 0)
Cursor.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
Cursor.Visible = false
Cursor.ZIndex = 100
Instance.new("UICorner", Cursor).CornerRadius = UDim.new(1, 0)

local Ring = Instance.new("Frame", ScreenGui)
Ring.Name = "Ring"
Ring.Size = UDim2.new(0, 40, 0, 40)
Ring.AnchorPoint = Vector2.new(0.5, 0.5)
Ring.Position = UDim2.new(0.5, 0, 0.5, 0)
Ring.BackgroundTransparency = 1
Ring.Visible = false
Ring.ZIndex = 99
local RingStroke = Instance.new("UIStroke", Ring)
RingStroke.Thickness = 2
RingStroke.Color = Color3.fromRGB(0, 0, 255)
Instance.new("UICorner", Ring).CornerRadius = UDim.new(1, 0)

if game.CoreGui:FindFirstChild("BuilderSlimUI") then game.CoreGui.BuilderSlimUI:Destroy() end
ScreenGui.Name = "BuilderSlimUI"
ScreenGui.Parent = game.CoreGui
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true

-- DESIGN DO MENU (Compacto)
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 160, 0, 480) 
-- RESTAURADA A POSIÇÃO ORIGINAL DO MENU: Centralizado verticalmente na esquerda
MainFrame.Position = UDim2.new(0, 10, 0.5, -240)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 8)
local Stroke = Instance.new("UIStroke", MainFrame)
Stroke.Color = Color3.new(1, 1, 0)
Stroke.Transparency = 0.5

-- TÍTULO E SUBTÍTULO
local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Position = UDim2.new(0, 0, 0, 5)
Title.Text = "Ajudante de build"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14

local SubTitle = Instance.new("TextLabel", MainFrame)
SubTitle.Size = UDim2.new(1, 0, 0, 15)
SubTitle.Position = UDim2.new(0, 0, 0, 25)
SubTitle.Text = "Feito por: AleOliveira2404"
SubTitle.TextColor3 = Color3.new(0.7, 0.7, 0.7)
SubTitle.BackgroundTransparency = 1
SubTitle.Font = Enum.Font.Gotham
SubTitle.TextSize = 9

local alignData = {
    lastPos = nil, lastSize = nil, lastRotation = nil, indicator = nil,
    flying = false, speed = 50, distancia = 0, modoTopo = false, frozen = false,
    cursorEnabled = false, fitaAtiva = false, fitaLinha = nil, fitaLength = 50
}

local function createButton(name, pos, sizeX, color, callback)
    local btn = Instance.new("TextButton", MainFrame)
    btn.Name = name
    btn.Size = UDim2.new(sizeX or 0.9, 0, 0, 28)
    btn.Position = pos
    btn.BackgroundColor3 = color or Color3.fromRGB(50, 50, 50)
    btn.Text = name
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 9
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 4)
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- Lógica (Mantida intacta)
local function updateDisc(cf, size)
    if alignData.indicator then alignData.indicator:Destroy() end
    local disc = Instance.new("Part", workspace)
    disc.Shape = Enum.PartType.Cylinder
    disc.Size = Vector3.new(0.05, 0.8, 0.8)
    disc.Color = alignData.modoTopo and Color3.new(1, 0, 0) or Color3.new(1, 1, 0)
    disc.Material = Enum.Material.Neon
    disc.Anchored, disc.CanCollide, disc.CanQuery = true, false, false
    if alignData.modoTopo then
        disc.CFrame = (cf * CFrame.new(0, size.Y/2 + alignData.distancia, 0)) * CFrame.Angles(0, 0, math.rad(90))
    else
        disc.CFrame = (cf * CFrame.new(size.X + alignData.distancia, -(size.Y/2), 0)) * CFrame.Angles(0, 0, math.rad(90))
    end
    alignData.indicator = disc
end

local function updateFita()
    if alignData.fitaLinha then alignData.fitaLinha:Destroy() end
    if not alignData.fitaAtiva or not alignData.lastPos then return end
    local line = Instance.new("Part", workspace)
    line.Name = "TapeLine"
    line.Shape = Enum.PartType.Block
    line.Size = Vector3.new(alignData.fitaLength, 0.05, 0.05)
    line.Color = Color3.new(1, 1, 1)
    line.Material = Enum.Material.Neon
    line.Anchored = true
    line.CanCollide = false
    line.CanQuery = false
    line.CFrame = alignData.lastPos * CFrame.new(alignData.lastSize.X/2 + alignData.fitaLength/2, 0, 0)
    alignData.fitaLinha = line
end

-- Botões (layout compacto)
createButton("MARCAR OBJETO", UDim2.new(0.05, 0, 0, 50), 0.9, nil, function()
    local params = RaycastParams.new()
    params.FilterDescendantsInstances = {Players.LocalPlayer.Character}
    params.FilterType = Enum.RaycastFilterType.Exclude
    local ray = workspace:Raycast(Camera.CFrame.Position, Camera.CFrame.LookVector * 2000, params)
    if ray and ray.Instance:IsA("BasePart") then
        local target = ray.Instance
        alignData.lastPos, alignData.lastSize = target.CFrame, target.Size
        alignData.lastRotation = target.CFrame - target.CFrame.p
        updateDisc(target.CFrame, target.Size)
        updateFita()
    end
end)

createButton("AUTO-ALINHAR", UDim2.new(0.05, 0, 0, 82), 0.9, nil, function()
    if alignData.lastPos then
        local hrp = Players.LocalPlayer.Character.HumanoidRootPart
        local offset = alignData.modoTopo and CFrame.new(0, alignData.lastSize.Y + alignData.distancia - 0.5, 4) or CFrame.new(alignData.lastSize.X + alignData.distancia, -(alignData.lastSize.Y/2) + 2.5, 6)
        hrp.CFrame = CFrame.new((alignData.lastPos * offset).p) * alignData.lastRotation
    end
end)

createButton("MODO TOPO/LADO", UDim2.new(0.05, 0, 0, 114), 0.9, nil, function()
    alignData.modoTopo = not alignData.modoTopo
    if alignData.lastPos then updateDisc(alignData.lastPos, alignData.lastSize) end
end)

local flyBtn = createButton("FLY: OFF", UDim2.new(0.05, 0, 0, 146), 0.43, Color3.fromRGB(60, 0, 0), function()
    alignData.flying = not alignData.flying
    local btn = MainFrame:FindFirstChild("FLY: OFF")
    btn.Text = alignData.flying and "FLY: ON" or "FLY: OFF"
    btn.BackgroundColor3 = alignData.flying and Color3.fromRGB(0, 60, 0) or Color3.fromRGB(60, 0, 0)
    if alignData.flying then
        local hrp = Players.LocalPlayer.Character.HumanoidRootPart
        local hum = Players.LocalPlayer.Character.Humanoid
        local bv = Instance.new("BodyVelocity", hrp)
        bv.MaxForce = Vector3.new(1e6, 1e6, 1e6)
        task.spawn(function()
            while alignData.flying do
                local cam = workspace.CurrentCamera
                local look = cam.CFrame.LookVector
                hrp.CFrame = CFrame.new(hrp.Position, hrp.Position + Vector3.new(look.X, 0, look.Z))
                local moveDir = hum.MoveDirection
                if moveDir.Magnitude > 0 then
                    bv.Velocity = moveDir * alignData.speed
                    if moveDir:Dot(cam.CFrame.LookVector) > 0 then
                        bv.Velocity = bv.Velocity + Vector3.new(0, cam.CFrame.LookVector.Y * alignData.speed, 0)
                    end
                else
                    bv.Velocity = Vector3.new(0, 0.1, 0)
                end
                task.wait()
            end
            bv:Destroy()
        end)
    end
end)

createButton("CONGELAR", UDim2.new(0.52, 0, 0, 146), 0.43, nil, function()
    alignData.frozen = not alignData.frozen
    Players.LocalPlayer.Character.HumanoidRootPart.Anchored = alignData.frozen
end)

createButton("TOGGLE CURSOR", UDim2.new(0.05, 0, 0, 178), 0.9, Color3.fromRGB(0, 40, 40), function()
    alignData.cursorEnabled = not alignData.cursorEnabled
    Cursor.Visible = alignData.cursorEnabled
    Ring.Visible = alignData.cursorEnabled
    if alignData.cursorEnabled then UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter else UserInputService.MouseBehavior = Enum.MouseBehavior.Default end
end)

createButton("CLICAR", UDim2.new(0.05, 0, 0, 210), 0.9, Color3.fromRGB(0, 40, 80), function()
    if alignData.cursorEnabled then
        local centerPos = Vector2.new((Camera.ViewportSize.X/2) + 30, Camera.ViewportSize.Y/2)
        ScreenGui.Enabled = false
        task.wait(0.1)
        VirtualInputManager:SendMouseButtonEvent(centerPos.X, centerPos.Y, 0, true, game, 1)
        VirtualInputManager:SendMouseButtonEvent(centerPos.X, centerPos.Y, 0, false, game, 1)
        ScreenGui.Enabled = true
    end
end)

local fitaBtn = createButton("FITA MÉTRICA: OFF", UDim2.new(0.05, 0, 0, 242), 0.9, Color3.fromRGB(60, 0, 0), function()
    alignData.fitaAtiva = not alignData.fitaAtiva
    local btn = MainFrame:FindFirstChild("FITA MÉTRICA: OFF")
    btn.Text = alignData.fitaAtiva and "FITA MÉTRICA: ON" or "FITA MÉTRICA: OFF"
    btn.BackgroundColor3 = alignData.fitaAtiva and Color3.fromRGB(0, 60, 0) or Color3.fromRGB(60, 0, 0)
    updateFita()
end)

local FitaL = Instance.new("TextLabel", MainFrame)
FitaL.Size = UDim2.new(1,0,0,15); FitaL.Position = UDim2.new(0,0,0,274); FitaL.Text = "Tamanho Fita: 50"; FitaL.TextColor3 = Color3.new(1,1,1); FitaL.BackgroundTransparency = 1; FitaL.TextSize = 9

createButton("- FITA", UDim2.new(0.05, 0, 0, 289), 0.43, nil, function() 
    alignData.fitaLength = math.max(1, alignData.fitaLength - 1) 
    FitaL.Text = "Tamanho Fita: "..alignData.fitaLength
    updateFita()
end)
createButton("+ FITA", UDim2.new(0.52, 0, 0, 289), 0.43, nil, function() 
    alignData.fitaLength = alignData.fitaLength + 1 
    FitaL.Text = "Tamanho Fita: "..alignData.fitaLength
    updateFita()
end)

local VelL = Instance.new("TextLabel", MainFrame)
VelL.Size = UDim2.new(1,0,0,15); VelL.Position = UDim2.new(0,0,0,321); VelL.Text = "Vel: 50"; VelL.TextColor3 = Color3.new(1,1,1); VelL.BackgroundTransparency = 1; VelL.TextSize = 9

createButton("- VEL", UDim2.new(0.05, 0, 0, 336), 0.43, nil, function() alignData.speed = math.max(10, alignData.speed-10) VelL.Text = "Vel: "..alignData.speed end)
createButton("+ VEL", UDim2.new(0.52, 0, 0, 336), 0.43, nil, function() alignData.speed = alignData.speed+10 VelL.Text = "Vel: "..alignData.speed end)

local DistL = Instance.new("TextLabel", MainFrame)
DistL.Size = UDim2.new(1,0,0,15); DistL.Position = UDim2.new(0,0,0,368); DistL.Text = "Dist: 0"; DistL.TextColor3 = Color3.new(1,1,1); DistL.BackgroundTransparency = 1; DistL.TextSize = 9

createButton("- DIST", UDim2.new(0.05, 0, 0, 383), 0.43, nil, function() 
    alignData.distancia = alignData.distancia - 0.5 
    DistL.Text = "Dist: "..alignData.distancia 
    if alignData.lastPos then updateDisc(alignData.lastPos, alignData.lastSize) end
end)
createButton("+ DIST", UDim2.new(0.52, 0, 0, 383), 0.43, nil, function() 
    alignData.distancia = alignData.distancia + 0.5 
    DistL.Text = "Dist: "..alignData.distancia 
    if alignData.lastPos then updateDisc(alignData.lastPos, alignData.lastSize) end
end)

createButton("FECHAR", UDim2.new(0.05, 0, 0, 420), 0.9, Color3.fromRGB(150, 0, 0), function() 
    if alignData.fitaLinha then alignData.fitaLinha:Destroy() end
    ScreenGui:Destroy() 
end)

-- AJUSTE NO BOTÃO DE MINIMIZAR (Mantida a lógica de ocultamento mais baixa)
local MinBtn = Instance.new("TextButton", ScreenGui)
MinBtn.Size = UDim2.new(0, 30, 0, 30)
-- Posição inicial (Menu Aberto): Ao lado do menu (X=175), no mesmo alinhamento vertical centralizado (Y=0.5, -240)
MinBtn.Position = UDim2.new(0, 175, 0.5, -240) 
MinBtn.Text = "<<"
MinBtn.BackgroundColor3 = Color3.new(1, 1, 0)
MinBtn.TextColor3 = Color3.new(0, 0, 0)
MinBtn.ZIndex = 10 -- Garante que fique por cima de outros elementos da UI
Instance.new("UICorner", MinBtn)
MinBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
    MinBtn.Text = MainFrame.Visible and "<<" or ">>"
    
    -- Lógica de reposicionamento ajustada:
    if MainFrame.Visible then
        -- Quando visível: Posição original ao lado do menu
        MinBtn.Position = UDim2.new(0, 175, 0.5, -240)
    else
        -- Quando OCULTO: Mantida a posição segura no topo que defini anteriormente (para não conflitar com o Roblox).
        -- X=50, Y=0.1, 100
        MinBtn.Position = UDim2.new(0, 50, 0.1, 100)
    end
end)
