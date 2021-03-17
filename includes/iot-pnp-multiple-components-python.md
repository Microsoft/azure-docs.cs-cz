---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5e0fa474412815a1ce830267ac2f09830a6353d3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834133"
---
V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení pomocí komponent, připojit ji k centru IoT a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná v Pythonu a je obsažená v sadě SDK pro zařízení Azure IoT pro Python. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

V tomto kurzu jste:

> [!div class="checklist"]
> * Stáhněte si vzorový kód.
> * Spusťte ukázkovou aplikaci zařízení a ověřte, že se připojí ke službě IoT Hub.
> * Zkontrolujte zdrojový kód.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

K dokončení tohoto kurzu potřebujete Python 3,7 na vašem vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [Python.org](https://www.python.org/)na víc platforem. Verzi Pythonu si můžete ověřit pomocí následujícího příkazu:  

```cmd/sh
python --version
```

Nejnovější doporučenou verzi si můžete stáhnout z [Python.org](https://www.python.org/)na víc platforem.

## <a name="download-the-code"></a>Stáhněte si kód

Balíček **Azure-IoT-Device** se zveřejňuje jako PIP.

V místním prostředí Python nainstalujte balíček následujícím způsobem:

```cmd/sh
pip install azure-iot-device
```

Pokud jste dokončili [rychlý Start: Připojte ukázkovou aplikaci IoT technologie Plug and Play zařízení běžící v systému Windows k IoT Hub (Python)](../articles/iot-pnp/quickstart-connect-device.md), již jste naklonoval úložiště.

Naklonujte úložiště IoT sady Python SDK:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje zařízení adaptéru technologie Plug and Play pro řízení teploty pro IoT. Model, který tato ukázka implementuje, používá [více komponent](../articles/iot-pnp/concepts-components.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Složka *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* obsahuje vzorový kód pro zařízení IoT technologie Plug and Play. Soubory pro příklad řadiče teploty jsou:

- temp_controller_with_thermostats. py
- pnp_helper. py

Kontroler teploty má více komponent a výchozí komponentu na základě modelu DTDL řadiče teploty.

Otevřete soubor *temp_controller_with_thermostats. py* v editoru dle vašeho výběru. Kód v tomto souboru:

1. Importy `pnp_helper.py` pro získání přístupu k podpůrným metodám.

1. Definuje dva identifikátory digitálního vlákna modelu (DTMIs), které jednoznačně reprezentují dvě různá rozhraní definovaná v modelu DTDL. Komponenty v reálném teplotním adaptéru by měly implementovat tato dvě rozhraní. Tato dvě rozhraní jsou již publikována v centrálním úložišti. Tyto DTMIs musí být pro uživatele známé a mohou se lišit v závislosti na scénáři implementace zařízení. Pro aktuální ukázku tyto dvě rozhraní reprezentují:

    - Termostat
    - Informace o zařízení vyvinuté v Azure.

1. Definuje DTMI `model_id` pro zařízení, které je právě implementováno. DTMI je definován uživatelem a musí odpovídat DTMI v souboru modelu DTDL.

1. Definuje názvy dané součásti v souboru DTDL. V DTDL a jedné součásti informací o zařízení se nacházejí dva termostaty. `serial_number`Ve výchozí komponentě je definována také konstanta s názvem. `serial_number`U zařízení se nedá změnit.

1. Definuje implementace obslužných rutin příkazů. Definují, co zařízení provede při přijímání požadavků na příkazy.

1. Definuje funkce pro vytvoření odezvy příkazu. Definují, jak zařízení reaguje na požadavky příkazu. Funkce odezvy příkazu vytvoříte, pokud příkaz potřebuje odeslat vlastní odpověď zpátky do centra IoT. Pokud není k dispozici funkce odpovědi pro příkaz, pošle se obecná odpověď. V této ukázce má pouze příkaz **getMaxMinReport** vlastní odpověď.

1. Definuje funkci pro odesílání telemetrie z tohoto zařízení. Termostaty i výchozí součást odesílají telemetrii. Tato funkce přebírá volitelný parametr názvu komponenty, který umožňuje určit, která komponenta tuto telemetrii poslala.

1. Definuje naslouchací proces pro žádosti příkazů.

1. Definuje naslouchací proces pro požadované aktualizace vlastností.

1. Má `main` funkci, která:

    - Pomocí sady SDK pro zařízení vytvořte klienta zařízení a připojte se ke službě IoT Hub. Zařízení pošle `model_id` , aby Centrum IoT mohl zařízení identifikovat jako zařízení IoT technologie Plug and Play.

    - `create_reported_properties`Vytvoří vlastnosti pomocí funkce v souboru pomocníka. Předat název součásti a vlastnosti jako páry klíč-hodnota této funkci.

    - Aktualizuje čitelné vlastnosti pro své komponenty voláním `patch_twin_reported_properties` .

    - Spustí naslouchání žádostí o příkazy pomocí `execute_command_listener` funkce. Funkce nastaví naslouchací proces pro požadavky příkazu ze služby. Při nastavování naslouchacího procesu můžete zadat `method_name` , `user_command_handler` a volitelné `create_user_response_handler` jako parametry.
        - `method_name`Definuje požadavek příkazu. V této ukázce model definuje příkazy pro **restartování** a **getMaxMinReport**.
        - `user_command_handler`Funkce definuje, co by mělo zařízení dělat při přijetí příkazu.
        - `create_user_response_handler`Funkce vytvoří odpověď, která se odešle do služby IoT Hub, když se příkaz úspěšně spustí. Tuto odpověď můžete zobrazit na portálu. Pokud tato funkce není k dispozici, je službě odeslána obecná odpověď.

    - Nástroj používá `execute_property_listener` k naslouchání aktualizacím vlastností.

    - Spustí odesílání telemetrie pomocí `send_telemetry` . Vzorový kód používá smyčku pro volání tří funkcí odesílání telemetrie. Každá z nich se volá každých osm sekund.

    - Zakáže všechny naslouchací procesy a úlohy a ukončí smyčku po stisknutí **q** nebo **q**.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Další informace o ukázkové konfiguraci najdete v [ukázkovém souboru Readme](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Pomocí následujícího příkazu spusťte ukázku:

```cmd/sh
python temp_controller_with_thermostats.py
```

Ukázkové zařízení odesílá zprávy telemetrie každé pár sekund do služby IoT Hub.

Zobrazí se následující výstup, který indikuje, že zařízení odesílá data telemetrie do centra, a teď je připravené přijímat příkazy a aktualizace vlastností.

![Potvrzovací zprávy zařízení](media/iot-pnp-multiple-components-python/multiple-component.png)

Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
