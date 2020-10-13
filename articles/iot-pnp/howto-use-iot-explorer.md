---
title: Instalace a použití aplikace Azure IoT Explorer | Microsoft Docs
description: Nainstalujte nástroj Azure IoT Explorer a použijte ho k interakci se zařízeními IoT technologie Plug and Play připojenými ke službě IoT Hub.
author: rido-min
ms.author: rmpablos
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e64586ced7a619de02ae3a9d6beda35660ca04a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577573"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalace a použití Azure IoT Exploreru

Azure IoT Explorer je grafický nástroj pro interakci s a testováním zařízení technologie Plug and Play IoT. Po instalaci nástroje na místním počítači ho můžete použít pro připojení k centru. Nástroj můžete použít k zobrazení telemetrie, kterou zařízení odesílá, k práci s vlastnostmi zařízení a k vyvolání příkazů.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte nástroj Azure IoT Explorer.
- Použijte nástroj k interakci a testování zařízení.

## <a name="prerequisites"></a>Požadavky

K použití nástroje Azure IoT Explorer potřebujete:

- Azure IoT Hub. Existuje mnoho způsobů, jak přidat službu IoT Hub k předplatnému Azure, jako je [vytvoření centra IoT pomocí rozhraní příkazového řádku Azure](../iot-hub/iot-hub-create-using-cli.md). K spuštění nástroje Azure IoT Explorer potřebujete připojovací řetězec služby IoT Hub. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zařízení zaregistrované ve službě IoT Hub. K vytváření a správě registrací zařízení v IoT Hub můžete použít Průzkumníka IoT.

## <a name="install-azure-iot-explorer"></a>Nainstalovat Azure IoT Explorer

