---
title: Připojte si ukázkový kód zařízení součásti Python technologie Plug and Play Preview k IoT Hub | Microsoft Docs
description: Sestavte a spouštějte ukázkový kód zařízení v Pythonu technologie Plug and Play ve verzi Preview, který používá více komponent a připojuje se ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0cde9caa2f2b68b1e75eac635a81865cc4b6b33c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352760"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>Kurz: připojení ukázkové IoT technologie Plug and Play ve verzi Preview aplikace zařízení s více komponentami pro IoT Hub (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play pro zařízení pomocí komponent a kořenového rozhraní, připojit ho ke službě IoT Hub a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná v Pythonu a je obsažená v sadě SDK pro zařízení Azure IoT pro Python. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete Python 3,7 na vašem vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [Python.org](https://www.python.org/)na víc platforem. Verzi Pythonu si můžete ověřit pomocí následujícího příkazu:  

```cmd/sh
python --version
```

Nejnovější doporučenou verzi si můžete stáhnout z [Python.org](https://www.python.org/)na víc platforem.

### <a name="azure-iot-explorer"></a>Průzkumník Azure IoT

Pokud chcete s ukázkovým zařízením pracovat v druhé části tohoto kurzu, použijte nástroj **Azure IoT Explorer** . [Stáhněte a nainstalujte si nejnovější verzi Azure IoT Exploreru](./howto-use-iot-explorer.md) pro váš operační systém.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum. Poznamenejte si tento připojovací řetězec, použijete ho později v tomto kurzu:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Připojovací řetězec služby IoT Hub můžete najít také pomocí nástroje Azure IoT Explorer.

Spuštěním následujícího příkazu Získejte _připojovací řetězec zařízení_ pro zařízení, které jste přidali do centra. Poznamenejte si tento připojovací řetězec, použijete ho později v tomto kurzu:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>Nastavení prostředí

Tento balíček se publikuje jako PIP pro aktualizaci Public Preview. Verze balíčku by měla být nejnovější nebo`2.1.4`

V místním prostředí Python nainstalujte soubor následujícím způsobem:

```cmd/sh
pip install azure-iot-device
```

Naklonujte úložiště IoT sady Python SDK a podívejte se na **verzi PNP-Preview-Refresh**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Kontrola kódu

Tato ukázka implementuje zařízení adaptéru technologie Plug and Play pro řízení teploty pro IoT. Model, který tato ukázka implementuje, používá [více komponent](concepts-components.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Složka *Azure-IoT-SDK-python\azure-IoT-device\samples\pnp* obsahuje vzorový kód pro zařízení IoT technologie Plug and Play. Soubory pro příklad řadiče teploty jsou:

- pnp_temp_controller_with_thermostats. py
- pnp_helper_preview_refresh. py

Kontroler teploty má více komponent a kořenového rozhraní na základě modelu DTDL řadiče teploty.

Otevřete soubor *pnp_temp_controller_with_thermostats. py* v editoru dle vašeho výběru. Kód v tomto souboru:

1. Importy `pnp_helper_preview_refresh.py` pro získání přístupu k podpůrným metodám.

1. Definuje dva identifikátory digitálního vlákna modelu (DTMIs), které jednoznačně reprezentují dvě různá rozhraní definovaná v modelu DTDL. Komponenty v reálném teplotním adaptéru by měly implementovat tato dvě rozhraní. Tato dvě rozhraní jsou již publikována v centrálním úložišti. Tyto DTMIs musí být pro uživatele známé a mohou se lišit v závislosti na scénáři implementace zařízení. Pro aktuální ukázku tyto dvě rozhraní reprezentují:

  - Termostat
  - Informace o zařízení vyvinuté v Azure.

. Definuje DTMI `model_id` pro zařízení, které je právě implementováno. DTMI je definován uživatelem a musí odpovídat DTMI v souboru modelu DTDL.

1. Definuje názvy dané součásti v souboru DTDL. V DTDL a jedné součásti informací o zařízení se nacházejí dva termostaty. `serial_number`V kořenovém rozhraní je také definována konstanta s názvem. `serial_number`U zařízení se nedá změnit.

1. Definuje implementace obslužných rutin příkazů. Definují, co zařízení provede při přijímání požadavků na příkazy.

1. Definuje funkce pro vytvoření odezvy příkazu. Definují, jak zařízení reaguje na požadavky příkazu. Funkce odezvy příkazu vytvoříte, pokud příkaz potřebuje odeslat vlastní odpověď zpátky do centra IoT. Pokud není k dispozici funkce odpovědi pro příkaz, pošle se obecná odpověď. V této ukázce má pouze příkaz **getMaxMinReport** vlastní odpověď.

1. Definuje funkci pro odesílání telemetrie z tohoto zařízení. Jak termostat, tak i kořenové rozhraní odesílají telemetrii. Tato funkce přebírá volitelný parametr názvu komponenty, který umožňuje určit, která komponenta tuto telemetrii poslala.

1. Definuje naslouchací proces pro žádosti příkazů.

1. Definuje naslouchací proces pro požadované aktualizace vlastností.

1. Má `main` funkci, která:

    1. Pomocí sady SDK pro zařízení vytvořte klienta zařízení a připojte se ke službě IoT Hub. Zařízení pošle `model_id` , aby Centrum IoT mohl zařízení identifikovat jako zařízení IoT technologie Plug and Play.

    1. `create_reported_properties`Vytvoří vlastnosti pomocí funkce v souboru pomocníka. Předat název součásti a vlastnosti jako páry klíč-hodnota této funkci.

    1. Aktualizuje čitelné vlastnosti pro své komponenty voláním `patch_twin_reported_properties` .

    1. Spustí naslouchání žádostí o příkazy pomocí `execute_command_listener` funkce. Funkce nastaví naslouchací proces pro požadavky příkazu ze služby. Při nastavování naslouchacího procesu můžete zadat `method_name` , `user_command_handler` a volitelné `create_user_response_handler` jako parametry.
        - `method_name`Definuje požadavek příkazu. V této ukázce model definuje příkazy pro **restartování**a **getMaxMinReport**.
        - `user_command_handler`Funkce definuje, co by mělo zařízení dělat při přijetí příkazu.
        - `create_user_response_handler`Funkce vytvoří odpověď, která se odešle do služby IoT Hub, když se příkaz úspěšně spustí. Tuto odpověď můžete zobrazit na portálu. Pokud tato funkce není k dispozici, je službě odeslána obecná odpověď.

    1. Nástroj používá `execute_property_listener` k naslouchání aktualizacím vlastností.

    1. Spustí odesílání telemetrie pomocí `send_telemetry` . Vzorový kód používá smyčku pro volání tří funkcí odesílání telemetrie. Každá z nich se volá každých osm sekund.

    1. Zakáže všechny naslouchací procesy a úlohy a ukončí smyčku po stisknutí **q** nebo **q**.

Teď, když jste viděli kód, vytvořte proměnnou prostředí s názvem **IOTHUB_DEVICE_CONNECTION_STRING** pro uložení připojovacího řetězce zařízení, na který jste si poznamenali dříve. Pomocí následujícího příkazu spusťte ukázku:

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

Ukázkové zařízení odesílá zprávy telemetrie každé pár sekund do služby IoT Hub.

Zobrazí se následující výstup, který indikuje, že zařízení odesílá data telemetrie do centra, a teď je připravené přijímat příkazy a aktualizace vlastností.

![Potvrzovací zprávy zařízení](media/tutorial-multiple-components-python/multiple-component.png)

Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili připojit zařízení IoT technologie Plug and Play k komponentám do služby IoT Hub. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře pro modelování IoT technologie Plug and Play Preview](concepts-developer-guide.md)
