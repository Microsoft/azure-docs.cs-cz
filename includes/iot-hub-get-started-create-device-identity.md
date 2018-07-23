## <a name="create-a-device-identity"></a>Vytvoření identity zařízení

V této části použijete Node.js nástroj zvaný [iothub-explorer] [ iot-hub-explorer] k vytvoření identity zařízení pro účely tohoto kurzu. V ID zařízení se rozlišují malá a velká písmena.

1. Spuštěním následujícího příkazu v prostředí příkazového řádku:

    `npm install -g iothub-explorer@latest`

1. Spusťte následující příkaz pro přihlášení k centru. Náhradní `{iot hub connection string}` jste dříve zkopírovali připojovacím řetězcem služby IoT Hub:

    `iothub-explorer login "{iot hub connection string}"`

1. Nakonec vytvořte novou identitu zařízení volá `myDeviceId` pomocí příkazu:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Poznamenejte si připojovací řetězec zařízení z výsledku. Tento připojovací řetězec zařízení používá aplikace pro zařízení pro připojení ke službě IoT Hub jako zařízení.

![][img-identity]

Odkazovat na [Začínáme se službou IoT Hub] [ lnk-getstarted] k programovému vytvoření identity zařízení.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/quickstart-send-telemetry-dotnet.md
