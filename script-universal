loadstring([[
--[[
  MODMENU TUFFO - VERSÃO MOBILE + AIMBOT VARIANTS
  Auto Shot | Crosshair Aimbot | Mira Diferenciada
  Adaptado para todos os modelos de celular
]]

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local StarterGui = game:GetService("StarterGui")
local GuiService = game:GetService("GuiService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()

-- ===================== DETECÇÃO MOBILE =====================
local isMobile = UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled
local screenSize = camera.ViewportSize
local scale = math.clamp(math.min(screenSize.X, screenSize.Y) / 800, 0.75, 1.35)

local function S(n)
	return math.floor(n * scale)
end

local HEADER_HEIGHT = isMobile and S(52) or S(42)
local BTN_H = isMobile and S(40) or S(36)
local BTN_GAP = isMobile and S(44) or S(42)
local FRAME_W = isMobile and math.min(S(380), screenSize.X - 20) or S(345)
local FRAME_H = isMobile and math.min(S(720), screenSize.Y - 40) or S(660)

-- ===================== VARIÁVEIS =====================
local isAiming = false
local autoEnabled = false
local infiniteJumpEnabled = false
local noclipEnabled = false
local menuVisible = true
local currentCategory = "Escalada"

local ladders = {}
local lastAutoLadder = nil
local lastAutoPos = Vector3.zero
local lastAutoTime = 0
local checkpointPos = nil
local checkpointMarker = nil
local jumpConnection = nil
local noclipConnection = nil
local autoRespawnToCheckpoint = true

local aimbotEnabled = false
local autoShotEnabled = false
local crosshairAimbotEnabled = false
local customCrosshairEnabled = false
local espEnabled = false
local boxEspEnabled = false
local tracersEnabled = false
local distanceEnabled = false
local aimFOV = 90
local aimSmoothness = 0.22
local aimPartName = "Head"
local espObjects = {}
local teamCheckEnabled = false
local lastShotTime = 0
local SHOT_COOLDOWN = 0.12

local tpToolInstance = nil
local fullbrightEnabled = false
local fullbrightConnection = nil
local originalLighting = {}
local antiSitEnabled = false
local antiSitConnection = nil
local xrayEnabled = false
local xrayConnection = nil
local xrayParts = {}

local portalAimEnabled = false
local greenPortal = nil
local yellowPortal = nil
local portalDebounce = false

local spectateEnabled = false
local spectateTarget = nil
local customSpeed = nil
local moveConnection = nil

local MAX_HEIGHT = 200
local TRUSS_WIDTH = 3.4
local TRUSS_DEPTH = 0.9
local AUTO_DISTANCE = 8
local AUTO_COOLDOWN = 1.4
local AUTO_MIN_MOVE = 3

local COL_BG      = Color3.fromRGB(8, 10, 12)
local COL_PANEL   = Color3.fromRGB(12, 16, 18)
local COL_GREEN   = Color3.fromRGB(0, 255, 100)
local COL_DIM     = Color3.fromRGB(0, 160, 70)
local COL_BTN     = Color3.fromRGB(14, 22, 18)
local COL_BORDER  = Color3.fromRGB(0, 100, 45)
local COL_PORTAL  = Color3.fromRGB(255, 160, 40)
local COL_AIM     = Color3.fromRGB(255, 50, 80) -- Mira diferenciada (vermelho)

local DrawingAvailable = false
pcall(function()
	if Drawing and Drawing.new then
		local test = Drawing.new("Line")
		if test then test:Remove() DrawingAvailable = true end
	end
end)

-- ===================== LOADING =====================
local function showLoadingScreen(callback)
	local gui = Instance.new("ScreenGui")
	gui.Name = "TuffoLoading"
	gui.IgnoreGuiInset = true
	gui.DisplayOrder = 999999
	gui.ResetOnSpawn = false
	gui.Parent = player:WaitForChild("PlayerGui")

	local bg = Instance.new("Frame")
	bg.Size = UDim2.new(1, 0, 1, 0)
	bg.BackgroundColor3 = Color3.fromRGB(4, 6, 6)
	bg.BorderSizePixel = 0
	bg.Parent = gui

	local title = Instance.new("TextLabel")
	title.Size = UDim2.new(1, 0, 0, S(28))
	title.Position = UDim2.new(0, 0, 0.28, 0)
	title.BackgroundTransparency = 1
	title.Text = "MODMENU_TUFFO"
	title.TextColor3 = COL_GREEN
	title.Font = Enum.Font.GothamBold
	title.TextSize = S(22)
	title.Parent = bg

	local sub = Instance.new("TextLabel")
	sub.Size = UDim2.new(1, 0, 0, S(18))
	sub.Position = UDim2.new(0, 0, 0.34, 0)
	sub.BackgroundTransparency = 1
	sub.Text = "loading mobile..."
	sub.TextColor3 = COL_DIM
	sub.Font = Enum.Font.Code
	sub.TextSize = S(13)
	sub.Parent = bg

	local barBg = Instance.new("Frame")
	barBg.Size = UDim2.new(0.45, 0, 0, S(5))
	barBg.Position = UDim2.new(0.275, 0, 0.42, 0)
	barBg.BackgroundColor3 = Color3.fromRGB(20, 30, 22)
	barBg.BorderSizePixel = 0
	barBg.Parent = bg
	Instance.new("UICorner", barBg).CornerRadius = UDim.new(0, 3)

	local bar = Instance.new("Frame")
	bar.Size = UDim2.new(0, 0, 1, 0)
	bar.BackgroundColor3 = COL_GREEN
	bar.BorderSizePixel = 0
	bar.Parent = barBg
	Instance.new("UICorner", bar).CornerRadius = UDim.new(0, 3)

	task.spawn(function()
		for i = 1, 5 do
			TweenService:Create(bar, TweenInfo.new(0.25), {Size = UDim2.new(i/5, 0, 1, 0)}):Play()
			task.wait(0.25)
		end
		task.wait(0.15)
		gui:Destroy()
		if callback then callback() end
	end)
end

-- ===================== GUI =====================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ModMenuTuffo"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.IgnoreGuiInset = true
screenGui.Enabled = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, FRAME_W, 0, FRAME_H)
mainFrame.Position = UDim2.new(0, 10, 0, 20)
mainFrame.BackgroundColor3 = COL_BG
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Parent = screenGui
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 10)

local stroke = Instance.new("UIStroke")
stroke.Thickness = 1.5
stroke.Color = COL_GREEN
stroke.Parent = mainFrame

-- HEADER
local dragBar = Instance.new("Frame")
dragBar.Size = UDim2.new(1, 0, 0, HEADER_HEIGHT)
dragBar.BackgroundColor3 = COL_PANEL
dragBar.BorderSizePixel = 0
dragBar.Active = true
dragBar.Parent = mainFrame
Instance.new("UICorner", dragBar).CornerRadius = UDim.new(0, 10)

local dragFix = Instance.new("Frame")
dragFix.Size = UDim2.new(1, 0, 0, 12)
dragFix.Position = UDim2.new(0, 0, 1, -12)
dragFix.BackgroundColor3 = COL_PANEL
dragFix.BorderSizePixel = 0
dragFix.Parent = dragBar

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -50, 1, 0)
title.Position = UDim2.new(0, 12, 0, 0)
title.BackgroundTransparency = 1
title.Text = "TUFFO  //  modmenu"
title.TextColor3 = COL_GREEN
title.Font = Enum.Font.GothamBold
title.TextSize = isMobile and S(13) or S(15)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = dragBar

local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, S(36), 0, S(32))
minimizeBtn.Position = UDim2.new(1, -S(42), 0.5, -S(16))
minimizeBtn.BackgroundColor3 = COL_BTN
minimizeBtn.Text = "−"
minimizeBtn.TextColor3 = COL_GREEN
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.TextSize = S(18)
minimizeBtn.Parent = dragBar
Instance.new("UICorner", minimizeBtn).CornerRadius = UDim.new(0, 6)

-- ARRASTE (mobile friendly)
local dragging, dragStart, startPos = false, nil, nil

local function updateDrag(input)
	local delta = input.Position - dragStart
	mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

dragBar.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = mainFrame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then dragging = false end
		end)
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		updateDrag(input)
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

