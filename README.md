
-- MENU DE MUSICA GLOBAL
-- Colocar en StarterPlayerScripts

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local jugador = Players.LocalPlayer

local evento = ReplicatedStorage:WaitForChild("ControlMusicaGlobal")
local consulta = ReplicatedStorage:WaitForChild("ConsultarAdminMusica")

-- COMPROBAR ADMINISTRADOR
local esAdmin = consulta:InvokeServer()

if not esAdmin then
    return
end

-- CREAR INTERFAZ
local gui = Instance.new("ScreenGui")
gui.Name = "MenuMusicaGlobal"
gui.ResetOnSpawn = false
gui.Parent = jugador:WaitForChild("PlayerGui")

local abrir = Instance.new("TextButton")
abrir.Size = UDim2.new(0, 110, 0, 40)
abrir.Position = UDim2.new(0, 15, 0.5, -20)
abrir.BackgroundColor3 = Color3.fromRGB(30, 100, 180)
abrir.Text = "🎵 MUSICA"
abrir.TextColor3 = Color3.new(1,1,1)
abrir.TextSize = 16
abrir.Parent = gui

local marco = Instance.new("Frame")
marco.Size = UDim2.new(0, 310, 0, 300)
marco.Position = UDim2.new(0.5, -155, 0.5, -150)
marco.BackgroundColor3 = Color3.fromRGB(25, 28, 38)
marco.Visible = false
marco.Parent = gui

local esquina = Instance.new("UICorner")
esquina.CornerRadius = UDim.new(0, 12)
esquina.Parent = marco

local function crearTexto(nombre, texto, posicion, tamano)
    local objeto = Instance.new("TextLabel")
    objeto.Name = nombre
    objeto.Size = tamano
    objeto.Position = posicion
    objeto.BackgroundTransparency = 1
    objeto.Text = texto
    objeto.TextColor3 = Color3.new(1,1,1)
    objeto.TextSize = 16
    objeto.Parent = marco
    return objeto
end

local function crearBoton(nombre, texto, posicion, tamano, color)
    local boton = Instance.new("TextButton")
    boton.Name = nombre
    boton.Size = tamano
    boton.Position = posicion
    boton.BackgroundColor3 = color
    boton.Text = texto
    boton.TextColor3 = Color3.new(1,1,1)
    boton.TextSize = 15
    boton.Parent = marco

    local redondeado = Instance.new("UICorner")
    redondeado.CornerRadius = UDim.new(0, 7)
    redondeado.Parent = boton

    return boton
end

crearTexto(
    "Titulo",
    "REPRODUCTOR GLOBAL",
    UDim2.new(0, 0, 0, 10),
    UDim2.new(1, 0, 0, 30)
)

local cerrar = crearBoton(
    "Cerrar",
    "X",
    UDim2.new(1, -38, 0, 5),
    UDim2.new(0, 30, 0, 30),
    Color3.fromRGB(180, 45, 45)
)

local caja = Instance.new("TextBox")
caja.Name = "IDMusica"
caja.Size = UDim2.new(0, 270, 0, 40)
caja.Position = UDim2.new(0, 20, 0, 55)
caja.BackgroundColor3 = Color3.fromRGB(48, 52, 65)
caja.PlaceholderText = "Introduce el ID de la musica"
caja.Text = ""
caja.TextColor3 = Color3.new(1,1,1)
caja.PlaceholderColor3 = Color3.fromRGB(180,180,180)
caja.TextSize = 15
caja.ClearTextOnFocus = false
caja.Parent = marco

local reproducir = crearBoton(
    "Reproducir",
    "▶ REPRODUCIR",
    UDim2.new(0, 20, 0, 110),
    UDim2.new(0, 270, 0, 38),
    Color3.fromRGB(35, 145, 75)
)

local pausar = crearBoton(
    "Pausar",
    "⏸ PAUSAR",
    UDim2.new(0, 20, 0, 157),
    UDim2.new(0, 130, 0, 38),
    Color3.fromRGB(190, 130, 30)
)

local continuar = crearBoton(
    "Continuar",
    "▶ CONTINUAR",
    UDim2.new(0, 160, 0, 157),
    UDim2.new(0, 130, 0, 38),
    Color3.fromRGB(35, 115, 180)
)

local detener = crearBoton(
    "Detener",
    "■ DETENER",
    UDim2.new(0, 20, 0, 204),
    UDim2.new(0, 270, 0, 35),
    Color3.fromRGB(175, 45, 45)
)

crearTexto(
    "TextoVolumen",
    "VOLUMEN",
    UDim2.new(0, 20, 0, 242),
    UDim2.new(0, 85, 0, 25)
)

local volumen = Instance.new("TextBox")
volumen.Name = "Volumen"
volumen.Size = UDim2.new(0, 90, 0, 28)
volumen.Position = UDim2.new(0, 115, 0, 240)
volumen.BackgroundColor3 = Color3.fromRGB(48,52,65)
volumen.Text = "50"
volumen.TextColor3 = Color3.new(1,1,1)
volumen.TextSize = 15
volumen.Parent = marco

local aplicar = crearBoton(
    "AplicarVolumen",
    "APLICAR",
    UDim2.new(0, 215, 0, 240),
    UDim2.new(0, 75, 0, 28),
    Color3.fromRGB(35, 100, 170)
)

local estado = crearTexto(
    "Estado",
    "Estado: listo",
    UDim2.new(0, 10, 0, 270),
    UDim2.new(1, -20, 0, 22)
)

-- ABRIR Y CERRAR
abrir.MouseButton1Click:Connect(function()
    marco.Visible = not marco.Visible
end)

cerrar.MouseButton1Click:Connect(function()
    marco.Visible = false
end)

-- CONTROLES
reproducir.MouseButton1Click:Connect(function()
    if caja.Text ~= "" then
        evento:FireServer("Reproducir", caja.Text)
    end
end)

pausar.MouseButton1Click:Connect(function()
    evento:FireServer("Pausar")
end)

continuar.MouseButton1Click:Connect(function()
    evento:FireServer("Continuar")
end)

detener.MouseButton1Click:Connect(function()
    evento:FireServer("Detener")
end)

aplicar.MouseButton1Click:Connect(function()
    local numero = tonumber(volumen.Text)

    if numero then
        numero = math.clamp(numero, 0, 100)
        volumen.Text = tostring(numero)
        evento:FireServer("Volumen", numero / 100)
    end
end)

-- ACTUALIZAR ESTADO
evento.OnClientEvent:Connect(function(mensaje)
    estado.Text = "Estado: " .. tostring(mensaje)
end)
