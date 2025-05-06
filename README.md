-- Script de Admin Menu "YHZ Menu Admin" (pode ser rodado em um Exploit)

local replicatedStorage = game:GetService("ReplicatedStorage")
local player = game.Players.LocalPlayer

-- Função para criar a interface do Menu
local function createAdminMenu()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "YHZMenuAdmin"
    screenGui.Parent = player.PlayerGui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 250, 0, 400)
    frame.Position = UDim2.new(0, 10, 0, 10)
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    frame.Parent = screenGui

    local titleLabel = Instance.new("TextLabel")
    titleLabel.Size = UDim2.new(1, 0, 0, 30)
    titleLabel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    titleLabel.Text = "YHZ Menu Admin"
    titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    titleLabel.TextSize = 20
    titleLabel.TextAlign = Enum.TextXAlignment.Center
    titleLabel.Parent = frame

    -- Função para criar os botões de comando
    local function createButton(text, command, value)
        local button = Instance.new("TextButton")
        button.Size = UDim2.new(1, 0, 0, 40)
        button.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
        button.Text = text
        button.TextColor3 = Color3.fromRGB(255, 255, 255)
        button.TextSize = 16
        button.TextAlign = Enum.TextXAlignment.Center
        button.Parent = frame

        -- Ação de clicar no botão
        button.MouseButton1Click:Connect(function()
            replicatedStorage:WaitForChild("AdminRemote"):FireServer(command, value)
        end)

        return button
    end

    -- Criar botões no menu
    createButton("Aumentar Velocidade", "speed", 50)
    createButton("Aumentar Pulo", "jump", 100)
    createButton("Ativar/Desativar Voo", "fly", true)
    createButton("Ativar/Desativar Imortalidade", "immortal", true)
    createButton("Reviver Jogador", "revive", nil)
    createButton("Teleporte (Padrão)", "tp", Vector3.new(100, 50, 100))
end

-- Função de comando do servidor
local function onAdminCommand(player, command, value)
    if command == "speed" then
        local character = player.Character
        if character and character:FindFirstChild("Humanoid") then
            character.Humanoid.WalkSpeed = value
        end
    elseif command == "jump" then
        local character = player.Character
        if character and character:FindFirstChild("Humanoid") then
            character.Humanoid.JumpHeight = value
        end
    elseif command == "fly" then
        local character = player.Character
        if character and character:FindFirstChild("Humanoid") then
            local bodyVelocity = Instance.new("BodyVelocity")
            bodyVelocity.MaxForce = Vector3.new(500000, 500000, 500000)
            bodyVelocity.Velocity = Vector3.new(0, 50, 0)
            bodyVelocity.Parent = character:FindFirstChild("HumanoidRootPart")
        end
    elseif command == "immortal" then
        local character = player.Character
        if character then
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.MaxHealth = math.huge
                humanoid.Health = math.huge
                humanoid.HealthChanged:Connect(function()
                    humanoid.Health = math.huge
                end)
            end
        end
    elseif command == "revive" then
        local character = player.Character
        if character then
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.Health = humanoid.MaxHealth
            end
        end
    elseif command == "tp" then
        local character = player.Character
        if character then
            character:SetPrimaryPartCFrame(CFrame.new(value))
        end
    end
end

-- Criar o RemoteEvent de Admin
local adminRemote = Instance.new("RemoteEvent")
adminRemote.Name = "AdminRemote"
adminRemote.Parent = replicatedStorage

-- Conectar a função de comandos no servidor
adminRemote.OnServerEvent:Connect(onAdminCommand)

-- Chamar a função para criar o menu de admin
createAdminMenu()
