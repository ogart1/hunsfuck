-- LOSTsCRIPTS - FPS Booster v2.0
-- By ogart
-- Delta Executor Compatible

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")
local GuiService = game:GetService("GuiService") -- Added for UI management

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

-- Configurações de Autenticação
local SCRIPT_KEY = ""
local USER_ID = ""
local authenticated = false
local isPremium = false

-- Função de Validação de Chave Interna
local function checkKeyLocal(key, user)
    if key == "ogart" and user == "ogart" then
        return true, true -- Válido e Premium
    elseif key == "gratisOG" then
        return true, false -- Válido e Não Premium (Grátis)
    end
    return false, false -- Inválido
end

-- Interface de Login
local function createLoginGUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "LOSTsLogin"
    ScreenGui.Parent = PlayerGui
    ScreenGui.ResetOnSpawn = false
    
    local LoginFrame = Instance.new("Frame")
    LoginFrame.Size = UDim2.new(0, 400, 0, 300)
    LoginFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
    LoginFrame.BackgroundColor3 = Color3.new(0.08, 0.08, 0.08) -- Darker background
    LoginFrame.BorderSizePixel = 0
    LoginFrame.Parent = ScreenGui
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 15)
    UICorner.Parent = LoginFrame

    local UIStroke = Instance.new("UIStroke")
    UIStroke.Color = Color3.new(0.2, 0.8, 0.2)
    UIStroke.Thickness = 2
    UIStroke.Parent = LoginFrame
    
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, 0, 0, 50)
    Title.Position = UDim2.new(0, 0, 0, 0)
    Title.BackgroundColor3 = Color3.new(0.1, 0.3, 0.1) -- Greenish title bar
    Title.Text = "🔑 LOSTsCRIPTS - Login 🔑"
    Title.TextColor3 = Color3.new(1, 1, 1)
    Title.TextScaled = true
    Title.Font = Enum.Font.GothamBold
    Title.Parent = LoginFrame
    
    local KeyLabel = Instance.new("TextLabel")
    KeyLabel.Size = UDim2.new(1, -20, 0, 30)
    KeyLabel.Position = UDim2.new(0, 10, 0, 70)
    KeyLabel.BackgroundTransparency = 1
    KeyLabel.Text = "🔐 Key:"
    KeyLabel.TextColor3 = Color3.new(0.8, 0.8, 0.8)
    KeyLabel.TextScaled = true
    KeyLabel.Font = Enum.Font.Gotham
    KeyLabel.TextXAlignment = Enum.TextXAlignment.Left
    KeyLabel.Parent = LoginFrame
    
    local KeyBox = Instance.new("TextBox")
    KeyBox.Size = UDim2.new(1, -20, 0, 40)
    KeyBox.Position = UDim2.new(0, 10, 0, 100)
    KeyBox.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15) -- Darker input box
    KeyBox.TextColor3 = Color3.new(1, 1, 1)
    KeyBox.PlaceholderText = "Digite sua key aqui..."
    KeyBox.Text = ""
    KeyBox.TextScaled = true
    KeyBox.Font = Enum.Font.Gotham
    KeyBox.Parent = LoginFrame
    
    local KeyCorner = Instance.new("UICorner")
    KeyCorner.CornerRadius = UDim.new(0, 8)
    KeyCorner.Parent = KeyBox
    
    local UserLabel = Instance.new("TextLabel")
    UserLabel.Size = UDim2.new(1, -20, 0, 30)
    UserLabel.Position = UDim2.new(0, 10, 0, 150)
    UserLabel.BackgroundTransparency = 1
    UserLabel.Text = "👤 User:"
    UserLabel.TextColor3 = Color3.new(0.8, 0.8, 0.8)
    UserLabel.TextScaled = true
    UserLabel.Font = Enum.Font.Gotham
    UserLabel.TextXAlignment = Enum.TextXAlignment.Left
    UserLabel.Parent = LoginFrame
    
    local UserBox = Instance.new("TextBox")
    UserBox.Size = UDim2.new(1, -20, 0, 40)
    UserBox.Position = UDim2.new(0, 10, 0, 180)
    UserBox.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15) -- Darker input box
    UserBox.TextColor3 = Color3.new(1, 1, 1)
    UserBox.PlaceholderText = "Digite seu usuário..."
    UserBox.Text = ""
    UserBox.TextScaled = true
    UserBox.Font = Enum.Font.Gotham
    UserBox.Parent = LoginFrame
    
    local UserCorner = Instance.new("UICorner")
    UserCorner.CornerRadius = UDim.new(0, 8)
    UserCorner.Parent = UserBox
    
    local LoginButton = Instance.new("TextButton")
    LoginButton.Size = UDim2.new(1, -20, 0, 40)
    LoginButton.Position = UDim2.new(0, 10, 0, 240)
    LoginButton.BackgroundColor3 = Color3.new(0.2, 0.6, 0.2) -- Greenish button
    LoginButton.Text = "✅ Login"
    LoginButton.TextColor3 = Color3.new(1, 1, 1)
    LoginButton.TextScaled = true
    LoginButton.Font = Enum.Font.GothamBold
    LoginButton.Parent = LoginFrame
    
    local LoginCorner = Instance.new("UICorner")
    LoginCorner.CornerRadius = UDim.new(0, 8)
    LoginCorner.Parent = LoginButton
    
    local Credits = Instance.new("TextLabel")
    Credits.Size = UDim2.new(1, 0, 0, 20)
    Credits.Position = UDim2.new(0, 0, 1, -20)
    Credits.BackgroundTransparency = 1
    Credits.Text = "💻 by ogart"
    Credits.TextColor3 = Color3.new(0.6, 0.6, 0.6)
    Credits.TextScaled = true
    Credits.Font = Enum.Font.Gotham
    Credits.Parent = LoginFrame
    
    LoginButton.MouseButton1Click:Connect(function()
        local key = KeyBox.Text
        local user = UserBox.Text
        
        if key == "" or user == "" then
            KeyBox.PlaceholderText = "❌ Preencha todos os campos!"
            UserBox.PlaceholderText = "❌ Preencha todos os campos!"
            wait(2)
            KeyBox.PlaceholderText = "Digite sua key aqui..."
            UserBox.PlaceholderText = "Digite seu usuário..."
            return
        end
        
        LoginButton.Text = "🔄 Verificando..."
        local valid, premiumStatus = checkKeyLocal(key, user)
        
        if valid then
            SCRIPT_KEY = key
            USER_ID = user
            authenticated = true
            isPremium = premiumStatus
            ScreenGui:Destroy()
            createMainGUI(isPremium)
        else
            LoginButton.Text = "❌ Key ou Usuário Inválidos!"
            wait(2)
            LoginButton.Text = "✅ Login"
        end
    end)
