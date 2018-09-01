---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c6e57d5094f455983b8b474b6930f628d654e457
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371212"
---
Do prvního scénáře, přidejte nový typ telemetrických dat na Contoso existující **chladič** typ zařízení.

V druhém scénáři Contoso chce testovat nové zařízení inteligentní žárovky. Ke spuštění testů, můžete vytvořit nového simulovaného zařízení s následujícími charakteristikami:

*Vlastnosti*

| Název                     | Hodnoty                      |
| ------------------------ | --------------------------- |
| Barva                    | Prázdné, červená, modrá            |
| Jas               | 0 až 100                    |
| Odhadovaný zbývající životnost | Odpočítávání za 10 000 hodin |

*Telemetrie*

V následující tabulce jsou uvedeny data na žárovku zprávy do cloudu jako datový proud:

| Název   | Hodnoty      |
| ------ | ----------- |
| Status | "na" "off" |
| Teplota | Stupně F |
| Online | Hodnota TRUE, false |

> [!NOTE]
> **Online** hodnota telemetrie je povinná pro všechny simulované typy.

*Metody*

V následující tabulce jsou uvedeny akce, které podporuje nové zařízení:

| Název        |
| ----------- |
| Přepnout   |
| Vypnout  |

*Počáteční stav*

Následující tabulka uvádí počáteční stav zařízení:

| Název                     | Hodnoty |
| ------------------------ | -------|
| Počáteční barva            | Bílá  |
| Počáteční jas       | 75     |
| Počáteční zbývající životnost   | 10 000 |
| Telemetrie počáteční stav | "na"   |
| Počáteční telemetrie teploty | 200   |

K dokončení kroků v této příručce s postupy, budete potřebovat aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Tato příručka, budete potřebovat:

