UserInputService = game:GetService("UserInputService")
RunService = game:GetService("RunService")

local ESPZombiesT = false
local billboardGuis = {}
local AutorepairT = false

function addText()
    local zombiesFolder = game.Workspace.Zombies
    for _, zombieModel in pairs(zombiesFolder:GetChildren()) do
        if zombieModel:IsA("Model") then
            local primaryPart = zombieModel.PrimaryPart
            if primaryPart then
                local billboardGui = Instance.new("BillboardGui")
                billboardGui.Adornee = primaryPart
                billboardGui.AlwaysOnTop = true
                billboardGui.Size = UDim2.new(0, 200, 0, 50)
                billboardGui.StudsOffset = Vector3.new(0, 4, 0)

                local textLabel = Instance.new("TextLabel")
                textLabel.Parent = billboardGui
                textLabel.BackgroundTransparency = 1
                textLabel.Size = UDim2.new(1, 0, 1, 0)
                textLabel.TextColor3 = Color3.new(0, 1, 1)
                textLabel.Font = Enum.Font.SourceSans
                textLabel.TextSize = 20

                local zombieType = zombieModel:GetAttribute("Type")
                if zombieType == "Normal" then
                    textLabel.Text = "僵尸"
                elseif zombieType == "Barrel" then
                    textLabel.Text = "自爆僵尸"
                    textLabel.TextColor3 = Color3.new(1, 0, 0)
                elseif zombieType == "Fast" then
                    textLabel.Text = "红眼"
                    textLabel.TextColor3 = Color3.new(1, 0, 0)
                elseif zombieType == "Sapper" then
                    textLabel.Text = "大师兄"
                elseif zombieType == "Igniter" then
                    textLabel.Text = "提灯人"
                end
                billboardGui.Parent = primaryPart
                table.insert(billboardGuis, billboardGui)
            end
        end
    end
end

function removeText()
    for _, billboardGui in pairs(billboardGuis) do
        billboardGui:Destroy()
    end
end

function ESPZombies()
    while ESPZombiesT == true do
        addText()
        wait(1)
        removeText()
    end
end

function Autorepair()
    while AutorepairT == true do
        local Players = game:GetService("Players")
        local localPlayer = Players.LocalPlayer
        local workspace = game:GetService("Workspace")
        local camera = workspace.CurrentCamera
        local character = localPlayer.Character
        local playerId = tostring(localPlayer.UserId)
        local buildingsFolder = workspace:FindFirstChild("Buildings")
        local playerSpecificFolder = buildingsFolder:FindFirstChild(playerId)

        local minDistance = math.huge
        local nearestBuildingHealth = nil

        local screenCenterX = camera.ViewportSize.X / 2
        local screenCenterY = camera.ViewportSize.Y / 2

        for _, model in ipairs(playerSpecificFolder:GetChildren()) do
            if model:IsA("Model") then
                local buildingHealth = model:FindFirstChild("BuildingHealth")
                if buildingHealth then
                    local hitbox = model:FindFirstChild("Hitbox")
                    if hitbox then
                        local screenPosition, onScreen = camera:WorldToScreenPoint(hitbox.CFrame.Position)
                        if onScreen then
                            local distance = (Vector2.new(screenPosition.X, screenPosition.Y) - Vector2.new(screenCenterX, screenCenterY)).Magnitude
                            if distance < minDistance then
                                minDistance = distance
                                nearestBuildingHealth = buildingHealth
                            end
                        end
                    end
                end
            end
        end

        local caltrops = workspace:FindFirstChild("Caltrops")
        if caltrops and caltrops:IsA("Model") then
            local buildingHealth = caltrops:FindFirstChild("BuildingHealth")
            if buildingHealth then
                local hitbox = caltrops:FindFirstChild("Hitbox")
                if hitbox then
                    local screenPosition, onScreen = camera:WorldToScreenPoint(hitbox.CFrame.Position)
                    if onScreen then
                        local distance = (Vector2.new(screenPosition.X, screenPosition.Y) - Vector2.new(screenCenterX, screenCenterY)).Magnitude
                        if distance < minDistance then
                            minDistance = distance
                            nearestBuildingHealth = buildingHealth
                        end
                    end
                end
            end
        end

        local stakes = workspace:FindFirstChild("Stakes")
        if stakes and stakes:IsA("Model") then
            local buildingHealth = stakes:FindFirstChild("BuildingHealth")
            if buildingHealth then
                local hitbox = stakes:FindFirstChild("Hitbox")
                if hitbox then
                    local screenPosition, onScreen = camera:WorldToScreenPoint(hitbox.CFrame.Position)
                    if onScreen then
                        local distance = (Vector2.new(screenPosition.X, screenPosition.Y) - Vector2.new(screenCenterX, screenCenterY)).Magnitude
                        if distance < minDistance then
                            minDistance = distance
                            nearestBuildingHealth = buildingHealth
                        end
                    end
                end
            end
        end

        if nearestBuildingHealth then
            local args = {
                [1] = "Repair",
                [2] = nearestBuildingHealth
            }
            local hammer = character:FindFirstChild("Hammer")
            if hammer then
                local remoteEvent = hammer:FindFirstChild("RemoteEvent")
                if remoteEvent then
                    remoteEvent:FireServer(unpack(args))
                end
            end
        end
        task.wait()
    end
