local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local VirtualUser = game:GetService("VirtualUser")
local Terrain = workspace:FindFirstChildOfClass("Terrain")

-- // CONFIGURAÇÃO ANTI-AFK (ATIVO POR PADRÃO)
local LocalPlayer = Players.LocalPlayer
LocalPlayer.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
end)

-- // INTERFACE ARRASTÁVEL
local sg = Instance.new("ScreenGui", (gethui and gethui()) or game:GetService("CoreGui"))
sg.Name = "PotatoAntiAFK_UI"

local Main = Instance.new("Frame", sg)
Main.Size = UDim2.fromOffset(200, 100)
Main.Position = UDim2.new(0.5, -100, 0.2, 0)
Main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Main.BorderSizePixel = 0
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)

-- Borda Neon
local stroke = Instance.new("UIStroke", Main)
stroke.Color = Color3.fromRGB(100, 255, 0)
stroke.Thickness = 2

local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "ULTRA OPTIMIZER"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.TextSize = 13

local PotatoBtn = Instance.new("TextButton", Main)
PotatoBtn.Size = UDim2.new(0.9, 0, 0, 35)
PotatoBtn.Position = UDim2.new(0.05, 0, 0.4, 0)
PotatoBtn.Text = "MODO BATATA: OFF"
PotatoBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
PotatoBtn.TextColor3 = Color3.new(1, 1, 1)
PotatoBtn.Font = Enum.Font.GothamBold
PotatoBtn.TextSize = 12
Instance.new("UICorner", PotatoBtn)

local AFKStatus = Instance.new("TextLabel", Main)
AFKStatus.Size = UDim2.new(1, 0, 0, 20)
AFKStatus.Position = UDim2.new(0, 0, 0.8, 0)
AFKStatus.Text = "ANTI-AFK: ATIVADO ✅"
AFKStatus.TextColor3 = Color3.fromRGB(0, 255, 127)
AFKStatus.BackgroundTransparency = 1
AFKStatus.Font = Enum.Font.Gotham
AFKStatus.TextSize = 10

-- // LÓGICA DO MODO BATATA
local PotatoAtivo = false

local function AtivarPotato()
    -- Terreno e Iluminação
    if Terrain then
        Terrain.WaterWaveSize = 0; Terrain.WaterWaveSpeed = 0
        Terrain.WaterReflectance = 0; Terrain.WaterTransparency = 0
    end
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    settings().Rendering.QualityLevel = 1

    -- Transforma em Massinha e Deleta Texturas
    for _, v in pairs(game:GetDescendants()) do
        if v:IsA("Part") or v:IsA("UnionOperation") or v:IsA("MeshPart") then
            v.Material = Enum.Material.SmoothPlastic
            v.Reflectance = 0
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v:Destroy()
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v.Enabled = false
        end
    end
end

PotatoBtn.MouseButton1Click:Connect(function()
    PotatoAtivo = not PotatoAtivo
    if PotatoAtivo then
        PotatoBtn.Text = "MODO BATATA: ON"
        PotatoBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 0)
        AtivarPotato()
    else
        PotatoBtn.Text = "MODO BATATA: OFF"
        PotatoBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    end
end)

-- // SISTEMA DE ARRASTAR
local dragging, dragInput, dragStart, startPos
Main.InputBegan:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = true; dragStart = input.Position; startPos = Main.Position end end)
UserInputService.InputChanged:Connect(function(input) if input == dragInput and dragging then local delta = input.Position - dragStart; Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y) end end)
Main.InputChanged:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseMovement then dragInput = input end end)
UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end end)
