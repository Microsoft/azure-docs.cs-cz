---
title: Připojení ukázkového kódu zařízení pro IoT technologie Plug and Play Preview IoT Hub | Microsoft Docs
description: Pomocí Java sestavíte a spustíte IoT technologie Plug and Play Preview ukázkový kód zařízení, který se připojuje ke centru IoT. K zobrazení informací odesílaných zařízením do centra použijte nástroj Azure IoT Explorer.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e1454a197620cacaa8c8303e8f36a8d4a87aec00
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531305"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Rychlý Start: připojení ukázkové aplikace technologie Plug and Play v rámci IoT pro IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

V tomto rychlém startu se dozvíte, jak vytvořit ukázkovou aplikaci IoT technologie Plug and Play zařízení, jak ji připojit ke službě IoT Hub, a pomocí nástroje Azure IoT Explorer zobrazit informace, které odesílá do centra. Ukázková aplikace je napsaná v jazyce Java a poskytuje se jako součást ukázek Azure IoT pro kolekci Java. Vývojář řešení může pomocí nástroje Azure IoT Explorer pochopit možnosti zařízení technologie Plug and Play IoT, aniž by musel zobrazovat žádný kód zařízení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete Java SE 8 na svém vývojovém počítači. Také je potřeba nainstalovat Maven 3.

Podrobnosti o tom, jak s nimi nastavovat, najdete v tématu [Příprava vývojového prostředí](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) v sadě SDK pro zařízení Microsoft Azure IoT pro jazyk Java.

### <a name="install-the-azure-iot-explorer"></a>Instalace Azure IoT Exploreru

Stáhněte a nainstalujte si nejnovější verzi **Azure IoT Exploreru** ze stránky [úložiště](https://github.com/Azure/azure-iot-explorer/releases) tohoto nástroje tak, že vyberete soubor. msi v části assets (prostředky) pro nejnovější aktualizaci.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Spuštěním následujícího příkazu Získejte _připojovací řetězec služby IoT Hub_ pro vaše centrum (Poznámka pro pozdější použití):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí

V tomto rychlém startu připravíte vývojové prostředí, které můžete použít ke klonování a sestavování ukázek Azure IoT pro jazyk Java.

Otevřete okno terminálu v adresáři dle vašeho výběru. Spuštěním následujícího příkazu naklonujte [ukázky Azure IoT pro](https://github.com/Azure-Samples/azure-iot-samples-java) úložiště GitHub Java do tohoto umístění:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Dokončení této operace může trvat několik minut.

## <a name="build-the-code"></a>Sestavení kódu

Pomocí klonovaného ukázkového kódu sestavíte aplikaci, která simuluje zařízení, které se připojuje ke službě IoT Hub. Aplikace odesílá telemetrie a vlastnosti a přijímá příkazy.

1. V místním okně terminálu přejděte do složky naklonovaného úložiště a přejděte do složky **/Azure-IoT-Samples-Java/Digital-Twin/Samples/Device/JdkSample** . Pak spuštěním následujícího příkazu nainstalujte požadované knihovny a sestavte aplikaci simulovaného zařízení:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurace _připojovacího řetězce zařízení_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Spuštění ukázky zařízení

Spusťte ukázkovou aplikaci pro simulaci technologie Plug and Play zařízení IoT, které odesílá telemetrii do služby IoT Hub. Chcete-li spustit ukázkovou aplikaci, použijte následující příkaz:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Zobrazí se zpráva oznamující, že je zařízení připojené, provádění různých kroků instalace a čekání na aktualizace služby, a potom i protokoly telemetrie. To znamená, že zařízení je nyní připraveno přijímat příkazy a aktualizace vlastností a zahájilo posílání dat telemetrie do centra. Ponechte ukázku spuštěnou při dokončení dalších kroků.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Použití Průzkumníka Azure IoT k ověření kódu

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Chcete-li zajistit, aby nástroj mohl číst definice modelů rozhraní ze zařízení, vyberte **Nastavení**. V nabídce nastavení se **v připojeném zařízení** už může zobrazit konfigurace technologie Plug and Play. Pokud ne, vyberte **+ Přidat zdroj definice modulu** a pak **na připojeném zařízení** ho přidejte.

1. Zpátky na stránce Přehled **zařízení** vyhledejte identitu zařízení, kterou jste vytvořili dříve. Když je aplikace zařízení pořád spuštěná na příkazovém řádku, ověřte, že se **stav připojení** zařízení v Azure IoT Exploreru hlásí jako _připojené_ (Pokud ne, stiskněte **aktualizovat** , dokud není). Kliknutím na zařízení zobrazíte další podrobnosti.

1. Rozbalte rozhraní s IDENTIFIKÁTORem **urn: java_sdk_sample: EnvironmentalSensor: 1** a odhalte rozhraní a technologie Plug and Play primitivních elementů IoT – vlastnosti, příkazy a telemetrie.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili připojit zařízení IoT technologie Plug and Play ke službě IoT Hub. Další informace o tom, jak vytvořit řešení, které komunikuje s technologie Plug and Play vašich zařízení IoT, najdete tady:

> [!div class="nextstepaction"]
> [Postupy: připojení a interakce se zařízením IoT technologie Plug and Play Preview](howto-develop-solution.md)
