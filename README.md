-- MissionGenerator.lua
-- By Aura_Mage11

local MissionGenerator = {}
MissionGenerator.__index = MissionGenerator

local function GetTimeOfDay()
	local hour = tonumber(string.sub(game.Lighting:GetMinutesAfterMidnight() / 60, 1, 2))
	return hour or 12
end

local MissionTemplates = {
	["Eliminate"] = function()
		return {
			Type = "Eliminate",
			Objective = "Eliminate enemy presence in the zone.",
			EnemyCount = math.random(4, 10),
			Location = Vector3.new(math.random(-400, 400), 0, math.random(-400, 400)),
			Reward = math.random(25, 100),
			PreferredTime = "Day"
		}
	end,

	["Defend"] = function()
		return {
			Type = "Defend",
			Objective = "Hold this area against incoming waves.",
			Duration = math.random(40, 75),
			Location = Vector3.new(math.random(-300, 300), 0, math.random(-300, 300)),
			EnemyWaves = math.random(2, 5),
			Reward = math.random(50, 120),
			PreferredTime = "Night"
		}
	end,

	["Retrieve"] = function()
		return {
			Type = "Retrieve",
			Objective = "Locate and secure a lost data core.",
			Item = "Data Core",
			SpawnRadius = 250,
			Location = Vector3.new(math.random(-250, 250), 0, math.random(-250, 250)),
			Reward = 75,
			PreferredTime = "Any"
		}
	end,

	["Escort"] = function()
		return {
			Type = "Escort",
			Objective = "Escort an NPC to a safe zone.",
			TargetName = "Engineer",
			StartLocation = Vector3.new(math.random(-200, 200), 0, math.random(-200, 200)),
			EndLocation = Vector3.new(math.random(-200, 200), 0, math.random(-200, 200)),
			EnemiesPerStop = math.random(2, 4),
			Reward = 90,
			PreferredTime = "Day"
		}
	end
}

function MissionGenerator:GetRecommendedMissions()
	local hour = GetTimeOfDay()
	local currentTime = hour >= 6 and hour <= 18 and "Day" or "Night"
	local available = {}

	for name, generate in pairs(MissionTemplates) do
		local preview = generate()
		if preview.PreferredTime == currentTime or preview.PreferredTime == "Any" then
			table.insert(available, name)
		end
	end

	return available
end

function MissionGenerator:Generate()
	local options = self:GetRecommendedMissions()
	local chosen = options[math.random(1, #options)]
	local mission = MissionTemplates[chosen]()
	mission.ID = "M_" .. math.random(1000, 9999)
	mission.TimeCreated = os.time()
	mission.ScaledDifficulty = math.clamp(#game.Players:GetPlayers(), 1, 10)
	return mission
end

return MissionGenerator
