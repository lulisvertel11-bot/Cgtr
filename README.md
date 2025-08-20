-- LocalScript en StarterPlayerScripts
-- Simulador de "lag" + overlay negro con mandarina

local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

-- ====== CONFIGURACIÓN ======
local lagActivo = false
local lagDelay = 0.25      -- segundos de retraso (aumenta para más "lag")
local lagCada = 0.10       -- cada cuánto insertar el retraso (segundos)
local teclaToggle = Enum.KeyCode.L -- Tecla para activar/desactivar
local mandarinasAsset = "rbxassetid://130666465334620"
-- ===========================

-- ====== UI ======
local gui = Instance.new("ScreenGui")
gui.Name = "LagMandarinaGUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Botón flotante para alternar
local boton = Instance.new("TextButton")
boton.Name = "ToggleLagButton"
boton.Size = UDim2.new(0, 220, 0, 48)
boton.Position = UDim2.new(1, -240, 1, -68)
boton.AnchorPoint = Vector2.new(0, 0)
boton.Text = "Activar Lag + Mandarina (L)"
boton.TextScaled = true
boton.BackgroundTransparency = 0.1
boton.Parent = gui

-- Overlay negro a pantalla completa
local overlay = Instance.new("Frame")
overlay.Name = "OverlayNegro"
overlay.Size = UDim2.fromScale(1, 1)
overlay.Position = UDim2.fromScale(0, 0)
overlay.BackgroundColor3 = Color3.new(0, 0, 0)
overlay.BackgroundTransparency = 1 -- inicia oculto
overlay.Visible = true
overlay.Parent = gui

-- Imagen de mandarina centrada
local mandarina = Instance.new("ImageLabel")
mandarina.Name = "Mandarina"
mandarina.Size = UDim2.new(0, 320, 0, 320)
mandarina.AnchorPoint = Vector2.new(0.5, 0.5)
mandarina.Position = UDim2.fromScale(0.5, 0.5)
mandarina.BackgroundTransparency = 1
mandarina.Image = mandarinasAsset
mandarina.ImageTransparency = 1 -- inicia oculta
mandarina.Parent = overlay

-- Texto de estado arriba
local etiqueta = Instance.new("TextLabel")
etiqueta.Size = UDim2.new(1, 0, 0, 28)
etiqueta.Position = UDim2.new(0, 0, 0, 0)
etiqueta.BackgroundTransparency = 1
etiqueta.TextScaled = true
etiqueta.Text = "Lag Desactivado"
etiqueta.Parent = gui
-- =================

-- Funciones para mostrar/ocultar overlay
local function mostrarOverlay()
	overlay.BackgroundTransparency = 0
	mandarina.ImageTransparency = 0
end

local function ocultarOverlay()
	overlay.BackgroundTransparency = 1
	mandarina.ImageTransparency = 1
end

local function actualizarUI()
	if lagActivo then
		boton.Text = "Desactivar Lag + Mandarina (L)"
		etiqueta.Text = "Lag Activado"
		mostrarOverlay()
	else
		boton.Text = "Activar Lag + Mandarina (L)"
		etiqueta.Text = "Lag Desactivado"
		ocultarOverlay()
	end
end
actualizarUI()

-- Toggle por botón
boton.MouseButton1Click:Connect(function()
	lagActivo = not lagActivo
	actualizarUI()
end)

-- Toggle por tecla (L)
UserInputService.InputBegan:Connect(function(input, gp)
	if gp then return end
	if input.KeyCode == teclaToggle then
		lagActivo = not lagActivo
		actualizarUI()
	end
end)

-- Simulación de lag: inserta esperas periódicas en el bucle del cliente
local ultimo = os.clock()
RunService.RenderStepped:Connect(function()
	if not lagActivo then return end
	local ahora = os.clock()
	if (ahora - ultimo) >= lagCada then
		ultimo = ahora
		task.wait(lagDelay) -- retraso artificial
	end
end)

-- Opcional: reducir FPS simulando "stutter" extra en Heartbeat
RunService.Heartbeat:Connect(function()
	if lagActivo then
		-- pequeñas pausas adicionales (ajusta si quieres menos "stutter")
		-- Descomenta si deseas un efecto más fuerte:
		-- task.wait(lagDelay * 0.4)
	end
end)
