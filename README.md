pcall(function() game.Players.LocalPlayer.Kick = function() end end)
local Player = game:GetService("Players").LocalPlayer
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local Camera = workspace.CurrentCamera
local TweenService = game:GetService("TweenService")
local Debris = game:GetService("Debris")
local isMobile = UserInputService.TouchEnabled
local scaleMultiplier = isMobile and 0.8 or 1
local buttonHeight = isMobile and 50 or 40
local fontSize = isMobile and 16 or 14
local notificationsEnabled = true

-- Dark theme colors
local COLOR_BG = Color3.fromRGB(20, 24, 28)
local COLOR_PANEL = Color3.fromRGB(28, 33, 38)
local COLOR_ACCENT = Color3.fromRGB(56, 139, 253)
local COLOR_TEXT = Color3.fromRGB(230, 235, 240)
local COLOR_SUB = Color3.fromRGB(170, 175, 180)

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "StoppedMenu"
screenGui.Parent = Player:WaitForChild("PlayerGui")
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.ResetOnSpawn = false

local reopenButton = nil
local lastReopenButtonPosition = nil

local mainContainer = Instance.new("Frame")
mainContainer.Name = "MainContainer"
mainContainer.Parent = screenGui
mainContainer.BackgroundColor3 = COLOR_BG
mainContainer.Position = UDim2.new(0.5, 0, 0.5, 0)
mainContainer.Size = UDim2.new(0, 820 * scaleMultiplier, 0, 520 * scaleMultiplier)
mainContainer.AnchorPoint = Vector2.new(0.5, 0.5)
mainContainer.ClipsDescendants = true
Instance.new("UICorner", mainContainer).CornerRadius = UDim.new(0, 10)

local mainStroke = Instance.new("UIStroke")
mainStroke.Parent = mainContainer
mainStroke.Color = COLOR_ACCENT
mainStroke.Thickness = 2
mainStroke.Transparency = 0.65

local dragFrame = Instance.new("Frame")
dragFrame.Name = "DragFrame"
dragFrame.Parent = mainContainer
dragFrame.BackgroundTransparency = 1
dragFrame.Size = UDim2.new(1, 0, 0, 64 * scaleMultiplier)

-- Left sidebar (tabs)
local tabContainer = Instance.new("Frame")
tabContainer.Name = "TabContainer"
tabContainer.Parent = mainContainer
tabContainer.BackgroundColor3 = Color3.fromRGB(18, 22, 26)
tabContainer.BorderSizePixel = 0
tabContainer.Position = UDim2.new(0, 12, 0, 84)
tabContainer.Size = UDim2.new(0, 180 * scaleMultiplier, 0, 380 * scaleMultiplier)
Instance.new("UICorner", tabContainer).CornerRadius = UDim.new(0, 8)

local tabLayout = Instance.new("UIListLayout")
tabLayout.Parent = tabContainer
tabLayout.SortOrder = Enum.SortOrder.LayoutOrder
tabLayout.Padding = UDim.new(0, 8)
tabLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left

-- Header
local header = Instance.new("Frame")
header.Parent = mainContainer
header.BackgroundColor3 = Color3.fromRGB(16, 20, 24)
header.Position = UDim2.new(0, 12, 0, 12)
header.Size = UDim2.new(0, 796 * scaleMultiplier, 0, 76 * scaleMultiplier)
Instance.new("UICorner", header).CornerRadius = UDim.new(0, 8)

local logo = Instance.new("ImageButton")
logo.Parent = header
logo.BackgroundTransparency = 1
logo.Position = UDim2.new(0.01, 0, 0.06, 0)
logo.Size = UDim2.new(0, 64 * scaleMultiplier, 0, 64 * scaleMultiplier)
logo.Image = "rbxassetid://117130789916072"
logo.ScaleType = Enum.ScaleType.Fit

local title = Instance.new("TextLabel")
title.Parent = header
title.BackgroundTransparency = 1
title.Position = UDim2.new(0.12, 0, 0.18, 0)
title.Size = UDim2.new(0, 480 * scaleMultiplier, 0, 48 * scaleMultiplier)
title.Font = Enum.Font.GothamBold
title.Text = "STOPPED MENU"
title.TextColor3 = COLOR_TEXT
title.TextSize = 20
title.TextXAlignment = Enum.TextXAlignment.Left

local tabName = Instance.new("TextLabel")
tabName.Parent = header
tabName.BackgroundTransparency = 1
tabName.Position = UDim2.new(0.55, 0, 0.18, 0)
tabName.Size = UDim2.new(0, 220 * scaleMultiplier, 0, 48 * scaleMultiplier)
tabName.Font = Enum.Font.GothamBold
tabName.Text = "Inicio"
tabName.TextColor3 = COLOR_TEXT
tabName.TextSize = 16
tabName.TextXAlignment = Enum.TextXAlignment.Left

-- small icon moved to the right so it doesn't overlap with the words
local titleIconSmall = Instance.new("ImageLabel")
titleIconSmall.Parent = header
titleIconSmall.BackgroundTransparency = 1
titleIconSmall.Position = UDim2.new(0.90, 0, 0.18, 0)
titleIconSmall.AnchorPoint = Vector2.new(0.5, 0.5)
titleIconSmall.Size = UDim2.new(0, 36 * scaleMultiplier, 0, 36 * scaleMultiplier)
titleIconSmall.Image = "rbxassetid://117130789916072"
titleIconSmall.ScaleType = Enum.ScaleType.Fit
titleIconSmall.ImageColor3 = COLOR_ACCENT

local contentFrame = Instance.new("Frame")
contentFrame.Parent = mainContainer
contentFrame.BackgroundColor3 = COLOR_PANEL
contentFrame.Position = UDim2.new(0, 204 * scaleMultiplier, 0, 84)
contentFrame.Size = UDim2.new(0, 588 * scaleMultiplier, 0, 380 * scaleMultiplier)
Instance.new("UICorner", contentFrame).CornerRadius = UDim.new(0, 8)

local contentLayout = Instance.new("UIListLayout")
contentLayout.Parent = contentFrame
contentLayout.SortOrder = Enum.SortOrder.LayoutOrder
contentLayout.Padding = UDim.new(0, 10)

-- Notification container
local notifContainer = Instance.new("Frame")
notifContainer.Name = "NotificationContainer"
notifContainer.Parent = screenGui
notifContainer.BackgroundTransparency = 1
notifContainer.Position = UDim2.new(1, -360, 0, 20)
notifContainer.Size = UDim2.new(0, 340, 1, -40)
notifContainer.ZIndex = 1000

local notifLayout = Instance.new("UIListLayout")
notifLayout.Parent = notifContainer
notifLayout.SortOrder = Enum.SortOrder.LayoutOrder
notifLayout.Padding = UDim.new(0, 10)
notifLayout.HorizontalAlignment = Enum.HorizontalAlignment.Right