-- ABAS
local tabFrame = Instance.new("ScrollingFrame")
tabFrame.Size = UDim2.new(1, -10, 0, S(36))
tabFrame.Position = UDim2.new(0, 5, 0, HEADER_HEIGHT + 6)
tabFrame.BackgroundTransparency = 1
tabFrame.BorderSizePixel = 0
tabFrame.ScrollBarThickness = 0
tabFrame.ScrollingDirection = Enum.ScrollingDirection.X
tabFrame.CanvasSize = UDim2.new(0, 640, 0, 0)
tabFrame.Parent = mainFrame

local function createTab(text, order)
	local tab = Instance.new("TextButton")
	tab.Size = UDim2.new(0, S(74), 1, 0)
	tab.Position = UDim2.new(0, order * S(76), 0, 0)
	tab.BackgroundColor3 = COL_BTN
	tab.Text = text
	tab.TextColor3 = COL_GREEN
	tab.Font = Enum.Font.GothamBold
	tab.TextSize = S(11)
	tab.Parent = tabFrame
	Instance.new("UICorner", tab).CornerRadius = UDim.new(0, 6)
	local s = Instance.new("UIStroke")
	s.Color = COL_BORDER
	s.Thickness = 1
	s.Parent = tab
	return tab
end

local tabEscalada   = createTab("🧗 Escala", 0)
local tabCheckpoint = createTab("📍 Check", 1)
local tabCombat     = createTab("🎯 Combat", 2)
local tabMovimento  = createTab("🏃 Move", 3)
local tabProtecao   = createTab("🛡️ Prot", 4)
local tabUGC        = createTab("🎭 UGC", 5)
local tabUGCR6      = createTab("🎭 UGC R6", 6)
local tabOlho       = createTab("👁 Vision", 7)

-- CONTEÚDO
local function createContent()
	local f = Instance.new("ScrollingFrame")
	f.Size = UDim2.new(1, -S(46), 1, -(HEADER_HEIGHT + S(60)))
	f.Position = UDim2.new(0, 5, 0, HEADER_HEIGHT + S(48))
	f.BackgroundTransparency = 1
	f.BorderSizePixel = 0
	f.ScrollBarThickness = 0
	f.CanvasSize = UDim2.new(0, 0, 0, 1400)
	f.ScrollingDirection = Enum.ScrollingDirection.Y
	f.Visible = false
	f.Parent = mainFrame
	return f
end

local contentEscalada   = createContent()
local contentCheckpoint = createContent()
local contentCombat     = createContent()
local contentMovimento  = createContent()
local contentProtecao   = createContent()
local contentUGC        = createContent()
local contentUGCR6      = createContent()
local contentOlho       = createContent()
contentEscalada.Visible = true

local function updateCanvasSize(frame)
	local maxY = 0
	for _, child in ipairs(frame:GetChildren()) do
		if child:IsA("GuiObject") then
			local bottom = child.Position.Y.Offset + child.Size.Y.Offset
			if bottom > maxY then maxY = bottom end
		end
	end
	frame.CanvasSize = UDim2.new(0, 0, 0, math.max(maxY + 280, 1100))
end

-- SETAS
local scrollArrowFrame = Instance.new("Frame")
scrollArrowFrame.Size = UDim2.new(0, S(36), 0, S(84))
scrollArrowFrame.Position = UDim2.new(1, -S(40), 0, HEADER_HEIGHT + S(48))
scrollArrowFrame.BackgroundTransparency = 1
scrollArrowFrame.Parent = mainFrame

local upArrow = Instance.new("TextButton")
upArrow.Size = UDim2.new(1, 0, 0, S(38))
upArrow.BackgroundColor3 = COL_BTN
upArrow.Text = "▲"
upArrow.TextColor3 = COL_GREEN
upArrow.Font = Enum.Font.GothamBold
upArrow.TextSize = S(16)
upArrow.Parent = scrollArrowFrame
Instance.new("UICorner", upArrow).CornerRadius = UDim.new(0, 6)

local downArrow = Instance.new("TextButton")
downArrow.Size = UDim2.new(1, 0, 0, S(38))
downArrow.Position = UDim2.new(0, 0, 0, S(42))
downArrow.BackgroundColor3 = COL_BTN
downArrow.Text = "▼"
downArrow.TextColor3 = COL_GREEN
downArrow.Font = Enum.Font.GothamBold
downArrow.TextSize = S(16)
downArrow.Parent = scrollArrowFrame
Instance.new("UICorner", downArrow).CornerRadius = UDim.new(0, 6)

local function getActiveContent()
	if contentEscalada.Visible then return contentEscalada end
	if contentCheckpoint.Visible then return contentCheckpoint end
	if contentCombat.Visible then return contentCombat end
	if contentMovimento.Visible then return contentMovimento end
	if contentProtecao.Visible then return contentProtecao end
	if contentUGC.Visible then return contentUGC end
	if contentUGCR6.Visible then return contentUGCR6 end
	if contentOlho.Visible then return contentOlho end
	return contentEscalada
end

local SCROLL_STEP = S(85)
local holdingUp, holdingDown = false, false

local function scrollUp()
	local frame = getActiveContent()
	updateCanvasSize(frame)
	frame.CanvasPosition = Vector2.new(0, math.max(0, frame.CanvasPosition.Y - SCROLL_STEP))
end

local function scrollDown()
	local frame = getActiveContent()
	updateCanvasSize(frame)
	local maxY = math.max(0, frame.CanvasSize.Y.Offset - frame.AbsoluteWindowSize.Y)
	frame.CanvasPosition = Vector2.new(0, math.min(maxY + 50, frame.CanvasPosition.Y + SCROLL_STEP))
end

upArrow.MouseButton1Down:Connect(function() holdingUp = true scrollUp() end)
upArrow.MouseButton1Up:Connect(function() holdingUp = false end)
upArrow.MouseLeave:Connect(function() holdingUp = false end)
downArrow.MouseButton1Down:Connect(function() holdingDown = true scrollDown() end)
downArrow.MouseButton1Up:Connect(function() holdingDown = false end)
downArrow.MouseLeave:Connect(function() holdingDown = false end)

task.spawn(function()
	while true do
		task.wait(0.09)
		if holdingUp then scrollUp() end
		if holdingDown then scrollDown() end
	end
end)

-- STATUS + HELPERS
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -12, 0, S(14))
statusLabel.Position = UDim2.new(0, 8, 1, -S(18))
statusLabel.BackgroundTransparency = 1
statusLabel.Text = "status: ready"
statusLabel.TextColor3 = COL_DIM
statusLabel.Font = Enum.Font.Code
statusLabel.TextSize = S(11)
statusLabel.TextXAlignment = Enum.TextXAlignment.Left
statusLabel.Parent = mainFrame

local function createButton(parent, text, y)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, -8, 0, BTN_H)
	btn.Position = UDim2.new(0, 0, 0, y)
	btn.BackgroundColor3 = COL_BTN
	btn.Text = text
	btn.TextColor3 = COL_GREEN
	btn.Font = Enum.Font.Gotham
	btn.TextSize = S(13)
	btn.Parent = parent
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 7)
	local s = Instance.new("UIStroke")
	s.Color = COL_BORDER
	s.Thickness = 1
	s.Parent = btn
	return btn
end

local function createTextBox(parent, placeholder, y)
	local box = Instance.new("TextBox")
	box.Size = UDim2.new(1, -8, 0, S(34))
	box.Position = UDim2.new(0, 0, 0, y)
	box.BackgroundColor3 = Color3.fromRGB(10, 16, 12)
	box.Text = ""
	box.PlaceholderText = placeholder
	box.TextColor3 = COL_GREEN
	box.PlaceholderColor3 = COL_DIM
	box.Font = Enum.Font.Gotham
	box.TextSize = S(13)
	box.ClearTextOnFocus = false
	box.Parent = parent
	Instance.new("UICorner", box).CornerRadius = UDim.new(0, 7)
	local s = Instance.new("UIStroke")
	s.Color = COL_BORDER
	s.Thickness = 1
	s.Parent = box
	return box
end

-- BOTÕES
local y = 0
local function nextY()
	local cur = y
	y = y + BTN_GAP
	return cur
end

-- Escalada
y = 0
local aimBtn = createButton(contentEscalada, "Ativar Mira", nextY())
local createBtn = createButton(contentEscalada, "Criar Truss", nextY())
local autoBtn = createButton(contentEscalada, "Automática: OFF", nextY())
local removeBtn = createButton(contentEscalada, "Remover Truss", nextY())

