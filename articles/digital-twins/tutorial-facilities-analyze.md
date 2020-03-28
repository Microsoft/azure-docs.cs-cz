---
title: 'Kurz: Analýza událostí v přehledech time series – Digitální dvojčata Azure| Dokumenty společnosti Microsoft'
description: Zjistěte, jak vizualizovat a analyzovat události z vašich prostorů Azure Digital Twins pomocí Azure Time Series Insights pomocí kroků v tomto kurzu.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 38bd1755ed87050cf8b91a0a82f6e5f1d2af9db5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933878"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Kurz: Vizualizace a analýza událostí z Azure Digital Twins pomocí Přehledy časových řad

Po nasazení instance Azure Digital Twins, zřizování prostorů a implementaci vlastní funkce pro monitorování konkrétních podmínek můžete vizualizovat události a data pocházející z vašich prostorů a hledat trendy a anomálie.

V [prvním kurzu](tutorial-facilities-setup.md)jste nakonfigurovali prostorový graf imaginární budovy s místností, která obsahuje senzory pro pohyb, oxid uhličitý a teplotu. V [druhém kurzu](tutorial-facilities-udf.md) jste si zřídili graf a uživatelem definovanou funkci. Funkce monitoruje tyto hodnoty senzorů a spouští oznámení za správných podmínek. To znamená, že místnost je prázdná a teplota a hladiny oxidu uhličitého jsou normální.

