---
title: Analýza událostí ze systému Azure Digital Twins | Microsoft Docs
description: Zjistěte, jak vizualizovat a analyzovat události z prostory vaší digitální dvojče Azure s Azure Time Series Insights pomocí kroků v tomto kurzu.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: b7c44184d0a0d8d5a8f1fbd80b053230f2c1ae84
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582016"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>Kurz: Vizualizace a analýza událostí z prostory vaší digitální dvojče Azure s využitím Time Series Insights

Po zřízení prostory vaší nasazení vaší instance Azure digitální dvojče a implementujte vlastní funkce pro monitorování určitých podmínek, můžete vizualizovat události a data přicházející z prostory vaší podívat na trendy a anomálie. 

V [první kurz](tutorial-facilities-setup.md), jste nakonfigurovali prostorový graf imaginární budovy, s prostor, který obsahuje pohybu, oxidu uhličitého a teploty senzorů. V [druhém kurzu](tutorial-facilities-udf.md) jste si zřídili graf a uživatelem definovanou funkci. Funkce monitoruje tyto hodnoty snímačů a aktivuje oznámení pro správné podmínky. To znamená prostor je prázdný a teploty a k oxidu uhličitého úrovně je normální. 

Tento kurz vám ukáže, jak integrovat, oznámení a data přicházející z nastavení digitální dvojče Azure s Azure Time Series Insights. Potom můžete vizualizovat hodnoty snímačů v čase. Můžete vyhledat trendy, jako jsou místa, které je stále nejlépe využil a které jsou za nejvytíženější v které denní době. Můžete také detekovat anomálie jako stuffier a hotter pocit, že které místnosti nebo zda je oblast v budově odeslání konzistentně vysoké teplotní hodnoty určující klimatizace vadný.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Stream dat pomocí Azure Event Hubs.
> * Analýza s Time Series Insights.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) vlastní systém Azure Digital Twins. Než budete pokračovat, ujistěte se, že máte následující:
- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins.
- Pracovní počítač se staženými a extrahovanými [ukázkami služby Digital Twins v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- [Sada .NET core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) na vývojovém počítači ke spuštění ukázky. Spustit `dotnet --version` ověření nainstalovanou správnou verzi. 


## <a name="stream-data-by-using-event-hubs"></a>Stream dat s využitím služby Event Hubs
Můžete použít [Event Hubs](../event-hubs/event-hubs-about.md) služby k vytvoření kanálu, abyste mohli Streamovat data. V této části se dozvíte, jak vytvořit Centrum událostí jako konektor mezi vaší instancí Azure digitální dvojče a Time Series Insights.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**. 

1. Vyhledejte a vyberte **Event Hubs**. Vyberte **Vytvořit**.

1. Zadejte **název** pro váš obor názvů služby Event Hubs. Zvolte **standardní** pro **cenová úroveň**, vaše **předplatné**, **skupiny prostředků** , který jste použili pro vaši instanci digitální dvojče a **umístění**. Vyberte **Vytvořit**. 

1. V nasazení oboru názvů služby Event Hubs, vyberte obor názvů, v části **prostředků**.

    ![Obor názvů služby Event Hubs po nasazení](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. V oboru názvů služby Event Hubs **přehled** podokně, vyberte **centra událostí** tlačítko v horní části. 
    ![Tlačítko centra událostí](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Zadejte **název** centra událostí, a vyberte **vytvořit**. 

   Po nasazení centra událostí se zobrazí v **Event Hubs** podokně oboru názvů Event Hubs pomocí **aktivní** stav. Vyberte centra událostí a otevřete její **přehled** podokně.

1. Vyberte **skupinu příjemců** tlačítko v horní části a zadejte název, například **tsievents** skupině příjemců. Vyberte **Vytvořit**.
    ![Skupina uživatelů centra událostí](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Po vytvoření se skupina uživatelů se zobrazí v seznamu dole v Centru událostí **přehled** podokně. 

1. Otevřít **zásady sdíleného přístupu** podokně centra událostí, a vyberte **přidat** tlačítko. Zadejte **ManageSend** jako název zásady, ujistěte se, že jsou vybrány všechna zaškrtávací políčka a vyberte **vytvořit**. 

    ![Připojovací řetězce centra událostí](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Otevřete ManageSend zásadu, kterou jste vytvořili a zkopírujte hodnoty **připojovací řetězec – primární klíč** a **připojovací řetězec – sekundární klíč** do dočasného souboru. Bude nutné tyto hodnoty a vytvořte koncový bod centra událostí v další části.

### <a name="create-an-endpoint-for-the-event-hub"></a>Vytvořit koncový bod centra událostí

1. V příkazovém okně, ujistěte se, že jste v **obsazení quickstart\src** složky Ukázky digitální dvojče Azure.

1. Otevřete soubor **actions\createEndpoints.yaml** v editoru. Obsah souboru nahraďte následujícím kódem:

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

1. Nahraďte zástupné symboly `Primary_connection_string_for_your_event_hub` s hodnotou **připojovací řetězec – primární klíč** pro Centrum událostí. Ujistěte se, že formát tento připojovací řetězec vypadá takto:

   ```
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Nahraďte zástupné symboly `Secondary_connection_string_for_your_event_hub` s hodnotou **připojovací řetězec – sekundární klíč** pro Centrum událostí. Ujistěte se, že formát tento připojovací řetězec vypadá takto: 

   ```
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Zástupné texty `Name_of_your_Event_Hubs_namespace` nahraďte názvem vašeho oboru názvů služby Event Hubs.

    > [!IMPORTANT]
    > Všechny hodnoty zadávejte bez uvozovek. Ujistěte se, že existuje alespoň jeden znak po dvojtečky v souboru YAML. Můžete také ověřit váš obsah souboru YAML pomocí jakékoli online validátoru YAML [tento nástroj](https://onlineyamltools.com/validate-yaml).


1. Uložte soubor a zavřete ho. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste ke svému účtu Azure.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Vytvoří dva koncové body pro vaše Centrum událostí.

   ![Koncové body pro službu Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analýza s využitím služby Time Series Insights

1. V levém podokně [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek**. 

1. Vyhledejte a vyberte nový prostředek **Time Series Insights**. Vyberte **Vytvořit**.

1. Zadejte **Název** vaší instance služby Time Series Insights a pak vyberte své **Předplatné**. Vyberte **skupiny prostředků** , který jste použili pro vaši instanci digitální dvojče a **umístění**. Vyberte **Vytvořit**.

    ![Vybrané možnosti pro vytvoření instance služby Time Series Insights](./media/tutorial-facilities-analyze/create-tsi.png)

1. Po nasazení instance, otevřete prostředí Time Series Insights a pak otevřete jeho **zdroje událostí** podokně. Vyberte **přidat** tlačítko v horní části stránky se přidat skupinu uživatelů.

1. V podokně **Nový zdroj událostí** zadejte **Název** a zkontrolujte, že jsou správně vybrané ostatní hodnoty. Vyberte **ManageSend** pro **název zásady centra událostí**a pak vyberte skupinu příjemců, který jste vytvořili v předchozí části pro **skupina uživatelů centra událostí**. Vyberte **Vytvořit**.

    ![Vybrané možnosti pro vytvoření zdroje událostí](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Otevřít **přehled** podokno pro vaše prostředí Time Series Insights, vyberte **přejít na prostředí** tlačítko v horní části. Pokud dojde k přístupu k datům upozornění, otevřete **zásady přístupu k datům** podokno pro vaši instanci služby Time Series Insights, vyberte **přidat**vyberte **Přispěvatel** roli a vyberte příslušné uživatele.

1. **Přejít na prostředí** tlačítko otevře [Průzkumníka služby Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Pokud se nezobrazí žádné události, simulace událostí zařízení tak, že přejdete na **připojení zařízení** digitální dvojče vzorku a spuštění projektu `dotnet run`.

1. Po několika simulovaných události se generují, přejděte zpět do Průzkumníku Time Series Insights a vyberte tlačítko pro aktualizaci v horní části. Měli byste vidět analytické grafy pro data ze simulovaných senzorů. 

    ![Grafu v Průzkumníku Time Series Insights](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. V Průzkumníku Time Series Insights potom můžete generovat grafy a Heat mapy pro jednotlivé události a data z vašich prostorách, senzorů a dalších prostředků. Na levé straně, použijte **míru** a **ROZDĚLIT podle** rozevírací seznamy a vytvářet vlastní vizualizace. 

   Vyberte například **události** pro **míru** a **DigitalTwins SensorHardwareId** pro **ROZDĚLIT podle**můžete generovat Heat pro každou z senzory. Heat mapě bude vypadat jako na následujícím obrázku:

   ![V Průzkumníku Time Series Insights Heatmapu](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zastavit zkoumání digitální dvojče Azure nad rámec tohoto bodu, bez obav odstraňte prostředky vytvořené v tomto kurzu:

1. V levé nabídce v [webu Azure portal](http://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků digitální dvojče a pak vyberte **odstranit**.

    > [!TIP]
    > Pokud zaznamenal/zaznamenala jste potíže odstraníte instanci digitální dvojče, aktualizace služby se týká jenom s opravou. Zkuste to prosím znovu odstraníte instanci.

2. V případě potřeby odstraňte ukázkové aplikace na svém počítači práce. 


## <a name="next-steps"></a>Další postup

Přejdete k dalším článku se dozvíte další informace o Prostorové řady grafů a objektové modely v digitální dvojče Azure. 
> [!div class="nextstepaction"]
> [Vysvětlení grafu prostorové inteligence a objektových modelů služby Digital Twins](concepts-objectmodel-spatialgraph.md)

