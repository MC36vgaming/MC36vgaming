local Player = game:GetService("Players").LocalPlayer
local Mouse = Player:GetMouse()
local UserInputService = game:GetService("UserInputService")

-- Create GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "AutoClickerGUI"
ScreenGui.Parent = Player.PlayerGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.5, -100, 0.5, -75)
Frame.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
Frame.BorderSizePixel = 0
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Text = "Auto Clicker"
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Parent = Frame

local ToggleButton = Instance.new("TextButton")
ToggleButton.Text = "Start Auto Clicker"
ToggleButton.Size = UDim2.new(0.8, 0, 0, 30)
ToggleButton.Position = UDim2.new(0.1, 0, 0.3, 0)
ToggleButton.BackgroundColor3 = Color3.new(0.4, 0.4, 0.4)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Parent = Frame

local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Text = "Click Speed (ms): 200"
SpeedLabel.Size = UDim2.new(0.8, 0, 0, 20)
SpeedLabel.Position = UDim2.new(0.1, 0, 0.6, 0)
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.TextColor3 = Color3.new(1, 1, 1)
SpeedLabel.Parent = Frame

local SpeedSlider = Instance.new("Slider")
SpeedSlider.Size = UDim2.new(0.8, 0, 0, 20)
SpeedSlider.Position = UDim2.new(0.1, 0, 0.75, 0)
SpeedSlider.MinValue = 50
SpeedSlider.MaxValue = 1000
SpeedSlider.Value = 200
SpeedSlider.Parent = Frame

-- Auto Clicker Variables
local autoClicking = false
local clickInterval = 0.2
local connection

SpeedSlider.Changed:Connect(function()
    clickInterval = SpeedSlider.Value / 1000
    SpeedLabel.Text = "Click Speed (ms): " .. math.floor(SpeedSlider.Value)
end)

local function autoClick()
    while autoClicking and task.wait(clickInterval) do
        if Player.Character then
            local tool = Player.Character:FindFirstChildOfClass("Tool")
            if tool then
                tool:Activate()
            end
        end
    end
end

ToggleButton.MouseButton1Click:Connect(function()
    autoClicking = not autoClicking
    if autoClicking then
        ToggleButton.Text = "Stop Auto Clicker"
        ToggleButton.BackgroundColor3 = Color3.new(0.8, 0.2, 0.2)
        autoClick()
    else
        ToggleButton.Text = "Start Auto Clicker"
        ToggleButton.BackgroundColor3 = Color3.new(0.4, 0.4, 0.4)
    end
end)

-- Make GUI draggable
local dragging
local dragInput
local dragStart
local startPos

local function update(input)
    local delta = input.Position - dragStart
    Frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

Frame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Frame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

Frame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)
