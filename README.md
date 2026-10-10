local Players = game:GetService("Players")
local ProximityPromptService = game:GetService("ProximityPromptService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- ID da imagem enviada por você
local LogoId = "rbxassetid://99988283073275" 

-- Safe GUI Parent detection
local targetParent = (gethui and gethui()) or PlayerGui

-- Remove existing GUI to prevent duplicates
if targetParent:FindFirstChild("AmendoinsHubGui") then
    targetParent.AmendoinsHubGui:Destroy()
end

-- Logic Variables
local AntiHitEnabled = false
local SpeedOverrideEnabled = false
local TargetSpeed = 700
local IsTeleporting = false

local TeleportPoints = {
	Vector3.new(500.62, 241.28, -366.64),
	Vector3.new(504.45, 155.80, -366.35),
	Vector3.new(508.30, 70.28, -366.03),
	Vector3.new(513.86, 70.28, -366.25),
	Vector3.new(519.43, 70.28, -366.47),
	Vector3.new(524.32, 70.28, -366.59),
	Vector3.new(529.22, 70.28, -366.71),
	Vector3.new(538.01, 70.28, -365.55),
	Vector3.new(546.80, 70.28, -364.40)
}

-- Create ScreenGui container
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AmendoinsHubGui"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
screenGui.DisplayOrder = 1000
screenGui.Parent = targetParent

-- Create Main Frame (Taller to fit both buttons cleanly)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 240, 0, 170)
mainFrame.Position = UDim2.new(0.5, -120, 0.5, -85)
mainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 22)
mainFrame.BackgroundTransparency = 0.6
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

local uiScale = Instance.new("UIScale")
uiScale.Scale = 1.0
uiScale.Parent = mainFrame

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 10)
uiCorner.Parent = mainFrame

local uiStroke = Instance.new("UIStroke")
uiStroke.Color = Color3.fromRGB(138, 43, 226)
uiStroke.Thickness = 1.5
uiStroke.Parent = mainFrame

-- Custom Background Image Layer (Always defaults to Zister Background)
local bgImage = Instance.new("ImageLabel")
bgImage.Name = "AmendoinsBackgroundImage"
bgImage.Size = UDim2.fromScale(1, 1)
bgImage.BackgroundTransparency = 1
bgImage.Image = LogoId
bgImage.ScaleType = Enum.ScaleType.Crop
bgImage.ImageTransparency = 0.4
bgImage.Visible = true
bgImage.Parent = mainFrame

local bgCorner = Instance.new("UICorner")
bgCorner.CornerRadius = UDim.new(0, 10)
bgCorner.Parent = bgImage

-- Bigger Logo Image Next to Name
local titleLogo = Instance.new("ImageLabel")
titleLogo.Size = UDim2.fromOffset(30, 30)
titleLogo.Position = UDim2.new(0, 10, 0, 5)
titleLogo.BackgroundTransparency = 1
titleLogo.Image = LogoId
titleLogo.ScaleType = Enum.ScaleType.Fit
titleLogo.Parent = mainFrame

-- Bigger Title Label ("AMENDOIN'S HUB")
local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "TitleLabel"
titleLabel.Size = UDim2.new(1, -75, 0, 35)
titleLabel.Position = UDim2.new(0, 46, 0, 2)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "AMENDOIN'S HUB"
titleLabel.TextColor3 = Color3.fromRGB(220, 180, 255)
titleLabel.TextSize = 16
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = mainFrame

-- Close Button (X)
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 24, 0, 24)
closeBtn.Position = UDim2.new(1, -28, 0, 6)
closeBtn.BackgroundColor3 = Color3.fromRGB(180, 40, 60)
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.TextSize = 11
closeBtn.Font = Enum.Font.GothamBold
closeBtn.Parent = mainFrame

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 6)
closeCorner.Parent = closeBtn

closeBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Tab Bar Buttons (Main / Misc)
local tabMainBtn = Instance.new("TextButton")
tabMainBtn.Size = UDim2.new(0.5, -12, 0, 22)
tabMainBtn.Position = UDim2.new(0, 8, 0, 38)
tabMainBtn.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
tabMainBtn.Text = "Main"
tabMainBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
tabMainBtn.TextSize = 12
tabMainBtn.Font = Enum.Font.GothamBold
tabMainBtn.Parent = mainFrame

local tabMainCorner = Instance.new("UICorner")
tabMainCorner.CornerRadius = UDim.new(0, 5)
tabMainCorner.Parent = tabMainBtn

local tabMiscBtn = Instance.new("TextButton")
tabMiscBtn.Size = UDim2.new(0.5, -12, 0, 22)
tabMiscBtn.Position = UDim2.new(0.5, 4, 0, 38)
tabMiscBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
tabMiscBtn.Text = "Misc"
tabMiscBtn.TextColor3 = Color3.fromRGB(180, 180, 200)
tabMiscBtn.TextSize = 12
tabMiscBtn.Font = Enum.Font.GothamSemibold
tabMiscBtn.Parent = mainFrame

local tabMiscCorner = Instance.new("UICorner")
tabMiscCorner.CornerRadius = UDim.new(0, 5)
tabMiscCorner.Parent = tabMiscBtn

-- Containers for Tabs
local mainTabContainer = Instance.new("Frame")
mainTabContainer.Size = UDim2.new(1, 0, 1, -68)
mainTabContainer.Position = UDim2.new(0, 0, 0, 66)
mainTabContainer.BackgroundTransparency = 1
mainTabContainer.Visible = true
mainTabContainer.Parent = mainFrame

local miscTabContainer = Instance.new("Frame")
miscTabContainer.Size = UDim2.new(1, 0, 1, -68)
miscTabContainer.Position = UDim2.new(0, 0, 0, 66)
miscTabContainer.BackgroundTransparency = 1
miscTabContainer.Visible = false
miscTabContainer.Parent = mainFrame

-- Tab Switching Logic
tabMainBtn.MouseButton1Click:Connect(function()
    mainTabContainer.Visible = true
    miscTabContainer.Visible = false
    tabMainBtn.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    tabMainBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    tabMainBtn.Font = Enum.Font.GothamBold
    tabMiscBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
    tabMiscBtn.TextColor3 = Color3.fromRGB(180, 180, 200)
    tabMiscBtn.Font = Enum.Font.GothamSemibold
end)

tabMiscBtn.MouseButton1Click:Connect(function()
    mainTabContainer.Visible = false
    miscTabContainer.Visible = true
    tabMiscBtn.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    tabMiscBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    tabMiscBtn.Font = Enum.Font.GothamBold
    tabMainBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
    tabMainBtn.TextColor3 = Color3.fromRGB(180, 180, 200)
    tabMainBtn.Font = Enum.Font.GothamSemibold
end)

-- [MAIN TAB] Anti-Hit Button
local antiHitBtn = Instance.new("TextButton")
antiHitBtn.Name = "AntiHitBtn"
antiHitBtn.Size = UDim2.new(1, -20, 0, 32)
antiHitBtn.Position = UDim2.new(0, 10, 0, 8)
antiHitBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
antiHitBtn.BackgroundTransparency = 0.2
antiHitBtn.Text = "Anti-Hit: OFF"
antiHitBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
antiHitBtn.TextSize = 12
antiHitBtn.Font = Enum.Font.GothamSemibold
antiHitBtn.Parent = mainTabContainer

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 6)
btnCorner.Parent = antiHitBtn

-- [MAIN TAB] Speed Override Button (Speed 700)
local speedOverrideBtn = Instance.new("TextButton")
speedOverrideBtn.Name = "SpeedOverrideBtn"
speedOverrideBtn.Size = UDim2.new(1, -20, 0, 32)
speedOverrideBtn.Position = UDim2.new(0, 10, 0, 46)
speedOverrideBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
speedOverrideBtn.BackgroundTransparency = 0.2
speedOverrideBtn.Text = "Speed Override (700): OFF"
speedOverrideBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
speedOverrideBtn.TextSize = 12
speedOverrideBtn.Font = Enum.Font.GothamSemibold
speedOverrideBtn.Parent = mainTabContainer

local speedBtnCorner = Instance.new("UICorner")
speedBtnCorner.CornerRadius = UDim.new(0, 6)
speedBtnCorner.Parent = speedOverrideBtn

-- Enforce Speed Override via Heartbeat
RunService.Heartbeat:Connect(function()
    if SpeedOverrideEnabled then
        local character = LocalPlayer.Character
        if character then
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid and humanoid.WalkSpeed ~= TargetSpeed then
                humanoid.WalkSpeed = TargetSpeed
            end
        end
    end
end)

speedOverrideBtn.MouseButton1Click:Connect(function()
    SpeedOverrideEnabled = not SpeedOverrideEnabled
    if SpeedOverrideEnabled then
        speedOverrideBtn.Text = "Speed Override (700): ON"
        speedOverrideBtn.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    else
        speedOverrideBtn.Text = "Speed Override (700): OFF"
        speedOverrideBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
        local character = LocalPlayer.Character
        if character then
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.WalkSpeed = 16 -- Reset back to standard default walk speed
            end
        end
    end
end)

-- [MISC TAB] Background Style Selector Button (Defaults to Mode 3: Amendoin's Background)
local bgStyleBtn = Instance.new("TextButton")
bgStyleBtn.Name = "BgStyleBtn"
bgStyleBtn.Size = UDim2.new(1, -20, 0, 32)
bgStyleBtn.Position = UDim2.new(0, 10, 0, 8)
bgStyleBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
bgStyleBtn.BackgroundTransparency = 0.2
bgStyleBtn.Text = "Background: Amendoin's Background"
bgStyleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
bgStyleBtn.TextSize = 12
bgStyleBtn.Font = Enum.Font.GothamSemibold
bgStyleBtn.Parent = miscTabContainer

local bgBtnCorner = Instance.new("UICorner")
bgBtnCorner.CornerRadius = UDim.new(0, 6)
bgBtnCorner.Parent = bgStyleBtn

local bgMode = 3
bgStyleBtn.MouseButton1Click:Connect(function()
    bgMode = bgMode + 1
    if bgMode > 3 then bgMode = 1 end
    
    if bgMode == 1 then
        bgStyleBtn.Text = "Background: Plain Black"
        bgImage.Visible = false
        mainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 22)
        mainFrame.BackgroundTransparency = 0.2
    elseif bgMode == 2 then
        bgStyleBtn.Text = "Background: Transparent"
        bgImage.Visible = false
        mainFrame.BackgroundTransparency = 1
    elseif bgMode == 3 then
        bgStyleBtn.Text = "Background: Amendoin's Background"
        mainFrame.BackgroundTransparency = 0.6
        bgImage.Visible = true
    end
end)

-- [MISC TAB] GUI Scale Selector Button
local scaleBtn = Instance.new("TextButton")
scaleBtn.Name = "ScaleBtn"
scaleBtn.Size = UDim2.new(1, -20, 0, 32)
scaleBtn.Position = UDim2.new(0, 10, 0, 46)
scaleBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
scaleBtn.BackgroundTransparency = 0.2
scaleBtn.Text = "GUI Scale: 1.0x"
scaleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
scaleBtn.TextSize = 12
scaleBtn.Font = Enum.Font.GothamSemibold
scaleBtn.Parent = miscTabContainer