local function notify(titleText, text, duration)
    if not notificationsEnabled then return end
    duration = duration or 3
    local notifFrame = Instance.new("Frame")
    notifFrame.Parent = notifContainer
    notifFrame.BackgroundColor3 = Color3.fromRGB(22, 26, 30)
    notifFrame.BorderSizePixel = 0
    notifFrame.Size = UDim2.new(1, 0, 0, 92)
    notifFrame.ClipsDescendants = true
    notifFrame.Position = UDim2.new(1, 50, 0, 0)
    notifFrame.ZIndex = 1000
    Instance.new("UICorner", notifFrame).CornerRadius = UDim.new(0, 10)

    local bar = Instance.new("Frame")
    bar.Parent = notifFrame
    bar.BackgroundColor3 = COLOR_ACCENT
    bar.Size = UDim2.new(0, 6, 1, 0)
    bar.Position = UDim2.new(0, 8, 0, 0)
    bar.ZIndex = 1001
    Instance.new("UICorner", bar).CornerRadius = UDim.new(0, 6)

    local iconFrame = Instance.new("Frame")
    iconFrame.Parent = notifFrame
    iconFrame.BackgroundColor3 = COLOR_ACCENT
    iconFrame.Position = UDim2.new(0, 22, 0, 14)
    iconFrame.Size = UDim2.new(0, 44, 0, 44)
    iconFrame.ZIndex = 1001
    Instance.new("UICorner", iconFrame).CornerRadius = UDim.new(0, 8)

    local iconLabel = Instance.new("TextLabel")
    iconLabel.Parent = iconFrame
    iconLabel.BackgroundTransparency = 1
    iconLabel.Size = UDim2.new(1, 0, 1, 0)
    iconLabel.Font = Enum.Font.GothamBold
    iconLabel.Text = "🧊"
    iconLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    iconLabel.TextSize = 24

    local tituloLabel = Instance.new("TextLabel")
    tituloLabel.Parent = notifFrame
    tituloLabel.BackgroundTransparency = 1
    tituloLabel.Position = UDim2.new(0, 84, 0, 14)
    tituloLabel.Size = UDim2.new(1, -100, 0, 22)
    tituloLabel.Font = Enum.Font.GothamBold
    tituloLabel.Text = titleText
    tituloLabel.TextColor3 = COLOR_TEXT
    tituloLabel.TextSize = 15
    tituloLabel.TextXAlignment = Enum.TextXAlignment.Left

    local textoLabel = Instance.new("TextLabel")
    textoLabel.Parent = notifFrame
    textoLabel.BackgroundTransparency = 1
    textoLabel.Position = UDim2.new(0, 84, 0, 36)
    textoLabel.Size = UDim2.new(1, -100, 0, 44)
    textoLabel.Font = Enum.Font.Gotham
    textoLabel.Text = text
    textoLabel.TextColor3 = COLOR_SUB
    textoLabel.TextSize = 13
    textoLabel.TextXAlignment = Enum.TextXAlignment.Left
    textoLabel.TextYAlignment = Enum.TextYAlignment.Top
    textoLabel.TextWrapped = true

    notifFrame:TweenPosition(UDim2.new(1, -340, 0, 0), Enum.EasingDirection.Out, Enum.EasingStyle.Back, 0.45, true)
    task.delay(duration, function()
        notifFrame:TweenPosition(UDim2.new(1, 50, 0, 0), Enum.EasingDirection.In, Enum.EasingStyle.Quad, 0.35, true, function()
            notifFrame:Destroy()
        end)
    end)
end

-- Helpers
local options = {}
local toggleStates = {}
local currentTab = "Inicio"

local function newPanelRow(height)
    local row = Instance.new("Frame")
    row.Parent = contentFrame
    row.BackgroundColor3 = Color3.fromRGB(24, 28, 32)
    row.Size = UDim2.new(1, -24, 0, height or buttonHeight)
    row.Position = UDim2.new(0, 12, 0, 0)
    row.BorderSizePixel = 0
    Instance.new("UICorner", row).CornerRadius = UDim.new(0, 8)
    return row
end

-- Slider
local function createSlider(name, min, max, callback)
    local sliderFrame = newPanelRow(70 * scaleMultiplier)
    local label = Instance.new("TextLabel")
    label.Parent = sliderFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 6)
    label.Size = UDim2.new(0.6, 0, 0, 20)
    label.Font = Enum.Font.Gotham
    label.Text = name
    label.TextColor3 = COLOR_TEXT
    label.TextSize = fontSize

    local valueLabel = Instance.new("TextLabel")
    valueLabel.Parent = sliderFrame
    valueLabel.BackgroundTransparency = 1
    valueLabel.Position = UDim2.new(0.72, 0, 0, 6)
    valueLabel.Size = UDim2.new(0.25, 0, 0, 20)
    valueLabel.Font = Enum.Font.GothamBold
    valueLabel.Text = tostring(min)
    valueLabel.TextColor3 = COLOR_ACCENT
    valueLabel.TextSize = fontSize

    local sliderBack = Instance.new("Frame")
    sliderBack.Parent = sliderFrame
    sliderBack.BackgroundColor3 = Color3.fromRGB(40, 44, 50)
    sliderBack.Position = UDim2.new(0.05, 0, 0.55, 0)
    sliderBack.Size = UDim2.new(0.9, 0, 0, 8 * scaleMultiplier)
    Instance.new("UICorner", sliderBack).CornerRadius = UDim.new(1, 0)

    local sliderFill = Instance.new("Frame")
    sliderFill.Parent = sliderBack
    sliderFill.BackgroundColor3 = COLOR_ACCENT
    sliderFill.Size = UDim2.new(0, 0, 1, 0)
    Instance.new("UICorner", sliderFill).CornerRadius = UDim.new(1, 0)

    local sliderButton = Instance.new("ImageButton")
    sliderButton.Parent = sliderBack
    sliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    sliderButton.Image = ""
    sliderButton.Position = UDim2.new(0, -8, 0.5, -8)
    sliderButton.Size = UDim2.new(0, 16 * scaleMultiplier, 0, 16 * scaleMultiplier)
    Instance.new("UICorner", sliderButton).CornerRadius = UDim.new(1, 0)

    local dragging = false
    local currentInput

    local function updateFromPosition(x)
        local absPos = sliderBack.AbsolutePosition.X
        local absSize = sliderBack.AbsoluteSize.X
        local relative = 0
        if absSize > 0 then
            relative = math.clamp((x - absPos) / absSize, 0, 1)
        end
        local value = math.floor(min + (max - min) * relative)
        sliderFill.Size = UDim2.new(relative, 0, 1, 0)
        sliderButton.Position = UDim2.new(relative, -8, 0.5, -8)
        valueLabel.Text = tostring(value)
        if callback then pcall(callback, value) end
    end

    sliderButton.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            currentInput = input
            if input.Position then updateFromPosition(input.Position.X) end
        end
    end)
    sliderBack.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            currentInput = input
            if input.Position then updateFromPosition(input.Position.X) end
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input == currentInput and input.Position then
            updateFromPosition(input.Position.X)
        elseif dragging and input.UserInputType == Enum.UserInputType.MouseMovement and input.Position then
            updateFromPosition(input.Position.X)
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input == currentInput then
            dragging = false
            currentInput = nil
        end
    end)

    table.insert(options, {frame = sliderFrame, name = name, tab = currentTab})
    return sliderFrame
end

-- Toggle
local function createToggle(name, callback)
    local toggleFrame = newPanelRow(buttonHeight)
    local label = Instance.new("TextLabel")
    label.Parent = toggleFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 0)
    label.Size = UDim2.new(0.7, 0, 1, 0)
    label.Font = Enum.Font.Gotham
    label.Text = name
    label.TextColor3 = COLOR_TEXT
    label.TextSize = fontSize
    label.TextXAlignment = Enum.TextXAlignment.Left

    local btn = Instance.new("TextButton")
    btn.Parent = toggleFrame
    btn.BackgroundColor3 = Color3.fromRGB(48, 52, 58)
    btn.Position = UDim2.new(0.78, 0, 0.12, 0)
    btn.Size = UDim2.new(0, 44 * scaleMultiplier, 0, 28 * scaleMultiplier)
    btn.Font = Enum.Font.GothamBold
    btn.Text = "OFF"
    btn.TextColor3 = COLOR_TEXT
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)

    local toggleKey = currentTab .. "_" .. name
    local state = toggleStates[toggleKey] or false
    if state then
        btn.BackgroundColor3 = COLOR_ACCENT
        btn.Text = "ON"
    end
    local enabled = state
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.BackgroundColor3 = enabled and COLOR_ACCENT or Color3.fromRGB(48, 52, 58)
        btn.Text = enabled and "ON" or "OFF"
        toggleStates[toggleKey] = enabled
        if callback then pcall(callback, enabled) end
    end)

    table.insert(options, {frame = toggleFrame, name = name, tab = currentTab})
    return toggleFrame, btn
end