-- Checkpoint
y = 0
local saveBtn = createButton(contentCheckpoint, "[1] Salvar Checkpoint", nextY())
local tpBtn = createButton(contentCheckpoint, "[2] Teleportar", nextY())
local clearBtn = createButton(contentCheckpoint, "[3] Limpar Checkpoint", nextY())
local autoTPBtn = createButton(contentCheckpoint, "Auto-TP Morte: ON", nextY())
local tptoolBtn = createButton(contentCheckpoint, "TP Tool: OFF", nextY())
local invisibleBtn = createButton(contentCheckpoint, "Invisible V2", nextY())
local portalBtn = createButton(contentCheckpoint, "Portais: OFF", nextY())
local gravityGunBtn = createButton(contentCheckpoint, "Gravity Gun", nextY())

-- Combat (com novas variantes de Aimbot)
y = 0
local aimbotBtn = createButton(contentCombat, "Aimbot: OFF", nextY())
local autoShotBtn = createButton(contentCombat, "Auto Shot: OFF", nextY())
local crosshairAimBtn = createButton(contentCombat, "Crosshair Aimbot: OFF", nextY())
local customMiraBtn = createButton(contentCombat, "Mira Vermelha: OFF", nextY())
local partBtn = createButton(contentCombat, "Alvo: Cabeça", nextY())
local flingBtn = createButton(contentCombat, "Fling", nextY())
local teamCheckBtn = createButton(contentCombat, "Team Check: OFF", nextY())
local espBtn = createButton(contentCombat, "ESP: OFF", nextY())
local boxEspBtn = createButton(contentCombat, "Box ESP: OFF", nextY())
local tracersBtn = createButton(contentCombat, "Tracers: OFF", nextY())
local distanceBtn = createButton(contentCombat, "Distance: OFF", nextY())

-- Movimento
y = 0
local speedBox = createTextBox(contentMovimento, "Velocidade", nextY())
local applyMoveBtn = createButton(contentMovimento, "Aplicar Velocidade", nextY())
local jumpBtn = createButton(contentMovimento, "Pulo Infinito: OFF", nextY())
local gravityBtn = createButton(contentMovimento, "Zero Gravidade", nextY())
local shiftLockBtn = createButton(contentMovimento, "Shift Lock", nextY())
local noclipBtn = createButton(contentMovimento, "Noclip: OFF", nextY())
local flyBtn = createButton(contentMovimento, "Fly", nextY())

-- Proteção
y = 0
local antiFlingBtn = createButton(contentProtecao, "Anti Fling", nextY())
local antiBangBtn = createButton(contentProtecao, "AntiBang", nextY())
local boostFpsBtn = createButton(contentProtecao, "Boost FPS", nextY())
local antiSitBtn = createButton(contentProtecao, "Anti Sit: OFF", nextY())

-- UGC
y = 0
local ugcAnimBtn = createButton(contentUGC, "UGC Animations", nextY())
local crouchBtn = createButton(contentUGC, "Crouch", nextY())
local jetbotBtn = createButton(contentUGC, "Jetbot", nextY())
local adaptationBtn = createButton(contentUGC, "Sonic Exe", nextY())
local goatBtn = createButton(contentUGC, "Goat Simulator", nextY())
local amongUsBtn = createButton(contentUGC, "Among Us", nextY())
local militaryBtn = createButton(contentUGC, "Military Bot", nextY())

-- UGC R6
y = 0
local reanimateBtn = createButton(contentUGCR6, "Reanimate", nextY())
local scp096Btn = createButton(contentUGCR6, "SCP-096 / Fling", nextY())
local r6AnimsBtn = createButton(contentUGCR6, "R6 Animations Hub", nextY())
local scp049Btn = createButton(contentUGCR6, "SCP-049", nextY())

-- Vision
y = 0
local xrayBtn = createButton(contentOlho, "X-Ray: OFF", nextY())
local fullbrightBtn = createButton(contentOlho, "Fullbright: OFF", nextY())
local spectateBox = createTextBox(contentOlho, "Nome do jogador (visão)", nextY())
local spectateBtn = createButton(contentOlho, "Ver visão: OFF", nextY())
local tpNameBox = createTextBox(contentOlho, "Nome do jogador (TP)", nextY())
local tpNameBtn = createButton(contentOlho, "TP por nome", nextY())

task.defer(function()
	for _, f in ipairs({contentEscalada, contentCheckpoint, contentCombat, contentMovimento, contentProtecao, contentUGC, contentUGCR6, contentOlho}) do
		updateCanvasSize(f)
	end
end)

-- ===================== MIRAS =====================
-- Mira verde (Escalada)
local crosshair = Instance.new("Frame")
crosshair.Size = UDim2.new(0, S(20), 0, S(20))
crosshair.Position = UDim2.new(0.5, -S(10), 0.5, -S(10))
crosshair.BackgroundTransparency = 1
crosshair.Visible = false
crosshair.Parent = screenGui

local function makeLine(parent, size, pos, color)
	local l = Instance.new("Frame")
	l.Size = size
	l.Position = pos
	l.BackgroundColor3 = color or COL_GREEN
	l.BorderSizePixel = 0
	l.Parent = parent
end
makeLine(crosshair, UDim2.new(0, 2, 0, S(8)), UDim2.new(0.5, -1, 0, 0))
makeLine(crosshair, UDim2.new(0, 2, 0, S(8)), UDim2.new(0.5, -1, 1, -S(8)))
makeLine(crosshair, UDim2.new(0, S(8), 0, 2), UDim2.new(0, 0, 0.5, -1))
makeLine(crosshair, UDim2.new(0, S(8), 0, 2), UDim2.new(1, -S(8), 0.5, -1))

-- Mira Portal (laranja)
local portalCrosshair = Instance.new("Frame")
portalCrosshair.Size = UDim2.new(0, S(28), 0, S(28))
portalCrosshair.Position = UDim2.new(0.5, -S(14), 0.5, -S(14))
portalCrosshair.BackgroundTransparency = 1
portalCrosshair.Visible = false
portalCrosshair.Parent = screenGui

local portalDiamond = Instance.new("TextLabel")
portalDiamond.Size = UDim2.new(1, 0, 1, 0)
portalDiamond.BackgroundTransparency = 1
portalDiamond.Text = "◇"
portalDiamond.TextColor3 = COL_PORTAL
portalDiamond.Font = Enum.Font.GothamBold
portalDiamond.TextSize = S(26)
portalDiamond.Parent = portalCrosshair

-- MIRA DIFERENCIADA (vermelha) - Aimbot
local aimCrosshair = Instance.new("Frame")
aimCrosshair.Size = UDim2.new(0, S(32), 0, S(32))
aimCrosshair.Position = UDim2.new(0.5, -S(16), 0.5, -S(16))
aimCrosshair.BackgroundTransparency = 1
aimCrosshair.Visible = false
aimCrosshair.Parent = screenGui

-- Círculo externo
local aimRing = Instance.new("Frame")
aimRing.Size = UDim2.new(1, 0, 1, 0)
aimRing.BackgroundTransparency = 1
aimRing.Parent = aimCrosshair
local ringStroke = Instance.new("UIStroke")
ringStroke.Color = COL_AIM
ringStroke.Thickness = 2
ringStroke.Parent = aimRing
Instance.new("UICorner", aimRing).CornerRadius = UDim.new(1, 0)

-- Cruz vermelha
makeLine(aimCrosshair, UDim2.new(0, 2, 0, S(10)), UDim2.new(0.5, -1, 0, S(2)), COL_AIM)
makeLine(aimCrosshair, UDim2.new(0, 2, 0, S(10)), UDim2.new(0.5, -1, 1, -S(12)), COL_AIM)
makeLine(aimCrosshair, UDim2.new(0, S(10), 0, 2), UDim2.new(0, S(2), 0.5, -1), COL_AIM)
makeLine(aimCrosshair, UDim2.new(0, S(10), 0, 2), UDim2.new(1, -S(12), 0.5, -1), COL_AIM)

-- Ponto central
local aimDot = Instance.new("Frame")
aimDot.Size = UDim2.new(0, S(4), 0, S(4))
aimDot.Position = UDim2.new(0.5, -S(2), 0.5, -S(2))
aimDot.BackgroundColor3 = COL_AIM
aimDot.BorderSizePixel = 0
aimDot.Parent = aimCrosshair
Instance.new("UICorner", aimDot).CornerRadius = UDim.new(1, 0)