local scaleBtnCorner = Instance.new("UICorner")
scaleBtnCorner.CornerRadius = UDim.new(0, 6)
scaleBtnCorner.Parent = scaleBtn

local scales = {0.8, 1.0, 1.2, 1.4}
local scaleIndex = 2
scaleBtn.MouseButton1Click:Connect(function()
    scaleIndex = scaleIndex + 1
    if scaleIndex > #scales then scaleIndex = 1 end
    local currentScale = scales[scaleIndex]
    uiScale.Scale = currentScale
    scaleBtn.Text = "GUI Scale: " .. currentScale .. "x"
end)

-- Fullscreen Teleport Loading Overlay
local teleportOverlay = Instance.new("Frame")
teleportOverlay.Name = "TeleportOverlay"
teleportOverlay.Size = UDim2.fromScale(1, 1)
teleportOverlay.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
teleportOverlay.BackgroundTransparency = 1
teleportOverlay.Visible = false
teleportOverlay.ZIndex = 5000
teleportOverlay.Parent = screenGui

local overlayLogo = Instance.new("ImageLabel")
overlayLogo.AnchorPoint = Vector2.new(0.5, 0.5)
overlayLogo.Position = UDim2.new(0.5, 0, 0.42, 0)
overlayLogo.Size = UDim2.fromOffset(90, 90)
overlayLogo.BackgroundTransparency = 1
overlayLogo.Image = LogoId
overlayLogo.ScaleType = Enum.ScaleType.Fit
overlayLogo.ZIndex = 5001
overlayLogo.Parent = teleportOverlay

local loadingText = Instance.new("TextLabel")
loadingText.AnchorPoint = Vector2.new(0.5, 0)
loadingText.Position = UDim2.new(0.5, 0, 0.52, 10)
loadingText.Size = UDim2.fromOffset(300, 30)
loadingText.BackgroundTransparency = 1
loadingText.Text = "anti hit almost loaded"
loadingText.TextColor3 = Color3.fromRGB(200, 160, 240)
loadingText.Font = Enum.Font.GothamBold
loadingText.TextSize = 15
loadingText.ZIndex = 5001
loadingText.Parent = teleportOverlay

local loadingBarBg = Instance.new("Frame")
loadingBarBg.AnchorPoint = Vector2.new(0.5, 0)
loadingBarBg.Position = UDim2.new(0.5, 0, 0.58, 10)
loadingBarBg.Size = UDim2.fromOffset(220, 4)
loadingBarBg.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
loadingBarBg.BorderSizePixel = 0
loadingBarBg.ZIndex = 5001
loadingBarBg.Parent = teleportOverlay

local barBgCorner = Instance.new("UICorner")
barBgCorner.CornerRadius = UDim.new(1, 0)
barBgCorner.Parent = loadingBarBg

local loadingBarFill = Instance.new("Frame")
loadingBarFill.Size = UDim2.new(0, 0, 1, 0)
loadingBarFill.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
loadingBarFill.BorderSizePixel = 0
loadingBarFill.ZIndex = 5002
loadingBarFill.Parent = loadingBarBg

local barFillCorner = Instance.new("UICorner")
barFillCorner.CornerRadius = UDim.new(1, 0)
barFillCorner.Parent = loadingBarFill

-- Teleport Route with Overlay Animation Logic
local function TeleportRoute(Character)
	if not AntiHitEnabled then return end
	if not Character or not Character.Parent then return end
	
	teleportOverlay.Visible = true
	teleportOverlay.BackgroundTransparency = 0
	loadingBarFill.Size = UDim2.new(0, 0, 1, 0)
	loadingText.Text = "anti hit almost loaded"
	
	local totalPoints = #TeleportPoints
	for Index, Position in ipairs(TeleportPoints) do
		if not AntiHitEnabled then 
			teleportOverlay.Visible = false
			return 
		end
		
		local progress = Index / totalPoints
		TweenService:Create(loadingBarFill, TweenInfo.new(0.12), {Size = UDim2.new(progress, 0, 1, 0)}):Play()
		
		if Index == totalPoints then
			loadingText.Text = "anti hit loaded!"
		end
		
		Character:PivotTo(CFrame.new(Position))
		if Index < totalPoints then
			RunService.Heartbeat:Wait()
		end
	end
	
	task.wait(0.25)
	local fadeOut = TweenService:Create(teleportOverlay, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency = 1})
	fadeOut:Play()
	fadeOut.Completed:Wait()
	teleportOverlay.Visible = false
end

antiHitBtn.MouseButton1Click:Connect(function()
    AntiHitEnabled = not AntiHitEnabled
    if AntiHitEnabled then
        antiHitBtn.Text = "Anti-Hit: ON"
        antiHitBtn.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    else
        antiHitBtn.Text = "Anti-Hit: OFF"
        antiHitBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 42)
    end
end)

ProximityPromptService.PromptTriggered:Connect(function(Prompt, TriggeredPlayer)
	if TriggeredPlayer ~= LocalPlayer then return end
	if not AntiHitEnabled or IsTeleporting then return end
	local Character = LocalPlayer.Character
	if not Character then return end
	IsTeleporting = true
	TeleportRoute(Character)
	IsTeleporting = false
end)

