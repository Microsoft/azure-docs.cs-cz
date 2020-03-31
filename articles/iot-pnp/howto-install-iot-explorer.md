---
title: Instalace a používání Průzkumníka Azure IoT | Dokumenty společnosti Microsoft
description: Nainstalujte nástroj Průzkumník Azure IoT a použijte ho k interakci se zařízeními IoT Plug and Play Preview připojenými k mému centru IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159179"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalace a používání Průzkumníka Azure IoT

Průzkumník Azure IoT je grafický nástroj pro interakci s a testování zařízení IoT Plug and Play Preview. Po instalaci nástroje do místního počítače jej můžete použít k připojení k zařízení. Pomocí tohoto nástroje můžete zobrazit telemetrii, kterou zařízení odesílá, pracovat s vlastnostmi zařízení a volat příkazy.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte nástroj Průzkumník Azure IoT.
- Pomocí nástroje můžete pracovat se zařízeními a testovat je.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít nástroj Průzkumník Azure IoT, potřebujete:

- Azure IoT hub. Existuje mnoho způsobů, jak přidat službu IoT hub do vašeho předplatného Azure, jako je [například vytvoření centra IoT pomocí azure cli](../iot-hub/iot-hub-create-using-cli.md). Ke spuštění nástroje PrůzkumníkA Azure ioT potřebujete připojovací řetězec služby IoT hub. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
- Zařízení registrované ve vašem centru IoT hub. K registraci zařízení můžete použít následující příkaz příkazu Azure CLI. Nezapomeňte nahradit `{YourIoTHubName}` `{YourDeviceID}` zástupné symboly a hodnotami:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Instalace Průzkumníka Azure IoT

