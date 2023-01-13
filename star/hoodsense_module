if getgenv().Hoodsense_Silent then return getgenv().Hoodsense_Silent end

local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local GuiService = game:GetService("GuiService")
local RunService = game:GetService("RunService")

local Heartbeat = RunService.Heartbeat
local LocalPlayer = Players.LocalPlayer
local CurrentCamera = Workspace.CurrentCamera
local Mouse = LocalPlayer:GetMouse()

local Drawingnew = Drawing.new
local Color3fromRGB = Color3.fromRGB
local Vector2new = Vector2.new
local GetGuiInset = GuiService.GetGuiInset
local Randomnew = Random.new
local mathfloor = math.floor
local CharacterAdded = LocalPlayer.CharacterAdded
local CharacterAddedWait = CharacterAdded.Wait
local WorldToViewportPoint = CurrentCamera.WorldToViewportPoint
local RaycastParamsnew = RaycastParams.new
local EnumRaycastFilterTypeBlacklist = Enum.RaycastFilterType.Blacklist
local Raycast = Workspace.Raycast
local GetPlayers = Players.GetPlayers
local Instancenew = Instance.new
local IsDescendantOf = Instancenew("Part").IsDescendantOf
local FindFirstChildWhichIsA = Instancenew("Part").FindFirstChildWhichIsA
local FindFirstChild = Instancenew("Part").FindFirstChild
local tableremove = table.remove
local tableinsert = table.insert

getgenv().Hoodsense_Silent = {
    Enabled = true,
    ShowFOV = true,
    FOV = 8,
    FOVSides = 300,
    FOVTransparency = 0.4,
    FOVThickness = 0.8,
    Prediction = 0.14,
    FOVFilled = false,
    FOVColour = Color3fromRGB(0, 147, 255),
    VisibleCheck = true,
    HitChance = 85,
    Selected = nil,
    SelectedPart = nil,
    TargetPart = {"HumanoidRootPart"},
    Ignored = {
        Teams = {
            {
                Team = LocalPlayer.Team,
                TeamColor = LocalPlayer.TeamColor,
            },
        },
        Players = {
            LocalPlayer,
        }
    }
}
local Hoodsense_Silent = getgenv().Hoodsense_Silent

local circle = Drawingnew("Circle")
circle.Transparency = Hoodsense_Silent.FOVTransparency
circle.Thickness =  Hoodsense_Silent.FOVThickness
circle.Color = Hoodsense_Silent.FOVColour  
circle.Filled = Hoodsense_Silent.FOVFilled
Hoodsense_Silent.FOVCircle = circle

function Hoodsense_Silent.UpdateFOV()
    if not (circle) then
        return
    end
    circle.Visible = Hoodsense_Silent.ShowFOV
    circle.Radius = (Hoodsense_Silent.FOV * 3)
    circle.Position = Vector2new(Mouse.X, Mouse.Y + GetGuiInset(GuiService).Y)
    circle.NumSides = Hoodsense_Silent.FOVSides
    circle.Color = Hoodsense_Silent.FOVColour
    return circle
end

local CalcChance = function(percentage)
    percentage = mathfloor(percentage)
    local chance = mathfloor(Randomnew().NextNumber(Randomnew(), 0, 1) * 100) / 100
    return chance <= percentage / 100
end

function Hoodsense_Silent.IsPartVisible(Part, PartDescendant)
    local Character = LocalPlayer.Character or CharacterAddedWait(CharacterAdded)
    local Origin = CurrentCamera.CFrame.Position
    local _, OnScreen = WorldToViewportPoint(CurrentCamera, Part.Position)
    if (OnScreen) then
        local raycastParams = RaycastParamsnew()
        raycastParams.FilterType = EnumRaycastFilterTypeBlacklist
        raycastParams.FilterDescendantsInstances = {Character, CurrentCamera}
        local Result = Raycast(Workspace, Origin, Part.Position - Origin, raycastParams)
        if (Result) then
            local PartHit = Result.Instance
            local Visible = (not PartHit or IsDescendantOf(PartHit, PartDescendant))
            return Visible
        end
    end
    return false
end

function Hoodsense_Silent.IgnorePlayer(Player)
    local Ignored = Hoodsense_Silent.Ignored
    local IgnoredPlayers = Ignored.Players
    for _, IgnoredPlayer in ipairs(IgnoredPlayers) do
        if (IgnoredPlayer == Player) then
            return false
        end
    end
    tableinsert(IgnoredPlayers, Player)
    return true
end

function Hoodsense_Silent.UnIgnorePlayer(Player)
    local Ignored = Hoodsense_Silent.Ignored
    local IgnoredPlayers = Ignored.Players
    for i, IgnoredPlayer in ipairs(IgnoredPlayers) do
        if (IgnoredPlayer == Player) then
            tableremove(IgnoredPlayers, i)
            return true
        end
    end
    return false
end

function Hoodsense_Silent.IgnoreTeam(Team, TeamColor)
    local Ignored = Hoodsense_Silent.Ignored
    local IgnoredTeams = Ignored.Teams
    for _, IgnoredTeam in ipairs(IgnoredTeams) do
        if (IgnoredTeam.Team == Team and IgnoredTeam.TeamColor == TeamColor) then
            return false
        end
    end
    tableinsert(IgnoredTeams, {Team, TeamColor})
    return true
end

function Hoodsense_Silent.UnIgnoreTeam(Team, TeamColor)
    local Ignored = Hoodsense_Silent.Ignored
    local IgnoredTeams = Ignored.Teams
    for i, IgnoredTeam in ipairs(IgnoredTeams) do
        if (IgnoredTeam.Team == Team and IgnoredTeam.TeamColor == TeamColor) then
            tableremove(IgnoredTeams, i)
            return true
        end
    end
    return false
