--// ✨ BLESSED HUB COMPLETE ✨
--// DESIGN HTML PREMIUM CONVERTIDO PARA LUA
--// LOCAL SCRIPT

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "BlessedHub"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- ESTADOS
local estados = {}

-- REMOTES
local Remotes = {}

local function scanRemotes()
	Remotes = {}

	for _,v in pairs(game:GetDescendants()) do
		if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
			table.insert(Remotes,v)
		end
	end

	print("📡 Remotes encontrados:",#Remotes)
end

local function fireAllRemotes()
	for _,v in pairs(Remotes) do
		pcall(function()
			if v:IsA("RemoteEvent") then
				v:FireServer()
			elseif v:IsA("RemoteFunction") then
				v:InvokeServer()
			end
		end)
	end
end

local function printRemotes()
	for i,v in pairs(Remotes) do
		print(i,v:GetFullName())
	end
end

-- MAIN
local main = Instance.new("Frame")
main.Parent = gui
main.Size = UDim2.new(0,700,0,420)
main.Position = UDim2.new(0.5,-350,0.5,-210)
main.BackgroundColor3 = Color3.fromRGB(13,13,13)
main.BorderSizePixel = 0
main.Active = true

Instance.new("UICorner",main).CornerRadius = UDim.new(0,20)

local stroke = Instance.new("UIStroke")
stroke.Parent = main
stroke.Color = Color3.fromRGB(255,215,0)
stroke.Thickness = 2

-- SHADOW
local shadow = Instance.new("ImageLabel")
shadow.Parent = main
shadow.BackgroundTransparency = 1
shadow.AnchorPoint = Vector2.new(0.5,0.5)
shadow.Position = UDim2.new(0.5,0,0.5,0)
shadow.Size = UDim2.new(1,60,1,60)
shadow.ZIndex = 0
shadow.Image = "rbxassetid://1316045217"
shadow.ImageTransparency = 0.5

-- HEADER
local header = Instance.new("Frame")
header.Parent = main
header.Size = UDim2.new(1,0,0,40)
header.BackgroundTransparency = 1

local title = Instance.new("TextLabel")
title.Parent = header
title.Text = "✨ BLESSED HUB"
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextColor3 = Color3.fromRGB(255,215,0)
title.BackgroundTransparency = 1
title.Position = UDim2.new(0,15,0,0)
title.Size = UDim2.new(1,-50,1,0)
title.TextXAlignment = Enum.TextXAlignment.Left

-- MIN BUTTON
local minBtn = Instance.new("TextButton")
minBtn.Parent = header
minBtn.Size = UDim2.new(0,40,1,0)
minBtn.Position = UDim2.new(1,-40,0,0)
minBtn.BackgroundTransparency = 1
minBtn.Text = "🗕"
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 18
minBtn.TextColor3 = Color3.fromRGB(255,215,0)

-- SIDEBAR
local sidebar = Instance.new("Frame")
sidebar.Parent = main
sidebar.Size = UDim2.new(0,180,1,-40)
sidebar.Position = UDim2.new(0,0,0,40)
sidebar.BackgroundColor3 = Color3.fromRGB(8,8,8)
sidebar.BorderSizePixel = 0

-- CONTENT
local content = Instance.new("Frame")
content.Parent = main
content.Size = UDim2.new(1,-180,1,-40)
content.Position = UDim2.new(0,180,0,40)
content.BackgroundTransparency = 1

local contentLayout = Instance.new("UIListLayout")
contentLayout.Parent = content
contentLayout.FillDirection = Enum.FillDirection.Horizontal
contentLayout.Padding = UDim.new(0,15)

-- DRAG
local dragging = false
local dragStart
local startPos

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
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)

	end
end)

-- MINIMIZE
local aberto = true

minBtn.MouseButton1Click:Connect(function()

	aberto = not aberto

	sidebar.Visible = aberto
	content.Visible = aberto

	if aberto then

		TweenService:Create(
			main,
			TweenInfo.new(0.2),
			{
				Size = UDim2.new(0,700,0,420)
			}
		):Play()

		minBtn.Text = "🗕"

	else

		TweenService:Create(
			main,
			TweenInfo.new(0.2),
			{
				Size = UDim2.new(0,260,0,40)
			}
		):Play()

		minBtn.Text = "🗖"

	end
end)

