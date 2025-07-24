local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

local KillTargetEvent = ReplicatedStorage:WaitForChild("KillTargetEvent")

-- 建立螢幕 GUI
local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
screenGui.Name = "KillUI"

-- 玩家列表與選中索引
local playerList = {}
local currentIndex = 1

local function updatePlayerList()
	playerList = {}
	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= LocalPlayer then
			table.insert(playerList, p)
		end
	end
	if currentIndex > #playerList then
		currentIndex = 1
	end
end

-- UI：目標顯示區
local targetFrame = Instance.new("Frame", screenGui)
targetFrame.Size = UDim2.new(0, 200, 0, 50)
targetFrame.Position = UDim2.new(0.5, -100, 0.8, 0)
targetFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

-- 左箭頭
local leftBtn = Instance.new("TextButton", targetFrame)
leftBtn.Size = UDim2.new(0, 50, 1, 0)
leftBtn.Position = UDim2.new(0, 0, 0, 0)
leftBtn.Text = "<"

-- 名稱顯示
local nameLabel = Instance.new("TextLabel", targetFrame)
nameLabel.Size = UDim2.new(0, 100, 1, 0)
nameLabel.Position = UDim2.new(0, 50, 0, 0)
nameLabel.TextColor3 = Color3.new(1, 1, 1)
nameLabel.BackgroundTransparency = 1
nameLabel.Text = "None"
nameLabel.TextScaled = true

-- 右箭頭
local rightBtn = Instance.new("TextButton", targetFrame)
rightBtn.Size = UDim2.new(0, 50, 1, 0)
rightBtn.Position = UDim2.new(0, 150, 0, 0)
rightBtn.Text = ">"

-- UI：Kill 按鈕
local killButton = Instance.new("TextButton", screenGui)
killButton.Size = UDim2.new(0, 100, 0, 100)
killButton.Position = UDim2.new(0.5, -50, 0.65, 0)
killButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
killButton.Text = "Kill"
killButton.TextColor3 = Color3.new(1, 1, 1)
killButton.TextScaled = true
killButton.AutoButtonColor = true
killButton.ClipsDescendants = true
killButton.BorderSizePixel = 0

-- 圓形化
local corner = Instance.new("UICorner", killButton)
corner.CornerRadius = UDim.new(1, 0)

-- 事件處理
leftBtn.MouseButton1Click:Connect(function()
	if #playerList > 0 then
		currentIndex = currentIndex - 1
		if currentIndex < 1 then
			currentIndex = #playerList
		end
		nameLabel.Text = playerList[currentIndex].Name
	end
end)

rightBtn.MouseButton1Click:Connect(function()
	if #playerList > 0 then
		currentIndex = currentIndex + 1
		if currentIndex > #playerList then
			currentIndex = 1
		end
		nameLabel.Text = playerList[currentIndex].Name
	end
end)

killButton.MouseButton1Click:Connect(function()
	if playerList[currentIndex] then
		KillTargetEvent:FireServer(playerList[currentIndex])
	end
end)

-- 初始與更新
updatePlayerList()
if playerList[currentIndex] then
	nameLabel.Text = playerList[currentIndex].Name
end

-- 玩家變動時更新
Players.PlayerAdded:Connect(function()
	updatePlayerList()
end)

Players.PlayerRemoving:Connect(function()
	updatePlayerList()
end)