local function updateCrosshairs()
	crosshair.Visible = isAiming and not portalAimEnabled and not customCrosshairEnabled
	portalCrosshair.Visible = portalAimEnabled
	aimCrosshair.Visible = customCrosshairEnabled or (aimbotEnabled and not portalAimEnabled)
end

-- BOTÕES PORTAL
local portalButtonsGui = Instance.new("ScreenGui")
portalButtonsGui.Name = "PortalButtons"
portalButtonsGui.ResetOnSpawn = false
portalButtonsGui.IgnoreGuiInset = true
portalButtonsGui.Enabled = false
portalButtonsGui.Parent = player:WaitForChild("PlayerGui")

local function makePortalButton(text, y, bgColor, strokeColor)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(0, S(74), 0, S(58))
	btn.Position = UDim2.new(1, -S(90), 1, y)
	btn.BackgroundColor3 = bgColor
	btn.BackgroundTransparency = 0.1
	btn.Text = text
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = S(11)
	btn.TextWrapped = true
	btn.Parent = portalButtonsGui
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)
	local s = Instance.new("UIStroke")
	s.Color = strokeColor
	s.Thickness = 2.2
	s.Parent = btn
	return btn
end

local removePortalsBtn = makePortalButton("X\nCLEAR", -S(285), Color3.fromRGB(140, 20, 20), Color3.fromRGB(255, 60, 60))
local yellowPortalBtn  = makePortalButton("YEL\nEXIT", -S(210), Color3.fromRGB(170, 130, 0), Color3.fromRGB(255, 220, 0))
local greenPortalBtn   = makePortalButton("GRN\nENTER", -S(135), Color3.fromRGB(0, 110, 25), Color3.fromRGB(0, 255, 90))

-- NOTIFY
local function notify(msg)
	local n = Instance.new("ScreenGui")
	n.ResetOnSpawn = false
	n.Parent = player.PlayerGui
	local f = Instance.new("Frame")
	f.Size = UDim2.new(0, S(300), 0, S(36))
	f.Position = UDim2.new(0.5, -S(150), 0, -50)
	f.BackgroundColor3 = COL_BG
	f.Parent = n
	Instance.new("UICorner", f).CornerRadius = UDim.new(0, 8)
	local s = Instance.new("UIStroke")
	s.Color = COL_GREEN
	s.Thickness = 1.2
	s.Parent = f
	local l = Instance.new("TextLabel")
	l.Size = UDim2.new(1, -10, 1, 0)
	l.Position = UDim2.new(0, 5, 0, 0)
	l.BackgroundTransparency = 1
	l.Text = msg
	l.TextColor3 = COL_GREEN
	l.Font = Enum.Font.Gotham
	l.TextSize = S(13)
	l.Parent = f
	TweenService:Create(f, TweenInfo.new(0.25), {Position = UDim2.new(0.5, -S(150), 0, 14)}):Play()
	task.delay(2.2, function()
		TweenService:Create(f, TweenInfo.new(0.2), {Position = UDim2.new(0.5, -S(150), 0, -50)}):Play()
		task.wait(0.25)
		n:Destroy()
	end)
end

-- FILTRO ESCALADA
local function getClimbFilter(character)
	local filter = {character}
	for _, ladder in ipairs(ladders) do table.insert(filter, ladder) end
	for _, plr in pairs(Players:GetPlayers()) do
		if plr.Character then table.insert(filter, plr.Character) end
	end
	return filter
end

-- ESCALADA
local function getWallHeight(hitPos, normal, rayParams)
	local height = 5
	for yy = 1, MAX_HEIGHT, 0.9 do
		local origin = hitPos + normal * 2.2 + Vector3.new(0, yy, 0)
		local result = workspace:Raycast(origin, -normal * 6, rayParams)
		if result and (result.Position - origin).Magnitude < 4.8 then
			height = yy
		else break end
	end
	return math.clamp(height + 6, 8, MAX_HEIGHT)
end

local function buildTruss(hitPos, normal, character)
	local hrp = character:FindFirstChild("HumanoidRootPart")
	if not hrp then return nil, 0 end

	local rayParams = RaycastParams.new()
	rayParams.FilterDescendantsInstances = getClimbFilter(character)
	rayParams.FilterType = Enum.RaycastFilterType.Exclude

	local height = getWallHeight(hitPos, normal, rayParams)
	local model = Instance.new("Model")
	model.Name = "LocalClimbTruss"

	local truss = Instance.new("TrussPart")
	truss.Size = Vector3.new(TRUSS_WIDTH, height, TRUSS_DEPTH)
	truss.Anchored = true
	truss.CanCollide = true
	truss.Transparency = 0.65
	truss.Material = Enum.Material.Wood
	truss.Color = Color3.fromRGB(110, 75, 45)

	local offset = normal * (TRUSS_DEPTH / 2 - 0.22)
	local centerY = hrp.Position.Y - 2.8 + height / 2
	local centerPos = Vector3.new(hitPos.X, centerY, hitPos.Z) + offset
	truss.CFrame = CFrame.lookAt(centerPos, centerPos - normal)
	truss.Parent = model

	local topPlatform = Instance.new("Part")
	topPlatform.Size = Vector3.new(TRUSS_WIDTH + 0.6, 0.35, 2.8)
	topPlatform.Anchored = true
	topPlatform.CanCollide = true
	topPlatform.Transparency = 0.6
	topPlatform.Material = Enum.Material.Wood
	topPlatform.Color = Color3.fromRGB(110, 75, 45)
	local topY = centerY + height / 2 - 0.4
	local topPos = Vector3.new(hitPos.X, topY, hitPos.Z) + normal * 0.15 - normal * 1.15
	topPlatform.CFrame = CFrame.lookAt(topPos, topPos - normal) * CFrame.new(0, 0, -0.9)
	topPlatform.Parent = model

	model.Parent = workspace
	table.insert(ladders, model)
	return model, height
end