-- OPTION
local function criarOption(parent,texto,callback,static)

	local opt = Instance.new("Frame")
	opt.Parent = parent
	opt.Size = UDim2.new(1,-10,0,36)
	opt.BackgroundColor3 = Color3.fromRGB(26,26,26)
	opt.BorderSizePixel = 0

	Instance.new("UICorner",opt).CornerRadius = UDim.new(0,8)

	local txt = Instance.new("TextLabel")
	txt.Parent = opt
	txt.Text = texto
	txt.Font = Enum.Font.Gotham
	txt.TextSize = 14
	txt.TextColor3 = static and Color3.fromRGB(120,120,120) or Color3.new(1,1,1)
	txt.BackgroundTransparency = 1
	txt.Position = UDim2.new(0,10,0,0)
	txt.Size = UDim2.new(0.7,0,1,0)
	txt.TextXAlignment = Enum.TextXAlignment.Left

	if static then
		return
	end

	local toggle = Instance.new("Frame")
	toggle.Parent = opt
	toggle.Size = UDim2.new(0,30,0,15)
	toggle.Position = UDim2.new(1,-40,0.5,-7)
	toggle.BackgroundColor3 = Color3.fromRGB(51,51,51)

	Instance.new("UICorner",toggle).CornerRadius = UDim.new(1,0)

	local ball = Instance.new("Frame")
	ball.Parent = toggle
	ball.Size = UDim2.new(0,13,0,13)
	ball.Position = UDim2.new(0,1,0.5,-6)
	ball.BackgroundColor3 = Color3.new(1,1,1)

	Instance.new("UICorner",ball).CornerRadius = UDim.new(1,0)

	estados[texto] = estados[texto] or false

	local function update()

		if estados[texto] then

			TweenService:Create(opt,TweenInfo.new(0.15),{
				BackgroundColor3 = Color3.fromRGB(255,215,0)
			}):Play()

			TweenService:Create(toggle,TweenInfo.new(0.15),{
				BackgroundColor3 = Color3.fromRGB(255,215,0)
			}):Play()

			TweenService:Create(ball,TweenInfo.new(0.15),{
				Position = UDim2.new(1,-14,0.5,-6)
			}):Play()

			txt.TextColor3 = Color3.new(0,0,0)

		else

			TweenService:Create(opt,TweenInfo.new(0.15),{
				BackgroundColor3 = Color3.fromRGB(26,26,26)
			}):Play()

			TweenService:Create(toggle,TweenInfo.new(0.15),{
				BackgroundColor3 = Color3.fromRGB(51,51,51)
			}):Play()

			TweenService:Create(ball,TweenInfo.new(0.15),{
				Position = UDim2.new(0,1,0.5,-6)
			}):Play()

			txt.TextColor3 = Color3.new(1,1,1)

		end
	end

	update()

	opt.InputBegan:Connect(function(i)

		if i.UserInputType == Enum.UserInputType.MouseButton1 then

			estados[texto] = not estados[texto]

			update()

			if callback then
				callback(estados[texto])
			end
		end
	end)
end

-- PANEL
local function painel(nome)

	local p = Instance.new("Frame")
	p.Parent = content
	p.Size = UDim2.new(0.5,-10,1,0)
	p.BackgroundColor3 = Color3.fromRGB(17,17,17)
	p.BorderSizePixel = 0

	Instance.new("UICorner",p).CornerRadius = UDim.new(0,15)

	local pad = Instance.new("UIPadding")
	pad.Parent = p
	pad.PaddingTop = UDim.new(0,10)
	pad.PaddingLeft = UDim.new(0,10)
	pad.PaddingRight = UDim.new(0,10)

	local title = Instance.new("TextLabel")
	title.Parent = p
	title.Text = nome
	title.Font = Enum.Font.GothamBold
	title.TextSize = 16
	title.TextColor3 = Color3.fromRGB(255,215,0)
	title.BackgroundTransparency = 1
	title.Size = UDim2.new(1,0,0,30)
	title.TextXAlignment = Enum.TextXAlignment.Left

	local list = Instance.new("UIListLayout")
	list.Parent = p
	list.Padding = UDim.new(0,8)

	return p
