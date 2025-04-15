local allowedPlayers = {
    "EZC_ClanBetter",
    "Antonprof23",
    "pistrunchikbrat7"
}

local player = game.Players.LocalPlayer

local function isPlayerAllowed()
    for _, name in ipairs(allowedPlayers) do
        if player.Name == name then
            return true
        end
    end
    return false
end

if not isPlayerAllowed() then
    local ScreenGui = Instance.new("ScreenGui")
    local Frame = Instance.new("Frame")
    local TextLabel = Instance.new("TextLabel")

    ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    Frame.Parent = ScreenGui
    Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    Frame.BackgroundTransparency = 0.5
    Frame.Position = UDim2.new(0.5, -150, 0.5, -25)
    Frame.Size = UDim2.new(0, 300, 0, 50)

    TextLabel.Parent = Frame
    TextLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    TextLabel.BackgroundTransparency = 1
    TextLabel.Size = UDim2.new(1, 0, 1, 0)
    TextLabel.Font = Enum.Font.SourceSansBold
    TextLabel.Text = "You are not logged"
    TextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    TextLabel.TextSize = 24

    wait(3)
    ScreenGui:Destroy()
    return
end

local DarkraiX = loadstring(game:HttpGet("https://raw.githubusercontent.com/GamingScripter/Kavo-Ui/main/Darkrai%20Ui", true))()

local Library = DarkraiX:Window("🟠Slab Hub Premium🟠","","",Enum.KeyCode.RightControl);

Tab1 = Library:Tab("ESP")
Tab2 = Library:Tab("ESP Settings") -- Новый таб для настроек

-- Перемещаем слайдеры в новый таб
Tab2:Slider("ESP Red",0,255,255,function(value)
    ESPColor = Color3.new(value/255, ESPColor.G, ESPColor.B)
end)

Tab2:Slider("ESP Green",0,255,255,function(value)
    ESPColor = Color3.new(ESPColor.R, value/255, ESPColor.B)
end)

Tab2:Slider("ESP Blue",0,255,255,function(value)
    ESPColor = Color3.new(ESPColor.R, ESPColor.G, value/255)
end)

local function ESP()
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local LocalPlayer = Players.LocalPlayer
    local Camera = workspace.CurrentCamera

    ESPColor = Color3.new(1, 1, 1)

    local function CreateESP(player)
        local Box = Drawing.new("Square")
        Box.Visible = false
        Box.Color = ESPColor
        Box.Thickness = 1
        Box.Transparency = 1
        Box.Filled = false

        local Name = Drawing.new("Text")
        Name.Visible = false
        Name.Color = ESPColor
        Name.Size = 14
        Name.Center = true
        Name.Outline = true

        local HealthBar = Drawing.new("Square")
        HealthBar.Visible = false
        HealthBar.Color = Color3.fromRGB(0, 255, 0)
        HealthBar.Thickness = 1
        HealthBar.Filled = true

        local Weapon = Drawing.new("Text")
        Weapon.Visible = false
        Weapon.Color = ESPColor
        Weapon.Size = 14
        Weapon.Center = true
        Weapon.Outline = true

        local Line = Drawing.new("Line")
        Line.Visible = false
        Line.Color = ESPColor
        Line.Thickness = 1

        local Highlight = Instance.new("Highlight")
        Highlight.FillTransparency = 0.5
        Highlight.OutlineTransparency = 0

        RunService.RenderStepped:Connect(function()
            Box.Color = ESPColor
            Name.Color = ESPColor
            Weapon.Color = ESPColor
            Line.Color = ESPColor
            Highlight.OutlineColor = ESPColor

            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local HumanoidRootPart = player.Character.HumanoidRootPart
                local Vector, OnScreen = Camera:WorldToViewportPoint(HumanoidRootPart.Position)
                local Distance = (Camera.CFrame.Position - HumanoidRootPart.Position).Magnitude

                if OnScreen and player ~= LocalPlayer then
                    if BoxEsp then
                        Box.Size = Vector2.new(1000/Distance, 2000/Distance)
                        Box.Position = Vector2.new(Vector.X - Box.Size.X/2, Vector.Y - Box.Size.Y/2)
                        Box.Visible = true
                    else
                        Box.Visible = false
                    end

                    if NameEsp then
                        Name.Position = Vector2.new(Vector.X, Vector.Y - Box.Size.Y/2 - 15)
                        Name.Text = player.Name
                        Name.Visible = true
                    else
                        Name.Visible = false
                    end

                    if HealthBarEnabled and player.Character:FindFirstChild("Humanoid") then
                        local Health = player.Character.Humanoid.Health
                        local MaxHealth = player.Character.Humanoid.MaxHealth
                        HealthBar.Size = Vector2.new(2, Box.Size.Y * (Health/MaxHealth))
                        HealthBar.Position = Vector2.new(Box.Position.X - 6, Box.Position.Y)
                        HealthBar.Visible = true
                    else
                        HealthBar.Visible = false
                    end

                    if WeaponEsp then
                        local Tool = player.Character:FindFirstChildOfClass("Tool")
                        Weapon.Position = Vector2.new(Vector.X, Vector.Y + Box.Size.Y/2 + 5)
                        Weapon.Text = Tool and Tool.Name or "None"
                        Weapon.Visible = true
                    else
                        Weapon.Visible = false
                    end

                    if LineEsp then
                        Line.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                        Line.To = Vector2.new(Vector.X, Vector.Y)
                        Line.Visible = true
                    else
                        Line.Visible = false
                    end

                    if Chams then
                        Highlight.Parent = player.Character
                    else
                        Highlight.Parent = nil
                    end
                else
                    Box.Visible = false
                    Name.Visible = false
                    HealthBar.Visible = false
                    Weapon.Visible = false
                    Line.Visible = false
                    Highlight.Parent = nil
                end
            else
                Box.Visible = false
                Name.Visible = false
                HealthBar.Visible = false
                Weapon.Visible = false
                Line.Visible = false
                Highlight.Parent = nil
            end
        end)
    end

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            CreateESP(player)
        end
    end

    Players.PlayerAdded:Connect(function(player)
        CreateESP(player)
    end)
