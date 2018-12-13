---
title: Exportujte data v Azure IoT Central | Dokumentace Microsoftu
description: Jak exportovat data z aplikace Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: cba0bad2e81ffddedfc4ca04e82e17e4286b389b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312115"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportujte data v Azure IoT Central

*Toto téma se vztahuje na správce.*

Tento článek popisuje způsob použití souvislá datová funkce exportu v Azure IoT Central exportování dat na své vlastní **Azure Blob Storage**, **Azure Event Hubs**, a **Azure Service Bus** instancí. Můžete to taky **měření**, **zařízení**, a **šablon** do vlastní cíle pro cestu teplé a studené cesty analytics. Můžete exportovat data do úložiště objektů Blob a spustit dlouhodobé analýzy trendů v Microsoft Power BI nebo exportovat data do služby Event Hubs a služby Service Bus, transformovat a rozšířit vaše data v téměř v reálném čase pomocí Azure Logic Apps nebo Azure Functions.

> [!Note]
> Když zapnete nepřetržitý export dat, získáte jenom data dále v tomto okamžiku. V současné době nelze načíst data po dobu, kdy byla nepřetržitý export dat vypnout. Pokud chcete zachovat dalších historických dat, zapněte nepřetržitý export dat již v rané fázi.

## <a name="prerequisites"></a>Požadavky

- Musíte být správcem ve vaší aplikaci IoT Central

## <a name="export-to-blob-storage"></a>Exportovat do úložiště objektů Blob