end

-- Interface Principal
local function createMainGUI(isPremiumUser)
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "LOSTsBooster"
    ScreenGui.Parent = PlayerGui
    ScreenGui.ResetOnSpawn = false
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Size = UDim2.new(0, 650, 0, 450) -- Slightly larger
    MainFrame.Position = UDim2.new(0.5, -325, 0.5, -225)
    MainFrame.BackgroundColor3 = Color3.new(0.05, 0.05, 0.05) -- Darker background
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = ScreenGui
    MainFrame.Active = true
    MainFrame.Draggable = true
    
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 15)
    UICorner.Parent = MainFrame
    
    local UIStroke = Instance.new("UIStroke") -- Added stroke for better appearance
    UIStroke.Color = Color3.new(0.2, 0.8, 0.2)
    UIStroke.Thickness = 2
    UIStroke.Parent = MainFrame
    
    -- Título
    local TitleBar = Instance.new("Frame")
    TitleBar.Size = UDim2.new(1, 0, 0, 50)
    TitleBar.Position = UDim2.new(0, 0, 0, 0)
    TitleBar.BackgroundColor3 = Color3.new(0.1, 0.3, 0.1) -- Greenish title bar
    TitleBar.BorderSizePixel = 0
    TitleBar.Parent = MainFrame
    
    local TitleCorner = Instance.new("UICorner")
    TitleCorner.CornerRadius = UDim.new(0, 15)
    TitleCorner.Parent = TitleBar
    
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, -100, 1, 0)
    Title.Position = UDim2.new(0, 15, 0, 0)
    Title.BackgroundTransparency = 1
    Title.Text = "🚀 LOSTsCRIPTS - FPS Booster " .. (isPremiumUser and "👑 PREMIUM" or "🆓 FREE")
    Title.TextColor3 = Color3.new(1, 1, 1)
    Title.TextScaled = true
    Title.Font = Enum.Font.GothamBold
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = TitleBar
    
    -- Botões de controle (Minimizar, Fechar)
    local CloseButton = Instance.new("TextButton")
    CloseButton.Size = UDim2.new(0, 40, 0, 30)
    CloseButton.Position = UDim2.new(1, -45, 0, 10)
    CloseButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2) -- Red for close
    CloseButton.Text = "X"
    CloseButton.TextColor3 = Color3.new(1, 1, 1)
    CloseButton.TextScaled = true
    CloseButton.Font = Enum.Font.GothamBold
    CloseButton.Parent = TitleBar
    
    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 8)
    CloseCorner.Parent = CloseButton
    
    CloseButton.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)

    local MinimizeButton = Instance.new("TextButton")
    MinimizeButton.Size = UDim2.new(0, 40, 0, 30)
    MinimizeButton.Position = UDim2.new(1, -95, 0, 10) -- Adjusted position
    MinimizeButton.BackgroundColor3 = Color3.new(0.8, 0.8, 0.2) -- Yellow for minimize
    MinimizeButton.Text = "—"
    MinimizeButton.TextColor3 = Color3.new(0, 0, 0)
    MinimizeButton.TextScaled = true
    MinimizeButton.Font = Enum.Font.GothamBold
    MinimizeButton.Parent = TitleBar
    
    local MinCorner = Instance.new("UICorner")
    MinCorner.CornerRadius = UDim.new(0, 8)
    MinCorner.Parent = MinimizeButton
    
    local minimized = false
    local originalSize = MainFrame.Size
    local originalPosition = MainFrame.Position
    
    MinimizeButton.MouseButton1Click:Connect(function()
        if not minimized then
            MainFrame:TweenSizeAndPosition(UDim2.new(0, 300, 0, 50), UDim2.new(1, -310, 0, 10), "Out", "Quad", 0.3) -- Minimize to corner
            MinimizeButton.Text = "🗖" -- Maximize icon
            minimized = true
        else
            MainFrame:TweenSizeAndPosition(originalSize, originalPosition, "Out", "Quad", 0.3)
            MinimizeButton.Text = "—"
            minimized = false
        end
    end)
    
    -- Container de Abas
    local TabContainer = Instance.new("Frame")
    TabContainer.Size = UDim2.new(0, 150, 1, -60) -- Vertical tabs
    TabContainer.Position = UDim2.new(0, 10, 0, 55)
    TabContainer.BackgroundTransparency = 1
    TabContainer.Parent = MainFrame

    local TabListLayout = Instance.new("UIListLayout")
    TabListLayout.FillDirection = Enum.FillDirection.Vertical
    TabListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Left
    TabListLayout.Padding = UDim.new(0, 5)
    TabListLayout.Parent = TabContainer
    
    -- Container de Conteúdo
    local ContentContainer = Instance.new("Frame")
    ContentContainer.Size = UDim2.new(1, -170, 1, -70) -- Adjusted for vertical tabs
    ContentContainer.Position = UDim2.new(0, 160, 0, 60)
    ContentContainer.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1) -- Darker content background
    ContentContainer.BackgroundTransparency = 0.5
    ContentContainer.BorderSizePixel = 0
    ContentContainer.Parent = MainFrame
    
    local ContentCorner = Instance.new("UICorner")
    ContentCorner.CornerRadius = UDim.new(0, 10)
    ContentCorner.Parent = ContentContainer
    
    -- Créditos
    local Credits = Instance.new("TextLabel")
    Credits.Size = UDim2.new(1, 0, 0, 25)
    Credits.Position = UDim2.new(0, 0, 1, -25)
    Credits.BackgroundTransparency = 1
    Credits.Text = "💻 Created by ogart | Status: " .. (isPremiumUser and "👑 PREMIUM" or "🆓 FREE")
    Credits.TextColor3 = Color3.new(0.7, 0.7, 0.7)
    Credits.TextScaled = true
    Credits.Font = Enum.Font.Gotham
    Credits.Parent = MainFrame
    
    -- Sistema de Abas
    local tabs = {}
    local currentTab = nil
    
    local function createTab(name, icon, content)
        local tabButton = Instance.new("TextButton")
        tabButton.Size = UDim2.new(1, 0, 0, 40) -- Full width of TabContainer
        tabButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
        tabButton.Text = icon .. " " .. name
        tabButton.TextColor3 = Color3.new(0.8, 0.8, 0.8)
        tabButton.TextScaled = true
        tabButton.Font = Enum.Font.Gotham
        tabButton.Parent = TabContainer
        
        local tabCorner = Instance.new("UICorner")
        tabCorner.CornerRadius = UDim.new(0, 8)
        tabCorner.Parent = tabButton
        
        local tabContent = Instance.new("ScrollingFrame")
        tabContent.Size = UDim2.new(1, 0, 1, 0)
        tabContent.Position = UDim2.new(0, 0, 0, 0)
        tabContent.BackgroundTransparency = 1
        tabContent.BorderSizePixel = 0
        tabContent.ScrollBarThickness = 6
        tabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
        tabContent.AutomaticCanvasSize = Enum.AutomaticSize.Y
        tabContent.Visible = false
        tabContent.Parent = ContentContainer
        
        local layout = Instance.new("UIListLayout")
        layout.SortOrder = Enum.SortOrder.LayoutOrder
        layout.Padding = UDim.new(0, 10)
        layout.Parent = tabContent
        
        content(tabContent)
        
        tabButton.MouseButton1Click:Connect(function()
            if currentTab then
                currentTab.button.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
                currentTab.button.TextColor3 = Color3.new(0.8, 0.8, 0.8)
                currentTab.content.Visible = false
            end
            
            tabButton.BackgroundColor3 = Color3.new(0.15, 0.45, 0.15) -- Highlight selected tab
            tabButton.TextColor3 = Color3.new(1, 1, 1)
            tabContent.Visible = true
            
            currentTab = {button = tabButton, content = tabContent}
        end)
        
        table.insert(tabs, {button = tabButton, content = tabContent})
        
        if #tabs == 1 then
            tabButton.MouseButton1Click()
        end
    end
    
    -- Função para criar botões
    local function createButton(parent, text, color, callback)
        local button = Instance.new("TextButton")
        button.Size = UDim2.new(1, -20, 0, 40)
        button.Position = UDim2.new(0, 10, 0, 0)
        button.BackgroundColor3 = color
        button.Text = text
        button.TextColor3 = Color3.new(1, 1, 1)
        button.TextScaled = true
        button.Font = Enum.Font.Gotham
        button.Parent = parent
        
        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(0, 8)
        corner.Parent = button
        
        button.MouseButton1Click:Connect(callback)
        
        return button
    end
    
    -- Função para criar toggle
    local function createToggle(parent, text, initialEnabled, callback)
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(1, -20, 0, 50)
        frame.Position = UDim2.new(0, 10, 0, 0)
        frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
        frame.BorderSizePixel = 0
        frame.Parent = parent
        
        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(0, 8)
        corner.Parent = frame
        
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, -60, 1, 0)
        label.Position = UDim2.new(0, 10, 0, 0)
        label.BackgroundTransparency = 1
        label.Text = text
        label.TextColor3 = Color3.new(1, 1, 1)
        label.TextScaled = true
        label.Font = Enum.Font.Gotham
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = frame
        
        local toggle = Instance.new("TextButton")
        toggle.Size = UDim2.new(0, 40, 0, 30)
        toggle.Position = UDim2.new(1, -50, 0.5, -15)
        toggle.BackgroundColor3 = initialEnabled and Color3.new(0, 0.8, 0) or Color3.new(0.8, 0, 0)
        toggle.Text = initialEnabled and "✅" or "❌"
        toggle.TextColor3 = Color3.new(1, 1, 1)
        toggle.TextScaled = true
        toggle.Font = Enum.Font.GothamBold
        toggle.Parent = frame
        
        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(0, 15)
        toggleCorner.Parent = toggle
        
        local enabled = initialEnabled
        
        toggle.MouseButton1Click:Connect(function()
            enabled = not enabled
            if enabled then
                toggle.BackgroundColor3 = Color3.new(0, 0.8, 0)
                toggle.Text = "✅"
            else
                toggle.BackgroundColor3 = Color3.new(0.8, 0, 0)
                toggle.Text = "❌"
            end
            callback(enabled)
        end)
        
        return frame
    end

    local function createSlider(parent, text, minValue, maxValue, initialValue, step, callback)
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(1, -20, 0, 60)
        frame.Position = UDim2.new(0, 10, 0, 0)
        frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
        frame.BorderSizePixel = 0
        frame.Parent = parent

        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(0, 8)
        corner.Parent = frame

        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, -20, 0, 20)
        label.Position = UDim2.new(0, 10, 0, 5)
        label.BackgroundTransparency = 1
        label.Text = text .. ": " .. tostring(initialValue)
        label.TextColor3 = Color3.new(1, 1, 1)
        label.TextScaled = true
        label.Font = Enum.Font.Gotham
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = frame

        local sliderFrame = Instance.new("Frame")
        sliderFrame.Size = UDim2.new(1, -40, 0, 10)
        sliderFrame.Position = UDim2.new(0, 20, 0, 35)
        sliderFrame.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
        sliderFrame.BorderSizePixel = 0
        sliderFrame.Parent = frame

        local sliderCorner = Instance.new("UICorner")
        sliderCorner.CornerRadius = UDim.new(0, 5)
        sliderCorner.Parent = sliderFrame

        local fill = Instance.new("Frame")
        fill.Size = UDim2.new((initialValue - minValue) / (maxValue - minValue), 0, 1, 0)
        fill.BackgroundColor3 = Color3.new(0, 0.6, 0)
        fill.BorderSizePixel = 0
        fill.Parent = sliderFrame

        local fillCorner = Instance.new("UICorner")
        fillCorner.CornerRadius = UDim.new(0, 5)
        fillCorner.Parent = fill

        local handle = Instance.new("Frame")
        handle.Size = UDim2.new(0, 20, 0, 20)
        handle.Position = UDim2.new((initialValue - minValue) / (maxValue - minValue), -10, 0.5, -10)
        handle.BackgroundColor3 = Color3.new(0.2, 0.8, 0.2)
        handle.BorderSizePixel = 0
        handle.Parent = sliderFrame

        local handleCorner = Instance.new("UICorner")
        handleCorner.CornerRadius = UDim.new(0, 10)
        handleCorner.Parent = handle

        local dragging = false
        local currentValue = initialValue

        handle.MouseButton1Down:Connect(function()
            dragging = true
            handle.BackgroundColor3 = Color3.new(0.4, 0.9, 0.4)
            GuiService.AutoSelectGuiEnabled = false -- Prevent Roblox from trying to select other UI
        end)

        UserInputService.InputChanged:Connect(function(input)
            if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                local relativeX = (input.Position.X - sliderFrame.AbsolutePosition.X) / sliderFrame.AbsoluteSize.X
                relativeX = math.clamp(relativeX, 0, 1)

                local newValue = minValue + relativeX * (maxValue - minValue)
                newValue = math.round(newValue / step) * step
                newValue = math.clamp(newValue, minValue, maxValue)
                
                currentValue = newValue
                label.Text = text .. ": " .. tostring(math.floor(currentValue * 10) / 10) -- Display one decimal place

                local fillScale = (currentValue - minValue) / (maxValue - minValue)
                fill.Size = UDim2.new(fillScale, 0, 1, 0)
                handle.Position = UDim2.new(fillScale, -10, 0.5, -10)

                callback(currentValue)
            end
        end)

        UserInputService.InputEnded:Connect(function(input)
            if dragging and input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = false
                handle.BackgroundColor3 = Color3.new(0.2, 0.8, 0.2)
                GuiService.AutoSelectGuiEnabled = true
            end
        end)

        return frame
    end
    
    -- ABA FPS BOOST
    createTab("FPS Boost", "🚀", function(parent)
        createButton(parent, "🔥 FPS MAX (Ultra Performance)", Color3.new(0.8, 0.1, 0.1), function()
            settings().Rendering.QualityLevel = Enum.QualityLevel.Level1 -- Lowest graphics setting
            Lighting.GlobalShadows = false
            Lighting.Brightness = 0
            Lighting.FogEnd = 9e9 -- Far fog
            Lighting.FogStart = 9e9
            
            for i,v in pairs(Lighting:GetDescendants()) do
                if v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("ColorCorrectionEffect") or v:IsA("BloomEffect") or v:IsA("DepthOfFieldEffect") then
                    v.Enabled = false
                end
            end
            
            for i, v in pairs(Workspace:GetDescendants()) do
                if v:IsA("Part") or v:IsA("Union") or v:IsA("MeshPart") then
                    v.Material = Enum.Material.Plastic -- Simplest material
                    v.Reflectance = 0
                    v.Transparency = 0
                end
                if v:IsA("Decal") then
                    v.Transparency = 1 -- Hide decals
                end
                if v:IsA("ParticleEmitter") or v:IsA("Trail") then
                    v.Enabled = false -- Disable particles and trails
                end
                if v:IsA("Explosion") then
                    v.BlastPressure = 1
                    v.BlastRadius = 1
                end
                if v:IsA("Sparkles") or v:IsA("Fire") or v:IsA("Smoke") then
                    v.Enabled = false
                end
            end
            Player.CharacterAppearanceLoaded:Connect(function(char) -- Apply to character parts
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.Material = Enum.Material.Plastic
                    end
                end
            end)
        end)
        
        createButton(parent, "⚡ FPS Medium (Balanced)", Color3.new(0.8, 0.5, 0.1), function()
            settings().Rendering.QualityLevel = Enum.QualityLevel.Level3
            Lighting.GlobalShadows = false
            Lighting.FogEnd = 100000
            Lighting.FogStart = 0
            
            for i,v in pairs(Lighting:GetDescendants()) do
                if v:IsA("BlurEffect") or v:IsA("SunRaysEffect") then
                    v.Enabled = false
                end
                if v:IsA("ColorCorrectionEffect") or v:IsA("BloomEffect") or v:IsA("DepthOfFieldEffect") then
                    v.Enabled = true -- Re-enable some effects if present
                end
            end
            
            for i, v in pairs(Workspace:GetDescendants()) do
                if v:IsA("Decal") then
                    v.Transparency = 0
                end
                if v:IsA("ParticleEmitter") or v:IsA("Trail") then
                    v.Enabled = true
                end
                if v:IsA("Sparkles") or v:IsA("Fire") or v:IsA("Smoke") then
                    v.Enabled = true
                end
            end
        end)
        
        createButton(parent, "💡 FPS Min (Light Boost)", Color3.new(0.2, 0.6, 0.2), function()
            settings().Rendering.QualityLevel = Enum.QualityLevel.Level5
            Lighting.GlobalShadows = false
            Lighting.FogEnd = 100000
            Lighting.FogStart = 0

            for i,v in pairs(Lighting:GetDescendants()) do
                if v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("ColorCorrectionEffect") or v:IsA("BloomEffect") or v:IsA("DepthOfFieldEffect") then
                    v.Enabled = true
                end
            end
            for i, v in pairs(Workspace:GetDescendants()) do
                if v:IsA("Decal") then
                    v.Transparency = 0
                end
                if v:IsA("ParticleEmitter") or v:IsA("Trail") then
                    v.Enabled = true
                end
                if v:IsA("Sparkles") or v:IsA("Fire") or v:IsA("Smoke") then
                    v.Enabled = true
                end
            end
        end)
        
        if isPremiumUser then
            createButton(parent, "👑 PREMIUM FPS Destroyer (Aggressive)", Color3.new(0.5, 0.1, 0.8), function()
                -- PREMIUM FPS
                for i,v in pairs(Workspace:GetDescendants()) do
                    if v:IsA("Texture") or v:IsA("SurfaceGui") then
                        v:Destroy()
                    end
                    if v:IsA("BasePart") then -- More aggressive material removal
                        v.Material = Enum.Material.Plastic
                    end
                end
                -- Continuously set quality to 1 for maximum boost
                RunService.Heartbeat:Connect(function()
                    settings().Rendering.QualityLevel = Enum.QualityLevel.Level1
                    Lighting.GlobalShadows = false
                end)
            end)
        else
            -- Mostrar botão "Upgrade to Premium" para usuários gratuitos
            createButton(parent, "Upgrade to Premium 👑", Color3.new(0.8, 0.6, 0), function()
                local upgradeGui = Instance.new("ScreenGui")
                upgradeGui.Name = "LOSTsUpgrade"
                upgradeGui.Parent = PlayerGui
                upgradeGui.ResetOnSpawn = false

                local upgradeFrame = Instance.new("Frame")
                upgradeFrame.Size = UDim2.new(0, 400, 0, 200)
                upgradeFrame.Position = UDim2.new(0.5, -200, 0.5, -100)
                upgradeFrame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
                upgradeFrame.BorderSizePixel = 0
                upgradeFrame.Parent = upgradeGui

                local upgradeCorner = Instance.new("UICorner")
                upgradeCorner.CornerRadius = UDim.new(0, 15)
                upgradeCorner.Parent = upgradeFrame

                local upgradeTitle = Instance.new("TextLabel")
                upgradeTitle.Size = UDim2.new(1, 0, 0, 40)
                upgradeTitle.BackgroundColor3 = Color3.new(0.1, 0.3, 0.1)
                upgradeTitle.Text = "🌟 Upgrade to Premium 🌟"
                upgradeTitle.TextColor3 = Color3.new(1, 1, 1)
                upgradeTitle.TextScaled = true
                upgradeTitle.Font = Enum.Font.GothamBold
                upgradeTitle.Parent = upgradeFrame

                local upgradeText = Instance.new("TextLabel")
                upgradeText.Size = UDim2.new(1, -20, 0, 80)
                upgradeText.Position = UDim2.new(0, 10, 0, 50)
                upgradeText.BackgroundTransparency = 1
                upgradeText.TextXAlignment = Enum.TextXAlignment.Center
                upgradeText.TextYAlignment = Enum.TextYAlignment.Center
                upgradeText.TextColor3 = Color3.new(0.9, 0.9, 0.9)
                upgradeText.TextWrapped = true
                upgradeText.Font = Enum.Font.Gotham
                upgradeText.TextSize = 18
                upgradeText.Text = "Para acessar os recursos PREMIUM, use a Key: 'ogart' e o User: 'ogart' ao fazer login!"
                upgradeText.Parent = upgradeFrame

                local closeUpgradeButton = Instance.new("TextButton")
                closeUpgradeButton.Size = UDim2.new(0, 100, 0, 30)
                closeUpgradeButton.Position = UDim2.new(0.5, -50, 1, -40)
                closeUpgradeButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
                closeUpgradeButton.Text = "OK"
                closeUpgradeButton.TextColor3 = Color3.new(1, 1, 1)
                closeUpgradeButton.TextScaled = true
                closeUpgradeButton.Font = Enum.Font.GothamBold
                closeUpgradeButton.Parent = upgradeFrame

                local closeUpgradeCorner = Instance.new("UICorner")
                closeUpgradeCorner.CornerRadius = UDim.new(0, 8)
                closeUpgradeCorner.Parent = closeUpgradeButton

                closeUpgradeButton.MouseButton1Click:Connect(function()
                    upgradeGui:Destroy()
                end)
            end)
        end
    end)
    
    -- ABA GRAPHICS
    createTab("Graphics", "🎨", function(parent)
        createToggle(parent, "🌟 Remove Textures/Decals", false, function(enabled)
            for i,v in pairs(Workspace:GetDescendants()) do
                if v:IsA("Decal") or v:IsA("Texture") then
                    v.Transparency = enabled and 1 or 0
                end
            end
        end)
        
        createToggle(parent, "💨 Remove Particles/Trails", false, function(enabled)
            for i,v in pairs(Workspace:GetDescendants()) do
                if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Sparkles") or v:IsA("Fire") or v:IsA("Smoke") then
                    v.Enabled = not enabled
                end
            end
        end)
        
        createToggle(parent, "🌫️ Remove Fog", false, function(enabled)
            Lighting.FogEnd = enabled and 9e9 or 100000
            Lighting.FogStart = enabled and 9e9 or 0
        end)
        
        createToggle(parent, "☀️ Remove Shadows", false, function(enabled)
            Lighting.GlobalShadows = not enabled
        end)

        createToggle(parent, "🌐 Disable Ambient Lighting", false, function(enabled)
            if enabled then
                Lighting.Ambient = Color3.new(0,0,0)
                Lighting.OutdoorAmbient = Color3.new(0,0,0)
            else
                Lighting.Ambient = Color3.new(0.5, 0.5, 0.5) -- Default Roblox ambient
                Lighting.OutdoorAmbient = Color3.new(0.5, 0.5, 0.5) -- Default Roblox outdoor ambient
            end
        end)

        createSlider(parent, "Material Quality", 0, 1, 1, 0.1, function(value)
            -- This is a conceptual slider. Direct control over material quality across all parts
            -- isn't granular via a single property. This could iterate and set materials.
            -- For simplicity, we'll keep it illustrative.
            -- A value of 0 could mean Plastic, 1 could mean full materials.
            for _, part in pairs(Workspace:GetDescendants()) do
                if part:IsA("BasePart") then
                    if value < 0.5 then
                        part.Material = Enum.Material.Plastic
                    else
                        -- Reset to default material if it had one, or a common material
                        part.Material = Enum.Material.SmoothPlastic
                    end
                end
            end
        end)
    end)
    
    -- ABA MOBILE (se for mobile)
    if UserInputService.TouchEnabled then
        createTab("Mobile", "📱", function(parent)
            createButton(parent, "📱 Mobile Optimization", Color3.new(0.1, 0.4, 0.8), function()
                settings().Rendering.QualityLevel = Enum.QualityLevel.Level1
                for i,v in pairs(Workspace:GetDescendants()) do
                    if v:IsA("Part") then
                        v.Material = Enum.Material.SmoothPlastic
                        v.TopSurface = Enum.SurfaceType.Smooth
                        v.BottomSurface = Enum.SurfaceType.Smooth
                        v.LeftSurface = Enum.SurfaceType.Smooth
                        v.RightSurface = Enum.SurfaceType.Smooth
                        v.FrontSurface = Enum.SurfaceType.Smooth
                        v.BackSurface = Enum.SurfaceType.Smooth
                    end
                end
            end)
            
            createToggle(parent, "🔋 Battery Saver", false, function(enabled)
                -- This flag might not exist or might be deprecated.
                -- Modern Roblox handles power saving more automatically.
                -- This is a placeholder for potential future flags.
                -- For now, it won't do anything specific.
                warn("Battery Saver toggle is a placeholder and may not have an effect.")
            end)
        end)
    end
    
    -- ABA SETTINGS
    createTab("Settings", "⚙️", function(parent)
        createButton(parent, "🔄 Reset All Settings", Color3.new(0.4, 0.4, 0.4), function()
            settings().Rendering.QualityLevel = Enum.QualityLevel.Automatic
            Lighting.GlobalShadows = true
            Lighting.FogEnd = 100000
            Lighting.FogStart = 0
            Lighting.Ambient = Color3.new(0.5, 0.5, 0.5)
            Lighting.OutdoorAmbient = Color3.new(0.5, 0.5, 0.5)

            -- Re-enable all effects in Lighting
            for i,v in pairs(Lighting:GetDescendants()) do
                if v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("ColorCorrectionEffect") or v:IsA("BloomEffect") or v:IsA("DepthOfFieldEffect") then
                    v.Enabled = true
                end
            end
            
            -- Reset decals, particles, etc.
            for i,v in pairs(Workspace:GetDescendants()) do
                if v:IsA("Decal") or v:IsA("Texture") then
                    v.Transparency = 0
                end
                if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Sparkles") or v:IsA("Fire") or v:IsA("Smoke") then
                    v.Enabled = true
                end
                if v:IsA("BasePart") then
                    v.Material = Enum.Material.Plastic -- Can't fully reset to original, but a good default
                    v.TopSurface = Enum.SurfaceType.Default
                    v.BottomSurface = Enum.SurfaceType.Default
                    v.LeftSurface = Enum.SurfaceType.Default
                    v.RightSurface = Enum.SurfaceType.Default
                    v.FrontSurface = Enum.SurfaceType.Default
                    v.BackSurface = Enum.SurfaceType.Default
                end
            end
        end)
        
        createButton(parent, "❓ How to Use", Color3.new(0.1, 0.5, 0.8), function()
            local infoGui = Instance.new("ScreenGui")
            infoGui.Name = "LOSTsInfo"
            infoGui.Parent = PlayerGui
            infoGui.ResetOnSpawn = false

            local infoFrame = Instance.new("Frame")
            infoFrame.Size = UDim2.new(0, 500, 0, 300)
            infoFrame.Position = UDim2.new(0.5, -250, 0.5, -150)
            infoFrame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
            infoFrame.BorderSizePixel = 0
            infoFrame.Parent = infoGui

            local infoCorner = Instance.new("UICorner")
            infoCorner.CornerRadius = UDim.new(0, 15)
            infoCorner.Parent = infoFrame

            local infoTitle = Instance.new("TextLabel")
            infoTitle.Size = UDim2.new(1, 0, 0, 40)
            infoTitle.BackgroundColor3 = Color3.new(0.1, 0.3, 0.1)
            infoTitle.Text = "ℹ️ How to Use LOSTsCRIPTS"
            infoTitle.TextColor3 = Color3.new(1, 1, 1)
            infoTitle.TextScaled = true
            infoTitle.Font = Enum.Font.GothamBold
            infoTitle.Parent = infoFrame

            local infoText = Instance.new("TextLabel")
            infoText.Size = UDim2.new(1, -20, 1, -60)
            infoText.Position = UDim2.new(0, 10, 0, 50)
            infoText.BackgroundTransparency = 1
            infoText.TextXAlignment = Enum.TextXAlignment.Left
            infoText.TextYAlignment = Enum.TextYAlignment.Top
            infoText.TextColor3 = Color3.new(0.9, 0.9, 0.9)
            infoText.TextWrapped = true
            infoText.Font = Enum.Font.Gotham
            infoText.TextSize = 16 -- Fixed font size for better readability
            infoText.Text = [[
Welcome to LOSTsCRIPTS FPS Booster!

🚀 **FPS Boost Tab:**
   - Choose from different performance modes:
     - **Ultra Performance:** Aggressive settings for maximum FPS.
     - **Balanced:** Good balance between visuals and performance.
     - **Light Boost:** Minor improvements.
     - **PREMIUM (Aggressive):** For the most demanding boost. (Exclusive to 'ogart' key)

🎨 **Graphics Tab:**
   - Toggle specific graphic elements:
     - **Remove Textures/Decals:** Hides images on parts.
     - **Remove Particles/Trails:** Disables effects like smoke, fire, sparkles.
     - **Remove Fog:** Clears environmental fog.
     - **Remove Shadows:** Disables global shadows.
     - **Disable Ambient Lighting:** Makes lighting harsher (can boost FPS).
     - **Material Quality Slider:** (Conceptual) Adjusts material complexity.

📱 **Mobile Tab (if applicable):**
   - **Mobile Optimization:** Specific tweaks for mobile devices.
   - **Battery Saver:** (Placeholder) Intended to reduce power consumption.

⚙️ **Settings Tab:**
   - **Reset All Settings:** Reverts all changes made by the script to default.
   - **How to Use:** You're here!
   - **Close Script:** Closes the UI and stops the script.

Enjoy a smoother Roblox experience!
            ]]
            infoText.Parent = infoFrame

            local infoCloseButton = Instance.new("TextButton")
            infoCloseButton.Size = UDim2.new(0, 100, 0, 30)
            infoCloseButton.Position = UDim2.new(0.5, -50, 1, -40)
            infoCloseButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
            infoCloseButton.Text = "OK"
            infoCloseButton.TextColor3 = Color3.new(1, 1, 1)
            infoCloseButton.TextScaled = true
            infoCloseButton.Font = Enum.Font.GothamBold
            infoCloseButton.Parent = infoFrame

            local infoCloseCorner = Instance.new("UICorner")
            infoCloseCorner.CornerRadius = UDim.new(0, 8)
            infoCloseCorner.Parent = infoCloseButton

            infoCloseButton.MouseButton1Click:Connect(function()
                infoGui:Destroy()
            end)
        end)
    end)
end

-- Inicializar
if not authenticated then
    createLoginGUI()
end