Přejděte na [vydání Průzkumníka Azure IoT](https://github.com/Azure/azure-iot-explorer/releases) a rozbalte seznam prostředků pro nejnovější verzi. Stáhněte a nainstalujte nejnovější verzi aplikace.

## <a name="use-azure-iot-explorer"></a>Použití Průzkumníka Azure IoT

U zařízení můžete buď připojit vlastní zařízení, nebo použít jedno z našich ukázkových simulovaných zařízení. Podle [těchto pokynů](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) spusťte ukázku simulovaných zařízení.

### <a name="connect-to-your-hub"></a>Připojení k rozbočovači

Při prvním spuštění Průzkumníka Azure IoT se zobrazí výzva pro připojovací řetězec služby IoT hub. Po přidání připojovacího řetězce vyberte **Připojit**. Pomocí nastavení nástroje můžete přepnout do jiného centra IoT aktualizací připojovacího řetězce.

Definice modelu pro zařízení IoT Plug and Play je uložena ve veřejném úložišti, v podnikovém úložišti nebo v připojeném zařízení. Ve výchozím nastavení nástroj vyhledá definici modelu v úložišti veřejného modelu a připojeném zařízení. Zdroje můžete přidávat a odebírat nebo konfigurovat prioritu zdrojů v **nastavení**:

Přidání zdroje:

1. Přejděte do **nastavení**.
1. Vyberte **Nový** a zvolte zdroj.
1. Pokud přidáváte úložiště modelu společnosti, zadejte připojovací řetězec.

Odebrání zdroje:

1. Přejděte do **nastavení**.
1. Najděte zdroj, který chcete odebrat.
1. Chcete-li jej odebrat, vyberte **x.** Nelze odebrat úložiště veřejného modelu, protože společné definice rozhraní pocházejí z tohoto úložiště.

Změna zdrojových priorit:

Jeden ze zdrojů definice modelu můžete přetáhnout do jiného pořadí v seznamu. Pokud dojde ke konfliktu, definiční zdroje s vyšším hodnocením přepíší zdroje s nižším hodnocením.

### <a name="view-devices"></a>Zobrazení zařízení

Po připojení nástroje k centru IoT se zobrazí stránka se seznamem **Zařízení** se seznamem identit zařízení registrovaných v centru IoT. Chcete-li zobrazit další informace, můžete rozbalit libovolnou položku v seznamu.

Na stránce se seznamem **Zařízení** můžete:

- Výběrem **možnosti Přidat** zaregistrujete nové zařízení ve svém centru. Pak zadejte ID zařízení. Pomocí výchozího nastavení můžete automaticky generovat ověřovací klíče a povolit připojení k rozbočovači.
- Vyberte zařízení a pak vyberte **Odstranit,** chcete-li odstranit identitu zařízení. Před dokončením této akce zkontrolujte podrobnosti o zařízení, abyste se ujistili, že smažete správnou identitu zařízení.
- Dotaz `capabilityID` podle `interfaceID`a . Přidejte `capabilityID` buď `interfaceID` svůj, nebo jako parametr pro dotazování vašich zařízení.

## <a name="interact-with-a-device"></a>Interakce se zařízením

Na stránce se seznamem **Zařízení** vyberte hodnotu ve **sloupci ID zařízení,** chcete-li zobrazit stránku podrobností pro registrované zařízení. Pro každé zařízení existují dvě části: **Zařízení** a **Digitální dvojče**.

### <a name="device"></a>Zařízení

Tato část zahrnuje **karty identity zařízení**, **dvojčete zařízení**, **telemetrie**, **metody Direct** a karet zpráv **z cloudu na zařízení.**

- Informace o [identitě zařízení](../iot-hub/iot-hub-devguide-identity-registry.md) můžete zobrazit a aktualizovat na kartě **Identita zařízení.**
- Informace o [dvojčeti zařízení](../iot-hub/iot-hub-devguide-device-twins.md) najdete na kartě **Dvojče zařízení.**
- Pokud je zařízení připojené a aktivně odesílá data, můžete [telemetrii](../iot-hub/iot-hub-devguide-messages-read-builtin.md) zobrazit na kartě **Telemetrie.**
- Přímou [metodu](../iot-hub/iot-hub-devguide-direct-methods.md) můžete volat na zařízení na kartě **Metoda Direct.**
- Na kartě **Zprávy z cloudu zařízení** můžete [odeslat zprávu z cloudu na](../iot-hub/iot-hub-devguide-messages-c2d.md) zařízení.

### <a name="digital-twin"></a>Digitální dvojče

Pomocí tohoto nástroje můžete zobrazit digitální dvojče instance zařízení. U zařízení IoT Plug and Play jsou v této části nástroje zobrazena všechna rozhraní přidružená k modelu schopností zařízení. Vyberte rozhraní, chcete-li rozšířit jeho odpovídající [ioT plug and play primitiva](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

### <a name="interface"></a>Rozhraní

Na stránce **Rozhraní** můžete zobrazit definici json rozhraní.

#### <a name="properties"></a>Vlastnosti

Vlastnosti jen pro čtení definované v rozhraní můžete zobrazit na stránce **Nezapisovatelné vlastnosti.** Můžete aktualizovat zapisovatelné vlastnosti definované v rozhraní na stránce **Zapisovatelné vlastnosti:**

1. Přejděte na stránku **Vlastnosti, kterou lze zapisovat.**
1. Klikněte na vlastnost, kterou chcete aktualizovat.
1. Zadejte novou hodnotu vlastnosti.
1. Zobrazit náhled datové části, která má být odeslána do zařízení.
1. Odešlete změnu.

Po odeslání změny můžete sledovat stav aktualizace: **synchronizace**, **úspěch**nebo **chyba**. Po dokončení synchronizace se zobrazí nová hodnota vaší vlastnosti ve sloupci **Ohlášená vlastnost.** Pokud přejdete na jiné stránky před dokončením synchronizace, nástroj vás stále upozorní po dokončení aktualizace. K zobrazení historie oznámení můžete také použít oznamovací centrum nástroje.

#### <a name="commands"></a>Příkazy

Chcete-li odeslat příkaz do zařízení, přejděte na stránku **Příkazy:**

1. V seznamu příkazů rozbalte příkaz, který chcete aktivovat.
1. Zadejte všechny požadované hodnoty příkazu.
1. Zobrazit náhled datové části, která má být odeslána do zařízení.
1. Odešlete příkaz.

#### <a name="telemetry"></a>Telemetrie

Chcete-li zobrazit telemetrii pro vybrané rozhraní, přejděte na jeho stránku **Telemetrie.**

## <a name="next-steps"></a>Další kroky

V tomto článku s postupem jste se dozvěděli, jak nainstalovat a používat Průzkumník Avaleda IoT k interakci s vašimi zařízeními IoT Plug and Play. Dalším krokem je naučit se [nainstalovat a používat rozšíření Azure CLI](./howto-install-pnp-cli.md).
