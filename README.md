--// [IMPERADOR] • LOADING SCREEN IMPERIAL (CHUVA VERMELHA & SENHA) //--
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Limpa carregamentos anteriores se houver
if CoreGui:FindFirstChild("FuturoReiLoading") then 
    CoreGui.FuturoReiLoading:Destroy() 
end

local LoadGui = Instance.new("ScreenGui")
LoadGui.Name = "FuturoReiLoading"
LoadGui.IgnoreGuiInset = true
LoadGui.Parent = CoreGui
LoadGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local LoadMain = Instance.new("Frame", LoadGui)
LoadMain.Size = UDim2.fromScale(1,1)
LoadMain.BackgroundColor3 = Color3.fromRGB(0,0,0)
LoadMain.BackgroundTransparency = 0.4 

-- // SOM DE CHUVA AO INICIAR // --
local RainSound = Instance.new("Sound", LoadMain)
RainSound.SoundId = "rbxassetid://1516791631"
RainSound.Volume = 0.8
RainSound.Looped = true
RainSound:Play()

-- // EFEITO DE AURA NAS BORDAS (AZUL) // --
local AuraFrame = Instance.new("Frame", LoadMain)
AuraFrame.Size = UDim2.fromScale(1, 1)
AuraFrame.BackgroundTransparency = 1
local AuraStroke = Instance.new("UIStroke", AuraFrame)
AuraStroke.Thickness = 12
AuraStroke.Color = Color3.fromRGB(0, 140, 255) -- Tema Azul original mantido
AuraStroke.Transparency = 0.4

task.spawn(function()
    while LoadGui.Parent do
        TweenService:Create(AuraStroke, TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {Transparency = 0.8, Thickness = 4}):Play()
        task.wait(3)
    end
end)

-- // EFEITO DE CHUVA FINA E VERMELHA (CAINDO DE LADO) // --
local isLoading = true
local function CreateDrop()
    if not isLoading then return end
    local drop = Instance.new("Frame", LoadMain)
    drop.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- Gotas vermelhas
    drop.BorderSizePixel = 0
    
    local sizeX = math.random(1, 2) -- Fina
    local sizeY = math.random(15, 35)
    drop.Size = UDim2.new(0, sizeX, 0, sizeY)
    
    drop.Position = UDim2.new(math.random() * 1.2 - 0.1, 0, 0, -50)

    local corner = Instance.new("UICorner", drop)
    corner.CornerRadius = UDim.new(1, 0)
    
    local endY = math.random(300, 800)
    local timeToFall = math.random(10, 20) / 10 
    
    -- Animação caindo na diagonal
    local windOffset = math.random(50, 150)
    local tween = TweenService:Create(drop, TweenInfo.new(timeToFall, Enum.EasingStyle.Linear), {
        Position = UDim2.new(drop.Position.X.Scale + (windOffset / workspace.CurrentCamera.ViewportSize.X), 0, 0, endY),
        BackgroundTransparency = 1,
        Rotation = 15
    })
    tween:Play()
    tween.Completed:Connect(function() drop:Destroy() end)
end

task.spawn(function()
    while isLoading do
        CreateDrop()
        task.wait(0.03) 
    end
end)

-- // BARRA DE CARREGAMENTO CENTRAL // --
local CenterContainer = Instance.new("Frame", LoadMain)
CenterContainer.Size = UDim2.new(0, 400, 0, 120)
CenterContainer.Position = UDim2.fromScale(0.5, 0.5)
CenterContainer.AnchorPoint = Vector2.new(0.5, 0.5)
CenterContainer.BackgroundTransparency = 1

local Logo = Instance.new("TextLabel", CenterContainer)
Logo.Size = UDim2.new(1, 0, 0, 40)
Logo.Position = UDim2.new(0, 0, 0, 10)
Logo.BackgroundTransparency = 1
Logo.Text = "🥶 IMPERADOR 🥶"
Logo.Font = Enum.Font.GothamBlack
Logo.TextSize = 38
Logo.TextColor3 = Color3.fromRGB(0, 140, 255)
Logo.TextStrokeTransparency = 0.6

local SubTitle = Instance.new("TextLabel", CenterContainer)
SubTitle.Size = UDim2.new(1, 0, 0, 20)
SubTitle.Position = UDim2.new(0, 0, 0, 50)
SubTitle.BackgroundTransparency = 1
SubTitle.Text = "PREPARANDO O RETORNO..."
SubTitle.Font = Enum.Font.GothamMedium
SubTitle.TextSize = 14
SubTitle.TextColor3 = Color3.fromRGB(200, 200, 200)

local BarBg = Instance.new("Frame", CenterContainer)
BarBg.Size = UDim2.new(0, 360, 0, 8)
BarBg.Position = UDim2.new(0.5, -180, 0, 80)
BarBg.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Instance.new("UICorner", BarBg).CornerRadius = UDim.new(1, 0)

local BarStroke = Instance.new("UIStroke", BarBg)
BarStroke.Color = Color3.fromRGB(0, 140, 255)
BarStroke.Thickness = 1
BarStroke.Transparency = 0.3

local BarFill = Instance.new("Frame", BarBg)
BarFill.Size = UDim2.new(0, 0, 1, 0)
BarFill.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
Instance.new("UICorner", BarFill).CornerRadius = UDim.new(1, 0)

local PctText = Instance.new("TextLabel", CenterContainer)
PctText.Size = UDim2.new(1, 0, 0, 20)
PctText.Position = UDim2.new(0, 0, 0, 95)
PctText.BackgroundTransparency = 1
PctText.Text = "0%"
PctText.Font = Enum.Font.GothamBold
PctText.TextSize = 13
PctText.TextColor3 = Color3.fromRGB(255, 255, 255)

