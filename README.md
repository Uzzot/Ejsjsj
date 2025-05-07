local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local primaryPart = character:WaitForChild("HumanoidRootPart") -- Certifica-se de pegar a PrimaryPart do personagem
local flying = false
local speed = 50

-- Cria BodyVelocity e BodyGyro
local bodyVelocity = Instance.new("BodyVelocity")
bodyVelocity.Velocity = Vector3.new(0, 0, 0)
bodyVelocity.MaxForce = Vector3.new(0, 0, 0)
bodyVelocity.P = 1250

local bodyGyro = Instance.new("BodyGyro")
bodyGyro.MaxTorque = Vector3.new(0, 0, 0)
bodyGyro.P = 3000

-- FunÃ§Ã£o para ativar o voo
local function fly()
    if not primaryPart then return end -- Garante que a PrimaryPart existe
    flying = true
    bodyVelocity.Parent = primaryPart
    bodyGyro.Parent = primaryPart
    bodyVelocity.MaxForce = Vector3.new(400000, 400000, 400000)
    bodyGyro.MaxTorque = Vector3.new(400000, 400000, 400000)
end

-- FunÃ§Ã£o para desativar o voo
local function stopFlying()
    if not flying then return end -- Evita erros caso jÃ¡ tenha parado
    flying = false
    bodyVelocity:Destroy()
    bodyGyro:Destroy()
    -- Recria os objetos para uso futuro
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.MaxForce = Vector3.new(0, 0, 0)
    bodyVelocity.P = 1250

    bodyGyro = Instance.new("BodyGyro")
    bodyGyro.MaxTorque = Vector3.new(0, 0, 0)
    bodyGyro.P = 3000
end

-- DetecÃ§Ã£o de teclas
local UIS = game:GetService("UserInputService")
UIS.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end -- Ignora eventos jÃ¡ processados (como menus)
    if input.KeyCode == Enum.KeyCode.F then
        if flying then
            stopFlying()
        else
            fly()
        end
    end
end)

-- AtualizaÃ§Ã£o contÃ­nua da posiÃ§Ã£o
game:GetService("RunService").Heartbeat:Connect(function()
    if flying and primaryPart then
        local camera = workspace.CurrentCamera
        local direction = Vector3.new(0, 0, 0)
        
        -- Calcula a direÃ§Ã£o de movimento com base nas teclas pressionadas
        if UIS:IsKeyDown(Enum.KeyCode.W) then direction = direction + camera.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.S) then direction = direction - camera.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.A) then direction = direction - camera.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.D) then direction = direction + camera.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.Space) then direction = direction + Vector3.new(0, 1, 0) end
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then direction = direction - Vector3.new(0, 1, 0) end

        -- Garante que o vetor direÃ§Ã£o nÃ£o seja zero antes de normalizar
        if direction.Magnitude > 0 then
            bodyVelocity.Velocity = direction.Unit * speed
        else
            bodyVelocity.Velocity = Vector3.zero
        end

        -- Alinha a orientaÃ§Ã£o com a cÃ¢mera
        bodyGyro.CFrame = camera.CFrame
    end
end)# Ejsjsj