* Visual Studio Code. Je možné [stáhněte si Visual Studio Code pro Mac, Linux a Windows](https://code.visualstudio.com/download).
* .NET core. Můžete si stáhnout [.NET Core pro Windows, Mac a Linux](https://www.microsoft.com/net/download).
* Postman. Můžete si stáhnout [Postman pro Mac, Windows nebo Linuxem](https://www.getpostman.com/apps).
* [Nasadí do vašeho předplatného Azure IoT hub](../articles/iot-hub/iot-hub-create-through-portal.md). Potřebujete připojovací řetězec služby IoT hub k dokončení kroků v této příručce. Získání připojovacího řetězce z webu Azure portal.
* Databáze Cosmos DB, která používá rozhraní SQL API a, který je nakonfigurovaný pro [silnou konzistenci](../articles/cosmos-db/manage-account.md). Potřebujete připojovací řetězec databáze Cosmos DB k dokončení kroků v této příručce. Získání připojovacího řetězce z webu Azure portal.

## <a name="prepare-your-development-environment"></a>Příprava vývojového prostředí

Proveďte následující úkoly Příprava vývojového prostředí:

* Stáhněte zdroj pro mikroslužby simulaci zařízení.
* Stáhněte zdroj pro mikroslužby adaptér úložiště.
* Místní spuštění mikroslužeb adaptér úložiště.

Pokyny v tomto článku se předpokládá, že používáte Windows. Pokud používáte jiný operační systém, budete muset upravit některé cesty k souborům a příkazů podle vašich potřeb.

### <a name="download-the-microservices"></a>Stáhněte si mikroslužby

Stáhněte a rozbalte [mikroslužeb adaptér úložiště](https://github.com/Azure/pcs-storage-adapter-dotnet/archive/master.zip) z Githubu do vhodného umístění na místním počítači.

Stáhněte a rozbalte [mikroslužeb simulace zařízení](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) z Githubu do vhodného umístění na místním počítači.

### <a name="run-the-storage-adapter-microservice"></a>Spustit adaptér mikroslužeb úložiště

Otevřít **počítače úložiště adaptér dotnet-master** složky ve Visual Studio Code. Získáte po kliknutí na **obnovení** tlačítka a opravte všechna nevyřešené závislosti.

Otevřít **.vscode/launch.json** soubor a přiřadit k připojovací řetězec služby Cosmos DB **PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING** proměnné prostředí.

<!-- Open the **WebService/appsettings.ini** file and assign your Cosmos DB connection string to the **documentdb_connstring** configuration setting.-->

Chcete-li spustit místně mikroslužbách, klikněte na tlačítko **ladit > Spustit ladění**.

**Terminálu** okně ve Visual Studio Code se zobrazí výstup ze spuštěné mikroslužeb, včetně adresy URL pro kontrolu stavu webové služby: [ http://127.0.0.1:9022/v1/status ](http://127.0.0.1:9022/v1/status). Když přejdete na tuto adresu, musí být stav "OK: aktivní a dobře".

Ponechte mikroslužeb adaptér úložiště, který je spuštěn v této instanci aplikace Visual Studio Code a další kroky dokončit.

## <a name="modify-the-chiller"></a>Upravit chladič

V této části přidáte nový **vnitřní teplota** typ telemetrie k existující **chladič** typ zařízení:

1. Vytvořte novou složku **C:\temp\devicemodels** na místním počítači.

1. Zkopírujte následující soubory ze staženého kopie mikroslužeb simulace zařízení do nové složky:

    | Zdroj | Cíl |
    | ------ | ----------- |
    | Services\Data\devicemodels\chiller-01.JSON | C:\temp\devicemodels\chiller-01.JSON |
    | Services\Data\devicemodels\scripts\chiller-01-State.js | C:\temp\devicemodels\scripts\chiller-01-State.js |
    | Services\Data\devicemodels\scripts\Reboot-Method.js | C:\temp\devicemodels\scripts\Reboot-Method.js |
    | Services\Data\devicemodels\scripts\FirmwareUpdate-Method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-Method.js |
    | Services\Data\devicemodels\scripts\EmergencyValveRelease-Method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-Method.js |
    | Services\Data\devicemodels\scripts\IncreasePressure-Method.js | C:\temp\devicemodels\scripts\IncreasePressure-Method.js |

1. Otevřít **C:\temp\devicemodels\chiller-01.json** souboru.

1. Aktualizace **SchemaVersion** hodnota následujícím způsobem:

    ```json
    "SchemaVersion": "1.0.0",
    ```

1. V **InitialState** části, přidejte následující dvě definice:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. V **Telemetrie** pole, přidejte následující definice:

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

1. Uložit **C:\temp\devicemodels\chiller-01.json** souboru.

1. Otevřít **C:\temp\devicemodels\scripts\chiller-01-state.js** souboru.

1. Přidejte následující pole na **stavu** proměnné:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aktualizace **hlavní** funkce takto:

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

1. Uložit **C:\temp\devicemodels\scripts\chiller-01-state.js** souboru.

## <a name="create-the-lightbulb"></a>Vytvořte na žárovku

V této části můžete definovat nové **žárovky** typ zařízení:

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

    Uložit změny do **C:\temp\devicemodels\lightbulb-01.json**.

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

    Uložit změny do **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

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

    Uložit změny do **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

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

    Uložit změny do **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Právě jste vytvořili vlastní verze **chladič** typu zařízení a vytvořili novou **žárovky** typ zařízení.

## <a name="test-the-devices"></a>Testování zařízení

V této části provedete testovací typy zařízení, které jste vytvořili v předchozích částech místně.

### <a name="run-the-device-simulation-microservice"></a>Spustit mikroslužeb simulace zařízení

Otevřít **zařízení simulace dotnet-master** složky, které jste si stáhli z Githubu v nové instanci sady Visual Studio Code. Získáte po kliknutí na **obnovení** tlačítka a opravte všechna nevyřešené závislosti.

Otevřít **.vscode/launch.json** soubor a přiřadit k připojovací řetězec služby IoT Hub **PCS_IOTHUB_CONNSTRING** proměnné prostředí.

Otevřít **WebService/Properties/launchSettings.json** soubor a přiřadit k připojovací řetězec služby IoT Hub **PCS_IOTHUB_CONNSTRING** proměnné prostředí.

Otevřít **WebService/appsettings.ini** soubor a upravit nastavení následujícím způsobem:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Chcete-li spustit místně mikroslužbách, klikněte na tlačítko **ladit > Spustit ladění**.

**Terminálu** okně ve Visual Studio Code se zobrazí výstup ze spuštěné mikroslužeb.

Ponechte mikroslužeb simulace zařízení běží v této instanci sady Visual Studio Code při dokončování dalších kroků.

### <a name="set-up-a-monitor-for-device-events"></a>Nastavení monitorování pro události zařízení

V této části použijete rozhraní příkazového řádku Azure k nastavení monitorování událostí, chcete-li zobrazit telemetrická data odesílaná ze zařízení připojená ke službě IoT hub.

Následující skript předpokládá, že je název služby IoT hub **test simulace zařízení**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Ponechte monitorování událostí při testování simulovaných zařízení.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Vytvořit simulaci s typem zařízení aktualizované chladič

V této části použijete nástroj Postman k vyžádání mikroslužeb simulace zařízení ke spuštění simulace použití typu aktualizované chladič zařízení. Postman je nástroj, který vám umožní odeslat požadavky REST k webové službě. Konfigurační soubory Postman, musíte se v místní kopii **zařízení – simulace dotnet** úložiště.

Nastavení nástroje Postman:

1. Otevřete nástroj Postman na místním počítači.

1. Klikněte na tlačítko **soubor > Import**. Pak klikněte na tlačítko **vybrat soubory**.

1. Přejděte **zařízení – simulace dotnet/docs/postman** složky. Vyberte **simulace zařízení Azure IoT řešení accelerator.postman_collection** a **simulace zařízení Azure IoT řešení accelerator.postman_environment** a klikněte na tlačítko **otevřete**.

1. Rozbalte **akcelerátor řešení simulace zařízení IoT Azure** na požadavky můžete odesílat.

1. Klikněte na tlačítko **prostředí bez** a vyberte **akcelerátor řešení simulace zařízení IoT Azure**.

Teď máte kolekci a prostředí načteny ve vašem pracovním prostoru, který slouží k interakci s mikroslužeb simulace zařízení Postman.

Pro konfiguraci a spuštění simulace:

1. V kolekci Postman, vyberte **vytvořit upravit chladič simulace** a klikněte na tlačítko **odeslat**. Tento požadavek vytvoří čtyři instance daného typu chladič simulované zařízení.

1. Výstup monitorování událostí v okně rozhraní příkazového řádku Azure zobrazí telemetrie ze simulovaných zařízení, včetně nového **internal_temperature** hodnoty.

Pro zastavení simulace, vyberte **zastavit simulaci** žádost do Postman a klikněte na **odeslat**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Vytvořit simulaci s typem zařízení žárovky

V této části použijete nástroj Postman k vyžádání mikroslužeb simulace zařízení ke spuštění simulace pomocí žárovky typu zařízení. Postman je nástroj, který vám umožní odeslat požadavky REST k webové službě.

Pro konfiguraci a spuštění simulace:

1. V kolekci Postman, vyberte **vytvořit žárovky simulaci** a klikněte na tlačítko **odeslat**. Tento požadavek vytvoří dvě instance typu žárovka s Simulovaná zařízení.

1. Výstup monitorování událostí v okně rozhraní příkazového řádku Azure zobrazí telemetrie ze simulovaných lightbulbs.

Pro zastavení simulace, vyberte **zastavit simulaci** žádost do Postman a klikněte na **odeslat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete zastavit dvěma místně spuštěné mikroslužby v jejich instance aplikace Visual Studio Code (**ladit > Zastavit ladění**).

Pokud už nepotřebujete instance služby IoT Hub a Cosmos DB, můžete je odstraňte z vašeho předplatného Azure, aby se zabránilo zbytečným poplatkům.