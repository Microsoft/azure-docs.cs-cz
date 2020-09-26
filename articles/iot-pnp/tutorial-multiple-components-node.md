---
title: Ukázka připojení IoT technologie Plug and Play Preview Node.js kódu zařízení komponenty do IoT Hub | Microsoft Docs
description: Sestavujte a spusťte ukázkovou technologie Plug and Play IoT Preview Node.js kódu zařízení, který používá více komponent a připojuje se ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js
ms.openlocfilehash: 24bfbf4199671da497844444a57e566e66eb8c90
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308210"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Kurz: připojení ukázkové IoT technologie Plug and Play ve verzi Preview aplikace zařízení s více součástmi pro IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

V tomto kurzu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play pro zařízení pomocí komponent a kořenového rozhraní, připojit ho ke službě IoT Hub a pomocí nástroje Azure IoT Explorer zobrazit informace, které posílá do centra. Ukázková aplikace je napsaná pro Node.js a je obsažená v sadě SDK pro zařízení Azure IoT Hub pro Node.js. Tvůrce řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by bylo nutné zobrazovat kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete Node.js na svém vývojovém počítači. Nejnovější doporučenou verzi si můžete stáhnout z [NodeJS.org](https://nodejs.org)na víc platforem.

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

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

## <a name="download-the-code"></a>Stáhněte si kód

V tomto kurzu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady SDK pro zařízení Azure IoT Hub pro Node.js.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje [sadu Microsoft Azure IoT SDK pro Node.js](https://github.com/Azure/azure-iot-sdk-node) úložiště GitHub do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Dokončení této operace může trvat několik minut.

## <a name="install-required-libraries"></a>Nainstalovat požadované knihovny

Pomocí sady SDK pro zařízení sestavíte zahrnutý vzorový kód. Aplikace, kterou vytváříte, simuluje technologie Plug and Play zařízení s více komponentami a kořenovým rozhraním, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V místním okně terminálu přejděte do složky naklonovaného úložiště a přejděte do složky */Azure-IoT-SDK-Node/Device/Samples/PNP* . Pak spuštěním následujícího příkazu nainstalujte požadované knihovny:

```cmd/sh
npm install
```

Tím se nainstalují relevantní soubory npm potřebné ke spuštění ukázek ve složce.

1. Nakonfigurujte proměnnou prostředí pomocí připojovacího řetězce zařízení, který jste si poznamenali dříve:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>Kontrola kódu

Přejděte do složky *Azure-IoT-SDK-node\device\samples\pnp* .

Složka *Azure-IoT-SDK-node\device\samples\pnp* obsahuje vzorový kód pro zařízení adaptéru pro řízení teploty IoT technologie Plug and Play.

Kód v souboru *pnpTemperatureController.js* implementuje zařízení adaptéru IoT technologie Plug and Play. Model, který tato ukázka implementuje, používá [více komponent](concepts-components.md). [Soubor modelu DTDL (Digital Real Definition Language) pro zařízení s teplotou](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definuje telemetrii, vlastnosti a příkazy, které zařízení implementuje.

Otevřete soubor *pnpTemperatureController.js* v editoru kódu dle vašeho výběru. Vzorový kód ukazuje, jak:

1. Definujte, `modelId` který z nich je DTMI pro zařízení, které je právě implementováno. Tato DTMI je definovaná uživatelem a musí odpovídat DTMI [modelu DTDL řadiče teploty](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

2. Implementujte komponenty definované v modelu DTDL řadiče teploty. Komponenty v reálném teplotním adaptéru by měly implementovat tato dvě rozhraní. Tato dvě rozhraní jsou již publikována v centrálním úložišti. V této ukázce jsou tato dvě rozhraní:
  - Termostat
  - Informace o zařízeních vyvinutých v Azure

3. Definujte názvy součástí. Tato ukázka má dvě termostaty a jednu komponentu informací o zařízení.

4. Zadejte název příkazu. Toto jsou příkazy, na které zařízení reaguje.

5. Definujte `serialNumber` konstantu. Toto `serialNumber` zařízení je pevně dané.

6. Definujte obslužné rutiny příkazů.

7. Definujte funkce pro odeslání odpovědí na příkazy.

8. Definujte pomocné funkce pro protokolování požadavků příkazu.

9. Definujte pomocnou funkci pro vytvoření vlastností.

10. Definujte naslouchací proces pro aktualizace vlastností.

11. Definujte funkci pro odeslání telemetrie z tohoto zařízení. Všechna termostaty i kořenové komponenty odesílají telemetrii. Tato funkce přijímá název komponenty jako parametr.

12. Definujte `main` funkci, která:

    1. Pomocí sady SDK pro zařízení vytvořte klienta zařízení a připojte se ke službě IoT Hub. Zařízení dodá `modelId` , aby IoT Hub mohl zařízení identifikovat jako zařízení technologie Plug and Play IoT.

    1. Spustí naslouchání žádostí o příkazy pomocí `onDeviceMethod` funkce. Funkce nastaví naslouchací proces pro požadavky příkazu ze služby:
        - DTDL zařízení definuje `reboot` `getMaxMinReport` příkazy a.
        - `commandHandler`Funkce definuje způsob, jakým zařízení reaguje na příkaz.

    1. Spustí odesílání telemetrie pomocí `setInterval` a `sendTelemetry` .

    1. Pomocí `helperCreateReportedPropertiesPatch` funkce vytvoří vlastnosti a `updateComponentReportedProperties` aktualizuje vlastnosti.

    1. Nástroj používá `desiredPropertyPatchListener` k naslouchání aktualizacím vlastností.

    1. Zakáže všechny naslouchací procesy a úlohy a ukončí smyčku po stisknutí **q** nebo **q**.

Teď, když jste viděli kód, použijte následující příkaz ke spuštění ukázky:

```cmd\sh
node pnpTemperatureController.js
```

Zobrazí se následující výstup, který indikuje, že zařízení začalo odesílat data telemetrie do centra, a teď je připravené přijímat příkazy a aktualizace vlastností.

![Potvrzovací zprávy zařízení](media/tutorial-multiple-components-node/multiple-component.png)

Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Použití Azure IoT Exploreru k ověření kódu

Po spuštění ukázky klienta zařízení použijte nástroj Azure IoT Explorer a ověřte, jestli funguje.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili připojit zařízení IoT technologie Plug and Play k komponentám do služby IoT Hub. Další informace o modelech zařízení IoT technologie Plug and Play najdete v tématech:

> [!div class="nextstepaction"]
> [Příručka pro vývojáře pro modelování IoT technologie Plug and Play Preview](concepts-developer-guide.md)