end

local Rayfield = loadstring(game:HttpGet("https://gitee.com/roblox-smk/rayfield/raw/master/Rayfield"))()

local Window =
    Rayfield:CreateWindow(
    {
        Name = "Rodhub",
        Icon = 0,
        LoadingTitle = "加载中",
        LoadingSubtitle = "小枫制作",
        Theme = "Default",
        DisableRayfieldPrompts = false,
        DisableBuildWarnings = false,
        ConfigurationSaving = {
            Enabled = false,
            FolderName = nil,
            FileName = ""
        },
        Discord = {
            Enabled = false,
            Invite = "",
            RememberJoins = true
        },
        KeySystem = false,
        KeySettings = {
            Title = "",
            Subtitle = "",
            Note = "",
            FileName = "",
            SaveKey = true,
            GrabKeyFromSite = false,
            Key = {""}
        }
    }
)

local MainTab = Window:CreateTab("主页")

local Button =
    MainTab:CreateButton(
    {
        Name = "关闭",
        Callback = function()
            Rayfield:Destroy()
        end
    }
)

local tpwalking
local Noclipping
local flyjump
local drawUserNamesT
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local billboardGuis = {}
local function onPlayerAdded(player)
    local function onCharacterAdded(character)
        local head = character:FindFirstChild("Head")
        if head then
            local billboardGui = Instance.new("BillboardGui")
            billboardGui.Parent = head
            billboardGui.AlwaysOnTop = true
            billboardGui.Size = UDim2.new(0, 200, 0, 50)
            billboardGui.StudsOffset = Vector3.new(0, 3, 0)
            local textLabel = Instance.new("TextLabel")
            textLabel.Parent = billboardGui
            textLabel.Size = UDim2.new(1, 0, 1, 0)
            textLabel.BackgroundTransparency = 1
            textLabel.TextColor3 = Color3.new(1, 1, 1)
            textLabel.Font = Enum.Font.SourceSansBold
            textLabel.TextSize = 14
            textLabel.TextStrokeTransparency = 0.3
            textLabel.TextStrokeColor3 = Color3.new(0, 0, 0)
            textLabel.Text = player.Name
            table.insert(billboardGuis, billboardGui)
        end
    end
    player.CharacterAdded:Connect(onCharacterAdded)
end

local function Drawusernames()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= Players.LocalPlayer then
            local character = player.Character
            if character then
                local head = character:FindFirstChild("Head")
                if head then
                    local billboardGui = Instance.new("BillboardGui")
                    billboardGui.Parent = head
                    billboardGui.AlwaysOnTop = true
                    billboardGui.Size = UDim2.new(0, 200, 0, 50)
                    billboardGui.StudsOffset = Vector3.new(0, 6, 0)
                    local textLabel = Instance.new("TextLabel")
                    textLabel.Parent = billboardGui
                    textLabel.Size = UDim2.new(1, 0, 1, 0)
                    textLabel.BackgroundTransparency = 1
                    textLabel.TextColor3 = Color3.new(1, 1, 1)
                    textLabel.Font = Enum.Font.SourceSansBold
                    textLabel.TextSize = 14
                    textLabel.TextStrokeTransparency = 0.3
                    textLabel.TextStrokeColor3 = Color3.new(0, 0, 0)
                    textLabel.Text = player.Name
                    table.insert(billboardGuis, billboardGui)
                end
            end
        end
    end
end

local function RemoveDrawusernames()
    for _, gui in pairs(billboardGuis) do
        gui:Destroy()
    end
    billboardGuis = {}
end

local GutsBlackpowder = Window:CreateTab("内脏与黑火药")

local Section = GutsBlackpowder:CreateSection("先开加速再输入速度，想变慢加速重开重新输入速度")

local Input =
    GutsBlackpowder:CreateInpu