V [Azure IoT Exploreru](https://github.com/Azure/azure-iot-explorer/releases) Vydáte verze a rozbalte seznam assetů pro nejnovější verzi. Stáhněte a nainstalujte nejnovější verzi aplikace.

>[!Important]
>V Průzkumníku IoT Explorer verze 0.11.0 podporuje jenom verzi IoT technologie Plug and Play GA (od září 2020). Pokud chcete používat funkce, které jsou k dispozici v předchozí verzi Preview – vydání z verze VIII 2019 nainstalujte verzi 0.10. x.

## <a name="use-azure-iot-explorer"></a>Použití Azure IoT Exploreru

V případě zařízení můžete buď připojit vlastní zařízení, nebo použít jedno z ukázkových simulovaných zařízení. Pomocí [těchto pokynů](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) spusťte ukázku simulovaného zařízení.

### <a name="connect-to-your-hub"></a>Připojení k centru

Při prvním spuštění aplikace Azure IoT Explorer budete vyzváni k zadání připojovacího řetězce služby IoT Hub. Po přidání připojovacího řetězce vyberte **připojit**. Nastavení tohoto nástroje můžete použít k přepnutí do jiného centra IoT pomocí aktualizace připojovacího řetězce.

Definice modelu pro zařízení IoT technologie Plug and Play je uložená ve veřejném úložišti, připojeném zařízení nebo v místní složce. Ve výchozím nastavení nástroj hledá definici modelu ve veřejném úložišti a připojeném zařízení. Můžete přidat nebo odebrat zdroje nebo nakonfigurovat prioritu zdrojů v **nastaveních**:

Přidání zdroje:

1. Přejít na **Nastavení domů/IoT technologie Plug and Play**
2. Vyberte **Přidat** a zvolte zdroj, z úložiště nebo místní složky.

Postup odebrání zdroje:

1. Přejít na **Nastavení domů/IoT technologie Plug and Play**
2. Vyhledejte zdroj, který chcete odebrat.
3. Vyberte **X** a odeberte je.

Změnit zdrojové priority:

Jeden ze zdrojů definice modelu můžete přetáhnout do jiného hodnocení v seznamu.

### <a name="view-devices"></a>Zobrazení zařízení

Po připojení nástroje k centru IoT se zobrazí stránka seznam **zařízení** se seznamem identit zařízení zaregistrovaných ve službě IoT Hub. Pokud chcete zobrazit další informace, můžete vybrat libovolnou položku v seznamu.

Na stránce seznam **zařízení** můžete:

- Když vyberete **Nový** , zaregistrujete nové zařízení do svého rozbočovače. Pak zadejte ID zařízení. Pomocí výchozího nastavení můžete automaticky generovat ověřovací klíče a povolit připojení k rozbočovači.
- Vyberte zařízení a pak vyberte **Odstranit** a odstraňte identitu zařízení. Před dokončením této akce zkontrolujte podrobnosti o zařízení, abyste měli jistotu, že odstraňujete správnou identitu zařízení.

## <a name="interact-with-a-device"></a>Interakce se zařízením

Na stránce seznam **zařízení** výběrem hodnoty ve sloupci **ID zařízení** zobrazíte stránku podrobností registrovaného zařízení. Pro každé zařízení jsou k dispozici dvě části: **zařízení** a **digitální dvojitá**dvojice.

### <a name="device"></a>Zařízení

Tato část obsahuje **identitu zařízení**, vydanou  **zařízení**, **telemetrii**, **přímou metodu**, **zprávu Cloud-zařízení**, karty **identity modulu**  .

- Informace o [identitě zařízení](../iot-hub/iot-hub-devguide-identity-registry.md) můžete zobrazit a aktualizovat na kartě **Identita zařízení** .
- Na kartě **zařízení** , na které se nachází, můžete získat přístup k [dvojitým](../iot-hub/iot-hub-devguide-device-twins.md) informacím o zařízení.
- Pokud je zařízení připojené a aktivně odesílá data, můžete zobrazit [telemetrii](../iot-hub/iot-hub-devguide-messages-read-builtin.md) na kartě **telemetrie** .
- Na zařízení na kartě **Přímá metoda** můžete zavolat [přímo metodu](../iot-hub/iot-hub-devguide-direct-methods.md) .
- Můžete odeslat zprávu typu [Cloud-zařízení](../iot-hub/iot-hub-devguide-messages-c2d.md) na kartě **zprávy typu cloud-zařízení** .
- Můžete získat přístup k [dvojitým informacím modulu](../iot-hub/iot-hub-devguide-module-twins.md) .

### <a name="iot-plug-and-play-components"></a>Komponenty IoT technologie Plug and Play

Pokud je zařízení připojené k centru pomocí **ID modelu**, zobrazí se na kartě **komponenty IoT technologie Plug and Play** , kde můžete zobrazit **ID modelu**.

Pokud je **ID modelu** dostupné v jednom z nakonfigurovaného úložiště – veřejné nebo místní složky, zobrazí se seznam součástí. Po výběru komponenty se zobrazí dostupné vlastnosti, příkazy a telemetrie.

Na stránce **Komponenta** můžete zobrazit vlastnosti jen pro čtení, aktualizovat vlastnosti s možností zápisu, vyvolat příkazy a zobrazit zprávy telemetrie, které tato součást vygenerovala.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Zobrazit součásti v Azure IoT Exploreru":::

#### <a name="properties"></a>Vlastnosti

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Zobrazit součásti v Azure IoT Exploreru":::

Vlastnosti jen pro čtení definované v rozhraní můžete zobrazit na kartě **vlastnosti (jen pro čtení)** . Můžete aktualizovat vlastnosti s možností zápisu definované v rozhraní na kartě **vlastnosti (zapisovatelné)** :

1. Přejít na kartu **vlastnosti (zapisovatelné)** .
1. Klikněte na vlastnost, kterou chcete aktualizovat.
1. Zadejte novou hodnotu pro vlastnost.
1. Zobrazte si náhled datové části, která se má odeslat do zařízení.
1. Odešlete změnu.

Po odeslání změny můžete sledovat stav aktualizace: **synchronizace**, **úspěch**nebo **Chyba**. Po dokončení synchronizace se zobrazí nová hodnota vlastnosti ve sloupci **nahlášená vlastnost** . Pokud přejdete na jiné stránky před dokončením synchronizace, nástroj vás po dokončení aktualizace stále upozorní. Historii oznámení můžete zobrazit také pomocí Centra oznámení nástroje.

#### <a name="commands"></a>Příkazy

Pokud chcete odeslat příkaz do zařízení, použijte kartu **příkazy** :

1. V seznamu příkazů rozbalte příkaz, který chcete aktivovat.
1. Zadejte všechny požadované hodnoty příkazu.
1. Zobrazte si náhled datové části, která se má odeslat do zařízení.
1. Odešlete příkaz.

#### <a name="telemetry"></a>Telemetrie

Chcete-li zobrazit telemetrii pro vybrané rozhraní, přejít na kartu **telemetrie** .

#### <a name="known-issues"></a>Známé problémy

- Podpora IoT Edge: aktuální verze nezobrazuje IoT Edge zařízení v seznamu zařízení.
- Funkce jazyka DTDL: aplikace IoT Explorer 0.12. x není plně kompatibilní s DTDL v2, funkce, které se nepodporují, zahrnují:
  - Dědičnost rozhraní s `extends`
  - Mapa v mapě (vnořená mapa)
  - Typ pole
  - Vlastní schémata
  - Vlastní sémantické typy

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak nainstalovat a používat Azure IoT Explorer k interakci s technologie Plug and Playmi zařízeními IoT. Navržený další krok se naučíte používat [příkazy Azure CLI IoT technologie Plug and Play](./howto-use-iot-pnp-cli.md).