local function createLadderManual()
	local character = player.Character
	if not character then return end
	local unitRay = camera:ViewportPointToRay(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
	local rayParams = RaycastParams.new()
	rayParams.FilterDescendantsInstances = getClimbFilter(character)
	rayParams.FilterType = Enum.RaycastFilterType.Exclude
	local result = workspace:Raycast(unitRay.Origin, unitRay.Direction * 500, rayParams)
	if not result or math.abs(result.Normal.Y) > 0.55 then
		notify("Mire numa parede")
		return
	end
	local model, height = buildTruss(result.Position, result.Normal, character)
	if model then notify("Truss criada (" .. string.format("%.1f", height) .. ")") end
end

local function tryAutoLadder()
	if not autoEnabled then return end
	local now = tick()
	if now - lastAutoTime < AUTO_COOLDOWN then return end
	local character = player.Character
	if not character then return end
	local hrp = character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local rayParams = RaycastParams.new()
	rayParams.FilterDescendantsInstances = getClimbFilter(character)
	rayParams.FilterType = Enum.RaycastFilterType.Exclude
	local result = workspace:Raycast(hrp.Position + Vector3.new(0, 1.1, 0), hrp.CFrame.LookVector * AUTO_DISTANCE, rayParams)
	if result and math.abs(result.Normal.Y) < 0.4 then
		if (result.Position - lastAutoPos).Magnitude > AUTO_MIN_MOVE or not lastAutoLadder then
			if lastAutoLadder and lastAutoLadder.Parent then
				lastAutoLadder:Destroy()
				for i, v in ipairs(ladders) do
					if v == lastAutoLadder then table.remove(ladders, i) break end
				end
			end
			local model = buildTruss(result.Position, result.Normal, character)
			if model then
				lastAutoLadder = model
				lastAutoPos = result.Position
				lastAutoTime = now
			end
		end
	end
end

-- PORTAIS
local function fixCharacterAfterTeleport(char)
	local hum = char:FindFirstChildOfClass("Humanoid")
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hum or not hrp then return end
	hum.PlatformStand = false
	hum.Sit = false
	hum.AutoRotate = true
	pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
	task.wait(0.05)
	pcall(function() hum:ChangeState(Enum.HumanoidStateType.Running) end)
	hrp.AssemblyLinearVelocity = Vector3.zero
	hrp.AssemblyAngularVelocity = Vector3.zero
end

local function createPortal(color, position, normal)
	local portal = Instance.new("Part")
	portal.Size = Vector3.new(6, 8, 0.4)
	portal.Anchored = true
	portal.CanCollide = false
	portal.Material = Enum.Material.Neon
	portal.Color = color
	portal.Transparency = 0.2
	portal.CFrame = CFrame.lookAt(position + normal * 0.3, position + normal * 2)
	portal.Parent = workspace
	local light = Instance.new("PointLight")
	light.Color = color
	light.Brightness = 2.5
	light.Range = 14
	light.Parent = portal
	return portal
end

local function getAimHit()
	local unitRay = camera:ViewportPointToRay(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
	local params = RaycastParams.new()
	params.FilterDescendantsInstances = {player.Character}
	params.FilterType = Enum.RaycastFilterType.Exclude
	return workspace:Raycast(unitRay.Origin, unitRay.Direction * 500, params)
end

local function spawnGreenPortal()
	local result = getAimHit()
	if not result then notify("Mire em uma superfície") return end
	if greenPortal and greenPortal.Parent then greenPortal:Destroy() end
	greenPortal = createPortal(Color3.fromRGB(0, 255, 70), result.Position, result.Normal)
	greenPortal.Name = "GreenPortal"
	notify("Portal verde criado")
	greenPortal.Touched:Connect(function(hit)
		if portalDebounce then return end
		local char = hit.Parent
		local hum = char and char:FindFirstChildOfClass("Humanoid")
		if hum and Players:GetPlayerFromCharacter(char) == player and yellowPortal and yellowPortal.Parent then
			portalDebounce = true
			local hrp = char:FindFirstChild("HumanoidRootPart")
			if hrp then
				hrp.CFrame = yellowPortal.CFrame * CFrame.new(0, 0, -5)
				task.wait()
				fixCharacterAfterTeleport(char)
			end
			task.wait(0.85)
			portalDebounce = false
		end
	end)
end

local function spawnYellowPortal()
	local result = getAimHit()
	if not result then notify("Mire em uma superfície") return end
	if yellowPortal and yellowPortal.Parent then yellowPortal:Destroy() end
	yellowPortal = createPortal(Color3.fromRGB(255, 210, 0), result.Position, result.Normal)
	yellowPortal.Name = "YellowPortal"
	notify("Portal amarelo criado")
	yellowPortal.Touched:Connect(function(hit)
		if portalDebounce then return end
		local char = hit.Parent
		local hum = char and char:FindFirstChildOfClass("Humanoid")
		if hum and Players:GetPlayerFromCharacter(char) == player and greenPortal and greenPortal.Parent then
			portalDebounce = true
			local hrp = char:FindFirstChild("HumanoidRootPart")
			if hrp then
				hrp.CFrame = greenPortal.CFrame * CFrame.new(0, 0, -5)
				task.wait()
				fixCharacterAfterTeleport(char)
			end
			task.wait(0.85)
			portalDebounce = false
		end
	end)
end

local function removeAllPortals()
	if greenPortal and greenPortal.Parent then greenPortal:Destroy() greenPortal = nil end
	if yellowPortal and yellowPortal.Parent then yellowPortal:Destroy() yellowPortal = nil end
	notify("Portais removidos")
end

local function togglePortalAim()
	portalAimEnabled = not portalAimEnabled
	if portalAimEnabled then
		portalBtn.Text = "Portais: ON"
		portalButtonsGui.Enabled = true
		notify("Mira de portais ON")
	else
		portalBtn.Text = "Portais: OFF"
		portalButtonsGui.Enabled = false
		notify("Mira de portais OFF")
	end
	updateCrosshairs()
end

greenPortalBtn.MouseButton1Click:Connect(spawnGreenPortal)
yellowPortalBtn.MouseButton1Click:Connect(spawnYellowPortal)
removePortalsBtn.MouseButton1Click:Connect(removeAllPortals)

-- ANTI SIT / NOCLIP / MOVE
local function toggleAntiSit()
	antiSitEnabled = not antiSitEnabled
	if antiSitConnection then antiSitConnection:Disconnect() antiSitConnection = nil end
	if antiSitEnabled then
		antiSitBtn.Text = "Anti Sit: ON"
		notify("Anti Sit ON")
		antiSitConnection = RunService.Heartbeat:Connect(function()
			local char = player.Character
			if not char then return end
			local hum = char:FindFirstChildOfClass("Humanoid")
			if not hum then return end
			pcall(function() hum:SetStateEnabled(Enum.HumanoidStateType.Seated, false) end)
			if hum.Sit or hum.SeatPart then
				hum.Sit = false
				if hum.SeatPart then
					local weld = hum.SeatPart:FindFirstChild("SeatWeld")
					if weld then weld:Destroy() end
				end
				pcall(function() hum:ChangeState(Enum.HumanoidStateType.Jumping) end)
			end
		end)
	else
		antiSitBtn.Text = "Anti Sit: OFF"
		notify("Anti Sit OFF")
		local char = player.Character
		if char then
			local hum = char:FindFirstChildOfClass("Humanoid")
			if hum then pcall(function() hum:SetStateEnabled(Enum.HumanoidStateType.Seated, true) end) end
		end
	end
end

local function toggleNoclip()
	noclipEnabled = not noclipEnabled
	if noclipConnection then noclipConnection:Disconnect() noclipConnection = nil end
	if noclipEnabled then
		noclipBtn.Text = "Noclip: ON"
		notify("Noclip ON")
		noclipConnection = RunService.Stepped:Connect(function()
			local char = player.Character
			if char then
				for _, part in pairs(char:GetDescendants()) do
					if part:IsA("BasePart") then part.CanCollide = false end
				end
			end
		end)
	else
		noclipBtn.Text = "Noclip: OFF"
		notify("Noclip OFF")
		local char = player.Character
		if char then
			for _, part in pairs(char:GetDescendants()) do
				if part:IsA("BasePart") then part.CanCollide = true end
			end
		end
	end
end

local function applyMovement()
	local speed = tonumber(speedBox.Text)
	if speed and speed > 0 then customSpeed = speed end
	if moveConnection then moveConnection:Disconnect() moveConnection = nil end
	moveConnection = RunService.Heartbeat:Connect(function()
		local char = player.Character
		if not char then return end
		local hum = char:FindFirstChildOfClass("Humanoid")
		if not hum then return end
		if customSpeed then hum.WalkSpeed = customSpeed end
	end)
	notify("Velocidade aplicada")
end

-- AIMBOT + AUTO SHOT + CROSSHAIR AIM
local function getClosestTarget()
	local closest, smallest = nil, aimFOV
	local camLook = camera.CFrame.LookVector
	local camPos = camera.CFrame.Position
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= player and plr.Character then
			if teamCheckEnabled and plr.Team and player.Team and plr.Team == player.Team then
				-- skip
			else
				local part = plr.Character:FindFirstChild(aimPartName)
				local hum = plr.Character:FindFirstChildOfClass("Humanoid")
				if part and hum and hum.Health > 0 then
					local dir = (part.Position - camPos).Unit
					local angle = math.deg(math.acos(math.clamp(camLook:Dot(dir), -1, 1)))
					if angle < smallest then
						smallest = angle
						closest = plr
					end
				end
			end
		end
	end
	return closest
end

local function doAutoShot(target)
	if not target or not target.Character then return end
	local now = tick()
	if now - lastShotTime < SHOT_COOLDOWN then return end
	lastShotTime = now

	-- Tenta ativar a tool equipada
	local char = player.Character
	if not char then return end
	local tool = char:FindFirstChildOfClass("Tool")
	if tool then
		pcall(function()
			tool:Activate()
		end)
	end
	-- Também simula click (alguns jogos usam)
	pcall(function()
		mouse1click()
	end)
end

-- FUNÇÕES GERAIS (resumidas)
local function findPlayerByName(name)
	if not name or name == "" then return nil end
	name = string.lower(name:gsub("%s+", ""))
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= player then
			if string.lower(plr.Name) == name or string.lower(plr.DisplayName) == name then return plr end
			if string.find(string.lower(plr.Name), name, 1, true) or string.find(string.lower(plr.DisplayName), name, 1, true) then return plr end
		end
	end
	return nil
end

local function stopSpectate()
	spectateEnabled = false
	spectateTarget = nil
	spectateBtn.Text = "Ver visão: OFF"
	local char = player.Character
	if char then
		local hum = char:FindFirstChildOfClass("Humanoid")
		if hum then camera.CameraSubject = hum end
	end
	camera.CameraType = Enum.CameraType.Custom
end

local function toggleSpectate()
	if spectateEnabled then stopSpectate() notify("Visão própria restaurada") return end
	local target = findPlayerByName(spectateBox.Text)
	if not target or not target.Character then notify("Jogador não encontrado") return end
	local hum = target.Character:FindFirstChildOfClass("Humanoid")
	if not hum then notify("Humanoid não encontrado") return end
	spectateEnabled = true
	spectateTarget = target
	spectateBtn.Text = "Ver visão: ON"
	camera.CameraSubject = hum
	notify("Vendo visão de " .. target.Name)
end

local function tpToName()
	local target = findPlayerByName(tpNameBox.Text)
	if not target or not target.Character or not target.Character:FindFirstChild("HumanoidRootPart") then
		notify("Jogador não encontrado") return
	end
	local myChar = player.Character
	if not myChar then return end
	myChar:PivotTo(target.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3))
	notify("TP para " .. target.Name)
end

local function clearESP()
	for plr, data in pairs(espObjects) do
		if data.highlight then pcall(function() data.highlight:Destroy() end) end
		if data.billboard then pcall(function() data.billboard:Destroy() end) end
		if data.box then for _, line in pairs(data.box) do pcall(function() if line.Remove then line:Remove() end end) end end
		if data.tracer then pcall(function() if data.tracer.Remove then data.tracer:Remove() end end) end
	end
	espObjects = {}
end

local function updateESP()
	if not (espEnabled or boxEspEnabled or tracersEnabled or distanceEnabled) then clearESP() return end
	local myRoot = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= player and plr.Character then
			local skip = teamCheckEnabled and plr.Team and player.Team and plr.Team == player.Team
			if not skip then
				local char = plr.Character
				local hum = char:FindFirstChildOfClass("Humanoid")
				local head = char:FindFirstChild("Head")
				local root = char:FindFirstChild("HumanoidRootPart")
				if hum and head and root and hum.Health > 0 then
					if not espObjects[plr] then
						local data = {}
						local hl = Instance.new("Highlight")
						hl.FillTransparency = 0.7
						hl.OutlineTransparency = 0
						hl.Parent = char
						data.highlight = hl
						local bb = Instance.new("BillboardGui")
						bb.Size = UDim2.new(0, 120, 0, 50)
						bb.StudsOffset = Vector3.new(0, 3.2, 0)
						bb.AlwaysOnTop = true
						bb.Adornee = head
						bb.Parent = char
						local nameLbl = Instance.new("TextLabel")
						nameLbl.Size = UDim2.new(1, 0, 0.33, 0)
						nameLbl.BackgroundTransparency = 1
						nameLbl.Text = plr.Name
						nameLbl.TextColor3 = COL_GREEN
						nameLbl.Font = Enum.Font.GothamBold
						nameLbl.TextSize = 12
						nameLbl.Parent = bb
						local hpLbl = Instance.new("TextLabel")
						hpLbl.Size = UDim2.new(1, 0, 0.33, 0)
						hpLbl.Position = UDim2.new(0, 0, 0.33, 0)
						hpLbl.BackgroundTransparency = 1
						hpLbl.Font = Enum.Font.Gotham
						hpLbl.TextSize = 11
						hpLbl.Parent = bb
						local distLbl = Instance.new("TextLabel")
						distLbl.Size = UDim2.new(1, 0, 0.33, 0)
						distLbl.Position = UDim2.new(0, 0, 0.66, 0)
						distLbl.BackgroundTransparency = 1
						distLbl.TextColor3 = Color3.fromRGB(180, 255, 200)
						distLbl.Font = Enum.Font.Code
						distLbl.TextSize = 11
						distLbl.Visible = false
						distLbl.Parent = bb
						data.billboard = bb
						data.name = nameLbl
						data.hp = hpLbl
						data.dist = distLbl
						if DrawingAvailable then
							local boxLines = {}
							for i = 1, 12 do
								local line = Drawing.new("Line")
								line.Visible = false
								line.Thickness = 1.5
								line.Color = Color3.fromRGB(0, 255, 100)
								table.insert(boxLines, line)
							end
							data.box = boxLines
							local tracer = Drawing.new("Line")
							tracer.Visible = false
							tracer.Thickness = 1.2
							tracer.Color = Color3.fromRGB(0, 255, 100)
							data.tracer = tracer
						end
						espObjects[plr] = data
					end
					local data = espObjects[plr]
					local pct = hum.Health / math.max(hum.MaxHealth, 1)
					data.hp.Text = math.floor(hum.Health) .. " HP"
					local hpColor = pct > 0.6 and Color3.fromRGB(0, 255, 100) or (pct > 0.3 and Color3.fromRGB(255, 220, 0) or Color3.fromRGB(255, 60, 60))
					data.hp.TextColor3 = hpColor
					data.highlight.FillColor = hpColor
					data.highlight.OutlineColor = hpColor
					data.highlight.Enabled = espEnabled
					if distanceEnabled and myRoot then
						data.dist.Text = string.format("%.0fm", (myRoot.Position - root.Position).Magnitude)
						data.dist.Visible = true
					else data.dist.Visible = false end
					if boxEspEnabled and data.box and DrawingAvailable then
						local cf = root.CFrame
						local size = Vector3.new(2.5, 5.5, 2)
						local corners = {
							(cf * CFrame.new( size.X/2,  size.Y/2,  size.Z/2)).Position,
							(cf * CFrame.new(-size.X/2,  size.Y/2,  size.Z/2)).Position,
							(cf * CFrame.new(-size.X/2,  size.Y/2, -size.Z/2)).Position,
							(cf * CFrame.new( size.X/2,  size.Y/2, -size.Z/2)).Position,
							(cf * CFrame.new( size.X/2, -size.Y/2,  size.Z/2)).Position,
							(cf * CFrame.new(-size.X/2, -size.Y/2,  size.Z/2)).Position,
							(cf * CFrame.new(-size.X/2, -size.Y/2, -size.Z/2)).Position,
							(cf * CFrame.new( size.X/2, -size.Y/2, -size.Z/2)).Position,
						}
						local screen = {}
						local onScreen = true
						for i, pos in ipairs(corners) do
							local sp, vis = camera:WorldToViewportPoint(pos)
							screen[i] = Vector2.new(sp.X, sp.Y)
							if not vis or sp.Z < 0 then onScreen = false end
						end
						local connections = {{1,2},{2,3},{3,4},{4,1},{5,6},{6,7},{7,8},{8,5},{1,5},{2,6},{3,7},{4,8}}
						for i, conn in ipairs(connections) do
							local line = data.box[i]
							if line then
								if onScreen then
									line.From = screen[conn[1]]
									line.To = screen[conn[2]]
									line.Color = hpColor
									line.Visible = true
								else line.Visible = false end
							end
						end
					elseif data.box then
						for _, line in pairs(data.box) do if line then line.Visible = false end end
					end
					if tracersEnabled and data.tracer and DrawingAvailable then
						local sp, vis = camera:WorldToViewportPoint(root.Position)
						if vis and sp.Z > 0 then
							data.tracer.From = Vector2.new(camera.ViewportSize.X/2, camera.ViewportSize.Y)
							data.tracer.To = Vector2.new(sp.X, sp.Y)
							data.tracer.Color = hpColor
							data.tracer.Visible = true
						else data.tracer.Visible = false end
					elseif data.tracer then data.tracer.Visible = false end
				end
			else
				if espObjects[plr] then
					local data = espObjects[plr]
					if data.highlight then pcall(function() data.highlight:Destroy() end) end
					if data.billboard then pcall(function() data.billboard:Destroy() end) end
					if data.box then for _, line in pairs(data.box) do pcall(function() if line.Remove then line:Remove() end end) end end
					if data.tracer then pcall(function() if data.tracer.Remove then data.tracer:Remove() end end) end
					espObjects[plr] = nil
				end
			end
		end
	end
end

local function toggleFullbright()
	if not fullbrightEnabled then
		originalLighting = {
			Brightness = Lighting.Brightness, ClockTime = Lighting.ClockTime,
			FogEnd = Lighting.FogEnd, GlobalShadows = Lighting.GlobalShadows,
			OutdoorAmbient = Lighting.OutdoorAmbient, Ambient = Lighting.Ambient
		}
		fullbrightEnabled = true
		fullbrightBtn.Text = "Fullbright: ON"
		fullbrightConnection = RunService.RenderStepped:Connect(function()
			Lighting.Brightness = 2
			Lighting.ClockTime = 14
			Lighting.FogEnd = 100000
			Lighting.GlobalShadows = false
			Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
			Lighting.Ambient = Color3.fromRGB(1, 1, 1)
		end)
		notify("Fullbright ON")
	else
		fullbrightEnabled = false
		fullbrightBtn.Text = "Fullbright: OFF"
		if fullbrightConnection then fullbrightConnection:Disconnect() fullbrightConnection = nil end
		for prop, value in pairs(originalLighting) do pcall(function() Lighting[prop] = value end) end
		notify("Fullbright OFF")
	end
end

local function toggleXray()
	xrayEnabled = not xrayEnabled
	if xrayConnection then xrayConnection:Disconnect() xrayConnection = nil end
	if xrayEnabled then
		xrayBtn.Text = "X-Ray: ON"
		xrayConnection = RunService.RenderStepped:Connect(function()
			for _, obj in pairs(workspace:GetDescendants()) do
				if obj:IsA("BasePart") and not obj:IsDescendantOf(player.Character) then
					if not xrayParts[obj] then xrayParts[obj] = obj.LocalTransparencyModifier end
					obj.LocalTransparencyModifier = 0.65
				end
			end
		end)
		notify("X-Ray ON")
	else
		xrayBtn.Text = "X-Ray: OFF"
		for part, original in pairs(xrayParts) do
			if part and part.Parent then pcall(function() part.LocalTransparencyModifier = original end) end
		end
		xrayParts = {}
		notify("X-Ray OFF")
	end
end

local function updateJumpConnection()
	if jumpConnection then jumpConnection:Disconnect() jumpConnection = nil end
	if infiniteJumpEnabled then
		jumpConnection = UserInputService.JumpRequest:Connect(function()
			local hum = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
			if hum and hum.Health > 0 then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
		end)
	end
end

local function createCheckpointMarker(cf)
	if checkpointMarker and checkpointMarker.Parent then checkpointMarker:Destroy() end
	local marker = Instance.new("Part")
	marker.Size = Vector3.new(4, 0.3, 4)
	marker.Anchored = true
	marker.CanCollide = false
	marker.Material = Enum.Material.Neon
	marker.Color = Color3.fromRGB(0, 255, 80)
	marker.Transparency = 0.25
	marker.CFrame = CFrame.new(cf.Position - Vector3.new(0, 2.8, 0))
	marker.Parent = workspace
	checkpointMarker = marker
end

local function saveCheckpoint()
	local char = player.Character
	if not char then return end
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	checkpointPos = hrp.CFrame
	createCheckpointMarker(checkpointPos)
	notify("Checkpoint salvo")
end

local function teleportToCheckpoint()
	if not checkpointPos then notify("Sem checkpoint") return end
	local char = player.Character
	if not char then return end
	local hrp = char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	hrp.CFrame = checkpointPos + Vector3.new(0, 3, 0)
	notify("Teleportado")
end

local function clearCheckpoint()
	checkpointPos = nil
	if checkpointMarker then checkpointMarker:Destroy() checkpointMarker = nil end
	notify("Checkpoint limpo")
end

-- SWITCH TAB
local function switchTab(cat)
	currentCategory = cat
	contentEscalada.Visible = cat == "Escalada"
	contentCheckpoint.Visible = cat == "Checkpoint"
	contentCombat.Visible = cat == "Combat"
	contentMovimento.Visible = cat == "Movimento"
	contentProtecao.Visible = cat == "Proteção"
	contentUGC.Visible = cat == "UGC"
	contentUGCR6.Visible = cat == "UGC R6"
	contentOlho.Visible = cat == "Olho"

	local on = Color3.fromRGB(0, 55, 25)
	tabEscalada.BackgroundColor3   = cat == "Escalada"   and on or COL_BTN
	tabCheckpoint.BackgroundColor3 = cat == "Checkpoint" and on or COL_BTN
	tabCombat.BackgroundColor3     = cat == "Combat"     and on or COL_BTN
	tabMovimento.BackgroundColor3  = cat == "Movimento"  and on or COL_BTN
	tabProtecao.BackgroundColor3   = cat == "Proteção"   and on or COL_BTN
	tabUGC.BackgroundColor3        = cat == "UGC"        and on or COL_BTN
	tabUGCR6.BackgroundColor3      = cat == "UGC R6"     and on or COL_BTN
	tabOlho.BackgroundColor3       = cat == "Olho"       and on or COL_BTN

	local active = getActiveContent()
	updateCanvasSize(active)
	active.CanvasPosition = Vector2.new(0, 0)
end

tabEscalada.MouseButton1Click:Connect(function() switchTab("Escalada") end)
tabCheckpoint.MouseButton1Click:Connect(function() switchTab("Checkpoint") end)
tabCombat.MouseButton1Click:Connect(function() switchTab("Combat") end)
tabMovimento.MouseButton1Click:Connect(function() switchTab("Movimento") end)
tabProtecao.MouseButton1Click:Connect(function() switchTab("Proteção") end)
tabUGC.MouseButton1Click:Connect(function() switchTab("UGC") end)
tabUGCR6.MouseButton1Click:Connect(function() switchTab("UGC R6") end)
tabOlho.MouseButton1Click:Connect(function() switchTab("Olho") end)

-- CONEXÕES
aimBtn.MouseButton1Click:Connect(function()
	isAiming = not isAiming
	aimBtn.Text = isAiming and "Desativar Mira" or "Ativar Mira"
	updateCrosshairs()
end)
createBtn.MouseButton1Click:Connect(createLadderManual)
autoBtn.MouseButton1Click:Connect(function()
	autoEnabled = not autoEnabled
	autoBtn.Text = autoEnabled and "Automática: ON" or "Automática: OFF"
end)
removeBtn.MouseButton1Click:Connect(function()
	for _, obj in ipairs(ladders) do if obj then obj:Destroy() end end
	ladders = {}
	lastAutoLadder = nil
	notify("Truss removidas")
end)

saveBtn.MouseButton1Click:Connect(saveCheckpoint)
tpBtn.MouseButton1Click:Connect(teleportToCheckpoint)
clearBtn.MouseButton1Click:Connect(clearCheckpoint)
autoTPBtn.MouseButton1Click:Connect(function()
	autoRespawnToCheckpoint = not autoRespawnToCheckpoint
	autoTPBtn.Text = autoRespawnToCheckpoint and "Auto-TP Morte: ON" or "Auto-TP Morte: OFF"
end)
tptoolBtn.MouseButton1Click:Connect(function()
	if tpToolInstance then
		tpToolInstance:Destroy()
		tpToolInstance = nil
		tptoolBtn.Text = "TP Tool: OFF"
	else
		local tool = Instance.new("Tool")
		tool.Name = "TP Tool"
		tool.RequiresHandle = false
		tool.Activated:Connect(function()
			local m = player:GetMouse()
			if m.Hit and player.Character then
				player.Character:PivotTo(CFrame.new(m.Hit.Position + Vector3.new(0, 3, 0)))
			end
		end)
		tool.Parent = player.Backpack
		tpToolInstance = tool
		tptoolBtn.Text = "TP Tool: ON"
	end
end)
invisibleBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fe-invisible-V2-51608"))() end)
	notify("Invisible V2")
end)
portalBtn.MouseButton1Click:Connect(togglePortalAim)
gravityGunBtn.MouseButton1Click:Connect(function()
	pcall(function()
		loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fe-Physics-Gun-aboaooaosbdbaoaoboabdoaboabd-160896"))()
	end)
	notify("Gravity Gun carregado")
end)