-- Player dropdown
local function createPlayerDropdown(labelText, onSelect)
    local dropdownFrame = newPanelRow(50 * scaleMultiplier)
    local label = Instance.new("TextLabel")
    label.Parent = dropdownFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 6)
    label.Size = UDim2.new(0.6, 0, 0, 22)
    label.Font = Enum.Font.Gotham
    label.Text = labelText
    label.TextColor3 = COLOR_TEXT
    label.TextSize = fontSize

    local btn = Instance.new("TextButton")
    btn.Parent = dropdownFrame
    btn.BackgroundColor3 = Color3.fromRGB(38, 42, 48)
    btn.Position = UDim2.new(0.55, 0, 0.15, 0)
    btn.Size = UDim2.new(0.42, 0, 0, 30 * scaleMultiplier)
    btn.Font = Enum.Font.Gotham
    btn.Text = "Selecione"
    btn.TextColor3 = COLOR_SUB
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)

    local open = false
    local entries = {}

    local function clearEntries()
        for _, v in ipairs(entries) do
            if v and v:IsDescendantOf(dropdownFrame) then v:Destroy() end
        end
        entries = {}
    end

    local function buildEntries()
        clearEntries()
        local players = Players:GetPlayers()
        local idx = 0
        for _, p in ipairs(players) do
            if p ~= Player then
                idx = idx + 1
                local itemBtn = Instance.new("TextButton")
                itemBtn.Parent = dropdownFrame
                itemBtn.BackgroundColor3 = Color3.fromRGB(40, 44, 50)
                itemBtn.Position = UDim2.new(0.55, 0, 0, 50 + ((idx-1) * 34 * scaleMultiplier))
                itemBtn.Size = UDim2.new(0.42, 0, 0, 30 * scaleMultiplier)
                itemBtn.Font = Enum.Font.Gotham
                itemBtn.Text = p.Name
                itemBtn.TextColor3 = COLOR_TEXT
                itemBtn.TextSize = 12
                itemBtn.Visible = open
                Instance.new("UICorner", itemBtn).CornerRadius = UDim.new(0, 6)
                itemBtn.MouseButton1Click:Connect(function()
                    btn.Text = p.Name
                    open = false
                    dropdownFrame.Size = UDim2.new(1, 0, 0, 50 * scaleMultiplier)
                    for _, otherBtn in ipairs(dropdownFrame:GetChildren()) do
                        if otherBtn:IsA("TextButton") and otherBtn ~= btn then
                            otherBtn.Visible = false
                        end
                    end
                    if onSelect then pcall(onSelect, p) end
                end)
                table.insert(entries, itemBtn)
            end
        end
        if idx == 0 then
            local no = Instance.new("TextLabel")
            no.Parent = dropdownFrame
            no.BackgroundTransparency = 1
            no.Position = UDim2.new(0.55, 0, 0, 50)
            no.Size = UDim2.new(0.42, 0, 0, 30 * scaleMultiplier)
            no.Font = Enum.Font.Gotham
            no.Text = "Nenhum jogador"
            no.TextColor3 = COLOR_SUB
            no.TextSize = 12
            table.insert(entries, no)
        end
    end

    btn.MouseButton1Click:Connect(function()
        open = not open
        if open then
            buildEntries()
            dropdownFrame.Size = UDim2.new(1, 0, 0, 50 + (#entries * 34 * scaleMultiplier))
            for _, e in ipairs(entries) do
                if e:IsA("TextButton") then e.Visible = true end
            end
        else
            dropdownFrame.Size = UDim2.new(1, 0, 0, 50 * scaleMultiplier)
            for _, e in ipairs(entries) do
                if e:IsA("TextButton") then e.Visible = false end
            end
        end
    end)

    table.insert(options, {frame = dropdownFrame, name = labelText, tab = currentTab})
    return dropdownFrame, btn
end

local function makeDraggable(frame)
    local dragging, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
        end
    end)
    frame.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.Position then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end
makeDraggable(dragFrame)

-- Tab button creation (label placed to the right of icon to avoid overlap)
local function createTabButton(name, iconId)
    local button = Instance.new("TextButton")
    button.Parent = tabContainer
    button.BackgroundColor3 = Color3.fromRGB(22, 26, 30)
    button.BorderSizePixel = 0
    button.Size = UDim2.new(1, -24, 0, 36 * scaleMultiplier)
    button.Font = Enum.Font.Gotham
    button.Text = ""
    button.AutoButtonColor = false
    Instance.new("UICorner", button).CornerRadius = UDim.new(0, 6)

    local icon = Instance.new("ImageLabel")
    icon.Parent = button
    icon.BackgroundTransparency = 1
    icon.Position = UDim2.new(0.03, 0, 0.5, -10)
    icon.Size = UDim2.new(0, 20 * scaleMultiplier, 0, 20 * scaleMultiplier)
    icon.Image = iconId
    icon.ImageColor3 = COLOR_ACCENT
    icon.ScaleType = Enum.ScaleType.Fit

    local lbl = Instance.new("TextLabel")
    lbl.Parent = button
    lbl.BackgroundTransparency = 1
    lbl.Position = UDim2.new(0.14, 0, 0, 0)
    lbl.Size = UDim2.new(0.82, 0, 1, 0)
    lbl.Font = Enum.Font.Gotham
    lbl.Text = name
    lbl.TextColor3 = COLOR_TEXT
    lbl.TextSize = fontSize - 1
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.TextYAlignment = Enum.TextYAlignment.Center

    button.MouseButton1Click:Connect(function()
        currentTab = name
        tabName.Text = name
        for _, opt in ipairs(options) do
            opt.frame.Visible = (opt.tab == currentTab)
        end
        for _, child in ipairs(tabContainer:GetChildren()) do
            if child:IsA("TextButton") then
                child.BackgroundColor3 = Color3.fromRGB(22, 26, 30)
            end
        end
        button.BackgroundColor3 = Color3.fromRGB(34, 40, 46)
        notify("Tab", "Selected tab: " .. name, 1.2)
    end)

    return button
end

-- Player refs
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local HRP = Character:WaitForChild("HumanoidRootPart")
LocalPlayer.CharacterAdded:Connect(function()
    task.wait(0.5)
    Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    HRP = Character:WaitForChild("HumanoidRootPart")
end)
=
-- Nitro functions (unchanged)
local nitroForce = 50
local nitroConnection
local function getCurrentVehicleSeat()
    local character = Players.LocalPlayer.Character
    if not character or not character:FindFirstChild("Humanoid") then return nil end
    local humanoid = character.Humanoid
    if humanoid.SeatPart and humanoid.SeatPart:IsA("VehicleSeat") then
        return humanoid.SeatPart
    end
    return nil
end
local function getCarFromSeat(seat)
    if not seat then return nil end
    local car = seat.Parent
    while car and car ~= workspace do
        local seats, parts = 0, 0
        for _, child in pairs(car:GetChildren()) do
            if child:IsA("VehicleSeat") or child:IsA("Seat") then seats = seats + 1 end
            if child:IsA("BasePart") then parts = parts + 1 end
        end
        if seats >= 1 and parts >= 3 then return car end
        car = car.Parent
    end
    return seat.Parent
end
local function nitroMethod1(seat)
    local car = getCarFromSeat(seat)
    if not car then return false end
    local success = pcall(function()
        local mainPart, maxSize = nil, 0
        for _, part in pairs(car:GetChildren()) do
            if part:IsA("BasePart") and part ~= seat then
                local size = part.Size.Magnitude
                if size > maxSize then maxSize = size; mainPart = part end
            end
        end
        if not mainPart then mainPart = seat end
        for _, obj in pairs(mainPart:GetChildren()) do if obj:IsA("BodyVelocity") then obj:Destroy() end end
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(4000, 0, 4000)
        bv.Velocity = mainPart.CFrame.LookVector * nitroForce
        bv.Parent = mainPart
        Debris:AddItem(bv, 0.5)
    end)
    return success
end
local function nitroMethod2(seat)
    local car = getCarFromSeat(seat)
    if not car then return false end
    local success = pcall(function()
        for _, part in pairs(car:GetChildren()) do
            if part:IsA("BasePart") then
                local currentVel = part.AssemblyLinearVelocity
                part.AssemblyLinearVelocity = currentVel + (part.CFrame.LookVector * nitroForce)
            end
        end
    end)
    return success
end
local function nitroMethod3(seat)
    local success = pcall(function()
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(math.huge, 0, math.huge)
        bv.Velocity = seat.CFrame.LookVector * nitroForce
        bv.Parent = seat
        Debris:AddItem(bv, 0.3)
    end)
    return success
end
local function activateNitro()
    local seat = getCurrentVehicleSeat()
    if not seat then notify("Nitro", "Entre em um veículo primeiro!", 3); return end
    nitroMethod1(seat); task.wait(0.1); nitroMethod2(seat); task.wait(0.1); nitroMethod3(seat)
    notify("Nitro", "Nitro ativado!", 2)
end
local function toggleNitro(state)
    if state then
        if nitroConnection then
            if isMobile and nitroConnection:IsA("TextButton") then nitroConnection:Destroy() else nitroConnection:Disconnect() end
        end
        if isMobile then
            local nitroButton = Instance.new("TextButton")
            nitroButton.Name = "NitroButtonMobile"
            nitroButton.Parent = screenGui
            nitroButton.Size = UDim2.new(0, 80, 0, 80)
            nitroButton.Position = UDim2.new(1, -100, 1, -100)
            nitroButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            nitroButton.Text = "NITRO"
            nitroButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            nitroButton.TextSize = 16
            nitroButton.ZIndex = 1000
            Instance.new("UICorner", nitroButton).CornerRadius = UDim.new(0, 20)
            nitroButton.MouseButton1Click:Connect(function() activateNitro() end)
            nitroConnection = nitroButton
        else
            nitroConnection = UserInputService.InputBegan:Connect(function(input, gameProcessed)
                if gameProcessed then return end
                if input.KeyCode == Enum.KeyCode.N then activateNitro() end
            end)
        end
        notify("Nitro", "Nitro automático ativado!", 3)
    else
        if nitroConnection then
            if isMobile and nitroConnection:IsA("TextButton") then nitroConnection:Destroy() else nitroConnection:Disconnect() end
            nitroConnection = nil
        end
        notify("Nitro", "Nitro automático desativado!", 3)
    end
end

-- Teleport utility
local function teleportTo(x, y, z)
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    hrp.CFrame = CFrame.new(x, y, z)
end

local locations = {
    {nome = "Praça", coords = Vector3.new(78.81835174560547, 440.23663330078125, 73.40168762207031)},
    {nome = "Venda de Carros", coords = Vector3.new(72.61282348632812, 443.0106201171875, -83.07221984863281)},
    {nome = "Garagem", coords = Vector3.new(310.7814025878906, 440.0667724609375, 87.86703491210938)},
    {nome = "Ilegal de Rua", coords = Vector3.new(500.7127990722656, 440.6731262207031, 389.0328369140625)},
    {nome = "Mecânica", coords = Vector3.new(541.42626953125, 440.0038757324219, 587.8919677734375)},
    {nome = "Mercadinho", coords = Vector3.new(73.50846099853516, 440.34564208984375, 236.1553497314453)},
    {nome = "Banco", coords = Vector3.new(86.18805694580078, 440.23004150390625, 534.4178466796875)},
    {nome = "Prefeitura", coords = Vector3.new(-45.148475646972656, 442.5613708496094, 99.92353820800781)},
}

-- Aimbot / FOV
local FOV_RADIUS = 35
local AIM_PART = "Head"
local TEAM_CHECK = true
local AimbotActive = false

local fovCircle = Drawing.new("Circle")
fovCircle.Visible = false
fovCircle.Color = COLOR_ACCENT
fovCircle.Radius = FOV_RADIUS
fovCircle.Thickness = 2
fovCircle.Filled = false
fovCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

RunService.RenderStepped:Connect(function()
    fovCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    fovCircle.Visible = (AimbotActive or FixedAimEnabled) and true or false
end)

local function getClosestTarget()
    local closest, minDist = nil, FOV_RADIUS
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild(AIM_PART) then
            if not TEAM_CHECK or p.Team ~= LocalPlayer.Team then
                local part = p.Character[AIM_PART]
                local pos, onscreen = Camera:WorldToViewportPoint(part.Position)
                if onscreen then
                    local dist = (Vector2.new(pos.X, pos.Y) - fovCircle.Position).Magnitude
                    if dist < minDist then
                        minDist = dist
                        closest = part
                    end
                end
            end
        end
    end
    return closest
end

local function aimAt(part)
    if not part then return end
    local camPos = Camera.CFrame.Position
    local dir = (part.Position - camPos).Unit
    Camera.CFrame = CFrame.new(camPos, camPos + dir)
end

local aimbotConn
local function startAimbot()
    if aimbotConn then aimbotConn:Disconnect() end
    aimbotConn = RunService.RenderStepped:Connect(function()
        if AimbotActive then
            local target = getClosestTarget()
            if target then aimAt(target) end
        end
    end)
end
startAimbot()

-- FixedAim
local FixedAimEnabled = false
local FixedAimActive = false
local FixedAimSmoothing = 0.28
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if FixedAimEnabled and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
        FixedAimActive = true
    end
end)
UserInputService.InputEnded:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if FixedAimEnabled and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
        FixedAimActive = false
    end
