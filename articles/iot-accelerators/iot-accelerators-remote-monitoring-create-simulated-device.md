---
title: Simulace zařízení pomocí vzdáleného monitorování IoT – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak používat simulátor zařízení s akcelerátorem řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 21d0587b951d909f0091eccf9a7f86a484bc5051
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324038"
---
# <a name="create-and-test-a-new-simulated-device"></a>Vytvoření a otestování nového simulovaného zařízení

Akcelerátor řešení vzdáleného monitorování umožňuje definovat vlastní simulovaná zařízení. V tomto článku se dozvíte, jak definovat nové simulované zařízení žárovky a pak ho místně otestovat. Akcelerátor řešení zahrnuje simulovaná zařízení, jako jsou chlazení a nákladní automobily. Můžete ale definovat vlastní simulovaná zařízení k testování řešení IoT ještě před nasazením reálných zařízení.

> [!NOTE]
> Tento článek popisuje, jak používat simulovaná zařízení hostovaná ve službě simulace zařízení. Pokud chcete vytvořit reálné zařízení, přečtěte si téma [připojení zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

V této příručce se dozvíte, jak přizpůsobit mikroslužbu simulace zařízení. Tato mikroslužba je součástí akcelerátoru řešení vzdáleného monitorování. Chcete-li zobrazit možnosti simulace zařízení, tato příručka používá dva scénáře v aplikaci společnosti Contoso IoT:

V prvním scénáři přidáte nový typ telemetrie do stávajícího typu zařízení **chladicího** zařízení společnosti Contoso.

Ve druhém scénáři chce contoso otestovat nové zařízení inteligentního žárovky. Chcete-li spustit testy, vytvořte nové simulované zařízení s následujícími charakteristikami:

*Vlastnosti*

| Name                     | Hodnoty                      |
| ------------------------ | --------------------------- |
| Color                    | Bílá, červená, modrá            |
| Světlost               | 0 až 100                    |
| Odhad zbývající životnost | Odpočítávání z 10 000 hodin |

*Telemetrie*

V následující tabulce jsou uvedena data, která žárovky do cloudu, jako datový proud:

| Name   | Hodnoty      |
| ------ | ----------- |
| Status | "zapnuto", "vypnuto" |
| Teplota | Stupně F |
| online | true, false |

> [!NOTE]
> Hodnota **online** telemetrie je povinná pro všechny simulované typy.

*Metody*

Následující tabulka uvádí akce, které nové zařízení podporuje:

| Name        |
| ----------- |
| Přepnout na   |
| Přepnout  |

*Počáteční stav*

V následující tabulce je uveden počáteční stav zařízení:

| Name                     | Hodnoty |
| ------------------------ | -------|
| Počáteční barva            | White  |
| Počáteční jas       | 75     |
| Počáteční zbývající životnost   | 10 000 |
| Stav počáteční telemetrie | pnete   |
| Počáteční teplota telemetrie | 200   |

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto návodu, budete potřebovat:

* Visual Studio Code. Můžete [si stáhnout Visual Studio Code pro Mac, Linux a Windows](https://code.visualstudio.com/download).
* .NET Core. Můžete stáhnout [.NET Core pro Mac, Linux a Windows](https://www.microsoft.com/net/download).
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postman. Můžete si stáhnout [pro Mac, Windows nebo Linux](https://www.getpostman.com/apps).
* [Centrum IoT nasazené ve vašem předplatném Azure](../../articles/iot-hub/iot-hub-create-through-portal.md). K dokončení kroků v této příručce potřebujete připojovací řetězec centra IoT. Připojovací řetězec můžete získat z Azure Portal.
* Cosmos DB databáze, která používá rozhraní SQL API a která je nakonfigurovaná pro [silnou konzistenci](../../articles/cosmos-db/how-to-manage-database-account.md). K dokončení kroků v této příručce potřebujete připojovací řetězec databáze Cosmos DB. Připojovací řetězec můžete získat z Azure Portal.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Při přípravě vývojového prostředí dokončete následující úlohy:

* Stáhněte si zdroj pro mikroslužbu simulace zařízení.
* Stáhněte si zdroj pro mikroslužbu adaptéru úložiště.
* Spusťte mikroslužbu adaptéru úložiště místně.

Pokyny v tomto článku předpokládají, že používáte systém Windows. Pokud používáte jiný operační systém, možná budete muset upravit některé cesty k souboru a příkazy tak, aby vyhovovaly vašemu prostředí.

### <a name="download-the-microservices"></a>Stažení mikroslužeb

Stáhněte a rozbalte [mikroslužby vzdáleného monitorování](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) z GitHubu do vhodného umístění v místním počítači. Článek předpokládá, že název této složky je **vzdálené monitorování-služby-dotnet-Master**.

Stáhněte a rozbalte [mikroslužbu simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z GitHubu do vhodného umístění v místním počítači. Článek předpokládá, že název této složky je **Device-simulace-dotnet-Master**.

### <a name="run-the-storage-adapter-microservice"></a>Spuštění mikroslužby adaptéru úložiště

Otevřete složku **Remote-Monitoring-Services-dotnet-master\storage-Adapter** v Visual Studio Code. Kliknutím na jakékoli tlačítko **obnovit** opravíte nevyřešené závislosti.

Otevřete soubor **Storage-Adapter/WebService/appsettings.ini** a přiřaďte k proměnné **documentDBConnectionString**  připojovací řetězec Cosmos DB.

Pokud chcete spustit mikroslužbu místně, klikněte na **ladění > spustit ladění**.

Okno **terminálu** v Visual Studio Code zobrazuje výstup z běžící mikroslužby včetně adresy URL pro kontrolu stavu webové služby: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status) . Při přechodu na tuto adresu by měl být ve stavu "OK: Alive" a dobře ".

Ponechte mikroslužbu adaptéru úložiště spuštěnou v této instanci Visual Studio Code při dokončení dalších kroků.

## <a name="modify-the-chiller"></a>Změnit chladicíer

V této části přidáte nový typ telemetrie **vnitřních teplot** na existující typ zařízení **chladicího** zařízení:

1. Na místním počítači vytvořte novou složku **C:\temp\devicemodels** .

1. Zkopírujte následující soubory do nové složky ze stažené kopie mikroslužby simulace zařízení:

    | Zdroj | Cíl |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.jsna | C:\temp\devicemodels\chiller-01.jsna |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Otevřete **C:\temp\devicemodels\chiller-01.jsv** souboru.

1. V části **initialState** přidejte následující dvě definice:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Do pole **telemetrie** přidejte následující definici:

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

1. Uložte **C:\temp\devicemodels\chiller-01.jsdo** souboru.

1. Otevřete soubor **C:\temp\devicemodels\scripts\chiller-01-state.js** .

1. Do proměnné **State** přidejte následující pole:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aktualizujte **Hlavní** funkci následujícím způsobem:

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

1. Uložte soubor **C:\temp\devicemodels\scripts\chiller-01-state.js** .

## <a name="create-the-lightbulb"></a>Vytvoření žárovky

V této části definujete nový typ zařízení **žárovky** :

1. Vytvořte soubor **C:\temp\devicemodels\lightbulb-01.jsna** a přidejte následující obsah:

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

    Uložte změny **C:\temp\devicemodels\lightbulb-01.jsv**.

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

    Uložte změny **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

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

    Uložte změny **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

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

    Uložte změny **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Nyní jste vytvořili přizpůsobenou verzi typu **chladicího** zařízení a vytvořili jste nový typ zařízení **žárovky** .

## <a name="test-the-devices"></a>Testování zařízení

V této části otestujete typy zařízení, které jste vytvořili v předchozích částech místně.

### <a name="run-the-device-simulation-microservice"></a>Spuštění mikroslužby simulace zařízení

Otevřete složku **Device-simulace-dotnet-Master** , kterou jste stáhli z GitHubu, do nové instance Visual Studio Code. Kliknutím na jakékoli tlačítko **obnovit** opravíte nevyřešené závislosti.

Otevřete soubor **WebService/appsettings.ini** a přiřaďte k proměnné **documentdb_connstring** připojovací řetězec Cosmos DB a upravte nastavení následujícím způsobem:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Pokud chcete spustit mikroslužbu místně, klikněte na **ladění > spustit ladění**.

Okno **terminálu** v Visual Studio Code zobrazuje výstup z běžící mikroslužby.

Ponechte mikroslužbu simulace zařízení spuštěnou v této instanci Visual Studio Code během provádění dalších kroků.

### <a name="set-up-a-monitor-for-device-events"></a>Nastavení monitorování pro události zařízení

V této části použijete rozhraní příkazového řádku Azure CLI k nastavení monitorování událostí pro zobrazení telemetrie odesílané ze zařízení připojených ke službě IoT Hub.

Následující skript předpokládá, že název vašeho centra IoT je typu **zařízení-simulace-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Při testování simulovaných zařízení nechte monitorování událostí spuštěné.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Vytvořit simulaci s aktualizovaným typem zařízení chlazení

V této části vyžádáte pomocí nástroje pro simulaci simulaci zařízení, aby spouštěla simulaci pomocí aktualizovaného typu zařízení chladicího zařízení. Post je nástroj, který umožňuje odesílat žádosti REST do webové služby. Konfigurační soubory, které potřebujete, jsou v místní kopii úložiště **typu zařízení-simulace-dotnet** .

Nastavení post:

1. Otevřete post na místním počítači.

1. Klikněte na **soubor > importovat**. Pak klikněte na **zvolit soubory**.

1. Přejděte do složky **Device-simulace-dotnet-Master/docs/post** . Vyberte **řešení pro simulaci zařízení Azure iot Accelerator.postman_collection** a **řešení simulace zařízení Azure IoT Accelerator.postman_environment** a klikněte na **otevřít**.

1. Rozšiřte **akcelerátor řešení pro simulaci zařízení Azure IoT** na požadavky, které můžete odeslat.

1. Klikněte na **žádné prostředí** a vyberte **akcelerátor řešení pro simulaci zařízení Azure IoT**.

Nyní máte v pracovním prostoru pro práci vytvořenou kolekci a prostředí, kterou můžete použít k interakci s mikroslužbou simulace zařízení.

Konfigurace a spuštění simulace:

1. V kolekci post vyberte **vytvořit upravenou simulaci chlazení** a klikněte na **Odeslat**. Tento požadavek vytvoří čtyři instance simulovaného typu zařízení chladicího zařízení.

1. Výstup sledování událostí v okně Azure CLI zobrazuje telemetrii ze simulovaných zařízení, včetně nových hodnot **internal_temperature** .

Chcete-li zastavit simulaci, vyberte možnost **zastavit simulaci** v příspěvku a klikněte na **Odeslat**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Vytvoření simulace s typem zařízení žárovky

V této části vyžádáte pomocí nástroje pro simulaci, aby služba simulace zařízení spustila simulaci pomocí typu zařízení žárovky. Post je nástroj, který umožňuje odesílat žádosti REST do webové služby.

Konfigurace a spuštění simulace:

1. V kolekci post vyberte **vytvořit simulaci žárovky** a klikněte na **Odeslat**. Tento požadavek vytvoří dvě instance simulovaného typu zařízení žárovky.

1. Výstup sledování událostí v okně Azure CLI zobrazuje telemetrii z simulovaných lightbulbs.

Chcete-li zastavit simulaci, vyberte možnost **zastavit simulaci** v příspěvku a klikněte na **Odeslat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V jejich instancích Visual Studio Code můžete zastavit dvě místně běžící mikroslužby (**ladění > zastavit ladění**).

Pokud už nepotřebujete instance IoT Hub a Cosmos DB, odstraňte je z předplatného Azure, abyste se vyhnuli zbytečným poplatkům.

## <a name="next-steps"></a>Další kroky

V této příručce se naučíte, jak vytvořit vlastní simulované typy zařízení a testovat je spuštěním mikroslužby simulace zařízení v místním prostředí.

Navržený další krok se naučíte, jak nasadit vlastní simulované typy zařízení do [akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
