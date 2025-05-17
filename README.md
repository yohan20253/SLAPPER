local Tool = script.Parent
local Handle = Tool:WaitForChild("Handle")
local Players = game:GetService("Players")

-- Configurações
local DANO = 30
local FORCA_EMPURRAO = 100 -- Aumente para mais empurrão

-- Função para aplicar tapa em todos (menos o atacante)
local function aplicarTapaParaTodos(atacante)
	for _, jogador in ipairs(Players:GetPlayers()) do
		if jogador ~= atacante and jogador.Character and jogador.Character:FindFirstChild("Humanoid") and jogador.Character:FindFirstChild("HumanoidRootPart") then
			local humanoid = jogador.Character.Humanoid
			humanoid:TakeDamage(DANO)

			-- Empurrão (Knockback)
			local bodyVelocity = Instance.new("BodyVelocity")
			bodyVelocity.Velocity = Vector3.new(0, 30, 0) + atacante.Character.HumanoidRootPart.CFrame.LookVector * FORCA_EMPURRAO
			bodyVelocity.MaxForce = Vector3.new(1e5, 1e5, 1e5)
			bodyVelocity.P = 1e5
			bodyVelocity.Parent = jogador.Character.HumanoidRootPart

			game:GetService("Debris"):AddItem(bodyVelocity, 0.2)
		end
	end
end

-- Detecta contato com outro jogador
Handle.Touched:Connect(function(hit)
	local character = hit:FindFirstAncestorOfClass("Model")
	if character and character:FindFirstChild("Humanoid") then
		local atacante = Tool.Parent
		if Players:GetPlayerFromCharacter(atacante) then
			aplicarTapaParaTodos(Players:GetPlayerFromCharacter(atacante))
		end
	end
end)
