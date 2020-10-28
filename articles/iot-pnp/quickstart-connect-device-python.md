---
title: Připojení kódu IoT technologie Plug and Play ukázkového zařízení v Pythonu do Azure IoT Hub | Microsoft Docs
description: Pomocí Pythonu můžete sestavovat a spouštět ukázkový kód zařízení technologie Plug and Play IoT, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 065e65c0c8ccf61dd67da19fd5a94d4506db73ff
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737301"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-to-iot-hub-python"></a>Rychlý Start: připojení ukázkové aplikace IoT technologie Plug and Play zařízení k IoT Hub (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit k centru IoT a použít nástroj Azure IoT Explorer k zobrazení telemetrie, kterou posílá. Ukázková aplikace je napsaná pro Python a je součástí sady Azure IoT Hub zařízení SDK pro Python. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

K dokončení tohoto rychlého startu potřebujete Python 3,7 na vašem vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [Python.org](https://www.python.org/)na víc platforem. Verzi Pythonu si můžete ověřit pomocí následujícího příkazu:  

```cmd/sh
python --version
```

V místním prostředí Python nainstalujte balíček následujícím způsobem:

```cmd/sh
pip install azure-iot-device
```

Naklonujte úložiště IoT sady Python SDK a podívejte se na **hlavní server** :

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>Spuštění ukázkového zařízení

Složka *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* obsahuje vzorový kód pro zařízení IoT technologie Plug and Play. V tomto rychlém startu se používá soubor *simple_thermostat. py* . Tento vzorový kód implementuje zařízení kompatibilní s IoT technologie Plug and Play a používá klientskou knihovnu pro zařízení Azure IoT Python.

V textovém editoru otevřete soubor **simple_thermostat. py** . Všimněte si, jak:

1. Definuje jeden (DTMI) identifikátor modelu s dvojitým zařízením, který jednoznačně představuje [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json). Pro uživatele musí být znám DTMI a může se lišit v závislosti na scénáři implementace zařízení. Pro aktuální vzorek model představuje termostat, který má telemetrii, vlastnosti a příkazy spojené s teplotou monitorování.

1. Obsahuje funkce pro definování implementace obslužných rutin příkazů. Napíšete tyto obslužné rutiny k definování způsobu, jakým zařízení reaguje na požadavky příkazu.

1. Má funkci pro definování odezvy příkazu. Můžete vytvořit funkce odezvy příkazu pro odeslání odpovědi zpět do služby IoT Hub.

1. Definuje funkci posluchače vstupní klávesnice, která umožňuje ukončit aplikaci.

1. Má funkci **Main** . Funkce **Main** :

    1. Pomocí sady SDK pro zařízení vytvořte klienta zařízení a připojte se ke službě IoT Hub.

    1. Aktualizuje vlastnosti. Model používáme, **termostat** , definujeme `targetTemperature` a `maxTempSinceLastReboot` jako dvě vlastnosti pro náš termostat, takže budeme používat. Vlastnosti jsou aktualizovány pomocí `patch_twin_reported_properties` metody definované v `device_client` .

    1. Spustí naslouchání žádostí o příkazy pomocí funkce **execute_command_listener** . Funkce nastaví naslouchací proces, aby naslouchal příkazům pocházejícím ze služby. Při nastavování naslouchacího procesu můžete zadat `method_name` , `user_command_handler` a `create_user_response_handler` .
        - `user_command_handler`Funkce definuje, co by mělo zařízení dělat při přijetí příkazu. Například pokud dojde k vypnutí vašeho alarmu, projeví se probuzení tohoto příkazu. Můžete si to představit jako "efekt" příkazu, který vyvoláte.
        - `create_user_response_handler`Funkce vytvoří odpověď, která se odešle do služby IoT Hub, když se příkaz úspěšně spustí. Pokud je například váš alarm vypnutý, odpovíte tím, že se podržíte do práce znovu, což znamená zpětnou vazbu ke službě. Můžete si to představit jako odpověď, kterou udělíte službě. Tuto odpověď můžete zobrazit na portálu.

    1. Spustí odesílání telemetrie. **Pnp_send_telemetry** je definována v souboru pnp_methods. py. Vzorový kód používá smyčku k volání této funkce každých osm sekund.

    1. Zakáže všechny naslouchací procesy a úlohy a při stisknutí **q** nebo **q** existuje smyčka.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Teď, když jste viděli kód, použijte následující příkaz ke spuštění ukázky:

```cmd/sh
python simple_thermostat.py
```

Zobrazí se následující výstup, který indikuje, že zařízení odesílá data telemetrie do centra, a teď je připravené přijmout příkazy a aktualizace vlastností:

```cmd/sh
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Interakce se zařízením IoT technologie Plug and Play připojeným k vašemu řešení](quickstart-service-python.md)
