---
title: Instalace a použití aplikace Azure IoT Explorer | Microsoft Docs
description: Nainstalujte nástroj Azure IoT Explorer a použijte ho k interakci se zařízeními IoT technologie Plug and Play ve verzi Preview připojenou ke službě IoT Hub.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: f59e449589c7f3027dc8a9daf9d8d12f04831dd7
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960577"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalace a použití Azure IoT Exploreru

Azure IoT Explorer je grafický nástroj pro interakci s a testováním zařízení IoT technologie Plug and Play ve verzi Preview. Po instalaci nástroje na místní počítač ho můžete použít pro připojení k zařízení. Nástroj můžete použít k zobrazení telemetrie, kterou zařízení odesílá, práci s vlastnostmi zařízení a příkazy volání.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte nástroj Azure IoT Explorer.
- Použijte nástroj k interakci a testování zařízení.

## <a name="prerequisites"></a>Požadavky

K použití nástroje Azure IoT Explorer potřebujete:

- Azure IoT Hub. Existuje mnoho způsobů, jak přidat službu IoT Hub k předplatnému Azure, jako je [vytvoření centra IoT pomocí rozhraní příkazového řádku Azure](../iot-hub/iot-hub-create-using-cli.md). K spuštění nástroje Azure IoT Explorer potřebujete připojovací řetězec služby IoT Hub. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Zařízení zaregistrované ve službě IoT Hub. K registraci zařízení můžete použít následující příkaz rozhraní příkazového řádku Azure CLI. Nezapomeňte nahradit zástupné symboly `{YourIoTHubName}` a `{YourDeviceID}` hodnotami:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Nainstalovat Azure IoT Explorer

V [Azure IoT Exploreru](https://github.com/Azure/azure-iot-explorer/releases) Vydáte verze a rozbalte seznam assetů pro nejnovější verzi. Stáhněte a nainstalujte nejnovější verzi aplikace.

## <a name="use-azure-iot-explorer"></a>Použití Azure IoT Exploreru

V případě zařízení můžete buď připojit vlastní zařízení, nebo použít některé z našich ukázkových simulovaných zařízení. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) spusťte ukázku simulovaného zařízení.

### <a name="connect-to-your-hub"></a>Připojení k centru

Při prvním spuštění aplikace Azure IoT Explorer budete vyzváni k zadání připojovacího řetězce služby IoT Hub. Po přidání připojovacího řetězce vyberte **připojit**. Nastavení tohoto nástroje můžete použít k přepnutí do jiného centra IoT pomocí aktualizace připojovacího řetězce.

Definice modelu pro zařízení IoT technologie Plug and Play je uložená ve veřejném úložišti, v úložišti společnosti nebo připojeném zařízení. Ve výchozím nastavení nástroj vyhledá definici modelu ve veřejném úložišti modelů a připojeném zařízení. Můžete přidat nebo odebrat zdroje nebo nakonfigurovat prioritu zdrojů v **nastaveních**:

Přidání zdroje:

1. Přejít na **Nastavení**.
1. Vyberte **Nový** a zvolte svůj zdroj.
1. Pokud přidáváte úložiště modelu společnosti, zadejte připojovací řetězec.

Postup odebrání zdroje:

1. Přejít na **Nastavení**.
1. Vyhledejte zdroj, který chcete odebrat.
1. Vyberte **X** a odeberte je. Úložiště veřejného modelu nejde odebrat, protože definice společných rozhraní pocházejí z tohoto úložiště.

Změnit zdrojové priority:

Jeden ze zdrojů definice modelu můžete přetáhnout do jiného hodnocení v seznamu. Pokud dojde ke konfliktu, potlačí zdroje definic s vyšším pořadím zdroje s nižším hodnocením.

### <a name="view-devices"></a>Zobrazit zařízení

Po připojení nástroje k centru IoT se zobrazí stránka seznam **zařízení** se seznamem identit zařízení zaregistrovaných ve službě IoT Hub. Pokud chcete zobrazit další informace, můžete rozbalit libovolnou položku v seznamu.

