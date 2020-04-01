---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cc53f7ae6eb6254eaa05bf643ecfa0188650df95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77461853"
---
Tato část popisuje, jak vytvořit službu IoT hub pomocí [portálu Azure](https://portal.azure.com).

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Na domovské stránce Azure vyberte **tlačítko + Vytvořit prostředek** a pak do pole Hledat na **marketplace** zadejte *IoT Hub.*

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **Vytvořit**.

1. Na kartě **Základy** vyplňte pole takto:

   - **Předplatné**: Vyberte předplatné, které chcete použít pro vaše centrum.

   - **Skupina prostředků**: Vyberte skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit nový, vyberte **Vytvořit nový** a vyplňte název, který chcete použít. Chcete-li použít existující skupinu prostředků, vyberte tuto skupinu prostředků. Další informace najdete [v tématu Správa skupin prostředků Správce prostředků Azure](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Oblast**: Vyberte oblast, ve které chcete, aby se vaše centrum nacházelo. Vyberte umístění, které je vám nejblíže. Některé funkce, jako jsou [streamy zařízení služby IoT Hub](../articles/iot-hub/iot-hub-device-streams-overview.md), jsou dostupné jenom v určitých oblastech. Pro tyto omezené funkce je nutné vybrat jednu z podporovaných oblastí.

   - **Název centra IoT:** Zadejte název svého centra. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Vytvoření centra na webu Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Vyberte **Další: Velikost a měřítko,** abyste pokračovali ve vytváření rozbočovače.

   ![Nastavení velikosti a škálování pro nové centrum pomocí portálu Azure](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Zde můžete přijmout výchozí nastavení. V případě potřeby můžete upravit libovolné z následujících polí: 

    - **Cenová a škálovací úroveň**: Vybraná úroveň. Můžete si vybrat z několika úrovní, v závislosti na tom, kolik funkcí chcete a kolik zpráv odesíláte prostřednictvím řešení za den. Úroveň free je určena pro testování a hodnocení. Umožňuje připojení 500 zařízení k rozbočovači a až 8 000 zpráv denně. Každé předplatné Azure můžete vytvořit jeden ioT hub ve volné vrstvě. 

      Pokud pracujete prostřednictvím rychlého startu pro datové proudy zařízení ioT hubu, vyberte bezplatnou úroveň.

    - **Jednotky IoT Hubu**: Počet zpráv povolených na jednotku a den závisí na cenové úrovni vašeho centra. Například pokud chcete, aby centrum pro podporu příchozího přenosu dat 700 000 zpráv, zvolte dvě jednotky vrstvy S1.
    Podrobnosti o dalších možnostech úrovně najdete v [tématu Výběr správné úrovně IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

    - **Azure Security Center:** Zapněte tuto možnost a přidejte další vrstvu ochrany proti ohrožení IoT a vašim zařízením. Tato možnost není k dispozici pro rozbočovače ve volné vrstvě. Další informace o této funkci najdete [v tématu Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/).

    - **Rozšířené nastavení** > **oddíly zařízení cloud**: Tato vlastnost se vztahuje na zprávy zařízení cloud k počtu současných čtenářů zpráv. Většina rozbočovačů potřebuje pouze čtyři oddíly.

1.  Vyberte **Další: Značky,** abyste pokračovali na další obrazovku.

    Značky jsou dvojice název/hodnota. Stejnou značku můžete přiřadit více zdrojům a skupinám prostředků, abyste mohli kategorizovat prostředky a konsolidovat fakturaci. Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](../articles/azure-resource-manager/management/tag-resources.md).

    ![Přiřazení značek pro centrum pomocí portálu Azure](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  Vyberte **další: Zkontrolujte + vytvořit** a zkontrolujte své volby. Zobrazí se něco podobného této obrazovce, ale s hodnotami, které jste vybrali při vytváření centra. 

    ![Kontrola informací pro vytvoření nového centra](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  Vyberte **Vytvořit,** chcete-li vytvořit nový rozbočovač. Vytvoření rozbočovače trvá několik minut.
