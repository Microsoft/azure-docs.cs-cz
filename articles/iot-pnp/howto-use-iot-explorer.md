---
title: Instalace a použití aplikace Azure IoT Explorer | Microsoft Docs
description: Nainstalujte nástroj Azure IoT Explorer a použijte ho k interakci se zařízeními IoT technologie Plug and Play připojenými ke službě IoT Hub. I když se tento článek zaměřuje na práci se zařízeními IoT technologie Plug and Play, můžete použít nástroj se všemi zařízeními připojenými k vašemu rozbočovači.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: contperf-fy21q2
ms.openlocfilehash: b0a86a32148086a3b644d0bf147d12be0d812536
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030311"
---
# <a name="install-and-use-azure-iot-explorer"></a>Instalace a použití Azure IoT Exploreru

Azure IoT Explorer je grafický nástroj pro komunikaci se zařízeními a připojenými ke službě IoT Hub. Tento článek se zaměřuje na použití nástroje k otestování vašich technologie Plug and Playch zařízení IoT. Po instalaci nástroje na místním počítači ho můžete použít pro připojení k centru. Nástroj můžete použít k zobrazení telemetrie, kterou zařízení odesílá, k práci s vlastnostmi zařízení a k vyvolání příkazů.

V tomto článku se dozvíte, jak:

- Nainstalujte a nakonfigurujte nástroj Azure IoT Explorer.
- Pomocí tohoto nástroje můžete pracovat se zařízeními IoT technologie Plug and Play a testovat je.

Obecnější informace o používání tohoto nástroje najdete v [souboru Readme](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)pro GitHub.

K použití nástroje Azure IoT Explorer potřebujete:

- Azure IoT Hub. Existuje mnoho způsobů, jak přidat službu IoT Hub k předplatnému Azure, jako je [vytvoření centra IoT pomocí rozhraní příkazového řádku Azure](../iot-hub/iot-hub-create-using-cli.md). K spuštění nástroje Azure IoT Explorer potřebujete připojovací řetězec služby IoT Hub. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zařízení zaregistrované ve službě IoT Hub. K vytváření a správě registrací zařízení v IoT Hub můžete použít Průzkumníka IoT.

## <a name="install-azure-iot-explorer"></a>Nainstalovat Azure IoT Explorer

V [Azure IoT Exploreru](https://github.com/Azure/azure-iot-explorer/releases) Vydáte verze a rozbalte seznam assetů pro nejnovější verzi. Stáhněte a nainstalujte nejnovější verzi aplikace.

>[!Important]
> Pokud chcete překládat modely z libovolného úložiště založeného na, aktualizujte na verzi 0.13. x. [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)

## <a name="use-azure-iot-explorer"></a>Použití Azure IoT Exploreru

V případě zařízení můžete buď připojit vlastní zařízení, nebo použít jedno z ukázkových simulovaných zařízení. U některých ukázkových simulovaných zařízení napsaných v různých jazycích si přečtěte téma [připojení ukázkové aplikace IoT technologie Plug and Play pro IoT Hub](quickstart-connect-device.md) rychlého startu.

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

Na stránce seznam **zařízení** výběrem hodnoty ve sloupci **ID zařízení** zobrazíte stránku podrobností registrovaného zařízení. Pro každé zařízení jsou k dispozici dvě části: **zařízení** a **digitální dvojitá** dvojice.

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

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Zobrazit vlastnosti v Azure IoT Exploreru":::

Vlastnosti jen pro čtení definované v rozhraní můžete zobrazit na kartě **vlastnosti (jen pro čtení)** . Můžete aktualizovat vlastnosti s možností zápisu definované v rozhraní na kartě **vlastnosti (zapisovatelné)** :

1. Přejít na kartu **vlastnosti (zapisovatelné)** .
1. Klikněte na vlastnost, kterou chcete aktualizovat.
1. Zadejte novou hodnotu pro vlastnost.
1. Zobrazte si náhled datové části, která se má odeslat do zařízení.
1. Odešlete změnu.

Po odeslání změny můžete sledovat stav aktualizace: **synchronizace**, **úspěch** nebo **Chyba**. Po dokončení synchronizace se zobrazí nová hodnota vlastnosti ve sloupci **nahlášená vlastnost** . Pokud přejdete na jiné stránky před dokončením synchronizace, nástroj vás po dokončení aktualizace stále upozorní. Historii oznámení můžete zobrazit také pomocí Centra oznámení nástroje.

#### <a name="commands"></a>Příkazy

Pokud chcete odeslat příkaz do zařízení, použijte kartu **příkazy** :

1. V seznamu příkazů rozbalte příkaz, který chcete aktivovat.
1. Zadejte všechny požadované hodnoty příkazu.
1. Zobrazte si náhled datové části, která se má odeslat do zařízení.
1. Odešlete příkaz.

#### <a name="telemetry"></a>Telemetrie

Chcete-li zobrazit telemetrii pro vybrané rozhraní, přejít na kartu **telemetrie** .

#### <a name="known-issues"></a>Známé problémy

Seznam funkcí IoT, které podporuje nejnovější verze nástroje, najdete v tématu [seznam funkcí](https://github.com/Azure/azure-iot-explorer/wiki).

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak nainstalovat a používat Azure IoT Explorer k interakci s technologie Plug and Playmi zařízeními IoT. Navržený další krok se naučíte, jak [nainstalovat a používat nástroje DTDL Authoring Tools](howto-use-dtdl-authoring-tools.md).
