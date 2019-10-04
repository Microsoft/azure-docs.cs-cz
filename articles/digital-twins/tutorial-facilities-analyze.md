---
title: 'Kurz: analýza událostí z nastavení digitálních vláken Azure | Microsoft Docs'
description: Pomocí kroků v tomto kurzu zjistíte, jak vizualizovat a analyzovat události z prostorů digitálních vláken Azure pomocí Azure Time Series Insights.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.openlocfilehash: af776372026bf9affcda03fa9188b854ebedfc73
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949806"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Kurz: vizualizace a analýza událostí z digitálních vláken Azure v prostorách pomocí Time Series Insights

Po nasazení instance digitálních vláken Azure si můžete zřídit své prostory a implementovat vlastní funkci pro monitorování konkrétních podmínek. můžete vizualizovat události a data přicházející z vašich prostorů a hledat trendy a anomálie.

V [prvním kurzu](tutorial-facilities-setup.md)jste nakonfigurovali prostorový graf imaginární budovy s místností, která obsahuje snímače pro pohyb, oxid uhličitý a teplotu. V [druhém kurzu](tutorial-facilities-udf.md)jste zřídili graf a uživatelsky definovanou funkci. Funkce monitoruje tyto hodnoty senzoru a aktivuje oznámení pro správné podmínky. To znamená, že místnost je prázdná a hladina teploty a oxidu uhličitého jsou normální.