-- Combat + Aimbot variants
aimbotBtn.MouseButton1Click:Connect(function()
	aimbotEnabled = not aimbotEnabled
	aimbotBtn.Text = aimbotEnabled and "Aimbot: ON" or "Aimbot: OFF"
	updateCrosshairs()
	notify(aimbotEnabled and "Aimbot ON" or "Aimbot OFF")
end)

autoShotBtn.MouseButton1Click:Connect(function()
	autoShotEnabled = not autoShotEnabled
	autoShotBtn.Text = autoShotEnabled and "Auto Shot: ON" or "Auto Shot: OFF"
	notify(autoShotEnabled and "Auto Shot ON" or "Auto Shot OFF")
end)

crosshairAimBtn.MouseButton1Click:Connect(function()
	crosshairAimbotEnabled = not crosshairAimbotEnabled
	crosshairAimBtn.Text = crosshairAimbotEnabled and "Crosshair Aimbot: ON" or "Crosshair Aimbot: OFF"
	if crosshairAimbotEnabled then
		aimbotEnabled = true
		aimbotBtn.Text = "Aimbot: ON"
	end
	updateCrosshairs()
	notify(crosshairAimbotEnabled and "Crosshair Aimbot ON" or "Crosshair Aimbot OFF")
end)

customMiraBtn.MouseButton1Click:Connect(function()
	customCrosshairEnabled = not customCrosshairEnabled
	customMiraBtn.Text = customCrosshairEnabled and "Mira Vermelha: ON" or "Mira Vermelha: OFF"
	updateCrosshairs()
	notify(customCrosshairEnabled and "Mira Vermelha ON" or "Mira Vermelha OFF")
end)