end

Tab1:Toggle("Box ESP",false,function(value)
    BoxEsp = value
end)

Tab1:Toggle("Name ESP",false,function(value)
    NameEsp = value
end)

Tab1:Toggle("Health Bar",false,function(value)
    HealthBarEnabled = value
end)

Tab1:Toggle("Weapon ESP",false,function(value)
    WeaponEsp = value
end)

Tab1:Toggle("Line ESP",false,function(value)
    LineEsp = value
end)

Tab1:Toggle("Chams",false,function(value)
    Chams = value
end)

ESP()

Tab3 = Library:Tab("Combat")

Tab3:Button("Aimbot PC",function()
  loadstring(game:HttpGet('https://pastebin.com/raw/27MKtrQB'))()
end)

Tab3:Button("AimBot Mobile",function()
  loadstring(game:HttpGet("https://raw.githubusercontent.com/Guthubt/V2COMPOVERPOWER/refs/heads/main/%25LUMI%25COMP%25OPP"))();
end)

Tab4 = Library:Tab("World")

local chinaHat = nil
local connection = nil

Tab4:Toggle("China Hat",false,function(value)
    if value then
        chinaHat = Instance.new("Part")
        chinaHat.Name = "ChinaHat"
        chinaHat.Size = Vector3.new(2, 2, 2)
        chinaHat.Material = Enum.Material.Neon
        chinaHat.CanCollide = false
        chinaHat.Transparency = 0.3
        
        local mesh = Instance.new("SpecialMesh")
        mesh.MeshType = Enum.MeshType.FileMesh
        mesh.MeshId = "rbxassetid://1778999"
        mesh.Scale = Vector3.new(3, 1, 3)
        mesh.Parent = chinaHat
        
        local weld = Instance.new("Weld")
        weld.Part0 = game.Players.LocalPlayer.Character.Head
        weld.Part1 = chinaHat
        weld.C0 = CFrame.new(0, 1.5, 0)
        weld.Parent = chinaHat
        chinaHat.Parent = game.Players.LocalPlayer.Character

        local hue = 0
        connection = game:GetService("RunService").RenderStepped:Connect(function()
            hue = (hue + 0.001) % 1
            chinaHat.Color = Color3.fromHSV(hue, 1, 1)
        end)
    else
        if connection then
            connection:Disconnect()
        end
        if chinaHat then
            chinaHat:Destroy()
            chinaHat = nil
        end
    end
end)

Tab4:Button("float",function()
  local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

local isBoostJumping = false
local boostForce = 120
local canBoostJump = true

local function startBoostJump()
    if canBoostJump and not isBoostJumping then
        isBoostJumping = true
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        
        local lookVector = workspace.CurrentCamera.CFrame.LookVector
        local boostDirection = Vector3.new(lookVector.X, 0.5, lookVector.Z).Unit
        rootPart.Velocity = boostDirection * boostForce
        
        canBoostJump = false
        wait(0.3)
        canBoostJump = true
        isBoostJumping = false
    end
end

UserInputService.JumpRequest:Connect(startBoostJump)

player.CharacterAdded:Connect(function(newCharacter)
    character = newCharacter
    humanoid = character:WaitForChild("Humanoid")
    rootPart = character:WaitForChild("HumanoidRootPart")
    canBoostJump = true
end)
end)

Tab5 = Library:Tab("Crosshair")

Tab5:Button("卍 Spin",function()
  loadstring(game:HttpGet('https://pastebin.com/raw/V3jxzaUt'))()
end)

Tab5:Button("ة spin",function()
  loadstring(game:HttpGet('https://pastebin.com/raw/L1KGmESi'))()
end)

Tab5:Button("# spin",function()
  loadstring(game:HttpGet('https://pastebin.com/raw/kz3csTFf'))()
end)

Tab5:Button("0-0 spin",function()
  loadstring(game:HttpGet('https://pastebin.com/raw/57MxBTbc'))()
end)

Tab5:Button("ioi spin",function()
  loadstring(game:HttpGet('https://pastebin.com/raw/sksNSYfK'))()
end)

Tab5:Button("delete crosshair",function()
  local PlayerGui = game.Players.LocalPlayer:WaitForChild("PlayerGui")
local crosshair = PlayerGui:FindFirstChild("ScreenGui")

if crosshair then
    crosshair:Destroy()
end
end)