end)
RunService.RenderStepped:Connect(function()
    if FixedAimEnabled and FixedAimActive then
        local target = getClosestTarget()
        if target then
            local camPos = Camera.CFrame.Position
            local desiredDir = (target.Position - camPos).Unit
            local currentLook = Camera.CFrame.LookVector
            local t = math.clamp(FixedAimSmoothing, 0, 1)
            local newDir = (currentLook:Lerp(desiredDir, t)).Unit
            Camera.CFrame = CFrame.new(camPos, camPos + newDir)
        end
    end
end)

-- ESP: fix box with life and create additional ESP elements (head dot + distance)
local espEnabled = false
local espBoxColor = Color3.fromRGB(255, 50, 50)
local espHealthColorFull = Color3.fromRGB(0, 255, 120)
local espHealthColorEmpty = Color3.fromRGB(255, 50, 50)
local espTextColor = COLOR_TEXT
local ESPs = {}

local function createESP(player)
    if player == LocalPlayer then return end
    local function OnCharacterAdded(character)
        if ESPs[player] then
            for _, d in pairs(ESPs[player]) do
                if d and d.Remove then
                    pcall(function() d:Remove() end)
                end
            end
        end
        -- box
        local box = Drawing.new("Square")
        box.Visible = false
        box.Filled = false
        box.Thickness = 2
        box.Color = espBoxColor
        -- health bar
        local healthBar = Drawing.new("Square")
        healthBar.Visible = false
        healthBar.Filled = true
        healthBar.Color = espHealthColorFull
        -- name
        local nameText = Drawing.new("Text")
        nameText.Visible = false
        nameText.Color = espTextColor
        nameText.Size = 16
        nameText.Center = true
        nameText.Outline = true
        nameText.Font = 2
        -- health text
        local healthText = Drawing.new("Text")
        healthText.Visible = false
        healthText.Color = espTextColor
        healthText.Size = 14
        healthText.Center = true
        healthText.Outline = true
        healthText.Font = 2
        -- head dot
        local headDot = Drawing.new("Circle")
        headDot.Visible = false
        headDot.Radius = 4
        headDot.Filled = true
        headDot.Color = COLOR_ACCENT
        -- distance text
        local distText = Drawing.new("Text")
        distText.Visible = false
        distText.Color = espTextColor
        distText.Size = 12
        distText.Center = true
        distText.Outline = true
        distText.Font = 2

        ESPs[player] = {
            box = box,
            healthBar = healthBar,
            nameText = nameText,
            healthText = healthText,
            headDot = headDot,
            distText = distText
        }
    end
    if player.Character then
        OnCharacterAdded(player.Character)
    end
    player.CharacterAdded:Connect(OnCharacterAdded)
end

