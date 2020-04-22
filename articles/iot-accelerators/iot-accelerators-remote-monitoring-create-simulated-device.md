---
title: Simulace zařízení pomocí vzdáleného monitorování IoT – Azure | Dokumenty společnosti Microsoft
description: Tento návod ukazuje, jak používat simulátor zařízení s akcelerátorem řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt
ms.openlocfilehash: d31e520dac1c7e2a13fbd9e24a0cd3167f69e904
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682011"
---
# <a name="create-and-test-a-new-simulated-device"></a>Vytvoření a testování nového simulovaného zařízení

Akcelerátor řešení vzdáleného monitorování umožňuje definovat vlastní simulovaná zařízení. Tento článek ukazuje, jak definovat nové simulované zařízení žárovky a pak jej otestovat místně. Akcelerátor řešení zahrnuje simulovaná zařízení, jako jsou chladiče a nákladní automobily. Můžete však definovat vlastní simulovaná zařízení k testování řešení IoT před nasazením skutečných zařízení.

> [!NOTE]
> Tento článek popisuje, jak používat simulovaná zařízení hostovaná ve službě simulace zařízení. Pokud chcete vytvořit skutečné zařízení, přečtěte si informace [o připojení zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

Tento návod ukazuje, jak přizpůsobit mikroslužbu simulace zařízení. Tato mikroslužba je součástí akcelerátoru řešení vzdáleného monitorování. Chcete-li zobrazit možnosti simulace zařízení, tento návod používá dva scénáře v aplikaci Contoso IoT:

V prvním scénáři přidáte nový typ telemetrie do existujícího typu zařízení **Contoso chiller.**

Ve druhém scénáři chce společnost Contoso otestovat nové zařízení inteligentní žárovky. Chcete-li spustit testy, vytvořte nové simulované zařízení s následujícími charakteristikami:

*Vlastnosti*

| Název                     | Hodnoty                      |
| ------------------------ | --------------------------- |
| Barvy                    | Bílá, Červená, Modrá            |
| Jas               | 0 až 100                    |
| Odhadovaná zbývající životnost | Odpočítávání od 10 000 hodin |

*Telemetrie*

V následující tabulce jsou uvedena data, která žárovka hlásí cloudu jako datový proud:

| Název   | Hodnoty      |
| ------ | ----------- |
| Status | "zapnuto", "vypnuto" |
| Teplota | Stupně F |
| online | pravda, nepravdivé |

> [!NOTE]
> Online **online** telemetrická hodnota je povinná pro všechny simulované typy.

*Metody*

V následující tabulce jsou uvedeny akce, které nové zařízení podporuje:

| Název        |
| ----------- |
| Zapnutí   |
| Vypněte  |

*Počáteční stav*

V následující tabulce je uveden počáteční stav zařízení:

| Název                     | Hodnoty |
| ------------------------ | -------|
| Počáteční barva            | White  |
| Počáteční jas       | 75     |
| Počáteční zbývající životnost   | 10 000 |
| Počáteční stav telemetrie | "zapnuto"   |
| Počáteční teplota telemetrie | 200   |

K dokončení kroků v tomto návodu, budete potřebovat aktivní předplatné Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto návodu, potřebujete:

* Visual Studio Code. Můžete [si stáhnout Visual Studio Code for Mac, Linux a Windows](https://code.visualstudio.com/download).
* Jádro .NET. Zde si můžete stáhnout [.NET Core pro Mac, Linux a Windows](https://www.microsoft.com/net/download).
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Pošťák. Můžete si stáhnout [Postman pro Mac, Windows nebo Linux](https://www.getpostman.com/apps).
* [Centrum IoT nasazené do vašeho předplatného Azure](../../articles/iot-hub/iot-hub-create-through-portal.md). K dokončení kroků v této příručce potřebujete připojovací řetězec centra IoT hub. Připojovací řetězec můžete získat z webu Azure Portal.
* Databáze Cosmos DB, která používá rozhraní SQL API a která je nakonfigurována pro [silnou konzistenci](../../articles/cosmos-db/how-to-manage-database-account.md). K dokončení kroků v této příručce potřebujete připojovací řetězec databáze Cosmos DB. Připojovací řetězec můžete získat z webu Azure Portal.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Pro přípravu vývojového prostředí proveďte následující úkoly:

* Stáhněte si zdroj pro mikroslužbu simulace zařízení.
* Stáhněte zdroj mikroslužby adaptéru úložiště.
* Spusťte mikroslužbu adaptéru úložiště místně.

Pokyny v tomto článku předpokládají, že používáte systém Windows. Pokud používáte jiný operační systém, bude pravděpodobně nutné upravit některé cesty a příkazy souborů tak, aby vyhovovaly vašemu prostředí.

### <a name="download-the-microservices"></a>Stažení mikroslužeb

Stáhněte a rozbalte [mikroslužby vzdáleného monitorování](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z GitHubu do vhodného umístění v místním počítači. Článek předpokládá, že název této složky je **vzdálené monitorování služeb-dotnet-master**.

Stáhněte a rozbalte [mikroslužbu simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z GitHubu na vhodné místo v místním počítači. Článek předpokládá, že název této složky je **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Spuštění mikroslužby adaptéru úložiště

Otevřete složku **vzdáleného monitorování služeb-dotnet-master\storage-adapter** v kódu sady Visual Studio. Chcete-li opravit všechny nevyřešené závislosti, klepněte na ně tlačítka **obnovení.**

Otevřete soubor **storage-adapter/WebService/appsettings.ini** a přiřaďte připojovací řetězec Cosmos DB proměnné **documentDBConnectionString.**

Chcete-li spustit mikroslužbu místně, klepněte na **tlačítko Ladění > Spustit ladění**.

Okno **Terminál** v kódu sady Visual Studio zobrazuje výstup ze spuštěné mikroslužby včetně adresy URL pro kontrolu stavu webové služby: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). Při přechodu na tuto adresu by měl být stav "OK: Alive and well".

Ponechte mikroslužbu adaptéru úložiště spuštěnou v této instanci kódu sady Visual Studio a dokončete další kroky.

## <a name="modify-the-chiller"></a>Upravte chladič

V této části přidáte nový typ telemetrie **vnitřní teploty** do existujícího typu **zařízení chladiče:**

1. Vytvořte novou složku **C:\temp\devicemodels** v místním počítači.

1. Zkopírujte následující soubory do nové složky ze stažené kopie mikroslužby simulace zařízení:

    | Zdroj | Cíl |
    | ------ | ----------- |
    | Služby\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Služby\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Otevřete soubor **C:\temp\devicemodels\chiller-01.json.**

1. V části **InitialState** přidejte následující dvě definice:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Do pole **Telemetrie** přidejte následující definici:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Uložte soubor **C:\temp\devicemodels\chiller-01.json.**

1. Otevřete soubor **C:\temp\devicemodels\scripts\chiller-01-state.js.**

1. Do proměnné **stavu** přidejte následující pole:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aktualizujte **hlavní** funkci takto:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Uložte soubor **C:\temp\devicemodels\scripts\chiller-01-state.js.**

## <a name="create-the-lightbulb"></a>Vytvořte žárovku

V této části definujete nový typ **zařízení žárovky:**

1. Vytvořte soubor **C:\temp\devicemodels\lightbulb-01.json** a přidejte následující obsah:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Uložte změny do **c:\temp\devicemodels\lightbulb-01.json**.

1. Vytvořte soubor **C:\temp\devicemodels\scripts\lightbulb-01-state.js** a přidejte následující obsah:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Uložte změny do **souboru C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Vytvořte soubor **C:\temp\devicemodels\scripts\SwitchOn-method.js** a přidejte následující obsah:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Uložte změny do **souboru C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Vytvořte soubor **C:\temp\devicemodels\scripts\SwitchOff-method.js** a přidejte následující obsah:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Uložte změny do **souboru C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Nyní jste vytvořili přizpůsobenou verzi typu **zařízení Chladič e-li** a vytvořili jste nový typ **zařízení žárovky.**

## <a name="test-the-devices"></a>Otestujte zařízení

V této části otestujete typy zařízení, které jste vytvořili v předchozích částech místně.

### <a name="run-the-device-simulation-microservice"></a>Spuštění mikroslužby simulace zařízení

Otevřete složku **device-simulation-dotnet-master,** kterou jste stáhli z GitHubu v nové instanci kódu Visual Studia. Chcete-li opravit všechny nevyřešené závislosti, klepněte na ně tlačítka **obnovení.**

Otevřete soubor **WebService/appsettings.ini** a přiřaďte připojovací řetězec Cosmos DB proměnné **documentdb_connstring** a také upravte nastavení následujícím způsobem:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Chcete-li spustit mikroslužbu místně, klepněte na **tlačítko Ladění > Spustit ladění**.

Okno **Terminálv** kódu sady Visual Studio zobrazuje výstup z běžící mikroslužby.

Ponechte mikroslužbu simulace zařízení spuštěnou v této instanci kódu sady Visual Studio a dokončete další kroky.

### <a name="set-up-a-monitor-for-device-events"></a>Nastavení monitoru pro události zařízení

V této části použijete azure CLI k nastavení monitorování událostí k zobrazení telemetrie odeslané ze zařízení připojených k centru IoT.

Následující skript předpokládá, že název vašeho centra IoT hub je **test simulace zařízení**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Při testování simulovaných zařízení ponechte monitor událostí spuštěný.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Vytvoření simulace s aktualizovaným typem chladicího zařízení

V této části použijete nástroj Postman k vyžádání mikroslužby simulace zařízení ke spuštění simulace pomocí aktualizovaného typu chladicího zařízení. Pošťák je nástroj, který umožňuje odesílat požadavky REST webové službě. Konfigurační soubory Postman, které potřebujete, jsou v místní kopii úložiště **device-simulation-dotnet.**

Nastavení pošťáka:

1. Otevřete pošťáka na místním počítači.

1. Klepněte na **položku Soubor > import .** Pak klepněte na **tlačítko Vybrat soubory**.

1. Přejděte do složky **device-simulation-dotnet-master/docs/postman.** Vyberte **akcelerátor řešení Azure IoT Device Simulation.postman_collection** a **Azure IoT Device Simulation solution accelerator.postman_environment** a klikněte na **Otevřít**.

1. Rozbalte **akcelerátor řešení Simulace zařízení Azure IoT** na požadavky, které můžete odeslat.

1. Klikněte na **Žádné prostředí** a vyberte **akcelerátor řešení Simulace zařízení Azure IoT**.

Nyní máte kolekci a prostředí načtené v pracovním prostoru Postman, který můžete použít k interakci s mikroslužbou simulace zařízení.

Konfigurace a spuštění simulace:

1. V kolekci Pošťák vyberte **Vytvořit upravenou simulaci chladiče** a klepněte na tlačítko **Odeslat**. Tento požadavek vytvoří čtyři instance simulovaného typu chladicího zařízení.

1. Výstup monitorování událostí v okně Azure CLI zobrazuje telemetrii ze simulovaných zařízení, včetně nových **internal_temperature** hodnot.

Chcete-li simulaci zastavit, vyberte v pořštěnu požadavek **na zastavení simulace** a klepněte na tlačítko **Odeslat**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Vytvoření simulace s typem zařízení žárovky

V této části použijete nástroj Postman k vyžádání mikroslužby simulace zařízení ke spuštění simulace pomocí typu zařízení žárovky. Pošťák je nástroj, který umožňuje odesílat požadavky REST webové službě.

Konfigurace a spuštění simulace:

1. V kolekci Pošťák vyberte **Vytvořit simulaci žárovky** a klepněte na **odeslat**. Tento požadavek vytvoří dvě instance typu zařízení simulované žárovky.

1. Výstup monitorování událostí v okně Azure CLI zobrazuje telemetrii ze simulovaných žárovek.

Chcete-li simulaci zastavit, vyberte v pořštěnu požadavek **na zastavení simulace** a klepněte na tlačítko **Odeslat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete zastavit dvě místně spuštěné mikroslužby v jejich instancích kódu sady Visual Studio **(ladění > zastavení ladění).**

Pokud už nepotřebujete instance Služby IoT Hub a Cosmos DB, odstraňte je z předplatného Azure, abyste se vyhnuli zbytečným poplatkům.

## <a name="next-steps"></a>Další kroky

Tato příručka vám ukázala, jak vytvořit vlastní simulované typy zařízení a otestovat je spuštěním mikroslužby simulace zařízení místně.

Dalším navrhovaným krokem je naučit se nasadit vlastní typy simulovaných zařízení do [akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