V tomto kurzu se dozvíte, jak můžete integrovat oznámení a data přicházející z nastavení digitálních vláken Azure pomocí Azure Time Series Insights. V průběhu času můžete vizualizovat hodnoty snímačů. Můžete hledat trendy, jako je například ta, kterou místnost získává nejvíc a které jsou nejvytíženější denním časem. Můžete také detekovat anomálie, například které místnosti jsou stuffier a Hotter, nebo zda oblast ve vaší budově posílá konzistentně vysoké hodnoty teploty, což značí vadné klimatizace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Streamování dat pomocí Event Hubs Azure
> * Analýza pomocí Time Series Insights.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) nastavení digitálních vláken Azure. Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instance digitálních vláken je spuštěná.
- Ukázky digitálních vláken se stáhly a extrahují na vašem pracovním počítači. [ C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp)
- [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači pro spuštění ukázky. Spusťte `dotnet --version` a ověřte, zda je nainstalovaná správná verze.

> [!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance digitálního vlákna.

## <a name="stream-data-by-using-event-hubs"></a>Streamování dat pomocí Event Hubs

Službu [Event Hubs](../event-hubs/event-hubs-about.md) můžete použít k vytvoření kanálu pro streamování dat. V této části se dozvíte, jak vytvořit centrum událostí jako konektor mezi digitálními a Time Series Insights instancemi Azure.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**.

1. Vyhledejte a vyberte **Event Hubs**. Vyberte **vytvořit**.

1. Zadejte **název** oboru názvů Event Hubs. Pro **cenovou úroveň**, vaše **předplatné**, **skupinu prostředků** , kterou jste použili pro instanci digitálního vlákna a **umístění**, vyberte **Standard** . Vyberte **vytvořit**.

1. V Event Hubs nasazení oboru názvů vyberte podokno **Přehled** a pak vyberte **Přejít k prostředku**.

    [obor názvů centra @no__t – 1Event po nasazení](./media/tutorial-facilities-analyze/open-event-hub-ns.png)](./media/tutorial-facilities-analyze/open-event-hub-ns.png#lightbox)

1. V podokně **přehledu** oboru názvů Event Hubs vyberte tlačítko **centra událostí** v horní části.
    [@no__t – tlačítko centra 1Event](./media/tutorial-facilities-analyze/create-event-hub.png)](./media/tutorial-facilities-analyze/create-event-hub.png#lightbox)

1. Zadejte **název** centra událostí a vyberte **vytvořit**.

   Po nasazení centra událostí se zobrazí v podokně **Event Hubs** Event Hubs oboru názvů s **aktivním** stavem. Vyberte toto centrum událostí a otevřete jeho podokno **přehledu** .

1. V horní části vyberte tlačítko **Skupina příjemců** a jako skupinu příjemců zadejte název, třeba **tsievents** . Vyberte **vytvořit**.

    [@no__t – skupina uživatelů centra 1Event](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Jakmile se skupina uživatelů vytvoří, zobrazí se v seznamu v dolní části podokna **přehledu** centra událostí.

1. Otevřete podokno **zásady sdíleného přístupu** pro centrum událostí a vyberte tlačítko **Přidat** . Jako název zásady zadejte **ManageSend** , ujistěte se, že jsou zaškrtnutá všechna políčka, a vyberte **vytvořit**.

    [@no__t – připojovací řetězce centra 1Event](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

1. Otevřete zásadu ManageSend, kterou jste vytvořili, a zkopírujte hodnoty **připojovacího řetězce – primární klíč** a **připojovací řetězec – sekundární klíč** do dočasného souboru. Tyto hodnoty budete potřebovat pro vytvoření koncového bodu centra událostí v další části.

### <a name="create-an-endpoint-for-the-event-hub"></a>Vytvoření koncového bodu centra událostí

1. V příkazovém okně se ujistěte, že jste ve složce **Occupancy-quickstart\src** v ukázce digitálních vláken Azure.

1. Otevřete soubor **actions\createEndpoints.yaml** v editoru. Obsah nahraďte následujícím:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Zástupné symboly `Primary_connection_string_for_your_event_hub` nahraďte hodnotou **připojovacího řetězce – primární klíč** pro centrum událostí. Ujistěte se, že formát tohoto připojovacího řetězce je následující:

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Zástupné symboly nahraďte `Secondary_connection_string_for_your_event_hub` hodnotou **připojovacího řetězce – sekundární klíč** pro centrum událostí. Ujistěte se, že formát tohoto připojovacího řetězce je následující: 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Zástupné symboly nahraďte `Name_of_your_Event_Hub` názvem vašeho centra událostí.

    > [!IMPORTANT]
    > Zadejte všechny hodnoty bez uvozovek. Ujistěte se, že je alespoň jeden znak mezery za dvojtečkami v souboru YAML. Obsah souboru YAML můžete také ověřit pomocí libovolného ověřovacího modulu online YAML, jako je například [Tento nástroj](https://onlineyamltools.com/validate-yaml).

1. Soubor uložte a zavřete. Spusťte následující příkaz v příkazovém okně a po zobrazení výzvy se přihlaste pomocí účtu Azure.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Vytvoří dva koncové body centra událostí.

   [@no__t – 1Endpoints pro Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analýza pomocí Time Series Insights

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**. 

1. Vyhledejte a vyberte prostředek **Time Series Insights** obecné dostupnosti (GA). Vyberte **vytvořit**.

1. Zadejte **název** instance Time Series Insights a pak vyberte své **předplatné**. Vyberte **skupinu prostředků** , kterou jste použili pro instanci digitálního vlákna a vaši **polohu**. Vyberte **Další: tlačítko zdroje události** nebo karta **zdroj události** .

    [@no__t – 1Selections pro vytvoření instance Time Series Insights](./media/tutorial-facilities-analyze/create-tsi.png)](./media/tutorial-facilities-analyze/create-tsi.png#lightbox)

1. Na kartě **zdroj události** zadejte **název**, vyberte **centrum událostí** jako **typ zdroje**a ujistěte se, že jsou vybrané jiné hodnoty správně. Vyberte **ManageSend** pro **název zásady přístupu centra událostí**a pak vyberte skupinu uživatelů, kterou jste vytvořili v předchozí části **skupiny uživatelů centra událostí**. Vyberte **zkontrolovat + vytvořit**.

    [@no__t – 1Selections pro vytvoření zdroje události](./media/tutorial-facilities-analyze/tsi-event-source.png)](./media/tutorial-facilities-analyze/tsi-event-source.png#lightbox)

1. V podokně **Revize + vytvořit** zkontrolujte zadané informace a vyberte **vytvořit**.

1. V podokně nasazení vyberte prostředek Time Series Insights, který jste právě vytvořili. Otevře se podokno **Přehled** pro vaše Time Series Insights prostředí.

1. V horní části vyberte tlačítko **Přejít k prostředí** . Pokud obdržíte upozornění pro přístup k datům, otevřete podokno **zásady přístupu k datům** pro vaši instanci Time Series Insights, vyberte **Přidat**, jako roli vyberte **Přispěvatel** a vyberte příslušného uživatele.

1. Tlačítko **Přejít na prostředí** otevře [Průzkumníka Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Pokud nezobrazuje žádné události, simulovat události zařízení tak, že přejdete na projekt pro **připojení k zařízení** v ukázce digitálního vlákna a spustíte `dotnet run`.

1. Po vygenerování několika simulovaných událostí se vraťte do Průzkumníka Time Series Insights a v horní části vyberte tlačítko Aktualizovat. Měli byste vidět analytické grafy, které se vytvářejí pro Simulovaná data senzorů. 

    [@no__t – 1Chart v Průzkumníkovi Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer.png)](./media/tutorial-facilities-analyze/tsi-explorer.png#lightbox)

1. V Průzkumníku Time Series Insights můžete vygenerovat grafy a Heat mapy pro různé události a data z místností, senzorů a dalších prostředků. Na levé straně můžete pomocí rozevíracích seznamů **měření** a **rozdělení** vytvořit vlastní vizualizace. 

   Například vyberte **události** pro **míru** a **DIGITALTWINS-SensorHardwareId** pro **rozdělení podle**, abyste vygenerovali heatmapu pro každé z vašich senzorů. Heatmapu bude vypadat podobně jako na následujícím obrázku:

   [@no__t – 1Heatmap v Průzkumníkovi Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přestat zkoumat digitální vlákna Azure nad rámec tohoto bodu, nebojte se odstranit prostředky vytvořené v tomto kurzu:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**, vyberte svou skupinu prostředků digitálního vynechání a pak vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste narazili na potíže s odstraněním instance digitálního vlákna, aktualizovala se aktualizace služby s touto opravou. Zkuste prosím instanci znovu odstranit.

2. V případě potřeby odstraňte ukázkové aplikace v pracovním počítači.

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte víc o grafech prostorových informací a objektových modelech v digitálních prostředcích Azure.

> [!div class="nextstepaction"]
> [Porozumění objektovým modelům a graf prostorové logiky digitálních vláken](concepts-objectmodel-spatialgraph.md)