-- // LÓGICA DE 0 A 100% // --
local totalTime = 4.0 
local steps = 100
local stepTime = totalTime / steps

for i = 1, 100 do
    TweenService:Create(BarFill, TweenInfo.new(stepTime, Enum.EasingStyle.Linear), {Size = UDim2.new(i/100, 0, 1, 0)}):Play()
    PctText.Text = tostring(i) .. "%"
    
    if i == 30 then SubTitle.Text = "PEDINDO PERMISSÃO PRA DEUS" end
    if i == 70 then SubTitle.Text = "ESPERANDO RESPOSTA..." end
    if i == 95 then SubTitle.Text = "PRONTO DEUS PERMITE." end
    
    task.wait(stepTime)
end

task.wait(0.6)
isLoading = false 
RainSound:Stop() -- Para o som da chuva

-- Oculta os itens de loading suavemente
for _, v in pairs(CenterContainer:GetDescendants()) do
    if v:IsA("TextLabel") then
        TweenService:Create(v, TweenInfo.new(0.6), {TextTransparency = 1, TextStrokeTransparency = 1}):Play()
    elseif v:IsA("Frame") then
        TweenService:Create(v, TweenInfo.new(0.6), {BackgroundTransparency = 1}):Play()
    elseif v:IsA("UIStroke") then
        TweenService:Create(v, TweenInfo.new(0.6), {Transparency = 1}):Play()
    end
end
task.wait(0.6)
CenterContainer.Visible = false

--=========================================--
--// DESIGN MODERNO DA TELA DE SENHA //--
--=========================================--

local LoginMain = Instance.new("Frame", LoadGui)
LoginMain.Size = UDim2.fromScale(1, 1)
LoginMain.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
LoginMain.BackgroundTransparency = 0.5 

local LoginContainer = Instance.new("Frame", LoginMain)
LoginContainer.Size = UDim2.new(0, 320, 0, 180)
LoginContainer.Position = UDim2.fromScale(0.5, 0.5)
LoginContainer.AnchorPoint = Vector2.new(0.5, 0.5)
LoginContainer.BackgroundColor3 = Color3.fromRGB(5, 5, 5) -- Fundo quase totalmente preto
LoginContainer.BackgroundTransparency = 0.2
local corner = Instance.new("UICorner", LoginContainer)
corner.CornerRadius = UDim.new(0, 8)

local stroke = Instance.new("UIStroke", LoginContainer)
stroke.Color = Color3.fromRGB(0, 140, 255)
stroke.Thickness = 1
stroke.Transparency = 0.3

local LoginTitle = Instance.new("TextLabel", LoginContainer)
LoginTitle.Size = UDim2.new(1, 0, 0, 40)
LoginTitle.Position = UDim2.new(0, 0, 0, 10)
LoginTitle.BackgroundTransparency = 1
LoginTitle.Text = "ACESSO IMPERIAL"
LoginTitle.Font = Enum.Font.GothamBlack
LoginTitle.TextSize = 18
LoginTitle.TextColor3 = Color3.fromRGB(255, 255, 255)

local PasswordInput = Instance.new("TextBox", LoginContainer)
PasswordInput.Size = UDim2.new(0.85, 0, 0, 35)
PasswordInput.Position = UDim2.new(0.075, 0, 0.4, 0)
PasswordInput.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
PasswordInput.PlaceholderText = "[SISTEMA BLOQUEADO] DIGITE O CÓDIGO DE BYPASS....."
PasswordInput.Text = ""
PasswordInput.TextColor3 = Color3.fromRGB(0, 140, 255)
PasswordInput.Font = Enum.Font.GothamMedium
PasswordInput.TextSize = 14
PasswordInput.ClearTextOnFocus = true
Instance.new("UICorner", PasswordInput).CornerRadius = UDim.new(0, 4)
Instance.new("UIStroke", PasswordInput).Color = Color3.fromRGB(40, 40, 40)

local LoginButton = Instance.new("TextButton", LoginContainer)
LoginButton.Size = UDim2.new(0.85, 0, 0, 35)
LoginButton.Position = UDim2.new(0.075, 0, 0.65, 0)
LoginButton.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
LoginButton.Text = "DESBLOQUEAR"
LoginButton.TextColor3 = Color3.fromRGB(255, 255, 255)
LoginButton.Font = Enum.Font.GothamBold
LoginButton.TextSize = 14
Instance.new("UICorner", LoginButton).CornerRadius = UDim.new(0, 4)

-- // LÓGICA DE VERIFICAÇÃO DE SENHA // --
local correctPassword = "I0000I" -- << SUA SENHA AQUI

local function checkPassword()
    if PasswordInput.Text == correctPassword then
        LoginButton.Text = "[ INICIALIZAR EXPLOIT ]"
        LoginButton.BackgroundColor3 = Color3.fromRGB(50, 255, 50)
        task.wait(0.5)
        
        TweenService:Create(LoginContainer, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.In), {Size = UDim2.new(0, 0, 0, 0), BackgroundTransparency = 1}):Play()
        TweenService:Create(LoadMain, TweenInfo.new(0.5), {BackgroundTransparency = 1}):Play()
        TweenService:Create(LoginMain, TweenInfo.new(0.5), {BackgroundTransparency = 1}):Play()
        TweenService:Create(AuraStroke, TweenInfo.new(0.5), {Transparency = 1}):Play()
        
        task.wait(0.5)
        LoadGui:Destroy()
        StartMainMenu() 
    else
        PasswordInput.Text = ""
        PasswordInput.PlaceholderText = "ACESSO NEGADO: INVASÃO DETECTADA."
        PasswordInput.PlaceholderColor3 = Color3.fromRGB(255, 50, 50)
        
        -- Efeito visual de erro suave
        local originalPos = LoginContainer.Position
        TweenService:Create(LoginContainer, TweenInfo.new(0.05), {Position = originalPos + UDim2.new(0, 5, 0, 0)}):Play()
        task.wait(0.05)
        TweenService:Create(LoginContainer, TweenInfo.new(0.05), {Position = originalPos + UDim2.new(0, -5, 0, 0)}):Play()
        task.wait(0.05)
        TweenService:Create(LoginContainer, TweenInfo.new(0.05), {Position = originalPos}):Play()
        task.wait(1)
        PasswordInput.PlaceholderText = "[SISTEMA BLOQUEADO] DIGITE O CÓDIGO DE BYPASS...."
        PasswordInput.PlaceholderColor3 = Color3.fromRGB(178, 178, 178)
    end
