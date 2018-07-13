## <a name="configure-the-nodejs-simulated-device"></a>Konfigurace simulovaného zařízení Node.js
1. Na řídicím panelu vzdáleného monitorování, klikněte na tlačítko **+ přidat zařízení** a potom přidat *vlastní zařízení*. Poznamenejte služby IoT Hub, název hostitele, id zařízení a klíč zařízení. Budete je potřebovat později v tomto kurzu při přípravě remote_monitoring.js zařízení klientská aplikace.
2. Zkontrolujte, že Node.js verze 0.12.x nebo novější nainstalován na vývojovém počítači. Spustit `node --version` z příkazového řádku nebo v prostředí, pokud chcete zkontrolovat verzi. Informace o použití Správce balíčků a nainstalujte Node.js v Linuxu najdete v tématu [instalace Node.js přes Správce balíčků][node-linux].
3. Pokud jste nainstalovali Node.js, naklonujte na nejnovější verzi [azure-iot-sdk uzel] [ lnk-github-repo] úložiště do svého vývojového počítače. Vždy používat **hlavní** větev pro nejnovější verzi knihovny a ukázky.
4. Z místní kopie [azure-iot-sdk uzel] [ lnk-github-repo] úložiště, zkopírujte následující dva soubory ze složky node/zařízení/samples k prázdné složce na vývojovém počítači:
   
   * Packages.JSON
   * remote_monitoring.js
5. Otevřete soubor remote_monitoring.js a hledejte následující definice proměnné:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Nahraďte **[připojovací řetězec služby IoT Hub zařízení]** vaším připojovacím řetězcem zařízení. Použijte hodnoty pro název hostitele služby IoT Hub, id zařízení a klíč zařízení, který jste si poznamenali z v kroku 1. Připojovací řetězec zařízení má následující formát:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Pokud je název hostitele vaší služby IoT Hub **contoso** a id vašeho zařízení je **mojezařízení**, aby váš připojovací řetězec vypadá jako následující fragment kódu:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Uložte soubor. Spusťte následující příkazy v prostředí nebo v příkazovém řádku ve složce, která obsahuje tyto soubory na nainstalujte potřebné balíčky a spusťte ukázkové aplikace:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Sledujte dynamické telemetrie v akci
Na řídicím panelu zobrazuje teploty a vlhkosti telemetrická data z existujících Simulovaná zařízení:

![Výchozí řídicí panel][image1]

Pokud vyberete Node.js simulované zařízení, které jste spustili v předchozí části, se zobrazí teploty, vlhkosti a externí teplotní telemetrie:

![Přidat na řídicí panel venkovní teplota][image2]

Řešení vzdáleného monitorování automaticky zjišťuje typ telemetrie další venkovní teplotu a přidá jej do grafu na řídicím panelu.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png