local function updateESP()
    if not espEnabled then
        for _, draws in pairs(ESPs) do
            for _, d in pairs(draws) do
                d.Visible = false
            end
        end
        return
    end
    local localChar = LocalPlayer.Character
    local localRoot = localChar and localChar:FindFirstChild("HumanoidRootPart")
    for player, draws in pairs(ESPs) do
        local char = player.Character
        if char and char:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("Humanoid") then
            local humanoid = char:FindFirstChild("Humanoid")
            local root = char:FindFirstChild("HumanoidRootPart")
            local health = humanoid.Health
            local maxH = humanoid.MaxHealth
            local pct = (maxH > 0) and (health / maxH) or 0
            local dist = localRoot and (localRoot.Position - root.Position).Magnitude or math.huge
            local pos, onScreen = Camera:WorldToViewportPoint(root.Position)
            if onScreen and dist <= 300 then
                -- box
                local boxSize = math.clamp(1000 / math.max(dist,1), 10, 120)
                local boxH = boxSize * 1.5
                draws.box.Size = Vector2.new(boxSize, boxH)
                draws.box.Position = Vector2.new(pos.X - boxSize/2, pos.Y - boxH/2)
                draws.box.Visible = true
                -- health bar (left)
                local barX = pos.X - boxSize/2 - 6
                local barY = pos.Y - boxH/2
                local hbHeight = boxH * pct
                draws.healthBar.Size = Vector2.new(6, hbHeight)
                draws.healthBar.Position = Vector2.new(barX, barY + boxH * (1 - pct))
                draws.healthBar.Color = espHealthColorFull:lerp(espHealthColorEmpty, 1-pct)
                draws.healthBar.Visible = true
                -- name
                draws.nameText.Text = player.Name
                draws.nameText.Position = Vector2.new(pos.X, pos.Y - boxH/2 - 20)
                draws.nameText.Visible = true
                -- health text
                draws.healthText.Text = math.floor(health) .. "/" .. math.floor(maxH)
                draws.healthText.Position = Vector2.new(pos.X, pos.Y - boxH/2 - 38)
                draws.healthText.Visible = true
                -- head dot (use head if exists)
                local head = char:FindFirstChild("Head")
                if head then
                    local hpos, hOn = Camera:WorldToViewportPoint(head.Position)
                    if hOn then
                        draws.headDot.Position = Vector2.new(hpos.X, hpos.Y)
                        draws.headDot.Visible = true
                    else
                        draws.headDot.Visible = false
                    end
                else
                    draws.headDot.Visible = false
                end
                -- distance text below
                draws.distText.Text = string.format("%.0f m", dist)
                draws.distText.Position = Vector2.new(pos.X, pos.Y + boxH/2 + 6)
                draws.distText.Visible = true
            else
                for _, d in pairs(draws) do d.Visible = false end
            end
        else
            for _, d in pairs(draws) do d.Visible = false end
        end
    end
end

-- Tracer functionality
local tracerEnabled = false
local tracerColor = Color3.fromRGB(0, 240, 120)
local tracers = {}
local function createTracer(player)
    if player == LocalPlayer or tracers[player] then return end
    local line = Drawing.new("Line")
    line.Visible = false
    line.Color = tracerColor
    line.Thickness = 2
    tracers[player] = line
end
local function removeTracer(player)
    if tracers[player] then tracers[player]:Remove(); tracers[player] = nil end
end
local function updateTracers()
    if not tracerEnabled then
        for _, l in pairs(tracers) do l.Visible = false end; return
    end
    local center = Camera.ViewportSize/2
    for player, line in pairs(tracers) do
        local c = player.Character
        if c and c:FindFirstChild("HumanoidRootPart") then
            local pos, on = Camera:WorldToViewportPoint(c.HumanoidRootPart.Position)
            if on then line.From = Vector2.new(center.X, center.Y); line.To = Vector2.new(pos.X, pos.Y); line.Visible = true else line.Visible = false end
        else line.Visible = false end
    end
end

-- Initialize ESP/tracers
for _, p in ipairs(Players:GetPlayers()) do createESP(p); createTracer(p) end
Players.PlayerAdded:Connect(function(p) createESP(p); createTracer(p) end)
Players.PlayerRemoving:Connect(function(p) if ESPs[p] then for _,d in pairs(ESPs[p]) do d:Remove() end; ESPs[p] = nil end; removeTracer(p) end)

RunService.RenderStepped:Connect(updateESP)
RunService.RenderStepped:Connect(updateTracers)

-- Infinite ammo (preserved)
local infiniteAmmoFuzilConn; local infiniteAmmoFuzilConns = {}
local function toggleInfiniteAmmoFuzil(state)
    if state then
        local function checkValue(obj)
            if obj:IsA("IntValue") or obj:IsA("NumberValue") then
                if obj.Value == 30 then obj.Value = 999 end
                local conn = obj.Changed:Connect(function() if obj.Value == 30 then obj.Value = 999 end end)
                table.insert(infiniteAmmoFuzilConns, conn)
            end
        end
        for _, d in ipairs(workspace:GetDescendants()) do pcall(function() checkValue(d) end) end
        infiniteAmmoFuzilConn = workspace.DescendantAdded:Connect(function(obj) pcall(function() checkValue(obj) end) end)
        LocalPlayer.CharacterAdded:Connect(function() task.wait(1); for _, d in ipairs(workspace:GetDescendants()) do pcall(function() checkValue(d) end) end end)
        notify("Munição Infinita", "Munição infinita (Fuzil) ATIVADA!", 3)
    else
        if infiniteAmmoFuzilConn then infiniteAmmoFuzilConn:Disconnect(); infiniteAmmoFuzilConn = nil end
        for _, c in ipairs(infiniteAmmoFuzilConns) do c:Disconnect() end
        infiniteAmmoFuzilConns = {}
        notify("Munição Infinita", "Munição infinita (Fuzil) DESATIVADA!", 3)
    end
end

local infiniteAmmoOitaoConn; local infiniteAmmoOitaoConns = {}
local function toggleInfiniteAmmoOitao(state)
    if state then
        local function checkValue(obj)
            if obj:IsA("IntValue") or obj:IsA("NumberValue") then
                if obj.Value == 10 then obj.Value = 999 end
                local conn = obj.Changed:Connect(function() if obj.Value == 10 then obj.Value = 999 end end)
                table.insert(infiniteAmmoOitaoConns, conn)
            end
        end
        for _, d in ipairs(workspace:GetDescendants()) do pcall(function() checkValue(d) end) end
        infiniteAmmoOitaoConn = workspace.DescendantAdded:Connect(function(obj) pcall(function() checkValue(obj) end) end)
        LocalPlayer.CharacterAdded:Connect(function() task.wait(1); for _, d in ipairs(workspace:GetDescendants()) do pcall(function() checkValue(d) end) end end)
        notify("Munição Infinita", "Munição infinita (Oitão) ATIVADA!", 3)
    else
        if infiniteAmmoOitaoConn then infiniteAmmoOitaoConn:Disconnect(); infiniteAmmoOitaoConn = nil end
        for _, c in ipairs(infiniteAmmoOitaoConns) do c:Disconnect() end
        infiniteAmmoOitaoConns = {}
        notify("Munição Infinita", "Munição infinita (Oitão) DESATIVADA!", 3)
    end
end

-- Auto farm: updated "Farm Gari" to collect garbage automatically from workspace.Lixeiro
local autoFarmEnabled = false
local farmConnection = nil

-- Delay between teleporting to next trash (in seconds)
local TELEPORT_DELAY_SECONDS = 10

local function findTrashEntries()
    local list = {}
    local lixeiro = workspace:FindFirstChild("Lixeiro")
    if not lixeiro then
        for _, v in ipairs(workspace:GetChildren()) do
            if string.find(string.lower(v.Name), "lixeiro") then lixeiro = v; break end
        end
    end
    if not lixeiro then return list end

    local function isPromptMatch(prompt)
        if not prompt then return false end
        local lname = string.lower(prompt.Name or "")
        local action = string.lower(prompt.ActionText or "")
        if lname:find("peg") or lname:find("lixo") or action:find("peg") or action:find("lixo") then
            return true
        end
        return false
    end

    local function inspectModel(model)
        for _, desc in ipairs(model:GetDescendants()) do
            if desc:IsA("ProximityPrompt") then
                if isPromptMatch(desc) then
                    table.insert(list, {target = model, prompt = desc})
                    return
                end
            end
        end
        for _, child in ipairs(model:GetDescendants()) do
            if child:IsA("RemoteEvent") or child:IsA("RemoteFunction") then
                local n = string.lower(child.Name or "")
                if n:find("peg") or n:find("lixo") or n:find("pega") then
                    table.insert(list, {target = model, remote = child})
                    return
                end
            end
        end
        local remote = model:FindFirstChild("PegarLixo") or model:FindFirstChild("pegarLixo") or model:FindFirstChild("pegaLixo")
        if remote and (remote:IsA("RemoteEvent") or remote:IsA("RemoteFunction")) then
            table.insert(list, {target = model, remote = remote})
            return
        end
        local folder = model:FindFirstChild("lixo automatico")
        if folder and folder:IsA("Folder") then
            for _, desc in ipairs(folder:GetDescendants()) do
                if desc:IsA("ProximityPrompt") and isPromptMatch(desc) then
                    table.insert(list, {target = model, prompt = desc})
                    return
                elseif (desc:IsA("RemoteEvent") or desc:IsA("RemoteFunction")) and string.find(string.lower(desc.Name or ""), "pegar") then
                    table.insert(list, {target = model, remote = desc})
                    return
                end
            end
        end
    end

    for _, child in ipairs(lixeiro:GetChildren()) do
        if child:IsA("Model") or child:IsA("Folder") or child:IsA("BasePart") then
            pcall(function() inspectModel(child) end)
        end
    end

    return list