Měření, zařízení a zařízení šablony data se vyexportují do vašeho účtu úložiště jednou za minutu, se každý soubor, který obsahuje batch změny od poslední exportovaný soubor. Exportovaná data se v [Apache AVRO](https://avro.apache.org/docs/current/index.html) formátu.

Další informace o [export do úložiště objektů Blob](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Export do služby Event Hubs a služby Service Bus

Měření, zařízení a zařízení šablony data se exportují do centra událostí nebo fronty Service Bus nebo téma. Exportované měření dat přijde téměř v reálném čase a obsahuje celé zprávy zařízení odesílat IoT Central, ne jenom hodnoty měření sami. Exportovaná zařízení data dorazí v dávkách každou minutu a obsahuje změny vlastnosti a nastavení všech zařízení a zařízení exportované šablony obsahuje změny pro všechny šablony zařízení.


Další informace o [export do služby Event Hubs a služby Service Bus](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Pokud nemáte existující sběrnice úložiště/událostí služby/Hubs exportovat do aplikace, postupujte podle těchto kroků:

### <a name="create-storage-account"></a>Vytvoření účtu úložiště

1. Vytvoření [nový účet úložiště na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Další informace v [dokumentace služby Azure Storage](https://aka.ms/blobdocscreatestorageaccount).
2. Pro typ účtu, vyberte **Obecné** nebo **úložiště objektů Blob**.
3. Zvolte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, které jsou **není stejný** jako pro aplikace s průběžnými platbami IoT Central. Připojíte se v tomto případě pomocí připojovacího řetězce.

4. Vytvoření kontejneru v účtu úložiště. Přejděte do účtu úložiště. V části **služby Blob Service**vyberte **procházet objekty BLOB**. Vyberte **+ kontejner** v horní části stránky vytvořte nový kontejner.

### <a name="create-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

1. Vytvoření [nový obor názvů služby Event Hubs na portálu Azure portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Další informace v [dokumentace služby Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Zvolte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, které jsou **není stejný** jako pro aplikace s průběžnými platbami IoT Central. Připojíte se v tomto případě pomocí připojovacího řetězce.
3. Vytvoření centra událostí ve vašem oboru názvů služby Event Hubs. Přejděte do svého oboru názvů a vyberte **+ Centrum událostí** v horní části stránky pro vytvoření instance centra událostí.

### <a name="create-service-bus-namespace"></a>Vytvoření oboru názvů služby Service Bus

1. Vytvoření [nový obor názvů služby Service Bus na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Další informace v [dokumentace služby Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Zvolte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, které jsou **není stejný** jako pro aplikace s průběžnými platbami IoT Central. Připojíte se v tomto případě pomocí připojovacího řetězce.

3. Přejděte do svého oboru názvů služby Service Bus a vyberte **+ fronta** nebo **+ téma** v horní části stránky k vytvoření fronty nebo tématu exportovat do.

## <a name="set-up-continuous-data-export"></a>Nastavit nepřetržitý export dat

Teď, když máte exportovat data do cílového úložiště/Event Hubs a služby Service Bus, nastavit nepřetržitý export dat pomocí těchto kroků. 

1. Přihlaste se do vaší aplikace IoT Central.

2. V nabídce vlevo klikněte na tlačítko **průběžný Export dat**.

    > [!Note]
    > Pokud nevidíte průběžný Export dat v nabídce vlevo, nejste správcem ve vaší aplikaci. Obraťte se na správce nastavit export dat.

    ![Vytvořit nový cde centra událostí](media/howto-export-data/export_menu.PNG)

3. Klikněte na tlačítko **+ nová** tlačítko v pravém horním rohu. Vyberte jednu z **Azure Blob Storage**, **Azure Event Hubs**, nebo **Azure Service Bus** jako cíl pro export. 

    > [!NOTE] 
    > Maximální počet exportů na aplikaci je pět. 

    ![Vytvořit nový nepřetržitý export dat](media/howto-export-data/export_new.PNG)

4. V rozevíracím seznamu vyberte vaše **úložiště účtu nebo Event Hubs oboru názvů nebo obor názvů sběrnice**. V seznamu, který je můžete také vybrat jako poslední možnost **zadejte připojovací řetězec**. 

    > [!NOTE] 
    > Zobrazí se pouze obory názvů úložiště účtů nebo Event Hubs obory názvů nebo služby Service Bus v **stejném předplatném jako aplikace IoT Central**. Pokud chcete exportovat do umístění mimo toto předplatné, zvolte **zadejte připojovací řetězec** a přejděte ke kroku 5.

    > [!NOTE] 
    > 7 dnů, zkušební verze aplikace, jediný způsob, jak nakonfigurovat průběžné data exportovat je do připojovacího řetězce. Je to proto 7denní zkušební verze aplikace nemusí k přidruženému předplatnému Azure.

    ![Vytvořit nový cde centra událostí](media/howto-export-data/export_create.PNG)

5. (Volitelné) Pokud jste zvolili **zadejte připojovací řetězec**, můžete vložit připojovací řetězec se zobrazí nové pole. Chcete-li získat připojovací řetězec pro váš:
    - Účet úložiště, přejděte na účet úložiště na webu Azure Portal.
        - V části **nastavení**, klikněte na tlačítko **přístupové klíče**
        - Zkopírujte připojovací řetězec key1 a key2 připojovací řetězec
    - Event Hubs nebo Azure Service Bus, přejděte do oboru názvů na webu Azure Portal.
        - V části **nastavení**, klikněte na tlačítko **sdílené zásady přístupu**
        - Zvolte výchozí **RootManageSharedAccessKey** nebo vytvořte novou
        - Zkopírujte primární nebo sekundární připojovací řetězec
 
6. Z rozevíracího seznamu vyberte kontejner/událostí centra nebo fronty nebo tématu.

7. V části **Data pro export**, určete každý typ hledaných dat exportovat na základě nastavení typu na **na**.

6. Nepřetržitý export dat zapnout, ujistěte se, že **export dat** je **na**. Vyberte **Uložit**.

  ![Nepřetržitý export dat konfigurace](media/howto-export-data/export_list.PNG)

7. Po několika minutách by se vaše data zobrazí v zvolený cíl.

## <a name="next-steps"></a>Další postup

Teď, když víte, jak exportovat data, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Export dat do úložiště objektů Blob v Azure](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Export dat do služby Azure Event Hubs a Azure Service Bus](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Tom, jak vizualizace dat v Power BI](howto-connect-powerbi.md)