end

function Hoodsense_Silent.TeamCheck(Toggle)
    if (Toggle) then
        return Hoodsense_Silent.IgnoreTeam(LocalPlayer.Team, LocalPlayer.TeamColor)
    end
    return Hoodsense_Silent.UnIgnoreTeam(LocalPlayer.Team, LocalPlayer.TeamColor)
end

function Hoodsense_Silent.IsIgnoredTeam(Player)
    local Ignored = Hoodsense_Silent.Ignored
    local IgnoredTeams = Ignored.Teams
    for _, IgnoredTeam in ipairs(IgnoredTeams) do
        if (Player.Team == IgnoredTeam.Team and Player.TeamColor == IgnoredTeam.TeamColor) then
            return true
        end
    end
    return false
end
function Hoodsense_Silent.IsIgnored(Player)
    local Ignored = Hoodsense_Silent.Ignored
    local IgnoredPlayers = Ignored.Players
    for _, IgnoredPlayer in ipairs(IgnoredPlayers) do
        if (typeof(IgnoredPlayer) == "number" and Player.UserId == IgnoredPlayer) then
            return true
        end
        if (IgnoredPlayer == Player) then
            return true
        end
    end
    return Hoodsense_Silent.IsIgnoredTeam(Player)
end

function Hoodsense_Silent.Raycast(Origin, Destination, UnitMultiplier)
    if (typeof(Origin) == "Vector3" and typeof(Destination) == "Vector3") then
        if (not UnitMultiplier) then UnitMultiplier = 1 end
        local Direction = (Destination - Origin).Unit * UnitMultiplier
        local Result = Raycast(Workspace, Origin, Direction)
        if (Result) then
            local Normal = Result.Normal
            local Material = Result.Material

            return Direction, Normal, Material
        end
    end
    return nil
end

function Hoodsense_Silent.Character(Player)
    return Player.Character
end

function Hoodsense_Silent.CheckHealth(Player)
    local Character = Hoodsense_Silent.Character(Player)
    local Humanoid = FindFirstChildWhichIsA(Character, "Humanoid")
    local Health = (Humanoid and Humanoid.Health or 0)
    return Health > 0
end

function Hoodsense_Silent.Check()
    return (Hoodsense_Silent.Enabled == true and Hoodsense_Silent.Selected ~= LocalPlayer and Hoodsense_Silent.SelectedPart ~= nil)
end
Hoodsense_Silent.checkSilentAim = Hoodsense_Silent.Check

function Hoodsense_Silent.GetClosestTargetPartToCursor(Character)
    local TargetParts = Hoodsense_Silent.TargetPart
    local ClosestPart = nil
    local ClosestPartPosition = nil
    local ClosestPartOnScreen = false
    local ClosestPartMagnitudeFromMouse = nil
    local ShortestDistance = 1/0
    local function CheckTargetPart(TargetPart)
        if (typeof(TargetPart) == "string") then
            TargetPart = FindFirstChild(Character, TargetPart)
        end
        if not (TargetPart) then
            return
        end
        local PartPos, onScreen = WorldToViewportPoint(CurrentCamera, TargetPart.Position)
        local GuiInset = GetGuiInset(GuiService)
        local Magnitude = (Vector2new(PartPos.X, PartPos.Y - GuiInset.Y) - Vector2new(Mouse.X, Mouse.Y)).Magnitude
        if (Magnitude < ShortestDistance) then
            ClosestPart = TargetPart
            ClosestPartPosition = PartPos
            ClosestPartOnScreen = onScreen
            ClosestPartMagnitudeFromMouse = Magnitude
            ShortestDistance = Magnitude
        end
    end
    if (typeof(TargetParts) == "string") then
        if (TargetParts == "All") then
            for _, v in ipairs(Character:GetChildren()) do
                if not (v:IsA("BasePart")) then
                    continue
                end
                CheckTargetPart(v)
            end
        else
            CheckTargetPart(TargetParts)
        end
    end
    if (typeof(TargetParts) == "table") then
        for _, TargetPartName in ipairs(TargetParts) do
            CheckTargetPart(TargetPartName)
        end
    end
    return ClosestPart, ClosestPartPosition, ClosestPartOnScreen, ClosestPartMagnitudeFromMouse
end

function Hoodsense_Silent.GetClosestPlayerToCursor()
    local TargetPart = nil
    local ClosestPlayer = nil
    local Chance = CalcChance(Hoodsense_Silent.HitChance)
    local ShortestDistance = 1/0
    if (not Chance) then
        Hoodsense_Silent.Selected = LocalPlayer
        Hoodsense_Silent.SelectedPart = nil
        return LocalPlayer
    end
    for _, Player in ipairs(GetPlayers(Players)) do
        local Character = Hoodsense_Silent.Character(Player)
        if (Hoodsense_Silent.IsIgnored(Player) == false and Character) then
            local TargetPartTemp, _, _, Magnitude = Hoodsense_Silent.GetClosestTargetPartToCursor(Character)
            if (TargetPartTemp and Hoodsense_Silent.CheckHealth(Player)) then
                if (circle.Radius > Magnitude and Magnitude < ShortestDistance) then
                    if (Hoodsense_Silent.VisibleCheck and not Hoodsense_Silent.IsPartVisible(TargetPartTemp, Character)) then continue end
                    ClosestPlayer = Player
                    ShortestDistance = Magnitude
                    TargetPart = TargetPartTemp
                end
            end
        end
    end
    Hoodsense_Silent.Selected = ClosestPlayer
    Hoodsense_Silent.SelectedPart = TargetPart
end
Heartbeat:Connect(function()
    Hoodsense_Silent.UpdateFOV()
    Hoodsense_Silent.GetClosestPlayerToCursor()
end)

return Hoodsense_Silent