Na stránce seznam **zařízení** můžete:

- Vyberte **Přidat** a zaregistrujte nové zařízení v centru. Pak zadejte ID zařízení. Pomocí výchozího nastavení můžete automaticky generovat ověřovací klíče a povolit připojení k rozbočovači.
- Vyberte zařízení a pak vyberte **Odstranit** a odstraňte identitu zařízení. Před dokončením této akce zkontrolujte podrobnosti o zařízení, abyste měli jistotu, že odstraňujete správnou identitu zařízení.
- Dotaz na `capabilityID` a `interfaceID`. Přidejte buď `capabilityID`, nebo `interfaceID` jako parametr pro dotazování zařízení.

## <a name="interact-with-a-device"></a>Interakce se zařízením

Na stránce seznam **zařízení** výběrem hodnoty ve sloupci **ID zařízení** zobrazíte stránku podrobností registrovaného zařízení. Pro zařízení jsou dvě části: **zařízení** a **digitální dvojitá**dvojice.

### <a name="device"></a>Zařízení

Tato část obsahuje karty **Identita zařízení**, **vyzdvojené zařízení**a **telemetrie** .

- Informace o [identitě zařízení](../iot-hub/iot-hub-devguide-identity-registry.md) můžete zobrazit a aktualizovat na kartě **Identita zařízení** .
- Na kartě **zařízení** , na které se nachází, můžete získat přístup k [dvojitým](../iot-hub/iot-hub-devguide-device-twins.md) informacím o zařízení.
- Pokud je zařízení připojené a aktivně odesílá data, můžete zobrazit [telemetrii](../iot-hub/iot-hub-devguide-messages-read-builtin.md) na kartě **telemetrie** .

### <a name="digital-twin"></a>Digitální vlákna

Nástroj můžete použít k zobrazení digitálního vlákna instance zařízení. V případě zařízení IoT technologie Plug and Play se v tomto článku zobrazují všechna rozhraní přidružená k modelu schopností zařízení. Vyberte rozhraní a rozbalte odpovídající [technologie Plug and Play primitivních elementů IoT](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

#### <a name="properties"></a>Vlastnosti

Vlastnosti určené jen pro čtení můžete zobrazit na stránce **vlastností** na rozhraní. Můžete aktualizovat zapisovatelné vlastnosti definované v rozhraní na stránce **vlastností s možností zápisu** .

1. Přejít na stránku **vlastností s možností zápisu** .
1. Klikněte na vlastnost, kterou chcete aktualizovat.
1. Zadejte novou hodnotu pro vlastnost.
1. Zobrazte si náhled datové části, která se má odeslat do zařízení.
1. Odešlete změnu.

Po odeslání změny můžete sledovat stav aktualizace: **synchronizace**, **úspěch**nebo **Chyba**. Po dokončení synchronizace se zobrazí nová hodnota vlastnosti ve sloupci **nahlášená vlastnost** . Pokud přejdete na jiné stránky před dokončením synchronizace, nástroj vás po dokončení aktualizace stále upozorní. Historii oznámení můžete zobrazit také pomocí Centra oznámení nástroje.

#### <a name="commands"></a>Příkazy

Pokud chcete odeslat příkaz do zařízení, otevřete stránku **příkazy** :

1. V seznamu příkazů rozbalte příkaz, který chcete aktivovat.
1. Zadejte všechny požadované hodnoty příkazu.
1. Zobrazte si náhled datové části, která se má odeslat do zařízení.
1. Odešlete příkaz.

#### <a name="telemetry"></a>Telemetrie

Chcete-li zobrazit telemetrii pro vybrané rozhraní, přejdete na stránku **telemetrie** .

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak nainstalovat a používat Azure IoT Explorer k interakci s technologie Plug and Playmi zařízeními IoT. Navržený další krok se naučíte, jak [nainstalovat a používat rozšíření Azure CLI](./howto-install-pnp-cli.md).