end

local function getModelBasePart(model)
    if not model then return nil end
    if model:IsA("BasePart") then return model end
    if model.PrimaryPart and model.PrimaryPart:IsA("BasePart") then return model.PrimaryPart end
    for _, v in ipairs(model:GetDescendants()) do
        if v:IsA("BasePart") then return v end
    end
    return nil
end

local function teleportNear(target)
    if not target then return end
    local part = nil
    if target:IsA("BasePart") then
        part = target
    else
        part = getModelBasePart(target)
    end
    if not part then return end
    local targetPos = part.Position
    local offset = Vector3.new(0, 2.2, 0)
    if part.CFrame then
        offset = part.CFrame.LookVector * -1.2 + Vector3.new(0, 2.2, 0)
    end
    local finalPos = targetPos + offset
    teleportTo(finalPos.X, finalPos.Y, finalPos.Z)
    task.wait(0.12)
end

local function attemptCollect(entry)
    if not entry then return false end

    if entry.prompt and entry.prompt:IsA("ProximityPrompt") then
        local prompt = entry.prompt
        if not prompt.Enabled then end
        local parentPart = prompt.Parent
        if parentPart and parentPart:IsA("BasePart") then
            teleportNear(parentPart)
        else
            local base = nil
            local p = prompt.Parent
            while p and p ~= workspace do
                if p:IsA("BasePart") then base = p; break end
                p = p.Parent
            end
            if base then teleportNear(base) end
        end

        task.wait(0.12)

        local hold = tonumber(prompt.HoldDuration) or 0
        for attempt = 1, 3 do
            local ok, err = pcall(function()
                if hold > 0 then
                    prompt:InputHoldBegin()
                    local waited = 0
                    local timeout = hold + 1.0
                    while prompt.Enabled and waited < timeout do
                        task.wait(0.1)
                        waited = waited + 0.1
                    end
                    prompt:InputHoldEnd()
                else
                    prompt:InputHoldBegin()
                    task.wait(0.08)
                    prompt:InputHoldEnd()
                end
            end)
            task.wait(0.25)
            if not prompt or not prompt.Parent or not prompt.Parent:IsDescendantOf(workspace) then
                return true
            end
            if ok then
                if entry.target and not entry.target:IsDescendantOf(workspace) then return true end
            end
            task.wait(0.15)
        end
        return false
    end

    if entry.remote then
        local rem = entry.remote
        local ok = false
        pcall(function()
            if rem:IsA("RemoteEvent") then
                pcall(function() rem:FireServer() end)
                pcall(function() rem:FireServer(entry.target) end)
                pcall(function() rem:FireServer(LocalPlayer) end)
                pcall(function() rem:FireServer(entry.target, LocalPlayer) end)
                ok = true
            elseif rem:IsA("RemoteFunction") then
                pcall(function() rem:InvokeServer() end)
                pcall(function() rem:InvokeServer(entry.target) end)
                pcall(function() rem:InvokeServer(LocalPlayer) end)
                ok = true
            end
        end)
        task.wait(0.35)
        if entry.target and not entry.target:IsDescendantOf(workspace) then return true end
        return ok
    end

    if entry.target then
        local cd = nil
        for _, d in ipairs(entry.target:GetDescendants()) do
            if d:IsA("ClickDetector") and (string.find(string.lower(d.Name or ""), "pegar") or string.find(string.lower(d.Name or ""), "lixo")) then
                cd = d
                break
            end
        end
        if cd then
            pcall(function() cd:MouseClick(LocalPlayer) end)
            task.wait(0.3)
            if entry.target and not entry.target:IsDescendantOf(workspace) then return true end
            return true
        end
    end

    return false
end

-- farmLoop updated to wait TELEPORT_DELAY_SECONDS between teleporting to the next trash
local function farmLoop()
    if not autoFarmEnabled then return end
    task.spawn(function()
        while autoFarmEnabled do
            local entries = findTrashEntries()
            if #entries == 0 then
                task.wait(2)
            else
                for i, entry in ipairs(entries) do
                    if not autoFarmEnabled then break end
                    if entry.target and not entry.target:IsDescendantOf(workspace) then
                        -- already gone; skip
                        continue
                    end
                    -- teleport close
                    pcall(function() teleportNear(entry.target) end)
                    task.wait(0.12)
                    -- attempt multiple collection strategies, up to N tries
                    local success = false
                    for attempt = 1, 3 do
                        if not autoFarmEnabled then break end
                        local ok = false
                        pcall(function() ok = attemptCollect(entry) end)
                        if ok then
                            success = true
                            break
                        end
                        task.wait(0.25)
                    end
                    -- After handling one entry, wait TELEPORT_DELAY_SECONDS before moving to the next
                    if autoFarmEnabled then
                        -- Optional notification (brief)
                        notify("Auto Farm", "Aguardando " .. tostring(TELEPORT_DELAY_SECONDS) .. "s antes do próximo lixo...", 1.4)
                        -- Respect the delay but allow early exit if autoFarmEnabled toggled off
                        local waited = 0
                        while waited < TELEPORT_DELAY_SECONDS and autoFarmEnabled do
                            task.wait(0.5)
                            waited = waited + 0.5
                        end
                    end
                end
            end
            task.wait(0.6)
        end
    end)
end

-- Fly PC: improved and more robust
local flyPCEnabled = false
local flyPCConnection = nil
local flyBV, flyBG = nil, nil
local flyVertical = 0
local flySpeed = 60
local flyInputBeginConn, flyInputEndConn = nil, nil

local function enableFlyPC()
    if flyPCEnabled then return end
    flyPCEnabled = true
    local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    if not flyBV then
        flyBV = Instance.new("BodyVelocity")
        flyBV.Name = "StoppedFlyBV"
        flyBV.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        flyBV.Velocity = Vector3.zero
    end
    if not flyBG then
        flyBG = Instance.new("BodyGyro")
        flyBG.Name = "StoppedFlyBG"
        flyBG.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
        flyBG.P = 1e5
        flyBG.CFrame = hrp.CFrame
    end

    if flyPCConnection then flyPCConnection:Disconnect() end
    flyPCConnection = RunService.RenderStepped:Connect(function()
        if not flyPCEnabled then return end
        local c = LocalPlayer.Character
        if not c then return end
        local hr = c:FindFirstChild("HumanoidRootPart")
        local hum = c:FindFirstChildOfClass("Humanoid")
        if not hr or not hum then return end
        if flyBV.Parent ~= hr then flyBV.Parent = hr end
        if flyBG.Parent ~= hr then flyBG.Parent = hr end
        local moveDir = hum.MoveDirection
        flyBV.Velocity = (moveDir * flySpeed) + Vector3.new(0, flyVertical * flySpeed, 0)
        flyBG.CFrame = workspace.CurrentCamera.CFrame
    end)

    if flyInputBeginConn then flyInputBeginConn:Disconnect(); flyInputBeginConn = nil end
    if flyInputEndConn then flyInputEndConn:Disconnect(); flyInputEndConn = nil end

    flyInputBeginConn = UserInputService.InputBegan:Connect(function(input, g)
        if g then return end
        if input.KeyCode == Enum.KeyCode.E then
            flyVertical = 1
        elseif input.KeyCode == Enum.KeyCode.Q then
            flyVertical = -1
        end
    end)
    flyInputEndConn = UserInputService.InputEnded:Connect(function(input, g)
        if input.KeyCode == Enum.KeyCode.E or input.KeyCode == Enum.KeyCode.Q then
            flyVertical = 0
        end
    end)
    notify("Fly PC", "Fly PC ATIVADO!", 3)
