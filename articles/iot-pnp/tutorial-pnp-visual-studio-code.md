---
title: Vytvoření a testování zařízení Náhled technologie IoT Plug and Play | Dokumenty společnosti Microsoft
description: Jako vývojář zařízení se dozvíte, jak pomocí kódu VS vytvořit a otestovat nový model schopností zařízení pro zařízení IoT Plug and Play Preview.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719717"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Kurz: Vytvoření a testování modelu schopností zařízení pomocí kódu Sady Visual Studio

Tento kurz ukazuje, jak jako vývojář zařízení použít kód sady Visual Studio k vytvoření _modelu schopností zařízení_. Model můžete použít ke generování kódu kostry ke spuštění na zařízení, které se připojuje k instanci služby Azure IoT Hub v cloudu.

Část v tomto kurzu, která popisuje, jak vytvořit generovaný kód kostry předpokládá, že používáte systém Windows.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření modelu schopností zařízení
> * Generovat kód kostry zařízení z modelu
> * Implementovat zástupné procedury ve vygenerovaném kódu
> * Spuštění kódu k testování interakcí s centrem IoT hub

## <a name="prerequisites"></a>Požadavky

Chcete-li pracovat s modelem schopností zařízení v tomto kurzu, potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/download): VS Code je k dispozici pro více platforem
* Rozšíření rozšíření [Azure IoT Tools for VS Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Pomocí následujících kroků nainstalujte rozšiřující balíček v kódu VS:

    1. V kódu VS vyberte kartu **Rozšíření.**
    1. Vyhledejte **nástroje Azure IoT .**
    1. Vyberte **Install** (Nainstalovat).

Chcete-li vytvořit vygenerovaný kód C v systému Windows v tomto kurzu, potřebujete:

* [Vytvářejte nástroje pro Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) s nástroji pro sestavení **C++** a úlohami **součástí správce balíčků NuGet.** Nebo pokud už máte [Visual Studio (Community, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 nebo 2015 se stejnými nainstalovanými úlohami.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Chcete-li otestovat kód zařízení v tomto kurzu, potřebujete:

* Průzkumník [Azure IoT](https://github.com/Azure/azure-iot-explorer/releases).
* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modelování zařízení

K vytvoření modelu schopností zařízení se používá _jazyk definice digitálních dvojčat._ Model se obvykle skládá z více definičních souborů _rozhraní_ a jednoho souboru modelu. **Nástroje Azure IoT pro kód VS** obsahují nástroje, které vám pomůžou vytvářet a upravovat tyto soubory JSON.

### <a name="create-the-interface-file"></a>Vytvoření souboru rozhraní

Vytvoření souboru rozhraní, který definuje možnosti vašeho zařízení IoT v kódu VS:

1. Vytvořte složku nazvanou **devicemodel**.

1. Spusťte Kód VS a otevřete paletu příkazů pomocí **kombinace kláves Ctrl+Shift+P.**

1. Zadejte **Plug and Play** a pak vyberte příkaz **IoT Plug & Play: Create Interface** .

1. Vyhledejte a vyberte složku **modelu zařízení,** kterou jste vytvořili.

1. Poté zadejte **EnvironmentalSensor** jako název rozhraní a stiskněte **klávesu Enter**. VS Code vytvoří ukázkový soubor rozhraní s názvem **EnvironmentalSensor.interface.json**.

1. Nahraďte obsah tohoto souboru následujícím polem JSON a nahraďte `{your name}` v `@id` poli jedinečnou hodnotou. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko. Další informace naleznete v [tématu Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). ID rozhraní musí být jedinečné pro uložení rozhraní do úložiště:

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

    Toto rozhraní definuje vlastnosti zařízení, jako je **název zákazníka**, typy telemetrie, jako je **teplota**, a příkazy, jako je **turnon**.

1. Na konec tohoto souboru rozhraní přidejte funkci příkazu nazvanou **blikat.** Před přidáním příkazu nezapomeňte přidat čárku. Zkuste zadat definici, abyste zjistili, jak vám intellisense, automatické dokončování a ověřování může pomoci upravit definici rozhraní:

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

Soubor modelu určuje rozhraní, která vaše zařízení IoT Plug and Play implementuje. V modelu obvykle existují alespoň dvě rozhraní – jedno nebo více, které definují specifické možnosti vašeho zařízení, a standardní rozhraní, které musí implementovat všechna zařízení IoT Plug and Play.

Vytvoření souboru modelu, který určuje rozhraní, která zařízení IoT Plug and Play implementuje v kódu VS:

1. K otevření palety příkazů použijte **kombinaci kláves Ctrl+Shift+P.**

1. Zadejte **Plug and Play** a pak vyberte příkaz **IoT Plug & Play: Create Capability Model** . Pak zadejte **SensorboxModel** jako název modelu. VS Code vytvoří ukázkový soubor rozhraní s názvem **SensorboxModel.capabilitymodel.json**.

1. Nahraďte obsah tohoto souboru následujícím jazykem `@id` JSON `EnvironmentalSensor` a nahraďte `{your name}` v poli a v rozhraní stejnou hodnotou, jakou jste použili v souboru **EnvironmentalSensor.interface.json.** ID rozhraní musí být jedinečné pro uložení rozhraní do úložiště:

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

    Model definuje zařízení, které implementuje rozhraní **EnvironmentalSensor** a standardní **deviceinformation** rozhraní.

1. Uložte soubor.

### <a name="download-the-deviceinformation-interface"></a>Stažení rozhraní DeviceInformation

Před generováním kódu kostry z modelu je nutné vytvořit místní kopii **DeviceInformation** z *úložiště veřejného modelu*. Úložiště veřejného modelu již obsahuje rozhraní **DeviceInformation.**

Stažení rozhraní **DeviceInformation** z úložiště veřejného modelu pomocí kódu VS:

1. K otevření palety příkazů použijte **kombinaci kláves Ctrl+Shift+P.**

1. Zadejte **Možnost Plug and Play**, vyberte příkaz Otevřít úložiště **modelů** a pak vyberte Otevřít **úložiště veřejných modelů**.

1. Vyberte **Rozhraní**, vyberte informační rozhraní `urn:azureiot:DeviceManagement:DeviceInformation:1`zařízení s ID a pak vyberte **Stáhnout**.

Nyní máte tři soubory, které tvoří model schopností zařízení:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Publikování modelu

Aby si nástroj Průzkumník Azure IoT přečetl model schopností zařízení, musíte ho publikovat v úložišti vaší společnosti. Chcete-li publikovat z VS Code, potřebujete připojovací řetězec pro úložiště společnosti:

1. Přejděte na [portál Azure Certified for IoT](https://aka.ms/ACFI).

1. K přihlášení k portálu použijte _svůj pracovní účet_ Microsoft.

1. Vyberte **úložiště společnosti** a potom **připojovací řetězce**.

1. Zkopírujte připojovací řetězec.

Otevření firemního úložiště v kódu VS:

1. K otevření palety příkazů použijte **kombinaci kláves Ctrl+Shift+P.**

1. Zadejte **Plug and Play** a pak vyberte příkaz **IoT Plug & Play: Open Model Repository** .

1. Vyberte **Otevřít úložiště organizačního modelu** a vložte do připojovacího řetězce.

1. Stisknutím **klávesy Enter** otevřete firemní úložiště.

Publikování modelu a rozhraní schopností zařízení do firemního úložiště:

1. K otevření palety příkazů použijte **kombinaci kláves Ctrl+Shift+P.**

1. Zadejte **Plug and Play** a pak vyberte příkaz **IoT Plug & Play: Submit files to Model Repository** .

1. Vyberte soubory **EnvironmentalSensor.interface.json** a **SensorboxModel.capabilitymodel.json** a vyberte **OK**.

Vaše soubory jsou nyní uloženy ve vašem firemním úložišti.

## <a name="generate-code"></a>Generování kódu

Nástroje Azure **IoT pro kód VS** můžete použít ke generování kódu kostry C z vašeho modelu. Chcete-li generovat kód kostry v kódu VS:

1. K otevření palety příkazů použijte **kombinaci kláves Ctrl+Shift+P.**

1. Zadejte **Plug and Play** a pak vyberte příkaz **IoT Plug & Play: Generate Device Code Stub** .

1. Vyberte soubor modelu **schopností SensorboxModel.capabilitymodel.json.**

1. Jako název projektu zadejte **sensorbox_app.**

1. Jako jazyk zvolte **ANSI C.**

1. Jako způsob připojení zvolte **připojovací řetězec zařízení Služby Via IoT Hub.**

1. Zvolte **CMake Project v systému Windows** jako šablonu projektu.

1. Zvolte **Via Vcpkg** jako způsob, jak zahrnout zařízení SDK.

VS Kód generuje kód kostry C a uloží soubory do **sensorbox_app** složky ve složce **modelcode.** VS Code otevře nové okno, které obsahuje generované soubory kódu.

## <a name="update-the-generated-code"></a>Aktualizace generovaného kódu

Před sestavením a spuštěním kódu je třeba implementovat vlastnosti se zakázaným inzerováním, telemetrii a příkazy.

Chcete-li poskytnout implementace pro kód se zakázaným inachy v kódu VS:

1. Otevřete soubor **SensorboxModel_impl.c.**

1. Přidejte kód k implementaci funkce se zakázaným inzerováním.

1. Uložte provedené změny.

## <a name="build-the-code"></a>Sestavení kódu

Než spustíte kód k testování zařízení IoT Plug and Play s centrem Azure IoT hub, musíte zkompilovat kód.

Podle pokynů v **souboru Readme.md** ve složce **sensorbox_app** vytvořte a spusťte kód v systému Windows. Následující část obsahuje pokyny pro načtení připojovacího řetězce zařízení, který se má použít při spuštění kódu zařízení.

## <a name="test-the-code"></a>Otestujte kód

Když spustíte kód, připojí se k centru IoT Hub a začne odesílat ukázkové telemetrie a hodnoty vlastností. Zařízení také reaguje na příkazy odeslané z ioT hubu. Chcete-li ověřit toto chování:

1. Vytvoření centra IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Přidejte zařízení do centra IoT hub a načtěte jeho připojovací řetězec:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Poznamenejte si připojovací řetězec.

1. Na příkazovém řádku přejděte do složky **azure-iot-sdk-c,** kde jste vytvořili sadu SDK a ukázky. Potom přejděte do složky **cmake\\\\sensorbox_app Release.**

1. Spusťte následující příkaz:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Pomocí nástroje PrůzkumníkA Azure IoT můžete pracovat se zařízením IoT Plug and Play připojeným k vašemu centru IoT Hub. Další informace najdete [v tématu Instalace a použití Průzkumníka Azure IoT](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste si vytvořili ioT plug and play připravený k certifikaci, přečtěte si, jak:

> [!div class="nextstepaction"]
> [Sestavení zařízení připraveného k certifikaci](tutorial-build-device-certification.md)