Tento kurz ukazuje, jak můžete integrovat oznámení a data pocházející z vašeho nastavení Azure Digital Twins s Azure Time Series Insights. Hodnoty senzorů pak můžete v průběhu času vizualizovat. Můžete se podívat na trendy, jako je místnost, která je stále nejvíce využít a které jsou nejrušnější časy dne. Můžete také zjistit anomálie, jako jsou místnosti, které se cítí dusnější a teplejší, nebo zda oblast ve vaší budově vysílá trvale vysoké hodnoty teploty, což naznačuje vadnou klimatizaci.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Streamujte data pomocí Azure Event Hubs.
> * Analyzujte pomocí přehledů časových řad.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) vlastní systém Azure Digital Twins. Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins.
- [Digitální dvojčata C # ukázky](https://github.com/Azure-Samples/digital-twins-samples-csharp) stáhnout a extrahovat na vašem pracovním počítači.
- [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači spustit ukázku. Spusťte `dotnet --version` a ověřte, zda je nainstalována správná verze.

> [!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance Digitální dvojčata.

## <a name="stream-data-by-using-event-hubs"></a>Streamování dat pomocí center událostí

Službu [Event Hubs](../event-hubs/event-hubs-about.md) můžete použít k vytvoření kanálu pro streamování dat. Tato část ukazuje, jak vytvořit centrum událostí jako konektor mezi instancemi Azure Digital Twins a Time Series Insights.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**.

1. Vyhledejte a vyberte **Event Hubs**. Vyberte **Vytvořit**.

    [![Vytvoření oboru názvů centra událostí](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png#lightbox)

1. Zadejte **název** oboru názvů Event Hubs. Zvolte **Standardní** pro **cenovou úroveň**, **předplatné**, **skupinu prostředků,** kterou jste použili pro instanci Digitální dvojčata, a **Umístění**. Vyberte **Vytvořit**.

1. V nasazení oboru názvů Event Hubs vyberte podokno **Přehled** a pak vyberte **Přejít na prostředek**.

    [![Obor názvů Event Hubs po nasazení](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png)](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png#lightbox)

1. V podokně **Přehled** oborů názvů Centra událostí vyberte nahoře tlačítko **Centrum událostí.**
    [![Tlačítko Centrum událostí](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png#lightbox)

1. Zadejte **název** centra událostí a vyberte **Vytvořit**.

   Po nasazení centra událostí se zobrazí v podokně **Centra událostí** v oboru názvů Event Hubs se stavem **Aktivní.** Výběrem tohoto centra událostí otevřete podokno **Přehled.**

1. V horní části vyberte tlačítko **Skupina příjemce** a zadejte název, například **tsievents** pro skupinu spotřebitelů. Vyberte **Vytvořit**.

    [![Skupina spotřebitelů Centra událostí](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Po vytvoření skupiny spotřebitelů se zobrazí v seznamu v dolní části podokna **Přehled** centra událostí.

1. Otevřete podokno **Zásady sdíleného přístupu** pro centrum událostí a vyberte tlačítko **Přidat.** Jako název zásady zadejte **ManageSend,** ujistěte se, že jsou zaškrtnuta všechna zaškrtávací políčka, a vyberte **Vytvořit**.

    [![Připojovací řetězce centra událostí](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Ověřte, zda vytváříte zásady SAS pro instanci centra událostí, nikoli obor názvů.

1. Otevřete zásadu **ManageSend,** kterou jste vytvořili, a zkopírujte hodnoty pro **připojovací řetězec – primární klíč** a **připojovací řetězec – sekundární klíč** do dočasného souboru. Tyto hodnoty budete potřebovat k vytvoření koncového bodu pro centrum událostí v další části.

### <a name="create-an-endpoint-for-the-event-hub"></a>Vytvoření koncového bodu pro centrum událostí

1. V příkazovém okně se ujistěte, že jste ve složce **obsazení-quickstart\src** ukázkové ukázky Azure Digital Twins.

1. V editoru otevřete soubor **actions\createEndpoints.yaml**. Obsah souboru nahraďte následujícím kódem:

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

1. Nahraďte `Primary_connection_string_for_your_event_hub` zástupné symboly hodnotou **připojovacího řetězce – primárního klíče** pro centrum událostí. Ujistěte se, že formát tohoto připojovacího řetězce je následující:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Nahraďte `Secondary_connection_string_for_your_event_hub` zástupné symboly hodnotou **připojovacího řetězce – sekundárního klíče** pro centrum událostí. Ujistěte se, že formát tohoto připojovacího řetězce je následující: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Nahraďte `Name_of_your_Event_Hub` zástupné symboly názvem centra událostí.

    > [!IMPORTANT]
    > Všechny hodnoty zadávejte bez uvozovek. Ujistěte se, že za dvojtečkami v souboru YAML je alespoň jeden znak mezery. Obsah souboru YAML můžete také ověřit pomocí libovolného online validátoru YAML, například [tohoto nástroje](https://onlineyamltools.com/validate-yaml).

1. Uložte soubor a zavřete ho. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste ke svému účtu Azure.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Vytvoří dva koncové body pro centrum událostí.

   [![Koncové body pro službu Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analýza s využitím služby Time Series Insights

1. V levém podokně [portálu Azure](https://portal.azure.com)vyberte **Vytvořit prostředek**. 

1. Vyhledejte a vyberte zdroj obecné dostupnosti **časových řad** (GA). Vyberte **Vytvořit**.

1. Zadejte **Název** vaší instance služby Time Series Insights a pak vyberte své **Předplatné**. Vyberte **skupinu prostředků,** kterou jste použili pro instanci Digitální dvojčata, a vaši **polohu**. Vyberte **další:** Tlačítko Zdroje událostí nebo kartu **Zdroj události.**

    [![Výběry pro vytvoření instance Time Series Insights](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png)](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png#lightbox)

1. Na kartě **Zdroj události** zadejte **název**, vyberte **centrum událostí** jako typ **Zdroj**a ujistěte se, že ostatní hodnoty jsou vybrány správně, aby odkazovaly na centrum událostí, které jste vytvořili. Vyberte **Spravovat odevšad** pro **přístup k centru událostí**a pak vyberte skupinu spotřebitelů, kterou jste vytvořili v předchozí části pro **skupinu spotřebitelů Centra událostí**. Vyberte **Zkontrolovat a vytvořit**.

    [![Výběry pro vytvoření zdroje událostí](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png)](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png#lightbox)

1. V podokně **Revize + Vytvoření** zkontrolujte zadané informace a vyberte **Vytvořit**.

1. V podokně nasazení vyberte prostředek Time Series Insights, který jste vytvořili. Otevře podokno **Přehled** pro prostředí Time Series Insights.

1. V horní části vyberte tlačítko **Přejít na prostředí.** Pokud se vám dostane upozornění na přístup k datům, otevřete podokno **Zásady přístupu k datům** pro instanci Time Series Insights, vyberte **přidat**, vyberte jako roli **přispěvatele** a vyberte příslušného uživatele.

1. Tlačítko **Přejít do prostředí** otevře průzkumník [Přehledů časových řad](../time-series-insights/time-series-insights-explorer.md). Pokud nezobrazuje žádné události, simulujte události zařízení procházením projektu **připojení zařízení** ukázky digitálních dvojčat a spuštěním `dotnet run`.

1. Po vygenerování několika simulovaných událostí se vraťte do průzkumníka Time Series Insights a nahoře vyberte tlačítko aktualizovat. Zobrazí se analytické grafy, které jsou vytvářeny pro simulovaná data senzorů. 

    [![Graf v průzkumníku Přehledy time series](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png)](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png#lightbox)

1. V průzkumníku Time Series Insights pak můžete generovat grafy a heatmapy pro různé události a data z vašich místností, senzorů a dalších zdrojů. Na levé straně můžete pomocí rozevíracích seznamů **MEASURE** a **SPLIT BY** vytvořit vlastní vizualizace. 

   Chcete-li například generovat heatmapu pro každý senzor, vyberte **možnost Události** pro **MEASURE** a **DigitalTwins-SensorHardwareId** pro **SPLIT BY**. Heatmap bude podobný následujícímu obrázku:

   [![Heatmap v průzkumníku Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přestat prozkoumávat Azure Digital Twins za tímto bodem, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V levé nabídce na [portálu Azure](https://portal.azure.com)vyberte **Všechny prostředky**, vyberte skupinu prostředků Digitální dvojčata a pak vyberte **Odstranit**.

    > [!TIP]
    > Pokud došlo k potížím s odstraněním instance Digitální dvojčata, byla s opravou zavedena aktualizace služby. Opakujte odstranění instance.

2. V případě potřeby odstraňte ukázkové aplikace v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Další informace o grafech prostorové inteligence a objektových modelech v Azure Digital Twins najdete v dalším článku.

> [!div class="nextstepaction"]
> [Vysvětlení grafu prostorové inteligence a objektových modelů služby Digital Twins](concepts-objectmodel-spatialgraph.md)
