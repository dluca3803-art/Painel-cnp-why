# Painel-cnp-why                        if light:IsA("SurfaceLight") then
                            light.Brightness *= 0.86
                            light.Range *= 0.9
                        end
                    end

                    if light.Color.B > light.Color.R or light.Color.G > light.Color.R then
                        light.Range *= 0.72
                        light.Brightness *= 0.9
                    end
                end
            end
        end
    end,
    function()
        if mm2LightingBackup.Technology then
            Lighting.Technology = mm2LightingBackup.Technology
            Lighting.GlobalShadows = mm2LightingBackup.GlobalShadows
            Lighting.ShadowSoftness = mm2LightingBackup.ShadowSoftness
            Lighting.Brightness = mm2LightingBackup.Brightness
            Lighting.ExposureCompensation = mm2LightingBackup.ExposureCompensation
            Lighting.EnvironmentDiffuseScale = mm2LightingBackup.EnvironmentDiffuseScale
            Lighting.EnvironmentSpecularScale = mm2LightingBackup.EnvironmentSpecularScale
            Lighting.Ambient = mm2LightingBackup.Ambient
            Lighting.OutdoorAmbient = mm2LightingBackup.OutdoorAmbient
            Lighting.FogStart = mm2LightingBackup.FogStart
            Lighting.FogEnd = mm2LightingBackup.FogEnd
        end

        if colorCorrCreated then
            colorCorrCreated:Destroy()
            colorCorrCreated = nil
        end

        for light, props in pairs(lightPropertiesBackup) do
            if light and light.Parent then
                light.Color = props.Color
                light.Shadows = props.Shadows
                light.Brightness = props.Brightness
                light.Range = props.Range
                if light:IsA("SurfaceLight") and props.Angle then
                    light.Angle = props.Angle
                end
            end
        end
        lightPropertiesBackup = {}
    end
)

-- 2. NÃ©voa Noturna
local fogBackup = {}
AddToggleScript(
    "NÃ©voa Noturna (Fog 03:00)",
    "Aplica nÃ©voa escura densa e altera o horÃ¡rio para 03:00.",
    function()
        fogBackup = {
            FogColor = Lighting.FogColor,
            FogStart = Lighting.FogStart,
            FogEnd = Lighting.FogEnd,
            ClockTime = Lighting.ClockTime
        }

        Lighting.FogColor = Color3.fromRGB(15, 15, 15)
        Lighting.FogStart = 10
        Lighting.FogEnd = 135
        Lighting.ClockTime = 3
    end,
    function()
        if fogBackup.FogColor then
            Lighting.FogColor = fogBackup.FogColor
            Lighting.FogStart = fogBackup.FogStart
            Lighting.FogEnd = fogBackup.FogEnd
            Lighting.ClockTime = fogBackup.ClockTime
        end
    end
)

-- 3. Equalizador de Ãudio
local soundAddedConnection = nil
local function aplicarAjuste(som)
    if som:IsA("Sound") then
        if som:FindFirstChild("Configurado") then return end
        
        local marcador = Instance.new("BoolValue")
        marcador.Name = "Configurado"
        marcador.Parent = som

        som.Volume = 1
        
        local eq = Instance.new("EqualizerSoundEffect")
        eq.Name = "Azure_Equalizer"
        eq.LowGain = 5
        eq.MidGain = -12
        eq.HighGain = 5
        eq.Parent = som
        
        local comp = Instance.new("CompressorSoundEffect")
        comp.Name = "Azure_Compressor"
        comp.Threshold = -20
        comp.Ratio = 4
        comp.Attack = 0.01
        comp.Parent = som
    end
end

AddToggleScript(
    "Ajuste de Ãudio Equalizado",
    "Aplica EQ e Compressor nos sons do jogo para clareza.",
    function()
        for _, v in pairs(game:GetDescendants()) do
            aplicarAjuste(v)
        end
        soundAddedConnection = game.DescendantAdded:Connect(aplicarAjuste)
    end,
    function()
        if soundAddedConnection then
            soundAddedConnection:Disconnect()
            soundAddedConnection = nil
        end
        for _, v in pairs(game:GetDescendants()) do
            if v:IsA("Sound") then
                local marcador = v:FindFirstChild("Configurado")
                if marcador then marcador:Destroy() end
                local eq = v:FindFirstChild("Azure_Equalizer")
                if eq then eq:Destroy() end
                local comp = v:FindFirstChild("Azure_Compressor")
                if comp then comp:
