<!DOCTYPE html>
<html>
<head>
    <title>Nano Laser Control</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        body { font-family: sans-serif; text-align: center; padding-top: 50px; background: #f4f4f4; }
        .button { 
            display: inline-block; padding: 20px 40px; font-size: 20px; 
            cursor: pointer; border-radius: 10px; border: none; color: white; margin: 10px;
        }
        .on { background-color: #ff4444; }
        .off { background-color: #333; }
        .connect { background-color: #008CBA; }
        #status { margin-top: 20px; color: #666; }
    </style>
</head>
<body>
    <h1>Laser Control</h1>
    <button class="button connect" onclick="connectBLE()">1. Se connecter au Nano</button>
    <br>
    <button class="button on" onclick="writeBLE(1)">ALLUMER</button>
    <button class="button off" onclick="writeBLE(0)">ÉTEINDRE</button>
    <p id="status">Statut : Déconnecté</p>

    <script>
        let laserCharacteristic;
        const serviceUuid = "19b10000-e8f2-537e-4f6c-d104768a1214";
        const charUuid = "19b10001-e8f2-537e-4f6c-d104768a1214";

        async function connectBLE() {
            try {
                document.getElementById('status').innerText = "Recherche...";
                const device = await navigator.bluetooth.requestDevice({
                    filters: [{ name: 'NanoLaser' }],
                    optionalServices: [serviceUuid]
                });
                const server = await device.gatt.connect();
                const service = await server.getPrimaryService(serviceUuid);
                laserCharacteristic = await service.getCharacteristic(charUuid);
                document.getElementById('status').innerText = "Statut : Connecté !";
            } catch (error) {
                console.log(error);
                document.getElementById('status').innerText = "Erreur : " + error;
            }
        }

        async function writeBLE(value) {
            if (laserCharacteristic) {
                const data = new Uint8Array([value]);
                await laserCharacteristic.writeValue(data);
            } else {
                alert("Connecte-toi d'abord !");
            }
        }
    </script>
</body>
</html>