end

local function disableFlyPC()
    flyPCEnabled = false
    if flyPCConnection then flyPCConnection:Disconnect(); flyPCConnection = nil end
    if flyInputBeginConn then flyInputBeginConn:Disconnect(); flyInputBeginConn = nil end
    if flyInputEndConn then flyInputEndConn:Disconnect(); flyInputEndConn = nil end
    if flyBV and flyBV.Parent then flyBV.Parent = nil end
    if flyBG and flyBG.Parent then flyBG.Parent = nil end
    flyVertical = 0
    notify("Fly PC", "Fly PC DESATIVADO!", 3)
end

local function toggleFlyPC(state)
    if state then enableFlyPC() else disableFlyPC() end
end

-- Build UI tabs & options
createTabButton("Inicio", "rbxassetid://10814531047")
createTabButton("Combate", "rbxassetid://16029076040")
createTabButton("Aimbots", "rbxassetid://13557340523")
createTabButton("Visual", "rbxassetid://121658694000474")
createTabButton("Teleporte", "rbxassetid://6023426915")
createTabButton("Outros", "rbxassetid://6023426915")

-- Populate options
currentTab = "Inicio"
createToggle("Bem-vindo", function(state) if state then notify("Bem-vindo!", "Obrigado por usar o Stopped Menu!", 4) end end)
createToggle("Mostrar Notificações", function(state) notificationsEnabled = state end)

currentTab = "Combate"
createToggle("Nitro Automático", toggleNitro)
createSlider("Força do Nitro", 20, 150, function(value) nitroForce = value; notify("Nitro", "Força ajustada para: "..value, 1.6) end)
createToggle("Munição Infinita (Fuzil)", toggleInfiniteAmmoFuzil)
createToggle("Munição Infinita (Oitão)", toggleInfiniteAmmoOitao)

currentTab = "Aimbots"
createToggle("Aimbot Ativo", function(state) AimbotActive = state; fovCircle.Visible = (AimbotActive or FixedAimEnabled); notify("Aimbot", state and "Aimbot ATIVADO!" or "Aimbot DESATIVADO!", 2) end)
createToggle("Team Check", function(state) TEAM_CHECK = state; notify("Aimbot", "Team Check: " .. (state and "ATIVADO" or "DESATIVADO"), 2) end)
createToggle("Trocar Parte", function(state) AIM_PART = state and "Torso" or "Head"; notify("Aimbot", "Mirando no: " .. AIM_PART, 2) end)
createSlider("FOV", 10, 500, function(value) FOV_RADIUS = value; fovCircle.Radius = value; notify("Aimbot", "FOV ajustado para: "..value, 1.4) end)
createToggle("Mira Fixa (FiveM)", function(state) FixedAimEnabled = state; fovCircle.Visible = (AimbotActive or FixedAimEnabled); notify("Mira Fixa", state and "Mira fixa ativada!" or "Mira fixa desativada!", 2) end)

currentTab = "Visual"
createToggle("ESP Box com Vida", function(state) espEnabled = state; notify("ESP", state and "ESP Box ATIVADO!" or "ESP Box DESATIVADO!", 2) end)
createToggle("ESP Tracer", function(state) tracerEnabled = state; notify("ESP Tracer", state and "ESP Tracer ATIVADO!" or "ESP Tracer DESATIVADO!", 2) end)

