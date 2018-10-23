---
title: Analýza událostí ze systému Azure Digital Twins | Microsoft Docs
description: Zjistěte, jak pomocí kroků v tomto kurzu vizualizovat a analyzovat události z prostorů Azure Digital Twins s využitím služby Azure Time Series Insights.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323216"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights

Po nasazení instance služby Azure Digital Twins, zřízení prostorů a implementaci vlastní funkce pro monitorování určitých podmínek můžete vizualizovat události a data přicházející z vašich prostorů a hledat trendy a anomálie. 

V [prvním kurzu](tutorial-facilities-setup.md) jste nakonfigurovali prostorový graf imaginární budovy s místností obsahující senzory pohybu, oxidu uhličitého a teploty. V [druhém kurzu](tutorial-facilities-udf.md) jste si zřídili graf a uživatelem definovanou funkci. Funkce monitoruje hodnoty z těchto senzorů a aktivuje oznámení při vhodných podmínkách – to znamená, že je místnost prázdná a úrovně teploty a oxidu uhličitého jsou v normálu. V tomto kurzu se dozvíte, jak můžete oznámení a data přicházející ze systému Digital Twins integrovat se službou Azure Time Series Insights. Hodnoty ze senzorů pak můžete vizualizovat v průběhu času a hledat trendy, jako jsou nejvyužívanější místnosti, denní doby největšího vytížení atd. Můžete také detekovat anomálie, jako například které místnosti se zdají být vydýchanější a teplejší nebo jestli se z určité oblasti v budově odesílají konzistentně vysoké teplotní hodnoty, které značí vadnou klimatizaci.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Streamování dat s využitím Event Hubs
> * Analýza s využitím služby Time Series Insights

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) vlastní systém Azure Digital Twins. Než budete pokračovat, ujistěte se, že máte následující:
- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins.
- Pracovní počítač se staženými a extrahovanými [ukázkami služby Digital Twins v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- Počítač pro vývoj se sadou [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) pro spuštění ukázky. Spuštěním příkazu `dotnet --version` ověřte, že máte nainstalovanou správnou verzi. 


## <a name="stream-data-using-event-hubs"></a>Streamování dat s využitím Event Hubs
Služba [Event Hubs](../event-hubs/event-hubs-about.md) umožňuje vytvořit kanál pro streamování dat. V této části se dozvíte, jak vytvořit centrum událostí jako konektor mezi instancemi služeb Digital Twins a TSI.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Na levém navigačním panelu klikněte na **Vytvořit prostředek**. 

1. Vyhledejte a vyberte **Event Hubs**. Klikněte na možnost **Vytvořit**.

1. Zadejte **Název** oboru názvů služby Event Hubs a zvolte **cenovou úroveň** *Standard*, **předplatné**, **skupinu prostředků**, kterou jste použili pro instanci služby Digital Twins, a **umístění**. Klikněte na možnost **Vytvořit**. 

1. Po nasazení přejděte do *nasazení* oboru názvů služby Event Hubs a v části **PROSTŘEDEK** klikněte na obor názvů.

    ![Obor názvů centra událostí](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. V podokně **Přehled** oboru názvů služby Event Hubs klikněte na tlačítko **Centrum událostí** v horní části. 
    ![Centrum událostí](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Zadejte **Název** centra událostí a klikněte na **Vytvořit**. Po nasazení se centrum událostí zobrazí v podokně **Centra událostí** oboru názvů služby Event Hubs a jeho **STAV** bude *Aktivní*. Kliknutím na toto centrum událostí otevřete podokno **Přehled**.

1. Klikněte na tlačítko **Skupina uživatelů** v horní části a zadejte název skupiny uživatelů, například *tsievents*. Klikněte na možnost **Vytvořit**.
    ![Skupina uživatelů centra událostí](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Po vytvoření se skupina uživatelů zobrazí v seznamu v dolní části podokna **Přehled** centra událostí. 

1. Otevřete podokno **Zásady sdíleného přístupu** pro vaše centrum událostí a klikněte na tlačítko **Přidat**. **Vytvořte** zásadu *ManageSend* a ujistěte se, že jsou všechna políčka zaškrtnutá. 

    ![Připojovací řetězce centra událostí](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Otevřete zásadu *ManageSend*, kterou jste vytvořili, a zkopírujte do dočasného souboru hodnoty **Připojovací řetězec – primární klíč** a **Připojovací řetězec – sekundární klíč**. Tyto hodnoty budete potřebovat k vytvoření koncového bodu pro centrum událostí v další části.

### <a name="create-endpoint-for-the-event-hub"></a>Vytvoření koncového bodu pro centrum událostí

1. V příkazovém okně se ujistěte, že se nacházíte ve složce **_occupancy-quickstart\src** ukázky služby Digital Twins.

1. V editoru otevřete soubor **_actions\createEndpoints.yaml_**. Obsah souboru nahraďte následujícím kódem:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. Zástupné texty `Primary_connection_string_for_your_event_hub` nahraďte hodnotou **Připojovací řetězec – primární klíč** centra událostí. Ujistěte se, že tento připojovací řetězec má následující formát:
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. Zástupné texty `Secondary_connection_string_for_your_event_hub` nahraďte hodnotou **Připojovací řetězec – sekundární klíč** centra událostí. Ujistěte se, že tento připojovací řetězec má následující formát: 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. Zástupné texty `Name_of_your_Event_Hubs_namespace` nahraďte názvem vašeho oboru názvů služby Event Hubs.

    > [!IMPORTANT]
    > Všechny hodnoty zadávejte bez uvozovek. Ujistěte se, že za dvojtečkami v souboru *YAML* vždy následuje alespoň jeden znak mezery. Obsah souboru *YAML* můžete zkontrolovat také pomocí nějakého online validátoru YAML, jako je například [tento nástroj](https://onlineyamltools.com/validate-yaml).


1. Uložte soubor a zavřete ho. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste ke svému účtu Azure.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Pro vaše centrum událostí se vytvoří dva koncové body.

   ![Koncové body pro službu Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analýza s využitím služby Time Series Insights

1. V levém navigačním podokně webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**. 

1. Vyhledejte a vyberte nový prostředek **Time Series Insights**. Klikněte na možnost **Vytvořit**.

1. Zadejte **Název** vaší instance služby Time Series Insights a pak vyberte své **Předplatné**. Vyberte **Skupinu prostředků**, kterou jste použili pro instanci služby Digital Twins, a vaše **Umístění**. Klikněte na možnost **Vytvořit**.

    ![Vytvoření služby TSI](./media/tutorial-facilities-analyze/create-tsi.png)

1. Po nasazení otevřete prostředí Time Series Insights a pak otevřete podokno **Zdroje událostí**. Kliknutím na tlačítko **Přidat** v horní části přidejte skupinu uživatelů.

1. V podokně **Nový zdroj událostí** zadejte **Název** a zkontrolujte, že jsou správně vybrané ostatní hodnoty. Jako **Název zásady centra událostí** vyberte *ManageSend* a pak jako **Skupinu uživatelů centra událostí** vyberte *skupinu uživatelů*, kterou jste vytvořili v předchozí části. Klikněte na možnost **Vytvořit**.

    ![Zdroj událostí služby TSI](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Otevřete podokno **Přehled** vašeho prostředí Time Series Insights a v horní části klikněte na tlačítko **Přejít k prostředí**. Pokud se zobrazí *upozornění na přístup k datům*, otevřete podokno **Zásady přístupu k datům** pro vaši instanci služby TSI, klikněte na **Přidat**, vyberte roli **Přispěvatel** a vyberte odpovídajícího uživatele.

1. Tlačítkem **Přejít k prostředí** se otevře [Průzkumník služby Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Pokud se v něm nezobrazí žádné události, přejděte do projektu **_device-connectivity_** vaší ukázky služby Digital Twins a spuštěním příkazu `dotnet run` simulujte události zařízení.

1. Až se vygeneruje několik simulovaných událostí, vraťte se do průzkumníka služby Time Series Insights a klikněte na tlačítko Aktualizovat v horní části. Mělo by se zobrazit vytváření analytických grafů pro vaše simulovaná data ze senzorů. 

    ![Průzkumník služby TSI](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. V průzkumníku Time Series Insights pak můžete generovat grafy a heat mapy pro různé události a data z místností, senzorů a dalších prostředků. Pokud chcete vytvořit vlastní vizualizace, na levé straně klikněte na rozevírací nabídky **MÍRA** a **ROZDĚLIT PODLE**. Například výběrem možnosti *Události* v nabídce **MÍRA** a možnosti *DigitalTwins-SensorHardwareId* v nabídce **ROZDĚLIT PODLE** vygenerujete heat mapu jednotlivých senzorů podobně jako na následujícím obrázku:

    ![Průzkumník služby TSI](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už službu Azure Digital Twins nechcete dál zkoumat, můžete prostředky vytvořené v rámci tohoto kurzu odstranit:

1. V levé nabídce na webu [Azure Portal](http://portal.azure.com) klikněte na **Všechny prostředky**, vyberte svou skupinu prostředků Digital Twins a **odstraňte** ji.
2. V případě potřeby můžete pokračovat odstraněním ukázkových aplikací i na pracovním počítači. 


## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte více o grafech prostorové inteligence a objektových modelech ve službě Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Vysvětlení grafu prostorové inteligence a objektových modelů služby Digital Twins](concepts-objectmodel-spatialgraph.md)