end

LoginButton.MouseButton1Click:Connect(checkPassword)
PasswordInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then checkPassword() end
end)

-- Efeito Hover no botão de login
LoginButton.MouseEnter:Connect(function()
    TweenService:Create(LoginButton, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(0, 160, 255)}):Play()
end)
LoginButton.MouseLeave:Connect(function()
    if LoginButton.Text ~= "[ INICIALIZAR EXPLOIT ]" then
        TweenService:Create(LoginButton, TweenInfo.new(0.2), {BackgroundColor3 = Color3.fromRGB(0, 140, 255)}):Play()
    end
end)


--=========================================--
--// INÍCIO DO SCRIPT PRINCIPAL (MENU) //--
--=========================================--
function StartMainMenu()
    local CoreGui = game:GetService("CoreGui")
    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local UserInputService = game:GetService("UserInputService")
    local TweenService = game:GetService("TweenService")
    local Lighting = game:GetService("Lighting")
    local TeleportService = game:GetService("TeleportService")
    local HttpService = game:GetService("HttpService")
    local StarterGui = game:GetService("StarterGui")
    local VirtualUser = game:GetService("VirtualUser")

    local LP = Players.LocalPlayer
    local Mouse = LP:GetMouse()
    local Camera = workspace.CurrentCamera

    if CoreGui:FindFirstChild("FuturoReiSupremo") then CoreGui.FuturoReiSupremo:Destroy() end

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "FuturoReiSupremo"
    ScreenGui.Parent = CoreGui
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    local CrownIconID = "rbxassetid://14567278958" 

    local Colors = {
        Bg = Color3.fromRGB(8, 8, 8),
        DarkBg = Color3.fromRGB(15, 15, 15),
        Accent = Color3.fromRGB(0, 140, 255),
        White = Color3.fromRGB(255, 255, 255),
        Text = Color3.fromRGB(240, 240, 240),
        Gray = Color3.fromRGB(80, 80, 80)
    }

    local function MakeDraggable(obj, dragObj)
        local dragging, dragStart, startPos
        dragObj.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                dragging = true
                dragStart = input.Position
                startPos = obj.Position
            end
        end)
        dragObj.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
        end)
        UserInputService.InputChanged:Connect(function(input)
            if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                local delta = input.Position - dragStart
                TweenService:Create(obj, TweenInfo.new(0.05), {
                    Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
                }):Play()
            end
        end)
    end

    local function CreateCorner(parent, rad)
        local c = Instance.new("UICorner", parent)
        c.CornerRadius = UDim.new(0, rad)
        return c
    end

    local function Notify(title, msg)
        pcall(function()
            StarterGui:SetCore("SendNotification", {
                Title = title;
                Text = msg;
                Icon = CrownIconID; 
                Duration = 3;
            })
        end)
    end

    local MiniButton = Instance.new("TextButton")
    MiniButton.Name = "MiniButton"
    MiniButton.Size = UDim2.new(0, 60, 0, 60)
    MiniButton.Position = UDim2.new(0.02, 0, 0.5, 0)
    MiniButton.BackgroundColor3 = Colors.Bg
    MiniButton.Text = ""
    MiniButton.Parent = ScreenGui
    CreateCorner(MiniButton, 30)
    MakeDraggable(MiniButton, MiniButton)

    local MiniStroke = Instance.new("UIStroke", MiniButton)
    MiniStroke.Color = Colors.Accent
    MiniStroke.Thickness = 3
    MiniStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

    spawn(function()
        while MiniButton.Parent do
            TweenService:Create(MiniStroke, TweenInfo.new(0.8), {Color = Colors.Accent}):Play()
            wait(0.8)
            TweenService:Create(MiniStroke, TweenInfo.new(0.8), {Color = Colors.Bg}):Play()
            wait(0.8)
        end
    end)

    local MiniIcon = Instance.new("ImageLabel", MiniButton)
    MiniIcon.Size = UDim2.new(0, 35, 0, 35)
    MiniIcon.Position = UDim2.new(0.5, -17.5, 0.5, -17.5)
    MiniIcon.BackgroundTransparency = 1
    MiniIcon.Image = CrownIconID
    MiniIcon.ImageColor3 = Colors.White 

    local Main = Instance.new("Frame")
    Main.Name = "MainFrame"
    Main.Size = UDim2.new(0, 720, 0, 480)
    Main.Position = UDim2.new(0.5, -360, 0.5, -240)
    Main.BackgroundColor3 = Colors.Bg
    Main.ClipsDescendants = true
    Main.Parent = ScreenGui
    Main.Visible = false
    CreateCorner(Main, 10)

    local MainStroke = Instance.new("UIStroke", Main)
    MainStroke.Color = Colors.Accent
    MainStroke.Thickness = 2

    local Grid = Instance.new("ImageLabel", Main)
    Grid.Size = UDim2.new(1, 0, 1, 0)
    Grid.Image = "rbxassetid://300138338"
    Grid.ImageColor3 = Colors.White
    Grid.ImageTransparency = 0.97
    Grid.TileSize = UDim2.new(0, 30, 0, 30)
    Grid.BackgroundTransparency = 1

    local isOpen = false
    MiniButton.MouseButton1Click:Connect(function()
        isOpen = not isOpen
        if isOpen then
            Main.Visible = true
            Main.Size = UDim2.new(0, 0, 0, 0)
            TweenService:Create(Main, TweenInfo.new(0.5, Enum.EasingStyle.Back), {Size = UDim2.new(0, 720, 0, 480), BackgroundTransparency = 0}):Play()
        else
            local closeTween = TweenService:Create(Main, TweenInfo.new(0.3, Enum.EasingStyle.Quad), {Size = UDim2.new(0, 0, 0, 0), BackgroundTransparency = 1})
            closeTween:Play()
            closeTween.Completed:Connect(function() Main.Visible = false end)
        end
    end)

    local Header = Instance.new("Frame", Main)
    Header.Size = UDim2.new(1, 0, 0, 55)
    Header.BackgroundColor3 = Colors.DarkBg
    CreateCorner(Header, 10)
    MakeDraggable(Main, Header)

    local Title = Instance.new("TextLabel", Header)
    Title.Text = "O REI ESTÁ DE VOLTA"
    Title.TextColor3 = Colors.White
    Title.Font = Enum.Font.GothamBlack
    Title.TextSize = 24
    Title.Size = UDim2.new(0, 400, 1, 0)
    Title.Position = UDim2.new(0, 20, 0, 0)
    Title.BackgroundTransparency = 1
    Title.TextXAlignment = Enum.TextXAlignment.Left

    spawn(function()
        while Title.Parent do
            Title.TextColor3 = Colors.White
            wait(0.5)
            Title.TextColor3 = Colors.Accent
            wait(0.5)
        end
    end)

    local Sidebar = Instance.new("Frame", Main)
    Sidebar.Size = UDim2.new(0, 180, 1, -55)
    Sidebar.Position = UDim2.new(0, 0, 0, 55)
    Sidebar.BackgroundColor3 = Colors.DarkBg

    local PageContainer = Instance.new("Frame", Main)
    PageContainer.Size = UDim2.new(1, -190, 1, -65)
    PageContainer.Position = UDim2.new(0, 190, 0, 60)
    PageContainer.BackgroundTransparency = 1

    local TabsList = Instance.new("UIListLayout", Sidebar)
    TabsList.Padding = UDim.new(0, 5)
    TabsList.HorizontalAlignment = Enum.HorizontalAlignment.Center

    local function AddTab(name, icon)
        local Btn = Instance.new("TextButton", Sidebar)
        Btn.Size = UDim2.new(0.9, 0, 0, 38)
        Btn.BackgroundColor3 = Colors.Bg
        Btn.Text = (icon or "") .. "   " .. name
        Btn.TextColor3 = Colors.Gray
        Btn.Font = Enum.Font.GothamBold
        Btn.TextSize = 13
        Btn.TextXAlignment = Enum.TextXAlignment.Left
        CreateCorner(Btn, 6)
        local pad = Instance.new("UIPadding", Btn)
        pad.PaddingLeft = UDim.new(0, 12)

        local Page = Instance.new("ScrollingFrame", PageContainer)
        Page.Size = UDim2.new(1, 0, 1, 0)
        Page.BackgroundTransparency = 1
        Page.ScrollBarThickness = 2
        Page.ScrollBarImageColor3 = Colors.Accent
        Page.Visible = false
        Instance.new("UIListLayout", Page).Padding = UDim.new(0, 8)
        
        Btn.MouseButton1Click:Connect(function()
            for _, obj in pairs(Sidebar:GetChildren()) do if obj:IsA("TextButton") then obj.BackgroundColor3 = Colors.Bg obj.TextColor3 = Colors.Gray end end
            for _, p in pairs(PageContainer:GetChildren()) do p.Visible = false end
            Page.Visible = true
            Btn.BackgroundColor3 = Colors.Accent
            Btn.TextColor3 = Colors.White
        end)
        return Page
    end

    local function AddButton(page, text, func)
        local B = Instance.new("TextButton", page)
        B.Size = UDim2.new(0.98, 0, 0, 35)
        B.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        B.Text = text
        B.TextColor3 = Colors.White
        B.Font = Enum.Font.GothamSemibold
        B.TextSize = 13
        CreateCorner(B, 5)
        local Stroke = Instance.new("UIStroke", B)
        Stroke.Color = Colors.White
        Stroke.Thickness = 1
        Stroke.Transparency = 0.8
        B.MouseButton1Click:Connect(function() task.spawn(func) end)
        return B
    end

    local function AddInputRow(page, btnText, placeholder, func)
        local Container = Instance.new("Frame", page)
        Container.Size = UDim2.new(0.98, 0, 0, 35)
        Container.BackgroundTransparency = 1
        
        local Layout = Instance.new("UIListLayout", Container)
        Layout.FillDirection = Enum.FillDirection.Horizontal
        Layout.Padding = UDim.new(0, 5)

        local B = Instance.new("TextButton", Container)
        B.Size = UDim2.new(0.7, -5, 1, 0)
        B.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        B.Text = btnText
        B.TextColor3 = Colors.White
        B.Font = Enum.Font.GothamSemibold
        B.TextSize = 13
        CreateCorner(B, 5)
        Instance.new("UIStroke", B).Color = Colors.White
        B.UIStroke.Transparency = 0.8

        local T = Instance.new("TextBox", Container)
        T.Size = UDim2.new(0.3, 0, 1, 0)
        T.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        T.PlaceholderText = placeholder
        T.Text = ""
        T.TextColor3 = Colors.White
        T.Font = Enum.Font.GothamMedium
        T.TextSize = 13
        CreateCorner(T, 5)
        Instance.new("UIStroke", T).Color = Colors.Accent
        T.UIStroke.Transparency = 0.5

        B.MouseButton1Click:Connect(function() task.spawn(function() func(T.Text) end) end)
    end

    local function AddLabel(page, text)
        local L = Instance.new("TextLabel", page)
        L.Size = UDim2.new(0.98, 0, 0, 25)
        L.BackgroundTransparency = 1
        L.Text = ":: " .. string.upper(text) .. " ::"
        L.TextColor3 = Colors.Accent
        L.Font = Enum.Font.GothamBlack
        L.TextSize = 11
    end

    -- ABA ADMIN
    local AdminTab = AddTab("LUGAR DO REI", "🌊")
    AddLabel(AdminTab, "Bibliotecas Principais")
    AddButton(AdminTab, "Infinite Yield (+400 cmds)", function() loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))() end)
    AddButton(AdminTab, "CMD-X (Admin Alternativo)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/CMD-X/CMD-X/master/Source", true))() end)
    AddButton(AdminTab, "Nameless Admin (FE)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/FilteringEnabled/NamelessAdmin/main/Source"))() end)
    AddLabel(AdminTab, "Exploração")
    AddButton(AdminTab, "Dark Dex V3", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/Babyhamsta/RBLX_Scripts/main/Universal/BypassedDarkDexV3.lua"))() end)
    AddButton(AdminTab, "Simple Spy V2 (Remotes)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/78n/SimpleSpy/main/SimpleSpySource.lua"))() end)

    -- ABA JOGADOR
    local PlayTab = AddTab("Jogador", "🌊")
    AddLabel(PlayTab, "Atributos customizáveis")

    AddButton(PlayTab, "GOD MODE (Vida Infinita)", function()
        pcall(function()
            if LP.Character and LP.Character:FindFirstChild("Humanoid") then
                local Humanoid = LP.Character.Humanoid
                Humanoid.MaxHealth = math.huge
                Humanoid.Health = math.huge
                if not LP.Character:FindFirstChild("GodField") then
                    local ff = Instance.new("ForceField", LP.Character)
                    ff.Name = "GodField"
                    ff.Visible = true 
                end
                Notify("GOD MODE", "Ativado! Você é imortal.")
                spawn(function()
                    while LP.Character and LP.Character:FindFirstChild("Humanoid") do
                        LP.Character.Humanoid.Health = LP.Character.Humanoid.MaxHealth
                        wait(0.5)
                    end
                end)
            end
        end)
    end)

    AddInputRow(PlayTab, "Definir Velocidade", "Num...", function(val) 
        local num = tonumber(val)
        if num and LP.Character and LP.Character:FindFirstChild("Humanoid") then
            LP.Character.Humanoid.WalkSpeed = num
        end
    end)

    AddInputRow(PlayTab, "Definir Pulo", "Num...", function(val) 
        local num = tonumber(val)
        if num and LP.Character and LP.Character:FindFirstChild("Humanoid") then
            LP.Character.Humanoid.JumpPower = num
        end
    end)

    AddLabel(PlayTab, "Outros")
    AddButton(PlayTab, "Anti-AFK", function() 
        LP.Idled:connect(function() VirtualUser:CaptureController() VirtualUser:ClickButton2(Vector2.new()) end)
        Notify("Anti-AFK", "Ativado.")
    end)
    AddButton(PlayTab, "Noclip (Atravessar)", function()
        RunService.Stepped:Connect(function() for _,v in pairs(LP.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end end)
        Notify("Noclip", "Ativado.")
    end)

    -- ABA TELEPORTES
    local TpTab = AddTab("Teleportes", "🌊")
    AddLabel(TpTab, "Teleporte Rápido")

    AddButton(TpTab, "Click TP (Ctrl + Click)", function()
        local Mouse = LP:GetMouse()
        Mouse.Button1Down:Connect(function()
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) and Mouse.Target then
                LP.Character:MoveTo(Mouse.Hit.p)
            end
        end)
        Notify("Teleporte", "Segure CTRL e Clique para teleportar.")
    end)

    AddInputRow(TpTab, "TP para Player", "Nome...", function(txt)
        if txt == "" then return end
        for _, p in pairs(Players:GetPlayers()) do
            if string.sub(string.lower(p.Name), 1, string.len(txt)) == string.lower(txt) or string.sub(string.lower(p.DisplayName), 1, string.len(txt)) == string.lower(txt) then
                if p.Character and p.Character:FindFirstChild("HumanoidRootPart") and LP.Character then
                    LP.Character.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame
                    Notify("Sucesso", "Teleportado para: " .. p.Name)
                    return
                end
            end
        end
        Notify("Erro", "Jogador não encontrado.")
    end)

    -- ABA COMBATE
    local CombatTab = AddTab("Combate", "🌊")
    AddLabel(CombatTab, "Vantagem Tática")

    _G.AimbotActive = false
    AddButton(CombatTab, "AIMBOT (LIGAR/DESLIGAR)", function()
        _G.AimbotActive = not _G.AimbotActive
        if _G.AimbotActive then
            Notify("Combate", "Aimbot Carregado!")
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Exunys/Aimbot-V2/main/Resources/Scripts/Main.lua"))()
        else
            if _G.Aimbot and _G.Aimbot.Settings then _G.Aimbot.Settings.Enabled = false end
            Notify("Combate", "Aimbot Desligado.")
        end
    end)

    _G.HitboxActive = false
    AddButton(CombatTab, "Hitbox Gigante + Wallhack (Toggle)", function()
        _G.HitboxActive = not _G.HitboxActive
        if _G.HitboxActive then
            Notify("Combate", "Hitbox & Visão Ativadas!")
            RunService.RenderStepped:Connect(function()
                if not _G.HitboxActive then return end
                for _, v in pairs(Players:GetPlayers()) do
                    if v ~= LP and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                        v.Character.HumanoidRootPart.Size = Vector3.new(15, 15, 15)
                        v.Character.HumanoidRootPart.Transparency = 1
                        if not v.Character:FindFirstChild("ReiHL") then
                            local hl = Instance.new("Highlight", v.Character)
                            hl.Name = "ReiHL"
                            hl.FillColor = Colors.Accent
                            hl.OutlineColor = Colors.White
                            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                        end
                    end
                end
            end)
        else
            Notify("Combate", "Desativado.")
            for _, v in pairs(Players:GetPlayers()) do
                if v.Character and v.Character:FindFirstChild("ReiHL") then v.Character.ReiHL:Destroy() end
                if v.Character and v.Character:FindFirstChild("HumanoidRootPart") then v.Character.HumanoidRootPart.Size = Vector3.new(2,2,1) end
            end
        end
    end)
    AddButton(CombatTab, "TriggerBot (Auto Click)", function() spawn(function() while wait(0.1) do if Mouse.Target and Mouse.Target.Parent:FindFirstChild("Humanoid") then mouse1click() end end end) end)

    -- ABA VISUAIS
    local VisTab = AddTab("Visuais", "🌊")
    AddLabel(VisTab, "Estilo")
    AddButton(VisTab, "Unnamed ESP", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/ic3w0lf22/Unnamed-ESP/master/Main.lua"))() end)
    AddButton(VisTab, "Fullbright (Luz)", function() Lighting.Brightness = 2 Lighting.ClockTime = 14 Lighting.GlobalShadows = false end)
    AddButton(VisTab, "Raio-X", function() for _,v in pairs(workspace:GetDescendants()) do if v:IsA("BasePart") then v.Transparency = 0.5 end end end)

    -- ABA JOGOS/HUBS
    local HubTab = AddTab("Script", "🌊")
    AddButton(HubTab, "VG Hub (Multi-Jogos)", function() loadstring(game:HttpGet('https://raw.githubusercontent.com/1201n0001/V.G-Hub/main/V.G%20Hub'))() end)
    AddButton(HubTab, "Owl Hub (FPS)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/CriShoux/OwlHub/master/OwlHub.txt"))() end)
    AddButton(HubTab, "Blox Fruits (Hoho)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/acsu123/HOHO_H/main/Loading_UI"))() end)
    AddButton(HubTab, "Brookhaven (Ice)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/IceMael7/NewIceHub/main/Brookhaven"))() end)
    AddButton(HubTab, "Doors (Black King)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/KINGHUB01/BlackKing-obf/main/Doors%20Blackking%20And%20Bobo"))() end)

    -- ABA MISC
    local MiscTab = AddTab("Misc", "🌊")
    AddLabel(MiscTab, "Música & Caos")

    AddInputRow(MiscTab, "Tocar Música (Som)", "ID do Som...", function(id)
        if id == "" then return end
        local Sound = Instance.new("Sound", workspace)
        Sound.Name = "FuturoReiSound"
        Sound.SoundId = "rbxassetid://" .. id
        Sound.Volume = 5
        Sound.Looped = true
        Sound:Play()
        Notify("Música", "Tocando ID: " .. id)
    end)

    AddButton(MiscTab, "Parar Música", function()
        for _, s in pairs(workspace:GetChildren()) do
            if s.Name == "FuturoReiSound" then s:Destroy() end
        end
    end)

    AddInputRow(MiscTab, "Chat Rainbow (RichText)", "Sua msg...", function(msg)
        if msg == "" then return end
        local function toHex(color) return string.format("#%02X%02X%02X", color.R * 255, color.G * 255, color.B * 255) end
        local coloredMsg = ""
        local freq = 0.5
        for i = 1, #msg do
            local r = math.sin(freq * i + 0) * 127 + 128
            local g = math.sin(freq * i + 2) * 127 + 128
            local b = math.sin(freq * i + 4) * 127 + 128
            local hex = toHex(Color3.fromRGB(r, g, b))
            coloredMsg = coloredMsg .. '<font color="' .. hex .. '">' .. string.sub(msg, i, i) .. '</font>'
        end
        local RS = game:GetService("ReplicatedStorage")
        if RS:FindFirstChild("DefaultChatSystemChatEvents") then
            RS.DefaultChatSystemChatEvents.SayMessageRequest:FireServer(coloredMsg, "All")
            Notify("Chat Rainbow", "Enviado!")
        else
            Notify("Erro", "Chat incompatível com script.")
        end
    end)

    AddButton(MiscTab, "Fling Universal (Derrubar)", function() loadstring(game:HttpGet("https://raw.githubusercontent.com/0Ben1/fe/main/obf_rf6iQURzs1syxgblQD9UJU8tJAmcC017Cn373866XC747y26hOOi86F87C55532c.lua.txt"))() end)
    AddButton(MiscTab, "Spinbot (Girar Rápido)", function()
        local s = Instance.new("BodyAngularVelocity", LP.Character.HumanoidRootPart)
        s.Name = "SpinBot"
        s.AngularVelocity = Vector3.new(0, 100, 0)
        s.MaxTorque = Vector3.new(0, math.huge, 0)
    end)

    AddButton(MiscTab, "Parar Spinbot", function()
        if LP.Character and LP.Character.HumanoidRootPart:FindFirstChild("SpinBot") then
            LP.Character.HumanoidRootPart.SpinBot:Destroy()
        end
    end)

    AddButton(MiscTab, "Ficar Invisível", function()
        if LP.Character then LP.Character.HumanoidRootPart.CFrame = CFrame.new(0,9e5,0) wait(0.2) LP.Character.HumanoidRootPart.Anchored = true end
        Notify("Troll", "Invisível.")
    end)

    -- ABA SERVIDOR
    local ServTab = AddTab("Servidor", "🌊")
    AddButton(ServTab, "Trocar de Server (Hop)", function() 
        local x={} for _,v in ipairs(HttpService:JSONDecode(game:HttpGetAsync("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100")).data) do if type(v)=="table" and v.maxPlayers>v.playing and v.id~=game.JobId then x[#x+1]=v.id end end
        if #x>0 then TeleportService:TeleportToPlaceInstance(game.PlaceId, x[math.random(1,#x)], LP) end
    end)
    AddButton(ServTab, "Reentrar", function() TeleportService:Teleport(game.PlaceId, LP) end)

    -- ABA TAGS
    local TagTab = AddTab("Tags", "🌊")
    AddLabel(TagTab, "Personalização de Rank")
    AddButton(TagTab, "ATIVAR TAG SUPREMA (Rainbow)", function()
        pcall(function()
            if LP.Character and LP.Character:FindFirstChild("Head") then
                if LP.Character.Head:FindFirstChild("ReiTag") then LP.Character.Head.ReiTag:Destroy() end
                local bg = Instance.new("BillboardGui", LP.Character.Head)
                bg.Name = "ReiTag"
                bg.Size = UDim2.new(0, 300, 0, 60)
                bg.StudsOffset = Vector3.new(0, 3.5, 0)
                bg.AlwaysOnTop = true
                local t = Instance.new("TextLabel", bg)
                t.Size = UDim2.new(1,0,1,0)
                t.BackgroundTransparency = 1
                t.TextSize = 22
                t.Font = Enum.Font.GothamBlack
                t.Text = "👑 IMPERADOR SUPREMO 👑"
                t.TextStrokeTransparency = 0
                t.TextStrokeColor3 = Color3.new(0,0,0)
                task.spawn(function()
                    local h = 0
                    while bg.Parent do
                        h = (h + 0.01) % 1
                        t.TextColor3 = Color3.fromHSV(h, 1, 1)
                        task.wait(0.04)
                    end
                end)
                Notify("Visual", "Tag Imperial Ativada!")
            end
        end)
    end)

    AddButton(TagTab, "Remover Tag", function()
         if LP.Character and LP.Character:FindFirstChild("Head") and LP.Character.Head:FindFirstChild("ReiTag") then
            LP.Character.Head.ReiTag:Destroy()
            Notify("Tags", "Tag removida.")
         end
    end)

    -- // SISTEMA DE FLY COM PAINEL (F1) // --
    local FlySettings = { Flying = false, Speed = 50, BodyVelocity = nil, BodyGyro = nil, Connection = nil }

    local FlyPanel = Instance.new("Frame", ScreenGui)
    FlyPanel.Size = UDim2.new(0, 200, 0, 100)
    FlyPanel.Position = UDim2.new(0.8, -20, 0.8, -20)
    FlyPanel.BackgroundColor3 = Colors.Bg
    FlyPanel.Visible = false
    CreateCorner(FlyPanel, 8)
    MakeDraggable(FlyPanel, FlyPanel)

    local FlyStroke = Instance.new("UIStroke", FlyPanel)
    FlyStroke.Color = Colors.Accent
    FlyStroke.Thickness = 2

    local FlyTitle = Instance.new("TextLabel", FlyPanel)
    FlyTitle.Size = UDim2.new(1, 0, 0, 30)
    FlyTitle.Text = "VELOCIDADE DO VOO"
    FlyTitle.TextColor3 = Colors.White
    FlyTitle.Font = Enum.Font.GothamBold
    FlyTitle.TextSize = 14
    FlyTitle.BackgroundTransparency = 1

    local SpeedInput = Instance.new("TextBox", FlyPanel)
    SpeedInput.Size = UDim2.new(0.8, 0, 0, 30)
    SpeedInput.Position = UDim2.new(0.1, 0, 0.5, 0)
    SpeedInput.BackgroundColor3 = Colors.DarkBg
    SpeedInput.Text = tostring(FlySettings.Speed)
    SpeedInput.TextColor3 = Colors.Accent
    SpeedInput.PlaceholderText = "Vel..."
    CreateCorner(SpeedInput, 5)

    SpeedInput.FocusLost:Connect(function()
        local val = tonumber(SpeedInput.Text)
        if val then
            FlySettings.Speed = val
            Notify("Fly", "Velocidade alterada para: " .. val)
        end
    end)

    local StartFly, StopFly

    function StopFly()
        FlySettings.Flying = false
        FlyPanel.Visible = false
        if FlySettings.Connection then FlySettings.Connection:Disconnect() end
        if FlySettings.BodyGyro then FlySettings.BodyGyro:Destroy() end
        if FlySettings.BodyVelocity then FlySettings.BodyVelocity:Destroy() end
        if LP.Character and LP.Character:FindFirstChild("Humanoid") then LP.Character.Humanoid.PlatformStand = false end
        Notify("FLY", "DESATIVADO.")
    end

    function StartFly()
        local char = LP.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        
        FlySettings.Flying = true
        FlyPanel.Visible = true
        
        FlySettings.BodyGyro = Instance.new("BodyGyro", char.HumanoidRootPart)
        FlySettings.BodyGyro.P = 9e4
        FlySettings.BodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        FlySettings.BodyGyro.CFrame = char.HumanoidRootPart.CFrame
        
        FlySettings.BodyVelocity = Instance.new("BodyVelocity", char.HumanoidRootPart)
        FlySettings.BodyVelocity.Velocity = Vector3.new(0, 0, 0)
        FlySettings.BodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        
        char.Humanoid.PlatformStand = true
        
        FlySettings.Connection = RunService.RenderStepped:Connect(function()
            if not FlySettings.Flying or not LP.Character or not LP.Character:FindFirstChild("HumanoidRootPart") then
                StopFly()
                return
            end
            local look = Camera.CFrame.LookVector
            local right = Camera.CFrame.RightVector
            local move = Vector3.new(0,0,0)
            
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then move = move + look end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then move = move - look end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then move = move - right end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then move = move + right end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then move = move + Vector3.new(0,1,0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then move = move - Vector3.new(0,1,0) end
            
            FlySettings.BodyVelocity.Velocity = move * FlySettings.Speed
            FlySettings.BodyGyro.CFrame = Camera.CFrame
        end)
        Notify("FLY", "ATIVADO! Use W,A,S,D + Espaço/Ctrl")
    end

    -- // SISTEMA DE TP RÁPIDO COM PAINEL (F2) // --
    local TpPanel = Instance.new("Frame", ScreenGui)
    TpPanel.Size = UDim2.new(0, 220, 0, 250)
    TpPanel.Position = UDim2.new(0.05, 0, 0.4, 0)
    TpPanel.BackgroundColor3 = Colors.Bg
    TpPanel.Visible = false
    CreateCorner(TpPanel, 8)
    MakeDraggable(TpPanel, TpPanel)

    local TpStroke = Instance.new("UIStroke", TpPanel)
    TpStroke.Color = Colors.Accent
    TpStroke.Thickness = 2

    local TpTitle = Instance.new("TextLabel", TpPanel)
    TpTitle.Size = UDim2.new(1, 0, 0, 30)
    TpTitle.Text = "TELEPORTE (F2)"
    TpTitle.TextColor3 = Colors.White
    TpTitle.Font = Enum.Font.GothamBold
    TpTitle.TextSize = 14
    TpTitle.BackgroundTransparency = 1

    local TpScroll = Instance.new("ScrollingFrame", TpPanel)
    TpScroll.Size = UDim2.new(1, -10, 1, -40)
    TpScroll.Position = UDim2.new(0, 5, 0, 35)
    TpScroll.BackgroundTransparency = 1
    TpScroll.ScrollBarThickness = 3
    TpScroll.ScrollBarImageColor3 = Colors.Accent

    local TpLayout = Instance.new("UIListLayout", TpScroll)
    TpLayout.Padding = UDim.new(0, 5)
    TpLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center

    local SavedCFrame = nil

    local function CreateTpButton(text, func)
        local btn = Instance.new("TextButton", TpScroll)
        btn.Size = UDim2.new(0.95, 0, 0, 35)
        btn.BackgroundColor3 = Colors.DarkBg
        btn.Text = text
        btn.TextColor3 = Colors.White
        btn.Font = Enum.Font.GothamSemibold
        btn.TextSize = 12
        CreateCorner(btn, 5)
        local stroke = Instance.new("UIStroke", btn)
        stroke.Color = Colors.Accent
        stroke.Transparency = 0.5
        
        btn.MouseButton1Click:Connect(function()
            if LP.Character and LP.Character:FindFirstChild("HumanoidRootPart") then
                func(LP.Character.HumanoidRootPart)
            end
        end)
    end

    CreateTpButton("Ir para o Spawn Principal", function(hrp)
        local spawnFound = false
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("SpawnLocation") then
                hrp.CFrame = v.CFrame + Vector3.new(0, 5, 0)
                spawnFound = true
                break
            end
        end
        if not spawnFound then
            hrp.CFrame = CFrame.new(0, 50, 0)
            Notify("TP", "Spawn não encontrado. TP para o centro (0,50,0).")
        else
            Notify("TP", "Teleportado para o Spawn!")
        end
    end)

    CreateTpButton("Salvar Posição Atual", function(hrp)
        SavedCFrame = hrp.CFrame
        Notify("TP", "Posição salva com sucesso!")
    end)

    CreateTpButton("Ir para Posição Salva", function(hrp)
        if SavedCFrame then
            hrp.CFrame = SavedCFrame
            Notify("TP", "Teleportado de volta!")
        else
            Notify("Erro", "Nenhuma posição salva.")
        end
    end)

    CreateTpButton("Ir para o Ponto Mais Alto", function(hrp)
        local highest = -math.huge
        local target = nil
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and v.Anchored and v.Size.Y > 1 then
                if v.Position.Y > highest and v.Position.Y < 5000 then 
                    highest = v.Position.Y
                    target = v
                end
            end
        end
        if target then
            hrp.CFrame = target.CFrame + Vector3.new(0, target.Size.Y/2 + 5, 0)
            Notify("TP", "Teleportado para o topo!")
        end
    end)

    -- // TECLAS DE ATALHO (F1 E F2) // --
    UserInputService.InputBegan:Connect(function(input, gpe)
        if gpe then return end
        
        if input.KeyCode == Enum.KeyCode.F1 then
            if FlySettings.Flying then
                StopFly()
            else
                StartFly()
            end
        elseif input.KeyCode == Enum.KeyCode.F2 then
            TpPanel.Visible = not TpPanel.Visible
            if TpPanel.Visible then
                Notify("PAINEL TP", "Aberto!")
            else
                Notify("PAINEL TP", "Fechado!")
            end
        end
    end)

    -- // NOTIFICAÇÃO FINAL // --
    Notify("IMPERADOR", "Script Supremo carregado com sucesso!")
end
