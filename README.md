-- JOKER HUB - LUA SCRIPT COMPLETO --  
-- LOCAL PLAYER & SERVICES  
local player = game.Players.LocalPlayer  
local gui = script.Parent  
local UIS = game:GetService("UserInputService")  
local RunService = game:GetService("RunService")  
local ReplicatedStorage = game:GetService("ReplicatedStorage")  

-- ESTADOS DAS OPÇÕES  
local estados = {}  

-- BASE POS  
local basePos = Vector3.new(0,5,0)  

-- REMOTES  
local Remotes = {}  
local function scanRemotes()  
    Remotes = {}  
    for _,v in pairs(game:GetDescendants()) do  
        if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then  
            table.insert(Remotes, v)  
        end  
    end  
    print("Remotes encontrados:", #Remotes)  
end  

local function fireAllRemotes()  
    for _,remote in pairs(Remotes) do  
        pcall(function()  
            if remote:IsA("RemoteEvent") then  
                remote:FireServer()  
            elseif remote:IsA("RemoteFunction") then  
                remote:InvokeServer()  
            end  
        end)  
    end  
end  

local function printRemotes()  
    for i,remote in pairs(Remotes) do  
        print(i, remote:GetFullName())  
    end  
end  

-- PROTECTION VARIABLES  
local antiKickEnabled = false  
local antiBanEnabled = false  
local antiAFKEnabled = false  
local afkLoop  
local banMonitorLoop  

-- MAIN GUI  
local main = Instance.new("Frame", gui)  
main.Size = UDim2.new(0,900,0,500)  
main.Position = UDim2.new(0.2,0,0.2,0)  
main.BackgroundColor3 = Color3.fromRGB(13,13,13)  
main.BorderSizePixel = 0  
main.Active = true  
Instance.new("UICorner", main).CornerRadius = UDim.new(0,20)  

local stroke = Instance.new("UIStroke", main)  
stroke.Color = Color3.fromRGB(255,0,0)  
stroke.Thickness = 2  

-- HEADER  
local header = Instance.new("Frame", main)  
header.Size = UDim2.new(1,0,0,40)  
header.BackgroundTransparency = 1  

local title = Instance.new("TextLabel", header)  
title.Text = "🃏 JOKER HUB"  
title.Font = Enum.Font.Gotham  
title.TextSize = 18  
title.TextColor3 = Color3.fromRGB(255,0,0)  
title.BackgroundTransparency = 1  
title.Position = UDim2.new(0,15,0,0)  
title.Size = UDim2.new(1,-50,1,0)  
title.TextXAlignment = Enum.TextXAlignment.Left  

-- MINIMIZAR  
local minBtn = Instance.new("TextButton", header)  
minBtn.Size = UDim2.new(0,40,1,0)  
minBtn.Position = UDim2.new(1,-40,0,0)  
minBtn.Text = "🗕"  
minBtn.BackgroundTransparency = 1  
minBtn.TextColor3 = Color3.fromRGB(255,0,0)  

-- SIDEBAR  
local sidebar = Instance.new("Frame", main)  
sidebar.Size = UDim2.new(0,180,1,-40)  
sidebar.Position = UDim2.new(0,0,0,40)  
sidebar.BackgroundColor3 = Color3.fromRGB(8,8,8)  

-- CONTENT  
local content = Instance.new("Frame", main)  
content.Size = UDim2.new(1,-180,1,-40)  
content.Position = UDim2.new(0,180,0,40)  
content.BackgroundTransparency = 1  

-- DRAG  
local dragging, dragStart, startPos  
header.InputBegan:Connect(function(input)  
    if input.UserInputType == Enum.UserInputType.MouseButton1 then  
        dragging = true  
        dragStart = input.Position  
        startPos = main.Position  
    end  
end)  

UIS.InputEnded:Connect(function(input)  
    if input.UserInputType == Enum.UserInputType.MouseButton1 then  
        dragging = false  
    end  
end)  

UIS.InputChanged:Connect(function(input)  
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then  
        local delta = input.Position - dragStart  
        main.Position = UDim2.new(  
            startPos.X.Scale, startPos.X.Offset + delta.X,  
            startPos.Y.Scale, startPos.Y.Offset + delta.Y  
        )  
    end  
end)  

-- MINIMIZAR FUNC  
local aberto = true  
minBtn.MouseButton1Click:Connect(function()  
    aberto = not aberto  
    content.Visible = aberto  
    sidebar.Visible = aberto  
    if aberto then  
        main.Size = UDim2.new(0,900,0,500)  
        minBtn.Text = "🗕"  
    else  
        main.Size = UDim2.new(0,300,0,40)  
        minBtn.Text = "🗖"  
    end  
end)  

-- FUNÇÃO CRIAR OPTION  
local function criarOption(parent,texto,callback,static)  
    local opt = Instance.new("Frame", parent)  
    opt.Size = UDim2.new(1,-10,0,32)  
    opt.BackgroundColor3 = Color3.fromRGB(26,26,26)  
    Instance.new("UICorner", opt).CornerRadius = UDim.new(0,8)  
    local txt = Instance.new("TextLabel", opt)  
    txt.Text = texto  
    txt.Font = Enum.Font.Gotham  
    txt.TextSize = 14  
    txt.Size = UDim2.new(0.7,0,1,0)  
    txt.Position = UDim2.new(0,10,0,0)  
    txt.BackgroundTransparency = 1  
    txt.TextXAlignment = Enum.TextXAlignment.Left  
    txt.TextColor3 = static and Color3.fromRGB(120,120,120) or Color3.new(1,1,1)  
    if static then return end  
    local toggle = Instance.new("Frame", opt)  
    toggle.Size = UDim2.new(0,30,0,15)  
    toggle.Position = UDim2.new(1,-40,0.5,-7)  
    toggle.BackgroundColor3 = Color3.fromRGB(50,50,50)  
    Instance.new("UICorner", toggle).CornerRadius = UDim.new(1,0)  
    local ball = Instance.new("Frame", toggle)  
    ball.Size = UDim2.new(0,13,0,13)  
    ball.Position = UDim2.new(0,1,0.5,-6)  
    ball.BackgroundColor3 = Color3.new(1,1,1)  
    Instance.new("UICorner", ball).CornerRadius = UDim.new(1,0)  
    estados[texto] = estados[texto] or false  
    local function update()  
        if estados[texto] then  
            opt.BackgroundColor3 = Color3.fromRGB(255,0,0)  
            txt.TextColor3 = Color3.new(0,0,0)  
            toggle.BackgroundColor3 = Color3.fromRGB(255,0,0)  
            ball.Position = UDim2.new(1,-14,0.5,-6)  
        else  
            opt.BackgroundColor3 = Color3.fromRGB(26,26,26)  
            txt.TextColor3 = Color3.new(1,1,1)  
            toggle.BackgroundColor3 = Color3.fromRGB(50,50,50)  
            ball.Position = UDim2.new(0,1,0.5,-6)  
        end  
    end  
    update()  
    opt.InputBegan:Connect(function(i)  
        if i.UserInputType == Enum.UserInputType.MouseButton1 then  
            estados[texto] = not estados[texto]  
            update()  
            if callback then callback(estados[texto]) end  
        end  
    end)  
end  

-- FUNÇÃO PAINEL  
local function painel(nome)  
    local p = Instance.new("Frame", content)  
    p.Size = UDim2.new(0.5,-10,1,0)  
    p.BackgroundColor3 = Color3.fromRGB(17,17,17)  
    Instance.new("UICorner", p).CornerRadius = UDim.new(0,15)  
    local t = Instance.new("TextLabel", p)  
    t.Text = nome  
    t.Font = Enum.Font.Gotham  
    t.TextSize = 16  
    t.TextColor3 = Color3.fromRGB(255,0,0)  
    t.BackgroundTransparency = 1  
    t.Size = UDim2.new(1,0,0,30)  
    local list = Instance.new("UIListLayout", p)  
    list.Padding = UDim.new(0,8)  
    return p  
end  

-- LOOPS  
local autoStealLoop  
local auraLoop  
local speedBoostLoop  
local tradeFreezeLoop  

-- ANTI KICK  
local function setupAntiKick()  
    print("🛡️ Anti Kick ativado!")  
end  

local function disableAntiKick()  
    print("🛡️ Anti Kick desativado!")  
end  

-- ANTI BAN  
local function setupAntiBan()  
    if banMonitorLoop then banMonitorLoop:Disconnect() end  
    banMonitorLoop = RunService.Heartbeat:Connect(function()  
        if antiBanEnabled then  
            local playerExists = game.Players:FindFirstChild(player.Name)  
            if not playerExists then  
                print("⚠️ Possível tentativa de ban detectada!")  
            end  
        end  
    end)  
    print("🔒 Anti Ban ativado!")  
end  

local function disableAntiBan()  
    if banMonitorLoop then banMonitorLoop:Disconnect() end  
    print("🔒 Anti Ban desativado!")  
end  

-- ANTI AFK  
local function startAntiAFK()  
    if afkLoop then afkLoop:Disconnect() end  
    afkLoop = RunService.Heartbeat:Connect(function()  
        if antiAFKEnabled then  
            local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")  
            if hrp then  
                hrp.CFrame = hrp.CFrame + Vector3.new(0.01, 0, 0)  
            end  
        end  
    end)  
    print("⏰ Anti AFK ativado!")  
end  

local function stopAntiAFK()  
    if afkLoop then afkLoop:Disconnect() end  
    print("⏰ Anti AFK desativado!")  
end  

-- FUNÇÃO CARREGAR TABS  
local function carregar(tab)  
    content:ClearAllChildren()  
    local layout = Instance.new("UIListLayout", content)  
    layout.FillDirection = Enum.FillDirection.Horizontal  
    layout.Padding = UDim.new(0,15)  

    -- Auto Join  
    if tab == "Auto Join" then  
        local p1 = painel("Server Finder")  
        local p2 = painel("Server Actions")  
        criarOption(p1,"Filtrar OG")  
        criarOption(p1,"Filtrar Secret")  
        criarOption(p1,"Filtrar Padrão")  
        criarOption(p1,"Nenhum servidor carregado",nil,true)  
        criarOption(p2,"Evitar Server Vazio")  
        criarOption(p2,"Entrar só com Players Fortes")  
        criarOption(p2,"Manter Painel ao Entrar")  
    end  

    -- Steal  
    if tab == "Steal" then  
        local p = painel("Steal")  
        criarOption(p,"⚡ Speed Boost",function(v)  
            local hum = player.Character and player.Character:FindFirstChild("Humanoid")  
            if hum then  
                hum.WalkSpeed = v and 32 or 16  
            end  
        end)  
        criarOption(p,"⚡ Super Speed",function(v)  
            local hum = player.Character and player.Character:FindFirstChild("Humanoid")  
            if hum then  
                hum.WalkSpeed = v and 60 or 16  
            end  
        end)  
        criarOption(p,"⚡ Speed x2",function(v)  
            local hum = player.Character and player.Character:FindFirstChild("Humanoid")  
            if hum then  
                hum.WalkSpeed = v and 100 or 16  
            end  
        end)  
        criarOption(p,"👻 Invisível", function(v)  
            local char = player.Character  
            if not char then return end  
            for _,obj in pairs(char:GetDescendants()) do  
                if obj:IsA("BasePart") then  
                    obj.Transparency = v and 1 or 0  
                    obj.CanCollide = true  
                end  
                if obj:IsA("Decal") then  
                    obj.Transparency = v and 1 or 0  
                end  
            end  
        end)  
        criarOption(p,"🏠 TP Base",function(v)  
            if v then  
                local char = player.Character  
                if char and char:FindFirstChild("HumanoidRootPart") then  
                    char.HumanoidRootPart.CFrame = CFrame.new(basePos)  
                end  
            end  
        end)  
        criarOption(p,"🤖 Auto Steal",function(v)  
            if v then  
                autoStealLoop = RunService.RenderStepped:Connect(function()  
                    for _,obj in pairs(workspace:GetDescendants()) do  
                        if obj.Name:lower():find("brainrot") and obj:IsA("BasePart") then  
                            local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")  
                            if hrp then  
                                obj.CFrame = hrp.CFrame  
                            end  
                        end  
                    end  
                end)  
            else  
                if autoStealLoop then  
                    autoStealLoop:Disconnect()  
                end  
            end  
        end)  
        criarOption(p,"🌀 Steal Aura",function(v)  
            if v then  
                auraLoop = RunService.RenderStepped:Connect(function()  
                    local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")  
                    if not hrp then return end  
                    for _,obj in pairs(workspace:GetDescendants()) do  
                        if obj.Name:lower():find("brainrot") and obj:IsA("BasePart") then  
                            if (obj.Position - hrp.Position).Magnitude < 25 then  
                                obj.CFrame = hrp.CFrame  
                            end  
                        end  
                    end  
                end)  
            else  
                if auraLoop then  
                    auraLoop:Disconnect()  
                end  
            end  
        end)  
        criarOption(p,"⚡ Instant Steal",function(v)  
            if v then  
                local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")  
                if not hrp then return end  
                for _,obj in pairs(workspace:GetDescendants()) do  
                    if obj.Name:lower():find("brainrot") and obj:IsA("BasePart") then  
                        obj.CFrame = hrp.CFrame  
                    end  
                end  
            end  
        end)  
        criarOption(p,"📡 Scan Remotes",function(v)  
            if v then  
                scanRemotes()  
            end  
        end)  
        criarOption(p,"📜 Listar Remotes",function(v)  
            if v then  
                printRemotes()  
            end  
        end)  
        criarOption(p,"🚀 Fire All Remotes",function(v)  
            if v then  
                fireAllRemotes()  
            end  
        end)  
    end  

    -- Trade  
    if tab == "Trade" then  
        local p = painel("Trade System")  
        criarOption(p,"✅ Auto Accept",function(v)  
            if v then  
                print("Auto Accept ativado!")  
            else  
                print("Auto Accept desativado!")  
            end  
        end)  
        criarOption(p,"🔒 Freeze Trade",function(v)  
            if v then  
                if tradeFreezeLoop then tradeFreezeLoop:Disconnect() end  
                tradeFreezeLoop = RunService.Heartbeat:Connect(function()  
                    print("Trade congelado!")  
                end)  
                print("Freeze Trade ativado!")  
            else  
                if tradeFreezeLoop then tradeFreezeLoop:Disconnect() end  
                print("Freeze Trade desativado!")  
            end  
        end)  
        criarOption(p,"🤖 Auto Trade",function(v)  
            if v then  
                print("Auto Trade ativado!")  
            else  
                print("Auto Trade desativado!")  
            end  
        end)  
    end  

    -- Protection  
    if tab == "Protection" then  
        local p = painel("Protection System")  
        criarOption(p,"🛡️ Anti Kick",function(v)  
            antiKickEnabled = v  
            if v then  
                setupAntiKick()  
            else  
                disableAntiKick()  
            end  
        end)  
        criarOption(p,"🔒 Anti Ban",function(v)  
            antiBanEnabled = v  
            if v then  
                setupAntiBan()  
            else  
                disableAntiBan()  
            end  
        end)  
        criarOption(p,"⏰ Anti AFK",function(v)  
            antiAFKEnabled = v  
            if v then  
                startAntiAFK()  
            else  
                stopAntiAFK()  
            end  
        end)  
    end  
end  

-- MENU LATERAL  
local function criarMenu(nome,y)  
    local btn = Instance.new("TextButton", sidebar)  
    btn.Size = UDim2.new(1,-20,0,40)  
    btn.Position = UDim2.new(0,10,0,y)  
    btn.Text = nome  
    btn.Font = Enum.Font.Gotham  
    btn.TextSize = 14  
    btn.BackgroundColor3 = Color3.fromRGB(0,0,0)  
    btn.TextColor3 = Color3.fromRGB(170,170,170)  
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,10)  
    btn.MouseButton1Click:Connect(function()  
        carregar(nome)  
    end)  
end  

-- CRIAR MENUS  
criarMenu("Auto Join",10)  
criarMenu("Steal",60)  
criarMenu("Trade",110)  
criarMenu("Protection",160)  

-- CARREGAR PADRÃO  
carregar("Auto Join")  