end

-- CLEAR CONTENT
local function clearContent()

	for _,v in pairs(content:GetChildren()) do
		if not v:IsA("UIListLayout") then
			v:Destroy()
		end
	end
end

-- LOAD TABS
function carregar(tab)

	clearContent()

	-- AUTO JOIN
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

	-- STEAL
	if tab == "Steal" then

		local p1 = painel("Steal")
		local p2 = painel("Extras")

		criarOption(p1,"⚡ Speed Boost",function(v)

			local hum = player.Character and player.Character:FindFirstChild("Humanoid")

			if hum then
				hum.WalkSpeed = v and 32 or 16
			end
		end)

		criarOption(p1,"⚡ Super Speed",function(v)

			local hum = player.Character and player.Character:FindFirstChild("Humanoid")

			if hum then
				hum.WalkSpeed = v and 60 or 16
			end
		end)

		criarOption(p1,"⚡ Speed x2",function(v)

			local hum = player.Character and player.Character:FindFirstChild("Humanoid")

			if hum then
				hum.WalkSpeed = v and 100 or 16
			end
		end)

		criarOption(p1,"👻 Invisível",function(v)

			local char = player.Character

			if not char then return end

			for _,obj in pairs(char:GetDescendants()) do

				if obj:IsA("BasePart") then
					obj.Transparency = v and 1 or 0
				end

				if obj:IsA("Decal") then
					obj.Transparency = v and 1 or 0
				end
			end
		end)

		criarOption(p1,"🏠 TP Base",function(v)

			if v then

				local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")

				if hrp then
					hrp.CFrame = CFrame.new(0,5,0)
				end
			end
		end)

		criarOption(p2,"🤖 Auto Steal")
		criarOption(p2,"🌀 Steal Aura")
		criarOption(p2,"⚡ Instant Steal")

		criarOption(p2,"📡 Scan Remotes",function(v)
			if v then
				scanRemotes()
			end
		end)

		criarOption(p2,"📜 Listar Remotes",function(v)
			if v then
				printRemotes()
			end
		end)

		criarOption(p2,"🚀 Fire All Remotes",function(v)
			if v then
				fireAllRemotes()
			end
		end)

	end

	-- TRADE
	if tab == "Trade" then

		local p = painel("Trade System")

		criarOption(p,"✅ Auto Accept")
		criarOption(p,"🔒 Freeze Trade")
		criarOption(p,"🤖 Auto Trade")

	end

	-- PROTECTION
	if tab == "Protection" then

		local p = painel("Protection")

		criarOption(p,"🛡️ Anti Kick")
		criarOption(p,"🔒 Anti Ban")
		criarOption(p,"⏰ Anti AFK")

	end
end

-- MENUS
local menus = {}

local function criarMenu(nome,y)

	local btn = Instance.new("TextButton")
	btn.Parent = sidebar
	btn.Size = UDim2.new(1,-20,0,40)
	btn.Position = UDim2.new(0,10,0,y)
	btn.BackgroundColor3 = Color3.fromRGB(0,0,0)
	btn.TextColor3 = Color3.fromRGB(220,220,220)
	btn.Text = nome
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	btn.BorderSizePixel = 0

	Instance.new("UICorner",btn).CornerRadius = UDim.new(0,10)

	table.insert(menus,btn)

	local function activate()

		for _,b in pairs(menus) do
			b.BackgroundColor3 = Color3.fromRGB(0,0,0)
			b.TextColor3 = Color3.fromRGB(220,220,220)
		end

		btn.BackgroundColor3 = Color3.fromRGB(255,215,0)
		btn.TextColor3 = Color3.new(0,0,0)

	end

	btn.MouseButton1Click:Connect(function()

		activate()

		carregar(nome)

	end)

	return btn
end

-- CREATE MENUS
local autoBtn = criarMenu("Auto Join",10)
criarMenu("Steal",60)
criarMenu("Trade",110)
criarMenu("Protection",160)

-- DEFAULT
autoBtn.BackgroundColor3 = Color3.fromRGB(255,215,0)
autoBtn.TextColor3 = Color3.new(0,0,0)

carregar("Auto Join")