partBtn.MouseButton1Click:Connect(function()
	if aimPartName == "Head" then
		aimPartName = "HumanoidRootPart"
		partBtn.Text = "Alvo: Tronco"
	else
		aimPartName = "Head"
		partBtn.Text = "Alvo: Cabeça"
	end
end)
flingBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-OP-FE-TOUCH-FLING-WORKS-IN-ALL-GAMES-WITH-COLLISION-NOT-MINE-50254"))() end)
	notify("Fling")
end)
teamCheckBtn.MouseButton1Click:Connect(function()
	teamCheckEnabled = not teamCheckEnabled
	teamCheckBtn.Text = teamCheckEnabled and "Team Check: ON" or "Team Check: OFF"
end)
espBtn.MouseButton1Click:Connect(function()
	espEnabled = not espEnabled
	espBtn.Text = espEnabled and "ESP: ON" or "ESP: OFF"
	if not (espEnabled or boxEspEnabled or tracersEnabled or distanceEnabled) then clearESP() end
	notify(espEnabled and "ESP ON" or "ESP OFF")
end)
boxEspBtn.MouseButton1Click:Connect(function()
	if not DrawingAvailable then notify("Box ESP não suportado") return end
	boxEspEnabled = not boxEspEnabled
	boxEspBtn.Text = boxEspEnabled and "Box ESP: ON" or "Box ESP: OFF"
end)
tracersBtn.MouseButton1Click:Connect(function()
	if not DrawingAvailable then notify("Tracers não suportado") return end
	tracersEnabled = not tracersEnabled
	tracersBtn.Text = tracersEnabled and "Tracers: ON" or "Tracers: OFF"
end)
distanceBtn.MouseButton1Click:Connect(function()
	distanceEnabled = not distanceEnabled
	distanceBtn.Text = distanceEnabled and "Distance: ON" or "Distance: OFF"
end)