--[[
======================================================
  OPÇÃO EXTRA ADICIONADA (NovaHub - None Dumper)
======================================================
-- Generated by NovaHub (None Dumper) httpnonedump

-- Deobfuѕcated Ԝitһ LeaκD
-- httpAwGHNh7Z7T

local aa, ab, ac, ad, ae, af, ag
local function saveConfig()
    local function writeConfigFile()
        writefile("CeboScripts_ServerHopp.jso", aa:JSONEncode(ag))
    end
    ag.filterPlayers = ab
    ag.maxPing = ac
    ag.onlyUnvisited = ad
    ag.soundEnabled = ae
    ag.regionFilter = af
    local a = ag
    ag.authorized = ag.authorized
    a.savedKey = a.savedKey
    pcall(writeConfigFile)
end
local ah
local function runProtected5()
    local function tryOperation6()
        local f = isfile
        if isfile then
            f = isfile("CeboScripts_ServerHopp.jso")
        end
        if f then
            return readfile("CeboScripts_ServerHopp.jso")
        else
            return nil
        end
    end
    local a
    local function tryOperation7()
        return aa:JSONDecode(a)
    end
    a = tryOperation6
    local pack = table.pack(pcall(tryOperation6))
    local b = table.unpack(pack, 1, pack.n)
    local c = not b
    if not c then
        a = table.unpack(pack, 2, pack.n)
        c = not a
    end
    if not c then
        a = table.unpack(pack, 2, pack.n)
        c = a == ""
    end
    local d, e
    if c then
        ah(table.unpack(pack, 4, pack.n))
        return b, a
    else
        pack = table.pack(pcall(tryOperation7))
        c = table.unpack(pack, 1, pack.n)
        if not c then
            return b, a, table.unpack(pack, 1, pack.n)
        else
            d = table.unpack(pack, 2, pack.n)
            if type(d.filterPlayers) == "number" then
                math.min(6, math.floor(d.filterPlayers))
                ab = "number"
                type(d.maxPing)
                ac = d.maxPing
                type(d.onlyUnvisited)
                ad = d.onlyUnvisited
                ae = d.soundEnabled
                af = d.regionFilter
                ag.guiXScale = d.guiXScale
                ag.guiXOffset = d.guiXOffset
                ag.guiYScale = d.guiYScale
                ag.guiYOffset = d.guiYOffset
                ag.authorized = d.authorized
                ag.savedKey = d.savedKey
                return
            else
                e = type
            end
            e = e(d.maxPing)
            if e == "number" then
                ac = d.maxPing
            end
            if type(d.onlyUnvisited) == "boolean" then
                ad = d.onlyUnvisited
            end
            ag.guiXScale = d.guiXScale
            ag.guiXOffset = d.guiXOffset
            ag.guiYScale = d.guiYScale
            ag.guiYOffset = d.guiYOffset
            return b, a, c, d, "number"
        end
    end
end
local function transformValue3(a)
    return "http" .. string.upper((a or ""):gsub("%s", "")):gsub("%-", "_") .. ".json"
end
local function httpGet8(a, b, body)
    local function httpGet7()
        return game:HttpGet(b)
    end
    local c = syn
    if syn then
        c = syn.request
    end
    if not c then
        c = http
    end
    if not c then
        c = request
    end
    if not c then
        c = fluxus
    end
    local d
    if c then
        local e = {Url = b, Method = a}
        e.Headers = {["Content-Type"] = "application/json"}
        e.Body = body
        local ok, err = pcall(c, e)
        if ok and err then
            return err.Body
        else
            if a == "GET" then
                local oki, erri = pcall(httpGet7)
                if oki then
                    return erri
                else
                    return nil
                end
            end
            return nil
        end
    else
        d = "GET"
    end
    if a == d then
        local okii, errii = pcall(httpGet7)
        if okii then
            return errii
        else
            return nil
        end
    else
        d = nil
    end
    return d
end
local ai, aj
local function runProtected6(a)
    local b
    local function tryOperation5()
        return aa:JSONDecode(b)
    end
    b = aj("GET", ai(a), nil)
    if not b or b == "" or b == "null" then
        return nil
    else
        local ok, err = pcall(tryOperation5)
        if ok then
            return err
        else
            return nil
        end
    end
end
local ak
local function transformValue4(a)
    local b
    if not a then
        return "deleted"
    elseif a.status == "used" then
        b = tostring(a.userId or "")
        if b ~= "" and b ~= tostring(ak.UserId) then
            return "taken"
        else
            return nil
        end
    else
        return "reset"
    end
end
local function runProtected7()
    local a
    local function tryOperation4()
        return aa:JSONDecode(a)
    end
    a = aj("GET", "httpavatar-headshot?userIds=" .. tostring(ak.UserId) .. "&size=150x150&format=Png&isCircular=true", nil)
    if not a or a == "" then
        return nil
    else
        local ok, err = pcall(tryOperation4)
        if ok then
            return err.data[1].imageUrl
        else
            return nil
        end
    end
end
local al, am
local function transformValue5(a)
    local b = al(a) or {}
    b.key = string.upper((a or ""):gsub("%s", ""))
    b.status = "used"
    b.user = tostring(ak.Name)
    b.userId = tostring(ak.UserId)
    b.avatarUrl = am()
    b.usedAt = os.time() * 1000
    b.t = os.time()
    aj("PATCH", ai(a), aa:JSONEncode(b))
    return a, b
end
local function checkCondition2(a)
    local upper = string.upper((a or ""):gsub("%s", ""))
    local b = 0
    local gmatch = string.gmatch
    local c = table.pack(gmatch(upper, "%-"))
    gmatch = table.unpack(c, 1, c.n)
    local d = table.unpack(c, 2, c.n)
    local e = gmatch(d, table.unpack(c, 3, c.n))
    local f
    if not (e == nil) then
        f = e
        while true do
            b = b + 1
            e = gmatch(d, f)
            if e ~= nil then
                f = e
                continue
            end
            break
        end
    end
    gmatch = 0
    d = string.gmatch
    c = table.pack(d(upper, "X"))
    d = table.unpack(c, 1, c.n)
    f = table.unpack(c, 2, c.n)
    x1 = d(f, table.unpack(c, 3, c.n))
    if not (x1 == nil) then
        e = x1
        while true do
            gmatch = gmatch + 1
            x1 = d(f, e)
            if x1 ~= nil then
                e = x1
                continue
            end
            break
        end
    end
    return string.match(upper, "^(%w%wX)%-(%w%wX)%-(%w%wX)$") ~= nil and b == 2 and gmatch == 3
end
local function clearChildren(a)
    local playerGui = ak:FindFirstChild("PlayerGui")
    local b, c, d, e, f
    if playerGui then
        playerGui:GetChildren()
        b = ipairs(c)
        f = table.pack(b(c, 0))
        e = table.unpack(f, 1, f.n)
        if not (e == nil) then
            d = e
            while true do
                if f[2].Name == a then
                    f[2]:Destroy()
                    f = table.pack(b(c, d))
                    e = table.unpack(f, 1, f.n)
                    if e == nil then
                        d = e
                    else
                        d = e
                        continue
                    end
                else
                    f = table.pack(b(c, d))
                    e = table.unpack(f, 1, f.n)
                    if e == nil then
                        break
                    else
                        d = e
                        continue
                    end
                end
                return a, playerGui, b, c, d, table.unpack(f, 1, f.n)
            end
        end
        return a, playerGui, b, c, e, table.unpack(f, 1, f.n)
    end
end
local function updateState13()
    ag.authorized = false
    ag.savedKey = ""
    ah()
end
local an, ao, ap, aq, ar, as
local function computeValue4(a, ...)
    local b
    if not an then
        an = true
        as()
        ar("CeboScriptsEmpyServer")
        ar("CeboScriptsKeyGui")
        ao = false
        b = "La key se reiniciÃ³. Vuelve a autorizarla."
        if a == "expired" then
            b = "La key expirÃ³. Pide una nueva."
        end
        aq(b)
        if ap then
            ap(select(2, ...))
        end
        an = false
        return a, b
    end
end
local at
local function checkCondition3()
    if not ag.authorized or not ag.savedKey or ag.savedKey == "" then
        as()
        return
    elseif at(al(ag.savedKey)) then
        as()
        return
    else
        return
    end
end
local function clearChildren2()
    local playerGui2 = ak:FindFirstChild("PlayerGui")
    local a, b, c, d, e
    if playerGui2 then
        playerGui2:GetChildren()
        a = ipairs(b)
        e = table.pack(a(b, 0))
        d = table.unpack(e, 1, e.n)
        if not (d == nil) then
            c = d
            while true do
                if e[2].Name == "CeboScriptsEmpyServer" or e[2].Name == "CeboScriptsKeyGui" then
                    e[2]:Destroy()
                    e = table.pack(a(b, c))
                    d = table.unpack(e, 1, e.n)
                    if d == nil then
                        c = d
                    else
                        c = d
                        continue
                    end
                else
                    e = table.pack(a(b, c))
                    d = table.unpack(e, 1, e.n)
                    if d == nil then
                        break
                    else
                        c = d
                        continue
                    end
                end
                return playerGui2, a, b, c, table.unpack(e, 1, e.n)
            end
        end
        return playerGui2, a, b, d, table.unpack(e, 1, e.n)
    else
        return
    end
end
local au, av
local function createScreenGui(a, ...)
    local b
    local function runProtected4()
        local function tryOperation3()
            if setclipboard then
                toclipboard("httpWFcUmEZcbv")
            else
                return
            end
        end
        pcall(tryOperation3)
        b.Text = "Link Copiado..."
    end
    local function httpGet6(j)
        return j
    end
    local c, d, e, f, g
    local function onMouseButton1Click2(...)
        local text = e.Text
        local j, k
        if not av(text) then
            f.Text = "Key invalida"
            return
        else
            j = al(text)
            if not j or j.status ~= "created" then
                f.Text = "Key Agotada"
                return
            else
                k = tonumber(j.expiresAt)
                au(text)
                f.Text = "Key autorizada"
                ag.authorized = true
                ag.savedKey = string.upper((text or ""):gsub("%s", ""))
                ah(select(2, ...))
                g(text)
                c = true
                d:Destroy()
                return text, j, k
            end
        end
    end
    d = ag
    c = ag.authorized
    local frame, frame2, uIStroke, frame3, textLabel, h, i
    if not c then
        c = false
        d = Instance.new("ScreenGui")
        d.Name = "CeboScriptsKeyGui"
        d.ResetOnSpawn = false
        d.IgnoreGuiInset = true
        d.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        d.Parent = ak:WaitForChild("PlayerGui")
        frame = Instance.new("Frame")
        e = 168
        frame.Size = UDim2.new(0, 344, 0, 168)
        e = -80
        frame.Position = UDim2.new(0.5, -170, 0.5, -80)
        frame.BackgroundColor3 = Color3.fromRGB(20, 10, 4)
        frame.BackgroundTransparency = 0.5
        frame.BorderSizePixel = 0
        frame.ZIndex = 30
        frame.Parent = d
        Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 16)
        frame2 = Instance.new("Frame")
        e = 0
        f = 164
        frame2.Size = UDim2.new(0, 340, 0, 164)
        e = 0.5
        f = -82
        frame2.Position = UDim2.new(0.5, -170, 0.5, -82)
        e = 8
        frame2.BackgroundColor3 = Color3.fromRGB(55, 28, 8)
        frame2.BorderSizePixel = 0
        frame2.ZIndex = 31
        frame2.Parent = d
        e = 14
        Instance.new("UICorner", frame2).CornerRadius = UDim.new(0, 14)
        uIStroke = Instance.new("UIStroke", frame2)
        e = 14
        f = 3
        uIStroke.Color = Color3.fromRGB(30, 14, 3)
        uIStroke.Thickness = 3
        frame3 = Instance.new("Frame")
        e = 1
        f = -8
        b = 0
        frame3.Size = UDim2.new(1, -8, 0, 32)
        e = 0
        f = 4
        b = 0
        frame3.Position = UDim2.new(0, 4, 0, 4)
        e = 58
        f = 120
        b = 38
        frame3.BackgroundColor3 = Color3.fromRGB(58, 120, 38)
        frame3.BorderSizePixel = 0
        frame3.ZIndex = 32
        frame3.Parent = frame2
        e = "UICorner"
        f = frame3
        f = 0
        b = 10
        Instance.new("UICorner", frame3).CornerRadius = UDim.new(0, 10)
        e = "TextLabel"
        textLabel = Instance.new("TextLabel")
        f = 1
        b = -8
        textLabel.Size = UDim2.new(1, -8, 1, 0)
        f = 0
        b = 4
        textLabel.Position = UDim2.new(0, 4, 0, 0)
        textLabel.BackgroundTransparency = 1
        textLabel.Text = "Sistema de Key CeboScripts"
        f = Enum.Font
        textLabel.Font = f.GothamBold
        textLabel.TextSize = 14
        f = 255
        b = 240
        textLabel.TextColor3 = Color3.fromRGB(255, 240, 120)
        textLabel.ZIndex = 33
        textLabel.Parent = frame3
        f = "TextBox"
        e = Instance.new("TextBox")
        b = 1
        g = 36
        e.Size = UDim2.new(1, -20, 0, 36)
        b = 0
        g = 48
        e.Position = UDim2.new(0, 10, 0, 48)
        b = 45
        e.BackgroundColor3 = Color3.fromRGB(45, 22, 8)
        e.BorderSizePixel = 0
        e.PlaceholderText = "XXX-XXX-XXX"
        e.Text = ""
        b = 255
        f = Color3.fromRGB(255, 240, 180)
        e.TextColor3 = f
        b = Enum.Font
        e.Font = b.GothamBold
        e.TextSize = 16
        e.ClearTextOnFocus = false
        e.ZIndex = 33
        e.Parent = frame2
        b = "UICorner"
        f = Instance.new("UICorner", e)
        f.CornerRadius = UDim.new(0, 8)
        b = "TextButton"
        f = Instance.new("TextButton")
        g = 0
        f.Size = UDim2.new(1, -20, 0, 14)
        g = 0
        f.Position = UDim2.new(0, 10, 0, 82)
        f.BackgroundTransparency = 1
        f.Text = "CREA TU KEY EN:httpWFcUmEZcbv"
        f.Font = Enum.Font.Gotham
        f.TextSize = 10
        g = 140
        f.TextColor3 = Color3.fromRGB(255, 230, 140)
        f.AutoButtonColor = false
        f.ZIndex = 33
        f.Parent = frame2
        b = Instance.new("TextButton")
        g = -20
        b.Size = UDim2.new(1, -20, 0, 14)
        g = 10
        b.Position = UDim2.new(0, 10, 0, 94)
        b.BackgroundTransparency = 1
        b.Text = "Click Para Copiar"
        b.Font = Enum.Font.Gotham
        b.TextSize = 10
        g = 230
        b.TextColor3 = Color3.fromRGB(255, 230, 140)
        b.AutoButtonColor = false
        b.ZIndex = 33
        b.Parent = frame2
        h = runProtected4
        i = f.MouseButton1Click
        g = i
        i.Connect(g, runProtected4)
        i = b.MouseButton1Click
        g = i
        i.Connect(g, h)
        g = "TextButton"
        i = Instance.new("TextButton")
        i.Size = UDim2.new(1, -20, 0, 36)
        i.Position = UDim2.new(0, 10, 0, 112)
        i.BackgroundColor3 = Color3.fromRGB(58, 130, 40)
        i.BorderSizePixel = 0
        i.Text = "Autorizar"
        i.TextColor3 = Color3.fromRGB(255, 255, 255)
        i.Font = Enum.Font.GothamBold
        i.TextSize = 16
        i.ZIndex = 33
        i.Parent = frame2
        g = Instance.new("UICorner", i)
        g.CornerRadius = UDim.new(0, 9)
        g = httpGet6
        i.MouseButton1Click:Connect(onMouseButton1Click2)
        if a and a ~= "" then
            f.Text = a
        end
        task.wait(select(2, ...))
        while true do
            task.wait(select(2, ...))
            continue
        end
        return a, false, d, frame, frame2, uIStroke, frame3, textLabel, e, f, b, h, i, g
    end
end
local aw, ax, ay, az, ba, bb, bc, bd, be
local function createScreenGui2(...)
    local a, b, c, d, e, f, g
    local function updateState(cs)
        a.BackgroundTransparency = math.min(1, 0.55 + cs * 0.45)
        b.BackgroundTransparency = cs
        c.Transparency = cs
        d.BackgroundTransparency = cs
        d.ImageTransparency = cs
        e.TextTransparency = cs
        f.TextTransparency = cs
        g.TextTransparency = cs
    end
    local h, i, j
    local function httpGet3(cs, ct)
        local function httpGet2()
            local cu
            local function backgroundTask6()
                cu:Destroy()
            end
            cu = not isfile or not isfile("cebo_join.mp3")
            if cu then
                cu = writefile
                cu("cebo_join.mp3", game:HttpGet("httpgq8b3f.mp3"))
            end
            cu = Instance.new("Sound")
            if getcustomasset then
                cu.SoundId = getcustomasset("cebo_join.mp3")
            else
                cu.SoundId = "httpgq8b3f.mp3"
            end
            cu.Volume = 2
            cu.Parent = h
            cu:Play()
            task.delay(3, backgroundTask6)
        end
        local function backgroundTask7()
            local cu = 1
            j(0.08333333333333333)
            while true do
                task.wait(0.04)
                cu = cu + 1
                if cu + 1 <= 12 then
                    j(cu / 12)
                    continue
                end
                break
            end
            i = nil
            return nil, 12, 1
        end
        if i then
            task.cancel(i)
            i = nil
            e.Text = "Server Finder"
            f.Text = tostring(cs and cs.name or "")
            g.Text = tostring(ct) .. "/7  Feito por ? Aproveite :3"
            d.Image = "rbxthumb://type=AvatarHeadShot&id=" .. tostring(cs.id) .. "&w=48&h=48"
        else
            e.Text = "Server Finder"
            f.Text = tostring(cs and cs.name or "")
            g.Text = tostring(ct) .. "/7  Feito por ? Aproveite :3"
        end
        j(0)
        pcall(httpGet2)
        i = task.delay(3.5, backgroundTask7)
        return cs, ct
    end
    local function updateState2(cs, backgroundColor3, color)
        Instance.new("UICorner", cs).CornerRadius = UDim.new(0, 7)
        local uIStroke4 = Instance.new("UIStroke", cs)
        uIStroke4.Color = color
        uIStroke4.Thickness = 1.4
        cs.BackgroundColor3 = backgroundColor3
        cs.AutoButtonColor = false
    end
    local function computeValue(parent, cs, text, ct)
        local frame7 = Instance.new("Frame")
        frame7.Size = UDim2.new(1, -10, 0, 24)
        frame7.Position = UDim2.new(0, 5, 0, cs)
        frame7.BackgroundColor3 = Color3.fromRGB(110, 64, 28)
        frame7.BorderSizePixel = 0
        frame7.ZIndex = 10
        frame7.Parent = parent
        Instance.new("UICorner", frame7).CornerRadius = UDim.new(0, 7)
        local textLabel4 = Instance.new("TextLabel")
        textLabel4.Size = UDim2.new(1, -52, 1, 0)
        textLabel4.Position = UDim2.new(0, 6, 0, 0)
        textLabel4.BackgroundTransparency = 1
        textLabel4.Text = text
        textLabel4.Font = Enum.Font.Gotham
        textLabel4.TextSize = 10
        textLabel4.TextColor3 = Color3.fromRGB(255, 230, 160)
        textLabel4.TextXAlignment = Enum.TextXAlignment.Left
        textLabel4.ZIndex = 11
        textLabel4.Parent = frame7
        local frame8 = Instance.new("Frame")
        frame8.Size = UDim2.new(0, 36, 0, 16)
        frame8.Position = UDim2.new(1, -42, 0.5, -8)
        local cu = ct
        if ct then
            cu = Color3.fromRGB(58, 120, 38)
        end
        if not cu then
            cu = Color3.fromRGB(70, 35, 18)
        end
        frame8.BackgroundColor3 = cu
        frame8.BorderSizePixel = 0
        frame8.ZIndex = 11
        frame8.Parent = frame7
        Instance.new("UICorner", frame8).CornerRadius = UDim.new(1, 0)
        cu = Instance.new("Frame")
        cu.Size = UDim2.new(0, 12, 0, 12)
        local cv = ct
        if ct then
            cv = UDim2.new(1, -14, 0.5, -6)
        end
        if not cv then
            cv = UDim2.new(0, 2, 0.5, -6)
        end
        cu.Position = cv
        cu.BackgroundColor3 = Color3.fromRGB(255, 240, 180)
        cu.BorderSizePixel = 0
        cu.ZIndex = 12
        cu.Parent = frame8
        Instance.new("UICorner", cu).CornerRadius = UDim.new(1, 0)
        cv = Instance.new("TextButton")
        cv.Size = UDim2.new(1, 0, 1, 0)
        cv.BackgroundTransparency = 1
        cv.Text = ""
        cv.ZIndex = 13
        cv.Parent = frame7
        return cv, frame8, cu, textLabel4
    end
    local k
    local function updateState3()
        k.Text = tostring(ab) .. " / 6"
    end
    local l
    local function updateState4(...)
        ab = math.max(1, ab - 1)
        l()
        ah(select(2, ...))
    end
    local function updateState5(...)
        ab = math.min(6, ab + 1)
        l()
        ah(select(2, ...))
    end
    local m
    local function getValue()
        local text2 = m.Text
        local cs = tonumber(string.gsub(text2, "%D", ""))
        local ct = cs
        if cs then
            ct = 0 < cs
        end
        if ct then
            ac = cs
        end
        m.Text = "Ping max: " .. tostring(ac)
        ah()
        return cs
    end
    local function updateState6(cs, ct, cu)
        local cv = cu
        if cu then
            cv = Color3.fromRGB(58, 120, 38)
        end
        if not cv then
            cv = Color3.fromRGB(70, 35, 18)
        end
        cs.BackgroundColor3 = cv
        cv = cu
        if cu then
            cv = UDim2.new(1, -14, 0.5, -6)
        end
        if not cv then
            cv = UDim2.new(0, 2, 0.5, -6)
        end
        ct.Position = cv
    end
    local n, o, p
    local function updateState7(...)
        ad = not ad
        p(n, o, ad)
        ah(select(2, ...))
    end
    local q, r
    local function updateState8(...)
        ae = not ae
        p(q, r, ae)
        ah(select(2, ...))
    end
    local s, t, u
    local function updateState9()
        ba = not ba
        if ba then
            t.Visible = false
            u.Visible = true
            s.TextColor3 = Color3.fromRGB(255, 255, 200)
        else
            t.Visible = true
            u.Visible = false
            s.TextColor3 = Color3.fromRGB(255, 240, 120)
        end
    end
    local v
    local function performTask()
        v()
    end
    local w, x
    local function updateState10(cs)
        local viewportSize = workspace.CurrentCamera.ViewportSize
        local ct = 200
        local cu = 264
        if 10 < x.AbsoluteSize.X then
            ct = x.AbsoluteSize.X
        end
        if 10 < x.AbsoluteSize.Y then
            cu = x.AbsoluteSize.Y
        end
        local x = viewportSize.X
        local cv = cs.X.Scale * x + cs.X.Offset
        local y = viewportSize.Y
        local cw = cs.Y.Scale * y + cs.Y.Offset
        if cv < 0 then
            cv = 0
        end
        if cw < 0 then
            cw = 0
        end
        if viewportSize.X - ct < cv then
            cv = math.max(0, viewportSize.X - ct)
        end
        if viewportSize.Y - cu < cw then
            cw = math.max(0, viewportSize.Y - cu)
        end
        x.Position = UDim2.new(0, cv, 0, cw)
        w.Position = UDim2.new(0, cv + 2, 0, cw + 4)
        ag.guiXScale = 0
        ag.guiXOffset = cv
        ag.guiYScale = 0
        ag.guiYOffset = cw
    end
    local y, z, bf
    local function connectEvents(cs)
        local function onChanged()
            local cu = Enum.UserInputState.End
            if cs.UserInputState == cu then
                y = false
                ah()
            end
        end
        local mouseButton1 = Enum.UserInputType.MouseButton1
        local ct = cs.UserInputType == mouseButton1
        if not ct then
            mouseButton1 = Enum.UserInputType.Touch
            ct = cs.UserInputType == mouseButton1
        end
        if ct then
            y = true
            z = cs.Position
            bf = x.Position
            cs.Changed:Connect(onChanged)
            return cs
        else
            return
        end
    end
    local bg
    local function transformValue(cs)
        local ct, scale, cu, scale2, cv
        if y then
            ct = cs.Position - z
            scale = bf.X.Scale
            cv = ct.X
            cu = bf.X.Offset + cv
            scale2 = bf.Y.Scale
            cv = bf.Y.Offset + ct.Y
            bg(UDim2.new(scale, cu, scale2, cv))
            return cs, ct
        end
    end
    local bh
    local function setText(text)
        bh.Text = text
    end
    local bi
    local function updateState11()
        local function backgroundTask5()
            bi.Text = "Reset visitados"
        end
        bc = {
            [game.JobId] = true,
        }
        bi.Text = "Visitados limpios"
        task.delay(1.2, backgroundTask5)
    end
    local function processItems()
        local map = workspace:FindFirstChild("Map")
        local wildPetSpawns, cs, ct, cu, cv, cw, cx, cy, cz, da
        if map then
            wildPetSpawns = map:FindFirstChild("WildPetSpawns")
            if wildPetSpawns then
                wildPetSpawns:GetChildren()
                cs = ipairs(ct)
                da = table.pack(cs(ct, 0))
                cv = table.unpack(da, 1, da.n)
                if not (cv == nil) then
                    cu = cv
                    local __brk2 = false
                    while true do
                        cw = ipairs(bd)
                        cx = bd
                        da = table.pack(cw(cx, 0))
                        cz = table.unpack(da, 1, da.n)
                        if cz == nil then
                            da = table.pack(cs(ct, cu))
                            cv = table.unpack(da, 1, da.n)
                            if cv ~= nil then
                                cu = cv
                                continue
                            end
                            return nil
                        end
                        cy = cz
                        while true do
                            if not string:match("^" .. table.unpack(da, 2, da.n)) then
                                da = table.pack(cw(cx, cy))
                                cz = table.unpack(da, 1, da.n)
                                if cz == nil then
                                    break
                                end
                                cy = cz
                                continue
                            end
                            __brk2 = true
                            break
                        end
                        if __brk2 then
                            break
                        end
                        da = table.pack(cs(ct, cu))
                        cv = table.unpack(da, 1, da.n)
                        if cv ~= nil then
                            cu = cv
                            continue
                        end
                        return nil
                    end
                    return nil
                end
            end
        end
        return nil
    end
    local function updateState12(cs)
        local humanoidRootPart = (ak.Character or ak.CharacterAdded:Wait()):FindFirstChild("HumanoidRootPart")
        local ct = humanoidRootPart
        if humanoidRootPart then
            ct = cs:IsA("BasePart")
        end
        if ct then
            humanoidRootPart.CFrame = cs.CFrame + Vector3.new(0, 5, 0)
        end
    end
    local function httpGet4(cs)
        local function httpGet()
            return game:HttpGet(cs)
        end
        local ok, err = pcall(httpGet)
        if ok and err then
            return err
        else
            return nil
        end
    end
    local bj
    local function runProtected(cs, ct)
        local cu
        local function tryOperation2()
            return aa:JSONDecode(cu)
        end
        cu = "http"
        cu = bj("http" .. az .. "/servers/Public?sortOrder=" .. ct .. "&limit=100&excludeFullGames=true" .. (cs ~= "" and "&cursor=" .. cs or ""))
        if cu then
            local ok, err = pcall(tryOperation2)
            if ok then
                return err
            end
        end
        return nil
    end
    local bk
    local function processItems2(cs, ct)
        local function checkCondition(dr, ds)
            local dt = tonumber(dr.playing) or 0
            return (tonumber(ds.playing) or 0) < dt
        end
        local cu = tonumber(cs) or 1
        local cv, cw
        if cu <= 1 then
            cv = 1
            cw = 1
        elseif cu == 2 then
            cv = 2
            cw = 3
        else
            cv = 5
            cw = 6
        end
        local cx
        local cy = ipairs(cx)
        local cz = cx
        local da = table.pack(cy(cz, 0))
        local db = table.unpack(da, 1, da.n)
        local dc = {}
        local dd, de, df, dg, dh, di, dj, dk, dl, dm, dn, dp, dq, id
        if not (db == nil) then
            df = db
            while true do
                de = 0
                dg = table.unpack(da, 2, da.n)
                dh = bk("", dg)
                if not dh or not dh.data then
                    da = table.pack(cy(cz, df))
                    db = table.unpack(da, 1, da.n)
                    if not (db == nil) then
                        df = db
                        continue
                    end
                else
                    dj = dh.data
                    table.pack(ipairs(dj))
                    while true do
                        di = ipairs(dj)
                        da = table.pack(di(dj, 0))
                        dl = table.unpack(da, 1, da.n)
                        if not (dl == nil) then
                            dk = dl
                            while true do
                                dn = tonumber(table.unpack(da, 2, da.n).playing) or 0
                                dp = not ad or not bc[table.unpack(da, 2, da.n).id]
                                dq = true
                                if af ~= "Any" and type(table.unpack(da, 2, da.n).region) == "string" then
                                    dq = string.lower(table.unpack(da, 2, da.n).region) == string.lower(af)
                                end
                                id = table.unpack(da, 2, da.n).id
                                if id ~= game.JobId and cv <= dn and dn <= cw and dp and dq then
                                    dc[table.unpack(da, 2, da.n).id] = table.unpack(da, 2, da.n)
                                end
                                da = table.pack(di(dj, dk))
                                dl = table.unpack(da, 1, da.n)
                                if dl ~= nil then
                                    dk = dl
                                    continue
                                end
                                break
                            end
                        end
                        dd = dh.nextPageCursor or ""
                        de = de + 1
                        di = 0
                        dj = next
                        dk = dc
                        dm = next(dc, nil)
                        if not (dm == nil) then
                            dl = dm
                            while true do
                                di = di + 1
                                dm = dj(dk, dl)
                                if dm ~= nil then
                                    dl = dm
                                    continue
                                end
                                break
                            end
                        end
                        if 40 > di then
                            if not (dd == "" or ct <= de) then
                                dh = bk(dd, dg)
                                if not (not dh or not dh.data) then
                                    continue
                                end
                                break
                            end
                            break
                        end
                        break
                    end
                    da = table.pack(cy(cz, df))
                    db = table.unpack(da, 1, da.n)
                    if db == nil then
                        break
                    else
                        df = db
                        continue
                    end
                end
                break
            end
        end
        cy = {}
        cz = next
        df = dc
        table.pack(next(dc, nil))
        dg = next(dc)
        if not (dg == nil) then
            db = dg
            table.insert(cy, dc)
            while true do
                da = table.pack(cz(df, db))
                dg = next(dc)
                if dg ~= nil then
                    db = dg
                    table.insert(cy, table.unpack(da, 2, da.n))
                    continue
                end
                break
            end
        end
        table.sort(cy, checkCondition)
        return cy
    end
    local bl
    local function getValue2()
        local cs = bl(6, 8)
        if #cs == 0 then
            local ct = {}
            bc = ct
            ct[game.JobId] = true
            cs = bl(6, 8)
        end
        return cs
    end
    local bm
    local function computeValue2(cs, ...)
        if not (not cs or not cs.id) then
            if ag.savedKey ~= "" then
                at(al(ag.savedKey))
            end
            bc[cs.id] = true
            bm("Hopping â " .. tostring(cs.playing or 1))
            task.wait(select(2, ...))
            aw:TeleportToPlaceInstance(az, cs.id, ak)
            return cs
        end
    end
    local bn
    local function processItems3(cs)
        local ct = tonumber(cs) or ab
        cs = ct
        bn = bl(ct, 3)
        if #bn == 0 then
            local da = {}
            bc = da
            da[game.JobId] = true
            bn = bl(cs, 3)
        end
        local cu = {}
        ct = cu
        local cv = ipairs(bn)
        local cw = bn
        local cx = table.pack(cv(cw, 0))
        local cy = table.unpack(cx, 1, cx.n)
        local cz
        if not (cy == nil) then
            cz = cy
            while true do
                if (tonumber(cx[2].playing) or 0) == cs then
                    table.insert(ct, table.unpack(cx, 2, cx.n))
                    cx = table.pack(cv(cw, cz))
                    cy = table.unpack(cx, 1, cx.n)
                    if not (cy == nil) then
                        cz = cy
                        continue
                    end
                else
                    cx = table.pack(cv(cw, cz))
                    cy = table.unpack(cx, 1, cx.n)
                    if cy == nil then
                        break
                    else
                        cz = cy
                        continue
                    end
                end
                break
            end
        end
        if 0 < #cu then
            return ct[math.random(1, #ct)]
        else
            return nil
        end
    end
    local function transformValue2(cs)
        return cs[math.random(1, #cs)]
    end
    local bo
    local function performTask2()
        bm("Logs (" .. tostring(bo) .. ")")
    end
    local function runProtected2()
        local function createSound()
            local sound
            local function backgroundTask4()
                sound:Destroy()
            end
            sound = Instance.new("Sound")
            sound.SoundId = "rbxassetid://6895079853"
            sound.Volume = 0.35
            sound.Parent = h
            sound:Play()
            task.delay(1.2, backgroundTask4)
        end
        if ae then
            pcall(createSound)
            return
        end
    end
    local bp
    local function processItems4(cs, ct)
        local cu, cv, cw, cx, cy
        if not (not cs or not ct or ct == "") then
            string.match(ct, "^Player%d+$")
            string.match(ct, "^Guest%d+$")
            cu = ipairs(bp)
            cv = bp
            cy = table.pack(cu(cv, 0))
            cx = table.unpack(cy, 1, cy.n)
            if not (cx == nil) then
                cw = cx
                while true do
                    if cy[2].id ~= cs then
                        cy = table.pack(cu(cv, cw))
                        cx = table.unpack(cy, 1, cy.n)
                        if cx ~= nil then
                            cw = cx
                            continue
                        end
                        break
                    end
                    return cs, ct, cu, cv, cw, table.unpack(cy, 1, cy.n)
                end
            end
            table.insert(bp, {id = cs, name = ct})
            return cs, ct
        end
    end
    local bq
    local function runProtected3(cs)
        local ct
        local function tryOperation()
            return aa:JSONDecode(ct)
        end
        ct = bj
        ct = ct("httpsearch?keyword=" .. aa:UrlEncode(cs) .. "&limit=25")
        local cu, cv, cw, cx, cy, cz, da, db, dc, dd, de
        if ct then
            de = table.pack(pcall(tryOperation))
            cu = table.unpack(de, 1, de.n)
            if not cu then
                return cs, ct, table.unpack(de, 1, de.n)
            else
                cv = table.unpack(de, 2, de.n)
                cw = type(cv.data) ~= "table"
                cw = ipairs(cx)
                cz = cw(cx, 0)
                if cz == nil then
                    cy = cz
                else
                    cy = cz
                    while true do
                        db = table.unpack(de, 2, de.n).id and table.unpack(de, 2, de.n).name
                        repeat
                            local shouldExit = false
                            while true do
                                if db then
                                    db = bq
                                    dc = cx.id
                                    dd = table.unpack(de, 2, de.n).name
                                    if dd then
                                        db(dc, dd)
                                        shouldExit = true
                                        break
                                    end
                                    dd = table.unpack(de, 2, de.n).displayName
                                    db = bq
                                    dc = cx.id
                                    da = table.unpack(de, 2, de.n)
                                    dd = da.name
                                    if dd then
                                        db(dc, dd)
                                        cz = ipairs(cx)
                                        if cz == nil then
                                            return cs, ct, cu, cv, cw, cx, cz, table.unpack(de, 1, de.n)
                                        else
                                            cy = cz
                                            goto __x23
                                        end
                                    end
                                    break
                                end
                                cz = ipairs(cx)
                                if not (cz ~= nil) then
                                    return cs, ct, cu, cv, cw, cx, cz, table.unpack(de, 1, de.n)
                                end
                                ::__x23::
                                db = table.unpack(de, 2, de.n).id and table.unpack(de, 2, de.n).name
                            end
                            if shouldExit then
                                break
                            end
                            dd = table.unpack(de, 2, de.n).displayName
                        until true
                        cz = ipairs(cx)
                        if cz == nil then
                            return cs, ct, cu, cv, cw, cx, cz, table.unpack(de, 1, de.n)
                        else
                            cy = cz
                            if table.unpack(de, 2, de.n).id then
                                db = table.unpack(de, 2, de.n).name
                            end
                        end
                    end
                end
            end
        end
        return cs, ct, cu, cv, cw, cx, cy, table.unpack(de, 1, de.n)
    end
    local br
    local function processItems5()
        local cs
        while true do
            if 0 < #bp then
                cs = table.remove(bp, 1)
                if not (cs and not br[cs.id]) then
                    continue
                end
                br[cs.id] = true
                return cs
            end
            break
        end
        while true do
            if 0 < #bp then
                cs = table.remove(bp, 1)
                if not (cs and not br[cs.id]) then
                    continue
                end
                br[cs.id] = true
                return cs
            end
            break
        end
        cs = math.random(400000000, 2500000000)
        br[cs] = true
        local ct = {id = cs}
        ct.name = "User" .. tostring(cs):sub(-5)
        return ct
    end
    local bs
    local function processItems6(cs)
        local ct, cu, cv, cw, cx, cy
        if not (not cs or not cs.Parent) then
            ay:Create(cs, bs, {BackgroundTransparency = 1}):Play()
            cs:GetDescendants()
            ct = ipairs(cu)
            cy = table.pack(ct(cu, 0))
            cw = table.unpack(cy, 1, cy.n)
            if not (cw == nil) then
                cv = cw
                local cz = {}
                while true do
                    if cy[2]:IsA("TextLabel") or cy[2]:IsA("TextButton") then
                        cx = table.unpack(cy, 2, cy.n)
                        cz.TextTransparency = 1
                        cz.BackgroundTransparency = 1
                        ay:Create(cx, bs, cz):Play()
                        cy = table.pack(ct(cu, cv))
                        cw = table.unpack(cy, 1, cy.n)
                        if not (cw == nil) then
                            cv = cw
                            continue
                        end
                    else
                        table.unpack(cy, 2, cy.n):IsA("ImageLabel")
                        table.unpack(cy, 2, cy.n):IsA("UIStroke")
                        cy = table.pack(ct(cu, cv))
                        cw = table.unpack(cy, 1, cy.n)
                        if cw == nil then
                            break
                        else
                            cv = cw
                            continue
                        end
                    end
                    task.wait(0.32)
                    return cs
                end
            end
            task.wait(0.32)
            return cs
        end
    end
    local function getValue3()
        local cs = ab
        if math.random(1, 10) <= 6 then
            return cs
        else
            return math.max(1, cs - 1)
        end
    end
    local bt, bu, bv, bw, bx, by, tweenInfo, bz, ca, cb, cc, cd, ce
    local function computeValue3(...)
        local cs, ct
        local function onMouseButton1Click()
            local cw = bw(ct)
            if cw then
                bt(cs, ct)
                task.wait(0.5)
                bv(cw)
                return cw
            else
                return
            end
        end
        local cu
        local function backgroundTask3(...)
            local function backgroundTask2()
                if bo < by then
                    ce()
                end
            end
            cc(cu)
            bo = math.max(0, bo - 1)
            bz(select(2, ...))
            task.delay(math.random(2, 6), backgroundTask2)
        end
        cs = cb()
        cu = 1
        bx = bx + 1
        bo = bo + 1
        ct = bz
        bz()
        ct = cd(select(2, ...))
        cu = Instance.new("Frame")
        cu.Size = UDim2.new(1, 0, 0, 0)
        cu.BackgroundColor3 = Color3.fromRGB(90, 50, 22)
        cu.BackgroundTransparency = 1
        cu.BorderSizePixel = 0
        cu.ClipsDescendants = true
        cu.LayoutOrder = bx
        cu.ZIndex = 6
        cu.Parent = bu
        Instance.new("UICorner", cu).CornerRadius = UDim.new(0, 6)
        local imageLabel = Instance.new("ImageLabel")
        imageLabel.Size = UDim2.new(0, 26, 0, 26)
        imageLabel.Position = UDim2.new(0, 4, 0.5, -13)
        imageLabel.BackgroundColor3 = Color3.fromRGB(40, 22, 8)
        imageLabel.BackgroundTransparency = 1
        imageLabel.BorderSizePixel = 0
        imageLabel.ImageTransparency = 1
        imageLabel.Image = "rbxthumb://type=AvatarHeadShot&id=" .. tostring(cs.id) .. "&w=48&h=48"
        imageLabel.ZIndex = 7
        imageLabel.Parent = cu
        Instance.new("UICorner", imageLabel).CornerRadius = UDim.new(1, 0)
        local textLabel5 = Instance.new("TextLabel")
        textLabel5.Size = UDim2.new(1, -78, 0, 14)
        textLabel5.Position = UDim2.new(0, 34, 0, 3)
        textLabel5.BackgroundTransparency = 1
        textLabel5.TextTransparency = 1
        textLabel5.Text = cs.name
        textLabel5.Font = Enum.Font.GothamBold
        textLabel5.TextSize = 10
        textLabel5.TextColor3 = Color3.fromRGB(255, 240, 180)
        textLabel5.TextXAlignment = Enum.TextXAlignment.Left
        textLabel5.TextTruncate = Enum.TextTruncate.AtEnd
        textLabel5.ZIndex = 7
        textLabel5.Parent = cu
        local textLabel6 = Instance.new("TextLabel")
        textLabel6.Size = UDim2.new(1, -78, 0, 12)
        textLabel6.Position = UDim2.new(0, 34, 0, 18)
        textLabel6.BackgroundTransparency = 1
        textLabel6.TextTransparency = 1
        textLabel6.Text = tostring(ct) .. "/7"
        textLabel6.Font = Enum.Font.Gotham
        textLabel6.TextSize = 10
        textLabel6.TextColor3 = Color3.fromRGB(230, 210, 150)
        textLabel6.TextXAlignment = Enum.TextXAlignment.Left
        textLabel6.ZIndex = 7
        textLabel6.Parent = cu
        local textButton2 = Instance.new("TextButton")
        textButton2.Size = UDim2.new(0, 38, 0, 22)
        textButton2.Position = UDim2.new(1, -42, 0.5, -11)
        textButton2.BackgroundColor3 = Color3.fromRGB(55, 155, 50)
        textButton2.BackgroundTransparency = 1
        textButton2.BorderSizePixel = 0
        textButton2.TextTransparency = 1
        textButton2.Text = "Join"
        textButton2.TextColor3 = Color3.fromRGB(255, 255, 255)
        textButton2.Font = Enum.Font.GothamBold
        textButton2.TextSize = 10
        textButton2.ZIndex = 7
        textButton2.Parent = cu
        Instance.new("UICorner", textButton2).CornerRadius = UDim.new(0, 5)
        local uIStroke5 = Instance.new("UIStroke", textButton2)
        uIStroke5.Color = Color3.fromRGB(25, 90, 22)
        uIStroke5.Thickness = 1
        uIStroke5.Transparency = 1
        local cv = {}
        cv.Size = UDim2.new(1, 0, 0, 36)
        cv.BackgroundTransparency = 0
        ay:Create(cu, tweenInfo, cv):Play()
        ay:Create(imageLabel, tweenInfo, {ImageTransparency = 0, BackgroundTransparency = 0}):Play()
        ay:Create(textLabel5, tweenInfo, {TextTransparency = 0}):Play()
        ay:Create(textLabel6, tweenInfo, {TextTransparency = 0}):Play()
        ay:Create(textButton2, tweenInfo, {TextTransparency = 0, BackgroundTransparency = 0}):Play()
        ay:Create(uIStroke5, tweenInfo, {Transparency = 0}):Play()
        ca(select(2, ...))
        textButton2.MouseButton1Click:Connect(onMouseButton1Click)
        task.delay(math.random(4, 12), backgroundTask3)
        return cs, ct, cu, imageLabel, textLabel5, textLabel6, textButton2, uIStroke5
    end
    local cf, cg, ch
    local function performTask3()
        local function backgroundTask(...)
            bm("Conectando al Servidor")
            task.wait(3)
            bm("Recibiendo datos de la WebSocker")
            task.wait(3)
            local cs = ipairs({
                [1] = "alex",
                [2] = "noah",
                [3] = "luna",
                [4] = "kai",
                [5] = "danna",
                [6] = "bruno",
                [7] = "vale",
                [8] = "enzo",
            })
            local ct = {
                [1] = "alex",
                [2] = "noah",
                [3] = "luna",
                [4] = "kai",
                [5] = "danna",
                [6] = "bruno",
                [7] = "vale",
                [8] = "enzo",
            }
            local cu = table.pack(cs(ct, 0))
            local cv = table.unpack(cu, 1, cu.n)
            local cw
            if not (cv == nil) then
                cw = cv
                while true do
                    ch(table.unpack(cu, 2, cu.n))
                    task.wait(select(2, ...))
                    cu = table.pack(cs(ct, cw))
                    cv = table.unpack(cu, 1, cu.n)
                    if cv ~= nil then
                        cw = cv
                        continue
                    end
                    break
                end
            end
            cs = #bp
            local cx
            if 2 <= cs then
                while true do
                    cv = math.random(1, cs)
                    cx = bp[cs]
                    bp[cs] = bp[cv]
                    bp[cv] = cx
                    cs = cs + -1
                    if 2 > cs + -1 then
                        break
                    end
                end
            end
            bn = cf()
            ct = {[1] = 16, [2] = 20, [3] = 24}
            by = cg(ct)
            bz()
            cs = 1
            if 1 <= by then
                ce(select(5, ...))
                task.wait(0.2)
                while true do
                    cs = cs + 1
                    if cs + 1 <= ct then
                        ce(select(2, ...))
                        task.wait(0.2)
                        continue
                    end
                    break
                end
                return cs, ct, 1
            else
                return
            end
        end
        task.spawn(backgroundTask)
    end
    local ci, cj
    local function backgroundTask8(...)
        local cs, ct
        if ao then
            cs = 1
            if bb then
                ct = ci()
                if ct then
                    bm("Found: " .. ct.Name)
                    local __cont2 = false
                    while true do
                        task.wait(select(2, ...))
                        cj(ct)
                        bb = false
                        ct = task.wait
                        ct(2)
                        while true do
                            ct = ao
                            if ct then
                                cs = cs + 1
                                if 8 > cs then
                                    ct = ci(select(2, ...))
                                    bm("Found: " .. ct.Name)
                                    __cont2 = true
                                    break
                                end
                                ct = at(al(ag.savedKey))
                                be(ct)
                                return 0, ct
                            else
                                break
                            end
                        end
                        if __cont2 then
                            __cont2 = false
                            continue
                        end
                        break
                    end
                    return cs
                end
            end
            task.wait(2)
            while true do
                if ao then
                    cs = cs + 1
                    if 8 > cs then
                        ct = ci(select(2, ...))
                        bm("Found: " .. ct.Name)
                        task.wait(select(2, ...))
                        cj(ct)
                        bb = false
                        task.wait(2)
                        continue
                    end
                    ct = at(al(ag.savedKey))
                    be(ct)
                    return 0, ct
                else
                    break
                end
            end
            return cs
        else
            return
        end
    end
    h = ao
    local ck, cl, cm, frame4, uIListLayout, uIPadding, scrollingFrame, uIPadding2, frame5, textLabel2, frame6, textLabel3, cn, co, textButton, uIStroke2, cp, uIStroke3, cq, cr
    if not h then
        ao = true
        w = "ScreenGui"
        h = Instance.new("ScreenGui")
        h.Name = "CeboScriptsEmpyServer"
        h.ResetOnSpawn = false
        h.IgnoreGuiInset = true
        x = Enum.ZIndexBehavior
        h.ZIndexBehavior = x.Sibling
        x = ak
        h.Parent = ak:WaitForChild("PlayerGui")
        x = "Frame"
        w = Instance.new("Frame")
        bh = 268
        w.Size = UDim2.new(0, 204, 0, 268)
        ck = ag.guiXScale
        cl = ag.guiXOffset + 2
        cm = ag.guiYScale
        s = 4
        bh = ag.guiYOffset + 4
        w.Position = UDim2.new(ck, cl, cm, bh)
        w.BackgroundColor3 = Color3.fromRGB(20, 10, 4)
        w.BackgroundTransparency = 0.5
        w.BorderSizePixel = 0
        w.ZIndex = 1
        w.Parent = h
        x = Instance.new("UICorner", w)
        x.CornerRadius = UDim.new(0, 14)
        x = Instance.new("Frame")
        bh = 0
        s = 264
        x.Size = UDim2.new(0, 200, 0, 264)
        cl = ag.guiXScale
        cm = ag.guiXOffset
        bh = ag.guiYScale
        s = ag.guiYOffset
        x.Position = UDim2.new(cl, cm, bh, s)
        bh = 8
        x.BackgroundColor3 = Color3.fromRGB(55, 28, 8)
        x.BorderSizePixel = 0
        x.Active = true
        x.ZIndex = 2
        x.Parent = h
        bh = 12
        Instance.new("UICorner", x).CornerRadius = UDim.new(0, 12)
        ck = Instance.new("UIStroke", x)
        bh = 14
        s = 3
        ck.Color = Color3.fromRGB(30, 14, 3)
        ck.Thickness = 2
        cl = Instance.new("TextButton")
        bh = 1
        s = -8
        a = 0
        b = 30
        cl.Size = UDim2.new(1, -8, 0, 30)
        bh = 0
        s = 4
        a = 0
        b = 4
        cl.Position = UDim2.new(0, 4, 0, 4)
        bh = 58
        s = 120
        a = 38
        cl.BackgroundColor3 = Color3.fromRGB(58, 120, 38)
        cl.BorderSizePixel = 0
        cl.Text = ""
        cl.AutoButtonColor = false
        cl.Active = true
        cl.ZIndex = 3
        cl.Parent = x
        bh = "UICorner"
        s = cl
        s = 0
        a = 9
        Instance.new("UICorner", cl).CornerRadius = UDim.new(0, 9)
        bh = "TextLabel"
        cm = Instance.new("TextLabel")
        s = 1
        a = -8
        b = 1
        c = 0
        cm.Size = UDim2.new(1, -8, 1, 0)
        s = 0
        a = 4
        b = 0
        c = 0
        cm.Position = UDim2.new(0, 4, 0, 0)
        cm.BackgroundTransparency = 1
        cm.Text = "Server Finder"
        s = Enum.Font
        cm.Font = s.GothamBold
        cm.TextSize = 13
        s = 255
        a = 240
        b = 120
        bh = Color3.fromRGB(255, 240, 120)
        cm.TextColor3 = bh
        a = Enum
        s = Enum.TextXAlignment
        cm.TextXAlignment = s.Center
        cm.ZIndex = 4
        cm.Parent = cl
        s = "TextLabel"
        bh = Instance.new("TextLabel")
        a = 1
        b = -34
        c = 0
        d = 16
        bh.Size = UDim2.new(1, -34, 0, 16)
        a = 0
        b = 8
        c = 0
        d = 34
        bh.Position = UDim2.new(0, 8, 0, 34)
        bh.BackgroundTransparency = 1
        bh.Text = "Conectando al Servidor"
        a = Enum.Font
        bh.Font = a.Gotham
        bh.TextSize = 10
        a = 255
        b = 230
        c = 140
        s = Color3.fromRGB(255, 230, 140)
        bh.TextColor3 = s
        b = Enum
        a = Enum.TextXAlignment
        bh.TextXAlignment = a.Left
        bh.ZIndex = 6
        bh.Parent = x
        a = "TextButton"
        s = Instance.new("TextButton")
        b = 0
        c = 18
        d = 0
        e = 16
        s.Size = UDim2.new(0, 18, 0, 16)
        b = 1
        c = -26
        d = 0
        e = 34
        s.Position = UDim2.new(1, -26, 0, 34)
        s.BackgroundTransparency = 1
        s.Text = "â"
        s.TextSize = 12
        b = Enum.Font
        a = b.GothamBold
        s.Font = a
        b = 255
        c = 240
        d = 120
        s.TextColor3 = Color3.fromRGB(255, 240, 120)
        s.ZIndex = 7
        s.Parent = x
        b = "Frame"
        a = Instance.new("Frame")
        c = 0
        d = 298
        e = 0
        f = 86
        a.Size = UDim2.new(0, 298, 0, 86)
        c = 0.5
        d = -149
        e = 0
        f = 80
        a.Position = UDim2.new(0.5, -149, 0, 80)
        c = 10
        d = 5
        e = 2
        a.BackgroundColor3 = Color3.fromRGB(10, 5, 2)
        a.BackgroundTransparency = 1
        a.BorderSizePixel = 0
        a.ZIndex = 20
        a.Parent = h
        c = "UICorner"
        d = a
        b = Instance.new("UICorner", a)
        d = 0
        e = 16
        b.CornerRadius = UDim.new(0, 16)
        c = "Frame"
        b = Instance.new("Frame")
        d = 0
        e = 294
        f = 0
        g = 82
        b.Size = UDim2.new(0, 294, 0, 82)
        d = 0.5
        e = -147
        f = 0
        g = 78
        b.Position = UDim2.new(0.5, -147, 0, 78)
        d = 55
        e = 28
        f = 8
        b.BackgroundColor3 = Color3.fromRGB(55, 28, 8)
        b.BackgroundTransparency = 1
        b.BorderSizePixel = 0
        b.ZIndex = 21
        b.Parent = h
        d = "UICorner"
        e = b
        c = Instance.new("UICorner", b)
        e = 0
        f = 14
        c.CornerRadius = UDim.new(0, 14)
        d = "UIStroke"
        e = b
        c = Instance.new("UIStroke", b)
        e = 30
        f = 14
        g = 3
        c.Color = Color3.fromRGB(30, 14, 3)
        c.Thickness = 3
        c.Transparency = 1
        e = "ImageLabel"
        d = Instance.new("ImageLabel")
        f = 0
        g = 48
        i = 0
        j = 48
        d.Size = UDim2.new(0, 48, 0, 48)
        f = 0
        g = 14
        i = 0.5
        j = -24
        d.Position = UDim2.new(0, 14, 0.5, -24)
        f = 40
        g = 22
        i = 8
        d.BackgroundColor3 = Color3.fromRGB(40, 22, 8)
        d.BackgroundTransparency = 1
        d.BorderSizePixel = 0
        d.ImageTransparency = 1
        d.ZIndex = 22
        d.Parent = b
        f = "UICorner"
        g = d
        e = Instance.new("UICorner", d)
        g = 1
        i = 0
        e.CornerRadius = UDim.new(1, 0)
        f = "TextLabel"
        e = Instance.new("TextLabel")
        g = 1
        i = -80
        j = 0
        bt = 20
        e.Size = UDim2.new(1, -80, 0, 20)
        g = 0
        i = 72
        j = 0
        bt = 8
        e.Position = UDim2.new(0, 72, 0, 8)
        e.BackgroundTransparency = 1
        e.TextTransparency = 1
        e.Text = "Server Finder"
        g = Enum.Font
        e.Font = g.GothamBold
        e.TextSize = 14
        g = 255
        i = 230
        j = 100
        f = Color3.fromRGB(255, 230, 100)
        e.TextColor3 = f
        i = Enum
        g = Enum.TextXAlignment
        e.TextXAlignment = g.Left
        e.ZIndex = 22
        e.Parent = b
        g = "TextLabel"
        f = Instance.new("TextLabel")
        i = 1
        j = -80
        bt = 0
        t = 18
        f.Size = UDim2.new(1, -80, 0, 18)
        i = 0
        j = 72
        bt = 0
        t = 28
        f.Position = UDim2.new(0, 72, 0, 28)
        f.BackgroundTransparency = 1
        f.TextTransparency = 1
        f.Text = ""
        i = Enum.Font
        f.Font = i.GothamBold
        f.TextSize = 13
        i = 255
        j = 240
        bt = 180
        g = Color3.fromRGB(255, 240, 180)
        f.TextColor3 = g
        j = Enum
        i = Enum.TextXAlignment
        g = i.Left
        f.TextXAlignment = g
        i = Enum.TextTruncate
        f.TextTruncate = i.AtEnd
        f.ZIndex = 22
        f.Parent = b
        i = "TextLabel"
        g = Instance.new("TextLabel")
        j = 1
        bt = -80
        t = 0
        g.Size = UDim2.new(1, -80, 0, 16)
        j = 0
        bt = 72
        t = 0
        g.Position = UDim2.new(0, 72, 0, 48)
        g.BackgroundTransparency = 1
        g.TextTransparency = 1
        g.Text = ""
        j = Enum.Font
        g.Font = j.Gotham
        g.TextSize = 12
        j = 235
        bt = 220
        t = 200
        i = Color3.fromRGB(235, 220, 200)
        g.TextColor3 = i
        j = Enum.TextXAlignment
        g.TextXAlignment = j.Left
        g.ZIndex = 22
        g.Parent = b
        i = nil
        j = updateState
        bt = httpGet3
        t = Instance.new("Frame")
        bu = 1
        u = -54
        t.Size = UDim2.new(1, -8, 1, -54)
        bu = 0
        u = 50
        t.Position = UDim2.new(0, 4, 0, 50)
        bu = 148
        t.BackgroundColor3 = Color3.fromRGB(148, 88, 36)
        t.BorderSizePixel = 0
        t.ZIndex = 3
        t.Parent = x
        bu = "UICorner"
        Instance.new("UICorner", t).CornerRadius = UDim.new(0, 9)
        bu = "Frame"
        frame4 = Instance.new("Frame")
        u = 1
        frame4.Size = UDim2.new(1, -6, 1, -6)
        u = 0
        frame4.Position = UDim2.new(0, 3, 0, 3)
        u = 50
        frame4.BackgroundColor3 = Color3.fromRGB(170, 108, 50)
        frame4.BorderSizePixel = 0
        frame4.ZIndex = 4
        frame4.Parent = t
        bu = Instance.new("UICorner", frame4)
        u = 8
        bu.CornerRadius = UDim.new(0, 8)
        frame4.ClipsDescendants = true
        bu = Instance.new("ScrollingFrame")
        u = -6
        bu.Size = UDim2.new(1, -6, 1, -6)
        u = 3
        bu.Position = UDim2.new(0, 3, 0, 3)
        u = 72
        bu.BackgroundColor3 = Color3.fromRGB(120, 72, 32)
        bu.BackgroundTransparency = 0.15
        bu.BorderSizePixel = 0
        bu.ScrollBarThickness = 4
        u = 230
        bu.ScrollBarImageColor3 = Color3.fromRGB(255, 230, 120)
        u = 0
        bu.CanvasSize = UDim2.new(0, 0, 0, 0)
        bu.AutomaticCanvasSize = Enum.AutomaticSize.Y
        bu.ZIndex = 5
        bu.Parent = frame4
        u = bu
        u = 0
        Instance.new("UICorner", bu).CornerRadius = UDim.new(0, 7)
        uIListLayout = Instance.new("UIListLayout")
        u = 0
        uIListLayout.Padding = UDim.new(0, 4)
        u = Enum.SortOrder
        uIListLayout.SortOrder = u.LayoutOrder
        uIListLayout.Parent = bu
        u = "UIPadding"
        uIPadding = Instance.new("UIPadding")
        uIPadding.PaddingTop = UDim.new(0, 4)
        uIPadding.PaddingBottom = UDim.new(0, 4)
        uIPadding.PaddingLeft = UDim.new(0, 4)
        u = UDim.new(0, 4)
        uIPadding.PaddingRight = u
        uIPadding.Parent = bu
        u = Instance.new("Frame")
        u.Size = UDim2.new(1, -8, 1, -54)
        u.Position = UDim2.new(0, 4, 0, 50)
        u.BackgroundColor3 = Color3.fromRGB(148, 88, 36)
        u.BorderSizePixel = 0
        u.Visible = false
        u.ZIndex = 8
        u.Parent = x
        Instance.new("UICorner", u).CornerRadius = UDim.new(0, 9)
        scrollingFrame = Instance.new("ScrollingFrame")
        scrollingFrame.Size = UDim2.new(1, -6, 1, -6)
        scrollingFrame.Position = UDim2.new(0, 3, 0, 3)
        scrollingFrame.BackgroundColor3 = Color3.fromRGB(170, 108, 50)
        scrollingFrame.BorderSizePixel = 0
        scrollingFrame.ScrollBarThickness = 4
        scrollingFrame.ScrollBarImageColor3 = Color3.fromRGB(255, 230, 120)
        scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 260)
        scrollingFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
        scrollingFrame.ZIndex = 9
        scrollingFrame.Parent = u
        Instance.new("UICorner", scrollingFrame).CornerRadius = UDim.new(0, 8)
        uIPadding2 = Instance.new("UIPadding")
        uIPadding2.PaddingBottom = UDim.new(0, 10)
        uIPadding2.Parent = scrollingFrame
        frame5 = Instance.new("Frame")
        frame5.Size = UDim2.new(1, -10, 0, 22)
        frame5.Position = UDim2.new(0, 5, 0, 5)
        frame5.BackgroundColor3 = Color3.fromRGB(58, 120, 38)
        frame5.BorderSizePixel = 0
        frame5.ZIndex = 10
        frame5.Parent = scrollingFrame
        Instance.new("UICorner", frame5).CornerRadius = UDim.new(0, 6)
        textLabel2 = Instance.new("TextLabel")
        textLabel2.Size = UDim2.new(1, -6, 1, 0)
        textLabel2.Position = UDim2.new(0, 4, 0, 0)
        textLabel2.BackgroundTransparency = 1
        textLabel2.Text = "Sala de Filtro"
        textLabel2.Font = Enum.Font.GothamBold
        textLabel2.TextSize = 11
        textLabel2.TextColor3 = Color3.fromRGB(255, 240, 120)
        textLabel2.TextXAlignment = Enum.TextXAlignment.Left
        textLabel2.ZIndex = 11
        textLabel2.Parent = frame5
        frame6 = Instance.new("Frame")
        k = 48
        frame6.Size = UDim2.new(1, -10, 0, 48)
        k = 30
        frame6.Position = UDim2.new(0, 5, 0, 30)
        frame6.BackgroundColor3 = Color3.fromRGB(120, 72, 32)
        frame6.BorderSizePixel = 0
        frame6.ZIndex = 10
        frame6.Parent = scrollingFrame
        Instance.new("UICorner", frame6).CornerRadius = UDim.new(0, 7)
        textLabel3 = Instance.new("TextLabel")
        k = 0
        textLabel3.Size = UDim2.new(1, -8, 0, 14)
        k = 0
        textLabel3.Position = UDim2.new(0, 4, 0, 2)
        textLabel3.BackgroundTransparency = 1
        textLabel3.Text = "Jugadores base (1-6)"
        textLabel3.Font = Enum.Font.Gotham
        textLabel3.TextSize = 9
        k = 160
        textLabel3.TextColor3 = Color3.fromRGB(255, 230, 160)
        textLabel3.TextXAlignment = Enum.TextXAlignment.Left
        textLabel3.ZIndex = 11
        textLabel3.Parent = frame6
        cn = updateState2
        co = computeValue
        k = "TextButton"
        textButton = Instance.new("TextButton")
        m = 0
        textButton.Size = UDim2.new(0, 26, 0, 20)
        m = 0
        textButton.Position = UDim2.new(0, 6, 0, 22)
        textButton.BorderSizePixel = 0
        textButton.Text = "â"
        m = 255
        textButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        textButton.Font = Enum.Font.GothamBold
        textButton.TextSize = 14
        textButton.ZIndex = 11
        textButton.Parent = frame6
        k = cn
        m = 140
        cp = Color3.fromRGB(140, 48, 28)
        n = 10
        m = Color3.fromRGB(90, 20, 10)
        k(textButton, cp, m)
        k = Instance.new("TextLabel")
        m = 54
        k.Size = UDim2.new(0, 54, 0, 20)
        m = 36
        k.Position = UDim2.new(0, 36, 0, 22)
        m = 22
        k.BackgroundColor3 = Color3.fromRGB(45, 22, 8)
        k.BorderSizePixel = 0
        m = ab
        m = " / 6"
        k.Text = tostring(ab) .. " / 6"
        m = 240
        k.TextColor3 = Color3.fromRGB(255, 240, 180)
        k.Font = Enum.Font.GothamBold
        k.TextSize = 11
        k.ZIndex = 11
        k.Parent = frame6
        m = k
        m = 0
        Instance.new("UICorner", k).CornerRadius = UDim.new(0, 7)
        m = k
        uIStroke2 = Instance.new("UIStroke", k)
        m = 255
        uIStroke2.Color = Color3.fromRGB(255, 210, 90)
        uIStroke2.Thickness = 1
        m = "TextButton"
        cp = Instance.new("TextButton")
        n = 0
        o = 20
        cp.Size = UDim2.new(0, 26, 0, 20)
        n = 0
        o = 22
        cp.Position = UDim2.new(0, 94, 0, 22)
        cp.BorderSizePixel = 0
        cp.Text = "+"
        n = 255
        cp.TextColor3 = Color3.fromRGB(255, 255, 255)
        cp.Font = Enum.Font.GothamBold
        cp.TextSize = 14
        cp.ZIndex = 11
        cp.Parent = frame6
        m = cn
        n = 58
        o = 130
        cq = Color3.fromRGB(58, 130, 40)
        o = 30
        q = 20
        n = Color3.fromRGB(30, 80, 20)
        m(cp, cq, n)
        m = Instance.new("TextBox")
        n = -10
        o = 0
        m.Size = UDim2.new(1, -10, 0, 22)
        n = 5
        o = 0
        m.Position = UDim2.new(0, 5, 0, 82)
        n = 50
        o = 22
        m.BackgroundColor3 = Color3.fromRGB(90, 50, 22)
        m.BorderSizePixel = 0
        o = ac
        m.Text = "Ping max: " .. tostring(ac)
        n = 240
        o = 180
        m.TextColor3 = Color3.fromRGB(255, 240, 180)
        m.Font = Enum.Font.Gotham
        m.TextSize = 10
        m.ClearTextOnFocus = true
        m.ZIndex = 10
        m.Parent = scrollingFrame
        n = m
        n = 0
        o = 7
        Instance.new("UICorner", m).CornerRadius = UDim.new(0, 7)
        n = m
        uIStroke3 = Instance.new("UIStroke", m)
        n = 80
        o = 40
        uIStroke3.Color = Color3.fromRGB(80, 40, 12)
        uIStroke3.Thickness = 1.2
        cq = co
        n = scrollingFrame
        o = 108
        q = ad
        table.pack(co(scrollingFrame, 108, "No visitados", ad))
        cr = co
        q = scrollingFrame
        r = 135
        bi = "Sonido"
        l = ae
        table.pack(co(scrollingFrame, 135, "Sonido", ae))
        l = "TextButton"
        bi = Instance.new("TextButton")
        p = 1
        v = -10
        y = 0
        z = 22
        bi.Size = UDim2.new(1, -10, 0, 22)
        p = 0
        v = 5
        y = 0
        z = 162
        bi.Position = UDim2.new(0, 5, 0, 162)
        bi.BorderSizePixel = 0
        bi.Text = "Reset visitados"
        p = 255
        v = 245
        y = 220
        l = Color3.fromRGB(255, 245, 220)
        bi.TextColor3 = l
        p = Enum.Font
        bi.Font = p.GothamBold
        bi.TextSize = 10
        bi.ZIndex = 10
        bi.Parent = scrollingFrame
        l = cn
        p = bi
        y = 150
        z = 78
        bf = 32
        v = Color3.fromRGB(150, 78, 32)
        z = 90
        bf = 40
        bg = 12
        y = Color3.fromRGB(90, 40, 12)
        l(p, v, y)
        l = updateState3
        p = textButton.MouseButton1Click
        y = updateState4
        v = p
        p = p.Connect
        p(v, updateState4)
        p = cp.MouseButton1Click
        y = updateState5
        v = p
        p = p.Connect
        p(v, updateState5)
        p = m.FocusLost
        y = getValue
        v = p
        p = p.Connect
        p(v, getValue)
        p = updateState6
        v = cq.MouseButton1Click
        z = updateState7
        y = v
        v = v.Connect
        v(y, updateState7)
        v = cr.MouseButton1Click
        z = updateState8
        y = v
        v = v.Connect
        v(y, updateState8)
        v = updateState9
        y = s.MouseButton1Click
        bf = performTask
        z = y
        y = y.Connect
        y(z, performTask)
        y = false
        z = nil
        bf = nil
        bg = updateState10
        bm = updateState10
        ci = x.Position
        updateState10(ci)
        bm = cl.InputBegan
        cj = connectEvents
        ci = bm
        bm = bm.Connect
        bm(ci, connectEvents)
        bm = ax.InputChanged
        cj = transformValue
        ci = bm
        bm = bm.Connect
        bm(ci, transformValue)
        bm = setText
        bc[game.JobId] = true
        ci = bi.MouseButton1Click
        bj = updateState11
        cj = ci
        ci = ci.Connect
        ci(cj, updateState11)
        ci = processItems
        cj = updateState12
        bj = httpGet4
        bk = runProtected
        bl = processItems2
        bn = {}
        cf = getValue2
        bv = computeValue2
        bw = processItems3
        bx = 0
        bp = {}
        br = {}
        bo = 0
        by = 18
        bs = 0.28
        ca = Enum
        cg = Enum.EasingStyle.Quad
        bz = Enum.EasingDirection.Out
        tweenInfo = TweenInfo.new(0.28, cg, bz)
        cg = 0.32
        bq = ca
        ca = ca.EasingStyle
        bz = ca.Quad
        ca = ca.EasingDirection.In
        bs = TweenInfo.new(0.32, bz, ca)
        cg = transformValue2
        bz = performTask2
        ca = runProtected2
        bq = processItems4
        ch = runProtected3
        cb = processItems5
        cc = processItems6
        cd = getValue3
        ce = computeValue3
        performTask3(select(28, ...))
        task.spawn(backgroundTask8)
        return h, w, x, ck, cl, cm, bh, s, a, b, c, d, e, f, g, nil, j, bt, t, frame4, bu, uIListLayout, uIPadding, u, scrollingFrame, uIPadding2, frame5, textLabel2, frame6, textLabel3, cn, co, textButton, k, uIStroke2, cp, m, uIStroke3, cq, n, 108, cr, q, 135, bi, l, p, v, false, nil, nil, bg, bm, ci, cj, bj, bk, bl, bn, cf, bv, bw, 0, bp, br, 0, 18, tweenInfo, bs, cg, bz, ca, bq, ch, cb, cc, cd, ce, performTask3
    end
end
aa = "Players"
aw = game
local players = game:GetService("Players")
ax = "TeleportService"
aa = game
aw = game:GetService("TeleportService")
ay = "HttpService"
ax = game
aa = game:GetService("HttpService")
ak = "UserInputService"
ay = game
ax = game:GetService("UserInputService")
az = "TweenService"
ak = game
ay = game:GetService("TweenService")
ak = players.LocalPlayer
az = game.PlaceId
ab = 107778070777162
if az == 107778070777162 then
    ab = 3
    ba = false
    ac = 200
    ad = true
    ae = true
    af = "Any"
    bb = true
    bc = {}
    ao = "WildPet_Racoon_WildPet_"
    bd = {
        [1] = "WildPet_Unicorn_WildPet_",
        [2] = "WildPet_GoldenDragonfly_WildPet_",
        [3] = "WildPet_Dragonfly_WildPet_",
        [4] = "WildPet_Racoon_WildPet_",
    }
    ag = {
        filterPlayers = 3,
        maxPing = 200,
        onlyUnvisited = true,
        soundEnabled = true,
        regionFilter = "Any",
        guiXScale = 0,
        guiXOffset = 14,
        guiYScale = 0,
        guiYOffset = 72,
        authorized = false,
        savedKey = "",
    }
    ah = saveConfig
    an = runProtected5
    runProtected5()
    an = false
    ao = false
    ap = nil
    aq = nil
    ai = transformValue3
    aj = httpGet8
    al = runProtected6
    at = transformValue4
    am = runProtected7
    au = transformValue5
    av = checkCondition2
    ar = clearChildren
    as = updateState13
    be = computeValue4
    checkCondition3(select(17, ...))
    pcall(clearChildren2)
    aq = createScreenGui
    if not ag.authorized then
        aq("")
    end
    ap = createScreenGui2
    createScreenGui2(select(2, ...))
    return
end

-- httpAwGHNh7Z7T
--]] 
