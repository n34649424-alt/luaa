-- Fishing Hub (Mobile Supported & Thai Language)
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Player = Players.LocalPlayer

-- ลบ UI เก่าออกป้องกันการทับซ้อน
local oldUI = Player.PlayerGui:FindFirstChild("MobileFishingHub")
if oldUI then oldUI:Destroy() end

local Settings = {
    AutoSkip = false,
    AutoFarm = false,
    Keybind = Enum.KeyCode.Q
}

-- ==================== 1. สร้าง UI หลัก ====================
local SG = Instance.new("ScreenGui")
SG.Name = "MobileFishingHub"
SG.ResetOnSpawn = false
SG.Parent = Player.PlayerGui

-- ปุ่มเปิด/ปิด UI (สำหรับมือถือ)
local OpenBtn = Instance.new("ImageButton")
OpenBtn.Parent = SG
OpenBtn.Position = UDim2.new(0.02, 0, 0.2, 0)
OpenBtn.Size = UDim2.new(0, 50, 0, 50)
OpenBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
OpenBtn.Image = "rbxassetid://6031280882" -- ไอคอนตกปลา
local OpenCorner = Instance.new("UICorner")
OpenCorner.CornerRadius = UDim.new(1, 0)
OpenCorner.Parent = OpenBtn

-- หน้าต่างเมนู
local MainFrame = Instance.new("Frame")
MainFrame.Parent = SG
MainFrame.Position = UDim2.new(0.5, -125, 0.3, 0)
MainFrame.Size = UDim2.new(0, 250, 0, 180)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.BackgroundTransparency = 0.2 -- โปร่งใสเหมือนในคลิป
MainFrame.Visible = false
MainFrame.Active = true
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

-- ฟังก์ชันทำให้ UI ลากด้วยนิ้วบนมือถือได้
local function MakeDraggable(frame)
    local dragging, dragInput, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end
MakeDraggable(MainFrame)

-- ==================== 2. สร้างปุ่มต่างๆ (ถอดแบบจากคลิป) ====================
-- ปุ่มกดข้ามแมนนวล (ทำหน้าที่เหมือนกด Q)
local ManualSkipBtn = Instance.new("TextButton")
ManualSkipBtn.Parent = MainFrame
ManualSkipBtn.Position = UDim2.new(0, 15, 0, 15)
ManualSkipBtn.Size = UDim2.new(0, 220, 0, 40)
ManualSkipBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ManualSkipBtn.BackgroundTransparency = 0.5
ManualSkipBtn.Text = "กดเพื่อข้ามมินิเกม [Q]"
ManualSkipBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ManualSkipBtn.Font = Enum.Font.SourceSansBold
ManualSkipBtn.TextSize = 16
Instance.new("UICorner", ManualSkipBtn).CornerRadius = UDim.new(0, 6)

-- ปุ่ม Auto Skip
local AutoSkipBtn = Instance.new("TextButton")
AutoSkipBtn.Parent = MainFrame
AutoSkipBtn.Position = UDim2.new(0, 15, 0, 65)
AutoSkipBtn.Size = UDim2.new(0, 220, 0, 40)
AutoSkipBtn.BackgroundColor3 = Color3.fromRGB(180, 20, 20)
AutoSkipBtn.Text = "ข้ามมินิเกมอัตโนมัติ: ปิด"
AutoSkipBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoSkipBtn.Font = Enum.Font.SourceSansBold
AutoSkipBtn.TextSize = 16
Instance.new("UICorner", AutoSkipBtn).CornerRadius = UDim.new(0, 6)

-- ปุ่ม Auto Farm
local AutoFarmBtn = Instance.new("TextButton")
AutoFarmBtn.Parent = MainFrame
AutoFarmBtn.Position = UDim2.new(0, 15, 0, 115)
AutoFarmBtn.Size = UDim2.new(0, 220, 0, 40)
AutoFarmBtn.BackgroundColor3 = Color3.fromRGB(180, 20, 20)
AutoFarmBtn.Text = "ออโต้ฟาร์มตกปลา: ปิด"
AutoFarmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoFarmBtn.Font = Enum.Font.SourceSansBold
AutoFarmBtn.TextSize = 16
Instance.new("UICorner", AutoFarmBtn).CornerRadius = UDim.new(0, 6)

-- ==================== 3. ระบบการทำงาน (Core Logic) ====================

-- เปิด/ปิด UI
OpenBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- ฟังก์ชันข้ามมินิเกม (Bypass)
local function ExecuteBypass()
    task.spawn(function()
        pcall(function()
            -- ค้นหาและจำลองการคลิกหน้าจอมินิเกม (สำหรับมือถือ)
            for _, gui in pairs(Player.PlayerGui:GetDescendants()) do
                if (gui:IsA("TextButton") or gui:IsA("ImageButton")) and gui.Visible then
                    local name = gui.Name:lower()
                    if name:match("fish") or name:match("minigame") or name:match("catch") or name:match("slider") then
                        local pos = gui.AbsolutePosition
                        local size = gui.AbsoluteSize
                        if size.X > 5 then
                            VirtualInputManager:SendMouseButtonEvent(pos.X + size.X/2, pos.Y + size.Y/2, 0, true, game, 0)
                            VirtualInputManager:SendMouseButtonEvent(pos.X + size.X/2, pos.Y + size.Y/2, 0, false, game, 0)
                        end
                    end
                end
            end
            
            -- ยิง RemoteEvent เพื่อเคลียร์มินิเกมทันที
            for _, remote in pairs(game:GetDescendants()) do
                if remote:IsA("RemoteEvent") then
                    local rName = remote.Name:lower()
                    if rName:match("fish") or rName:match("hook") or rName:match("minigame") then
                        remote:FireServer(true)
                        remote:FireServer("Success")
                        remote:FireServer("Completed")
                    end
                end
            end
        end)
    end)
end

-- การกดปุ่มข้าม (มือถือแตะปุ่ม / คอมกด Q)
ManualSkipBtn.MouseButton1Click:Connect(ExecuteBypass)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Settings.Keybind then
        ExecuteBypass()
    end
end)

-- เปิด/ปิด Auto Skip
AutoSkipBtn.MouseButton1Click:Connect(function()
    Settings.AutoSkip = not Settings.AutoSkip
    if Settings.AutoSkip then
        AutoSkipBtn.Text = "ข้ามมินิเกมอัตโนมัติ: เปิด"
        AutoSkipBtn.BackgroundColor3 = Color3.fromRGB(0, 160, 80)
    else
        AutoSkipBtn.Text = "ข้ามมินิเกมอัตโนมัติ: ปิด"
        AutoSkipBtn.BackgroundColor3 = Color3.fromRGB(180, 20, 20)
    end
end)

-- เปิด/ปิด Auto Farm
AutoFarmBtn.MouseButton1Click:Connect(function()
    Settings.AutoFarm = not Settings.AutoFarm
    if Settings.AutoFarm then
        AutoFarmBtn.Text = "ออโต้ฟาร์มตกปลา: เปิด"
        AutoFarmBtn.BackgroundColor3 = Color3.fromRGB(0, 160, 80)
    else
        AutoFarmBtn.Text = "ออโต้ฟาร์มตกปลา: ปิด"
        AutoFarmBtn.BackgroundColor3 = Color3.fromRGB(180, 20, 20)
    end
end)

-- ลูปการทำงานอัตโนมัติ
task.spawn(function()