applyMoveBtn.MouseButton1Click:Connect(applyMovement)
jumpBtn.MouseButton1Click:Connect(function()
	infiniteJumpEnabled = not infiniteJumpEnabled
	updateJumpConnection()
	jumpBtn.Text = infiniteJumpEnabled and "Pulo Infinito: ON" or "Pulo Infinito: OFF"
end)
gravityBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-No-gravity-trip-47019"))() end)
	notify("Zero Gravidade")
end)
shiftLockBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Shift-lock-lite-213005"))() end)
	notify("Shift Lock")
end)
noclipBtn.MouseButton1Click:Connect(toggleNoclip)
flyBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-FLY-GUI-V11-205450"))() end)
	notify("Fly")
end)

antiFlingBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Gaze-anti-fling-ig-2-51386"))() end)
	notify("Anti Fling")
end)
antiBangBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-tarekscripter-anti-bang-KILL-HIM-208556"))() end)
	notify("AntiBang")
end)
boostFpsBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Boostfps-26436"))() end)
	notify("Boost FPS")
end)
antiSitBtn.MouseButton1Click:Connect(toggleAntiSit)

ugcAnimBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fe-Ugc-Animations-242778"))() end)
	notify("UGC Animations")
end)
crouchBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-FE-R15-Crouch-Script-64510"))() end)
	notify("Crouch carregado")
end)
jetbotBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Jetbot-guy-FE-r15-237958"))() end)
	notify("Jetbot carregado")
end)
adaptationBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fe-Sonic-Exe-240487"))() end)
	notify("Sonic Exe carregado")
end)
goatBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Goat-simulator-mobile-version-animation-r15-240881"))() end)
	notify("Goat Simulator carregado")
end)
amongUsBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Among-us-animation-R15-246162"))() end)
	notify("Among Us carregado")
end)
militaryBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Military-Bot-V2-FE-r15-239559"))() end)
	notify("Military Bot carregado")
end)

reanimateBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-uh-reanimate-134885"))() end)
	notify("Reanimate")
end)
scp096Btn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-FE-SCP-096-or-R6-or-FLING-3108"))() end)
	notify("SCP-096 / Fling carregado")
end)
r6AnimsBtn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-FE-R6-ANIMATIONS-HUB-78089"))() end)
	notify("R6 Animations Hub carregado")
end)
scp049Btn.MouseButton1Click:Connect(function()
	pcall(function() loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-SCP-049-FE-R6-GUI-Script-130754"))() end)
	notify("SCP-049 carregado")
end)

xrayBtn.MouseButton1Click:Connect(toggleXray)
fullbrightBtn.MouseButton1Click:Connect(toggleFullbright)
spectateBtn.MouseButton1Click:Connect(toggleSpectate)
tpNameBtn.MouseButton1Click:Connect(tpToName)

-- Minimize
local originalSize = mainFrame.Size
minimizeBtn.MouseButton1Click:Connect(function()
	menuVisible = not menuVisible
	if not menuVisible then
		contentEscalada.Visible = false
		contentCheckpoint.Visible = false
		contentCombat.Visible = false
		contentMovimento.Visible = false
		contentProtecao.Visible = false
		contentUGC.Visible = false
		contentUGCR6.Visible = false
		contentOlho.Visible = false
		tabFrame.Visible = false
		statusLabel.Visible = false
		scrollArrowFrame.Visible = false
		mainFrame.Size = UDim2.new(0, mainFrame.Size.X.Offset, 0, HEADER_HEIGHT)
		minimizeBtn.Text = "+"
	else
		tabFrame.Visible = true
		statusLabel.Visible = true
		scrollArrowFrame.Visible = true
		mainFrame.Size = originalSize
		minimizeBtn.Text = "−"
		switchTab(currentCategory)
	end
end)

UserInputService.InputBegan:Connect(function(input, gp)
	if gp then return end
	if input.KeyCode == Enum.KeyCode.H then
		minimizeBtn.MouseButton1Click:Fire()
	end
end)

-- LOOPS
RunService.Heartbeat:Connect(function()
	if autoEnabled then tryAutoLadder() end
	if spectateEnabled and spectateTarget and spectateTarget.Character then
		local hum = spectateTarget.Character:FindFirstChildOfClass("Humanoid")
		if hum then camera.CameraSubject = hum end
	end
end)

RunService.RenderStepped:Connect(function()
	-- Aimbot + Crosshair Aimbot
	if aimbotEnabled or crosshairAimbotEnabled then
		local target = getClosestTarget()
		if target and target.Character then
			local part = target.Character:FindFirstChild(aimPartName)
			if part then
				camera.CFrame = camera.CFrame:Lerp(CFrame.new(camera.CFrame.Position, part.Position), aimSmoothness)
				if autoShotEnabled then
					doAutoShot(target)
				end
			end
		end
	end

	if espEnabled or boxEspEnabled or tracersEnabled or distanceEnabled then
		updateESP()
	end
end)

player.CharacterAdded:Connect(function(newChar)
	local hrp = newChar:WaitForChild("HumanoidRootPart", 8)
	if not hrp then return end
	task.wait(0.4)
	if checkpointPos and autoRespawnToCheckpoint then
		pcall(function() hrp.CFrame = checkpointPos + Vector3.new(0, 3, 0) end)
		notify("Respawn no checkpoint")
	end
	if antiSitEnabled then
		local hum = newChar:FindFirstChildOfClass("Humanoid")
		if hum then pcall(function() hum:SetStateEnabled(Enum.HumanoidStateType.Seated, false) end) end
	end
end)

-- START
showLoadingScreen(function()
	screenGui.Enabled = true
	switchTab("Escalada")
	notify("ModMenu online (Mobile Ready)")
end)
]])()
