---
title: Vytvoření a otestování zařízení IoT technologie Plug and Play Preview | Microsoft Docs
description: Jako vývojář zařízení se dozvíte, jak pomocí VS Code vytvořit a otestovat nový model schopností zařízení pro zařízení technologie Plug and Play ve verzi Preview.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719717"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Kurz: vytvoření a otestování modelu schopností zařízení pomocí Visual Studio Code

V tomto kurzu se dozvíte, jak jako vývojář zařízení použít Visual Studio Code k vytvoření _modelu schopností zařízení_. Model můžete použít k vygenerování kostry kódu pro spuštění na zařízení, které se připojuje k instanci Azure IoT Hub v cloudu.

V části v tomto kurzu, který popisuje, jak sestavit generovaný kostru kódu, se předpokládá, že používáte systém Windows.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření modelu schopností zařízení
> * Generování kódu kostry zařízení z modelu
> * Implementace zástupných procedur ve vygenerovaném kódu
> * Spusťte kód pro otestování interakcí se službou IoT Hub.

## <a name="prerequisites"></a>Požadavky

Pokud chcete pracovat s modelem schopností zařízení v tomto kurzu, budete potřebovat:

* [Visual Studio Code](https://code.visualstudio.com/download): vs Code je k dispozici pro více platforem.
* Sady [nástrojů Azure IoT pro rozšíření vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) . K instalaci balíčku rozšíření v VS Code použijte následující postup:

    1. V VS Code vyberte kartu **rozšíření** .
    1. Vyhledejte **nástroje Azure IoT Tools**.
    1. Vyberte **Install** (Nainstalovat).

K sestavení vygenerovaného kódu jazyka C v systému Windows v tomto kurzu budete potřebovat:

* [Nástroje pro sestavení pro sadu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) pomocí  **C++ nástrojů sestavení** a úloh **komponent správce balíčků NuGet** . Nebo pokud už máte [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 nebo 2015 se stejnými úlohami.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

K otestování kódu zařízení v tomto kurzu budete potřebovat:

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modelování zařízení

K vytvoření modelu schopností zařízení používáte _digitální ovládací jazyk s definicemi_ . Model se obvykle skládá z více definičních souborů _rozhraní_ a jednoho souboru modelu. **Nástroje Azure IoT Tools for vs Code** obsahují nástroje, které vám pomůžou vytvořit a upravit tyto soubory JSON.

### <a name="create-the-interface-file"></a>Vytvoření souboru rozhraní

Pokud chcete vytvořit soubor rozhraní, který definuje možnosti vašeho zařízení IoT v VS Code:

1. Vytvořte složku s názvem **devicemodel**.

1. Spusťte VS Code a stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a pak vyberte příkaz **& IoT Plug and Play: Create Interface** .

1. Vyhledejte a vyberte složku **devicemodel** , kterou jste vytvořili.

1. Pak jako název rozhraní zadejte **EnvironmentalSensor** a stiskněte klávesu **ENTER**. VS Code vytvoří vzorový soubor rozhraní s názvem **EnvironmentalSensor. Interface. JSON**.

1. Nahraďte obsah tohoto souboru následující JSON a nahraďte `{your name}` v poli `@id` s jedinečnou hodnotou. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace najdete v tématu [Formát digitálního Nevlákenového identifikátoru](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). Aby bylo možné uložit rozhraní do úložiště, musí být ID rozhraní jedinečné:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Toto rozhraní definuje vlastnosti zařízení, jako je například **název zákazníka**, typy telemetrie, jako je například **teplota**a příkazy, jako je například **turnon**.

1. Přidejte na konec tohoto souboru rozhraní funkci příkazového řádku s názvem **Blink** . Nezapomeňte před přidáním příkazu přidat čárku. Zkuste zadat definici, abyste viděli, jak vám IntelliSense, automatické dokončování a ověřování pomůžou upravit definici rozhraní:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Uložte soubor.

### <a name="create-the-model-file"></a>Vytvoření souboru modelu

Soubor modelu určuje rozhraní, které vaše zařízení technologie Plug and Play IoT implementuje. V modelu jsou obvykle alespoň dvě rozhraní – jedna nebo víc, která definují konkrétní možnosti vašeho zařízení, a standardní rozhraní, které musí všechna zařízení technologie Plug and Play IoT implementovat.

Chcete-li vytvořit soubor modelu, který určuje rozhraní, které vaše zařízení IoT technologie Plug and Play implementuje v VS Code:

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a potom vyberte **& IoT Plug and Play: Create Capability model** . Pak jako název modelu zadejte **SensorboxModel** . VS Code vytvoří vzorový soubor rozhraní s názvem **SensorboxModel. capabilitymodel. JSON**.

1. Nahraďte obsah tohoto souboru následující JSON a nahraďte `{your name}` v poli `@id` a v rozhraní `EnvironmentalSensor` se stejnou hodnotou, jakou jste použili v souboru **EnvironmentalSensor. Interface. JSON** . Aby bylo možné uložit rozhraní do úložiště, musí být ID rozhraní jedinečné:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Model definuje zařízení, které implementuje rozhraní **EnvironmentalSensor** a standardní rozhraní **DeviceInformation** .

1. Uložte soubor.

### <a name="download-the-deviceinformation-interface"></a>Stažení rozhraní DeviceInformation

Předtím, než můžete z modelu vygenerovat kostru kódu, je nutné vytvořit místní kopii **DeviceInformation** z *úložiště veřejného modelu*. Úložiště veřejného modelu již obsahuje rozhraní **DeviceInformation** .

Chcete-li stáhnout rozhraní **DeviceInformation** z úložiště veřejného modelu pomocí vs Code:

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play**, vyberte příkaz **Otevřít úložiště modelu** a pak vyberte **Otevřít úložiště veřejného modelu**.

1. Vyberte **rozhraní**a pak vyberte rozhraní informací o zařízení s ID `urn:azureiot:DeviceManagement:DeviceInformation:1`a pak vyberte **Stáhnout**.

Teď máte tři soubory, které tvoří model schopností zařízení:

* urn_azureiot_DeviceManagement_DeviceInformation_1. Interface. JSON
* EnvironmentalSensor. Interface. JSON
* SensorboxModel. capabilitymodel. JSON

## <a name="publish-the-model"></a>Publikování modelu

Aby mohl nástroj Azure IoT Explorer číst model schopností zařízení, je nutné ho publikovat v úložišti vaší společnosti. K publikování z VS Code potřebujete připojovací řetězec pro úložiště společnosti:

1. Přejděte na [portál Azure Certified for IoT](https://aka.ms/ACFI).

1. Pomocí svého _pracovního účtu_ Microsoft se přihlaste k portálu.

1. Vyberte **úložiště společnosti** a pak **připojovací řetězce**.

1. Zkopírujte připojovací řetězec.

Otevření firemního úložiště v VS Code:

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a potom vyberte **& IoT plug-in, příkaz Open model úložiště** .

1. Vyberte **Otevřít úložiště organizačních modelů** a vložte ho do svého připojovacího řetězce.

1. Stisknutím klávesy **ENTER** otevřete firemní úložiště.

Publikování modelu a rozhraní schopností zařízení do úložiště vaší společnosti:

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a potom vyberte příkaz **Plug and & Play: Odeslat soubory do úložiště modelu** .

1. Vyberte soubory **EnvironmentalSensor. Interface. JSON** a **SensorboxModel. capabilitymodel. JSON** a vyberte **OK**.

Vaše soubory jsou nyní uloženy v úložišti vaší společnosti.

## <a name="generate-code"></a>Generování kódu

K vygenerování kostry kódu C z modelu můžete použít **nástroje Azure IoT Tools for vs Code** . Chcete-li vygenerovat kostru kódu v VS Code:

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a potom vyberte **IoT plug & Play: příkaz generovat kód zařízení stub** .

1. Vyberte soubor modelu schopností **SensorboxModel. capabilitymodel. JSON** .

1. Jako název projektu zadejte **sensorbox_app** .

1. Jako jazyk vyberte **ANSI C** .

1. Jako způsob připojení vyberte **prostřednictvím IoT Hub připojovací řetězec zařízení** .

1. Vyberte **projekt cmake ve Windows** jako šablonu projektu.

1. Vyberte **prostřednictvím Vcpkg** jako způsob, jak zahrnout sadu SDK pro zařízení.

VS Code generuje kostru kódu C a uloží soubory do složky **sensorbox_app** ve složce **modelcode** . VS Code otevře nové okno, které obsahuje soubory generovaného kódu.

## <a name="update-the-generated-code"></a>Aktualizovat generovaný kód

Předtím, než budete moci sestavit a spustit kód, je nutné implementovat podložit vlastnosti, telemetrie a příkazy.

Chcete-li poskytnout implementace pro podložit kód v VS Code:

1. Otevřete soubor **SensorboxModel_impl. c** .

1. Přidejte kód pro implementaci funkcí podložit.

1. Uložte provedené změny.

## <a name="build-the-code"></a>Sestavení kódu

Předtím, než spustíte kód pro otestování zařízení IoT technologie Plug and Play v Azure IoT Hub, je nutné kód zkompilovat.

Podle pokynů v souboru **Readme.MD** ve složce **sensorbox_app** Sestavte a spusťte kód ve Windows. Následující část obsahuje pokyny pro načtení připojovacího řetězce zařízení, který se použije při spuštění kódu zařízení.

## <a name="test-the-code"></a>Testování kódu

Když kód spustíte, připojí se k IoT Hub a spustí odesílání ukázkových telemetrie a hodnot vlastností. Zařízení také reaguje na příkazy odesílané z IoT Hub. Ověření tohoto chování:

1. Vytvoření centra IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Přidejte zařízení do služby IoT Hub a načtěte jeho připojovací řetězec:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Poznamenejte si připojovací řetězec.

1. Na příkazovém řádku přejděte do složky **Azure-IoT-SDK-c** , kde jste vytvořili sadu SDK a ukázky. Pak přejděte do složky pro **\\pro cmaki sensorbox_app\\vydané verze** .

1. Spusťte následující příkaz:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Pomocí nástroje Azure IoT Explorer můžete komunikovat se zařízením IoT technologie Plug and Play připojeném ke službě IoT Hub. Další informace najdete v tématu [instalace a použití Azure IoT Exploreru](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili IoT technologie Plug and Play připraveni k certifikaci, se naučíte:

> [!div class="nextstepaction"]
> [Sestavení zařízení, které je připravené k certifikaci](tutorial-build-device-certification.md)