currentTab = "Teleporte"
do
    local dropdownFrame = newPanelRow(50 * scaleMultiplier)
    local label = Instance.new("TextLabel")
    label.Parent = dropdownFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 6)
    label.Size = UDim2.new(0.6, 0, 0, 22)
    label.Font = Enum.Font.Gotham
    label.Text = "Locais de Teleporte"
    label.TextColor3 = COLOR_TEXT
    label.TextSize = fontSize

    local btn = Instance.new("TextButton")
    btn.Parent = dropdownFrame
    btn.BackgroundColor3 = Color3.fromRGB(38, 42, 48)
    btn.Position = UDim2.new(0.55, 0, 0.15, 0)
    btn.Size = UDim2.new(0.42, 0, 0, 30 * scaleMultiplier)
    btn.Font = Enum.Font.Gotham
    btn.Text = "Praça"
    btn.TextColor3 = COLOR_SUB
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)

    local open = false
    local items = {"Praça","Venda de Carros","Garagem","Ilegal de Rua","Mecânica","Mercadinho","Banco","Prefeitura"}
    btn.MouseButton1Click:Connect(function()
        open = not open
        if open then
            dropdownFrame.Size = UDim2.new(1, 0, 0, 50 + (#items * 34 * scaleMultiplier))
            for i,item in ipairs(items) do
                local child = dropdownFrame:FindFirstChild("item"..i)
                if not child then
                    local ib = Instance.new("TextButton")
                    ib.Name = "item"..i
                    ib.Parent = dropdownFrame
                    ib.BackgroundColor3 = Color3.fromRGB(40,44,50)
                    ib.Position = UDim2.new(0.55,0,0,50 + ((i-1)*34*scaleMultiplier))
                    ib.Size = UDim2.new(0.42,0,0,30*scaleMultiplier)
                    ib.Font = Enum.Font.Gotham
                    ib.Text = item
                    ib.TextColor3 = COLOR_TEXT
                    ib.TextSize = 12
                    Instance.new("UICorner", ib).CornerRadius = UDim.new(0,6)
                    ib.MouseButton1Click:Connect(function()
                        btn.Text = item
                        open = false
                        dropdownFrame.Size = UDim2.new(1,0,0,50*scaleMultiplier)
                        for _, o in ipairs(dropdownFrame:GetChildren()) do if o:IsA("TextButton") and o~=btn then o.Visible=false end end
                        local loc = locations[i]
                        if loc then teleportTo(loc.coords.X,loc.coords.Y,loc.coords.Z); notify("Teleport","Teleportado para: "..loc.nome,2) end
                    end)
                else
                    child.Visible = true
                end
            end
        else
            dropdownFrame.Size = UDim2.new(1,0,0,50*scaleMultiplier)
            for _, child in ipairs(dropdownFrame:GetChildren()) do if child:IsA("TextButton") and child~=btn then child.Visible=false end end
        end
    end)
    table.insert(options, {frame = dropdownFrame, name = "Locais de Teleporte", tab = currentTab})
end

createPlayerDropdown("Teleporte para Player", function(player)
    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local pos = player.Character.HumanoidRootPart.Position
        teleportTo(pos.X,pos.Y,pos.Z)
        notify("Teleport","Teleportado para: "..player.Name,2)
    else notify("Teleport","Jogador não disponível para teleport.",2) end
end)

currentTab = "Outros"
createToggle("Auto Farm Gari", function(state)
    autoFarmEnabled = state
    notify("Auto Farm", state and "Auto Farm Gari ATIVADO!" or "Auto Farm Gari DESATIVADO!", 2)
    if state then farmLoop() end
end)
createToggle("Fly Mobile", function(s) if s then notify("Fly Mobile", "Enable via GUI", 1) end end)
createToggle("Fly PC", toggleFlyPC)

-- BHOP
local bhopConn = nil
local function toggleBhop(state)
    if state then
        if bhopConn then bhopConn:Disconnect() end
        bhopConn = RunService.Heartbeat:Connect(function()
            local char = LocalPlayer.Character
            if not char then return end
            local humanoid = char:FindFirstChildOfClass("Humanoid")
            if not humanoid then return end
            local moveDir = humanoid.MoveDirection
            local onGround = humanoid.FloorMaterial ~= Enum.Material.Air
            if onGround and moveDir.Magnitude > 0.2 then humanoid.Jump = true end
        end)
        notify("BHOP","BHOP ATIVADO!",2)
    else
        if bhopConn then bhopConn:Disconnect(); bhopConn = nil end
        notify("BHOP","BHOP DESATIVADO!",2)
    end
end
createToggle("BHOP", toggleBhop)

-- WalkSpeed
local currentSpeed = 16
createSlider("WalkSpeed", 16, 200, function(value) currentSpeed = value; local h = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid"); if h then h.WalkSpeed = value end; notify("WalkSpeed","WalkSpeed ajustado para: "..tostring(value),1.2) end)

-- NoClip
local noClipEnabled = false
local noClipChangedParts = {}
local noClipConn
local function toggleNoClip(state)
    noClipEnabled = state
    if state then
        local char = LocalPlayer.Character
        if char then
            noClipChangedParts = {}
            for _, part in ipairs(char:GetDescendants()) do
                if part:IsA("BasePart") and part.CanCollide then
                    table.insert(noClipChangedParts, part)
                    part.CanCollide = false
                end
            end
        end
        noClipConn = LocalPlayer.CharacterAdded:Connect(function(nc) task.wait(0.5); for _, p in ipairs(nc:GetDescendants()) do if p:IsA("BasePart") and p.CanCollide then table.insert(noClipChangedParts,p); p.CanCollide = false end end end)
        notify("NoClip","NoClip ATIVADO!",2)
    else
        for _, p in ipairs(noClipChangedParts) do pcall(function() p.CanCollide = true end) end
        noClipChangedParts = {}
        if noClipConn then noClipConn:Disconnect(); noClipConn = nil end
        notify("NoClip","NoClip DESATIVADO!",2)
    end
end
createToggle("NoClip", toggleNoClip)

-- Clone skin
local selectedCloneUserId = nil
createPlayerDropdown("Selecionar Player (Clonar Skin)", function(player) if player then selectedCloneUserId = player.UserId else selectedCloneUserId = nil end end)
do
    local cloneFrame = newPanelRow(buttonHeight)
    local label = Instance.new("TextLabel")
    label.Parent = cloneFrame
    label.BackgroundTransparency = 1
    label.Position = UDim2.new(0.03, 0, 0, 0)
    label.Size = UDim2.new(0.7, 0, 1, 0)
    label.Font = Enum.Font.Gotham
    label.Text = "Clonar Skin do Player Selecionado"
    label.TextColor3 = COLOR_TEXT
    label.TextSize = fontSize
    label.TextXAlignment = Enum.TextXAlignment.Left

    local btn = Instance.new("TextButton")
    btn.Parent = cloneFrame
    btn.BackgroundColor3 = COLOR_ACCENT
    btn.Position = UDim2.new(0.78, 0, 0.15, 0)
    btn.Size = UDim2.new(0, 90 * scaleMultiplier, 0, 28 * scaleMultiplier)
    btn.Font = Enum.Font.GothamBold
    btn.Text = "Clonar Skin"
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.TextSize = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    btn.MouseButton1Click:Connect(function()
        if not selectedCloneUserId then notify("Skin","Nenhum jogador selecionado.",2); return end
        local ok, desc = pcall(function() return Players:GetHumanoidDescriptionFromUserId(selectedCloneUserId) end)
        if not ok or not desc then notify("Skin","Não foi possível obter a descrição do jogador.",2); return end
        local myHumanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if not myHumanoid then notify("Skin","Seu humanoide não está disponível.",2); return end
        local applied, err = pcall(function() myHumanoid:ApplyDescription(desc) end)
        if applied then notify("Skin","Skin clonada com sucesso!",2) else notify("Skin","Falha ao aplicar skin: "..tostring(err),2) end
    end)
    table.insert(options, {frame = cloneFrame, name = "Clonar Skin", tab = currentTab})
end

-- Ensure default tab visible
currentTab = "Inicio"
for _, opt in ipairs(options) do opt.frame.Visible = (opt.tab == currentTab) end

-- Minimize / Reopen behavior
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Parent = header
minimizeBtn.BackgroundColor3 = Color3.fromRGB(30, 34, 38)
minimizeBtn.Position = UDim2.new(0.88, 0, 0.18, 0)
minimizeBtn.Size = UDim2.new(0, 36 * scaleMultiplier, 0, 36 * scaleMultiplier)
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Text = "—"
minimizeBtn.TextColor3 = COLOR_TEXT
minimizeBtn.TextSize = 18
Instance.new("UICorner", minimizeBtn).CornerRadius = UDim.new(0, 6)

minimizeBtn.MouseButton1Click:Connect(function()
    if mainContainer.Visible then
        mainContainer.Visible = false
        if reopenButton then reopenButton:Destroy(); reopenButton = nil end
        reopenButton = Instance.new("ImageButton")
        reopenButton.Parent = screenGui
        reopenButton.BackgroundColor3 = Color3.fromRGB(30, 34, 38)
        reopenButton.Size = UDim2.new(0, 72 * scaleMultiplier, 0, 72 * scaleMultiplier)
        reopenButton.Position = lastReopenButtonPosition or UDim2.new(0.5, 0, 0.5, 0)
        reopenButton.AnchorPoint = Vector2.new(0.5, 0.5)
        reopenButton.Image = "rbxassetid://117130789916072"
        reopenButton.ScaleType = Enum.ScaleType.Fit
        Instance.new("UICorner", reopenButton).CornerRadius = UDim.new(0, 12)
        local stroke = Instance.new("UIStroke", reopenButton)
        stroke.Color = COLOR_ACCENT
        stroke.Thickness = 2
        reopenButton.ZIndex = 10

        local dragging, dragStart, startPos
        local isClicking = false
        local clickStartTime = 0
        local clickStartPos = Vector2.new(0, 0)
        local CLICK_THRESHOLD = 0.2
        local MOVE_THRESHOLD = 5

        reopenButton.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true; isClicking = true; dragStart = input.Position; startPos = reopenButton.Position; clickStartTime = tick(); clickStartPos = Vector2.new(input.Position.X, input.Position.Y)
            end
        end)
        reopenButton.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
                local clickDuration = tick() - clickStartTime
                local endPos = Vector2.new(input.Position.X, input.Position.Y)
                local moveDistance = (endPos - clickStartPos).Magnitude
                if isClicking and clickDuration < CLICK_THRESHOLD and moveDistance < MOVE_THRESHOLD then
                    mainContainer.Visible = true
                    if reopenButton then lastReopenButtonPosition = reopenButton.Position; reopenButton:Destroy(); reopenButton = nil end
                    notify("Janela", "Restaurado", 1.2)
                end
                isClicking = false
            end
        end)
        UserInputService.InputChanged:Connect(function(input)
            if dragging and input.Position then
                local delta = input.Position - dragStart
                reopenButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
                local currentPos = Vector2.new(input.Position.X, input.Position.Y)
                if (currentPos - clickStartPos).Magnitude > MOVE_THRESHOLD then isClicking = false end
            end
        end)
        notify("Janela", "Minimizado", 1.2)
    else
        mainContainer.Visible = true
        if reopenButton then lastReopenButtonPosition = reopenButton.Position; reopenButton:Destroy(); reopenButton = nil end
        notify("Janela", "Restaurado", 1.2)
    end
end)

local closeBtnMain = Instance.new("TextButton")
closeBtnMain.Parent = header
closeBtnMain.BackgroundColor3 = Color3.fromRGB(30, 34, 38)
closeBtnMain.Position = UDim2.new(0.94, 0, 0.18, 0)
closeBtnMain.Size = UDim2.new(0, 36 * scaleMultiplier, 0, 36 * scaleMultiplier)
closeBtnMain.Font = Enum.Font.GothamBold
closeBtnMain.Text = "X"
closeBtnMain.TextColor3 = Color3.fromRGB(200, 80, 80)
closeBtnMain.TextSize = 18
Instance.new("UICorner", closeBtnMain).CornerRadius = UDim.new(0, 6)
closeBtnMain.MouseButton1Click:Connect(function() screenGui:Destroy() end)

-- Layout update handlers to adjust content canvas
contentLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    contentFrame.CanvasSize = UDim2.new(0, 0, 0, contentLayout.AbsoluteContentSize.Y + 20)
end)
tabLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    tabContainer.CanvasSize = UDim2.new(0, 0, 0, tabLayout.AbsoluteContentSize.Y + 20)
end)

-- Initial notification
notify("Stopped Menu", "GUI loaded successfully!", 2.2)
