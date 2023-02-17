repeat
    wait()
until game:IsLoaded()
local gm = getrawmetatable(game)
setreadonly(gm, false)
local namecall = gm.__namecall
gm.__namecall =
    newcclosure(
    function(self, ...)
        local args = {...}
        if not checkcaller() and getnamecallmethod() == "FireServer" and tostring(self) == "MainEvent" then
            if tostring(getcallingscript()) ~= "Framework" then
                return
            end
        end
        if not checkcaller() and getnamecallmethod() == "Kick" then
            return
        end
        return namecall(self, unpack(args))
    end
)
