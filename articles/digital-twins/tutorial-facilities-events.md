---
title: 'Kurz: zachycení událostí z oblasti digitálních vláken Azure | Microsoft Docs'
description: Pomocí kroků v tomto kurzu se dozvíte, jak dostávat oznámení z vašich prostorů díky integraci digitálních vláken Azure s Logic Apps.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.openlocfilehash: 26976956722d77e2dfb8c17734c207b2667c0126
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949178"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Kurz: příjem oznámení z digitálních vláken Azure pomocí Logic Apps

Po nasazení instance digitálního vlákna Azure můžete zřídit své prostory a implementovat vlastní funkce pro monitorování konkrétních podmínek. při výskytu monitorovaných podmínek můžete správce Office informovat e-mailem.

V [prvním kurzu](tutorial-facilities-setup.md)jste nakonfigurovali prostorový graf imaginární budovy. Místnost v budově obsahuje snímače pro pohyb, oxid uhličitý a teplotu. V [druhém kurzu](tutorial-facilities-udf.md)jste zřídili graf a uživatelsky definovanou funkci pro monitorování těchto hodnot senzorů a aktivovali oznámení v případě, že je místnost prázdná a teplota a oxid uhličitého jsou v pohodlném rozsahu. 

V tomto kurzu se dozvíte, jak můžete integrovat tato oznámení s Azure Logic Apps k odesílání e-mailů, když je taková místnost dostupná. Správce systému Office může tyto informace využít k tomu, aby se uživatelům usnadnila nejvyšší produktivita místnosti pro schůzky.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Integrujte události s Azure Event Grid.
> * Upozorněte události pomocí Logic Apps.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) nastavení digitálních vláken Azure. Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instance digitálních vláken je spuštěná.
- Ukázky digitálních vláken se stáhly a extrahují na vašem pracovním počítači. [ C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp)
- [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači pro spuštění ukázky. Spusťte `dotnet --version` a ověřte, zda je nainstalovaná správná verze.
- Účet Office 365 pro odesílání e-mailů s oznámením

> [!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance digitálního vlákna.

## <a name="integrate-events-with-event-grid"></a>Integrace událostí s Event Grid

V této části nastavíte [Event Grid](../event-grid/overview.md) pro shromažďování událostí z instance digitálního vlákna Azure a jejich přesměrování na [obslužnou rutinu události](../event-grid/event-handlers.md) , jako je například Logic Apps.

### <a name="create-an-event-grid-topic"></a>Vytvoření tématu Event gridu

[Téma Event Grid](../event-grid/concepts.md#topics) poskytuje rozhraní pro směrování událostí generovaných uživatelsky definovanou funkcí. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**. 

1. Vyhledejte a vyberte **Event Grid téma**. Vyberte **vytvořit**.

1. Zadejte **název** vašeho tématu Event gridu a vyberte **předplatné**. Vyberte **skupinu prostředků** , kterou jste použili nebo vytvořili pro instanci digitálního vlákna a **umístění**. Vyberte **vytvořit**. 

    [@no__t – 1Create téma Event gridu](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Přejděte do tématu Event Grid z vaší skupiny prostředků, vyberte **Přehled**a zkopírujte hodnotu pro **koncový bod tématu** do dočasného souboru. Tuto adresu URL budete potřebovat v další části. 

1. Vyberte **přístupové klíče**a zkopírujte **YOUR_KEY_1** a **YOUR_KEY_2** do dočasného souboru. Tyto hodnoty budete potřebovat pro vytvoření koncového bodu v další části.

    [@no__t – klíče mřížky 1Event](./media/tutorial-facilities-events/event-grid-keys.png)](./media/tutorial-facilities-events/event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Vytvoření koncového bodu pro téma Event gridu

1. V okně příkazového řádku se ujistěte, že jste ve složce **Occupancy-quickstart\src** v ukázce digitálních vláken.

1. Otevřete soubor **actions\createEndpoints.yaml** v editoru Visual Studio Code. Ujistěte se, že má následující obsah:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. Zástupný symbol `<Primary connection string for your Event Grid>` nahraďte hodnotou **YOUR_KEY_1**.

1. Zástupný symbol `<Secondary connection string for your Event Grid>` nahraďte hodnotou **YOUR_KEY_2**.

1. Zástupný symbol pro **cestu** nahraďte cestou k tématu Event Grid. Získat tuto cestu odebráním **https://** a koncových cest prostředků z adresy URL **koncového bodu tématu** . Měl by vypadat podobně jako v tomto formátu: *yourEventGridName.yourLocation.eventgrid.Azure.NET*.

    > [!IMPORTANT]
    > Zadejte všechny hodnoty bez uvozovek. Ujistěte se, že je alespoň jeden znak mezery za dvojtečkami v souboru YAML. Obsah souboru YAML můžete také ověřit pomocí libovolného ověřovacího modulu online YAML, jako je [Tento nástroj](https://onlineyamltools.com/validate-yaml).

1. Soubor uložte a zavřete. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Tento příkaz vytvoří koncový bod pro Event Grid. 

   [@no__t – 1Endpoints pro Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Oznamovat události pomocí Logic Apps

Službu [Azure Logic Apps](../logic-apps/logic-apps-overview.md) můžete použít k vytvoření automatizovaných úloh pro události obdržené z jiných služeb. V této části nastavíte Logic Apps k vytváření e-mailových oznámení pro události směrované z prostorových senzorů s využitím [tématu Event gridu](../event-grid/overview.md).

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**.

1. Vyhledejte a vyberte nový prostředek **Aplikace logiky** . Vyberte **vytvořit**.

1. Zadejte **název** prostředku aplikace logiky a pak vyberte své **předplatné**, **skupinu prostředků**a **umístění**. Vyberte **vytvořit**.

    [@no__t – 1Create prostředku Logic Apps](./media/tutorial-facilities-events/create-logic-app.png)](./media/tutorial-facilities-events/create-logic-app.png#lightbox)

1. Otevřete prostředek Logic Apps, když se nasadí, a pak otevřete podokno **návrháře aplikace logiky** . 

1. Vyberte, kdy se má aktivovat **událost prostředku Event Grid** . Po zobrazení výzvy se přihlaste ke svému tenantovi pomocí svého účtu Azure. Pokud se zobrazí výzva, vyberte v případě potřeby přístup k prostředku Event Grid možnost **Povolení přístupu** . Vyberte **pokračovat**.

1. V okně **při výskytu události prostředku (Preview)** : 
   
   a. Vyberte **předplatné** , které jste použili k vytvoření tématu Event Grid.

   b. Pro **typ prostředku**vyberte **Microsoft. EventGrid. témata** .

   r. Z rozevíracího seznamu pro **název prostředku**vyberte prostředek Event Grid.

   [@no__t – podokno návrháře aplikací 1Logic](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Vyberte tlačítko **Nový krok** .

1. V okně **zvolit akci** :

   a. Vyhledejte **kód JSON pro analýzu**fráze a vyberte akci **analyzovat JSON** .

   b. V poli **obsah** vyberte ze seznamu **dynamický obsah** položku **tělo** .

   r. Vyberte **použít ukázkovou datovou část k vygenerování schématu**. Vložte následující datovou část JSON a potom vyberte **Hotovo**.

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    Tato datová část obsahuje smyšlené hodnoty. Logic Apps používá tuto ukázkovou datovou část k vygenerování *schématu*.

    [@no__t – okno analýzy aplikace 1Logic pro Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Vyberte tlačítko **Nový krok** .

1. V okně **zvolit akci** :

   a. V seznamu **akcí** vyberte **> podmínka** nebo **Podmínka** vyhledávání. 

   b. V prvním textovém poli **zvolit hodnotu** vyberte možnost **EventType** ze seznamu **dynamického obsahu** pro okno **analyzovat JSON** .

   r. V druhém textovém poli **zvolit hodnotu** zadejte `UdfCustom`.

   [podmínky @no__t – 1Selected](./media/tutorial-facilities-events/logic-app-condition.png)](./media/tutorial-facilities-events/logic-app-condition.png#lightbox)

1. V okně **if true** :

   a. Vyberte **přidat akci**a vyberte **Office 365 Outlook**.

   b. V seznamu **Akce** vyberte **Odeslat e-mail**. Vyberte **Přihlásit** se a použijte přihlašovací údaje k e-mailovým účtům. Pokud se zobrazí výzva, vyberte možnost **Povolení přístupu** .

   r. Do pole **do** zadejte ID e-mailu pro příjem oznámení. V části **Předmět**zadejte text **digitální zdvojení oznámení pro špatnou kvalitu ovzduší v prostoru**. Pak vyberte **TopologyObjectId** ze seznamu **dynamického obsahu** pro **analyze JSON**.

   trojrozměrné. V části **tělo** v jednom okně zadejte text podobný tomuto: **snížená kvalita vzduchu zjištěná v místnosti a teplota musí být upravena**. Můžete si je klidně vypracovat pomocí prvků ze seznamu **dynamického obsahu** .

   [výběry ![Logic aplikací "Odeslat e-mail"](./media/tutorial-facilities-events/logic-app-send-email.png)](./media/tutorial-facilities-events/logic-app-send-email.png#lightbox)

1. V horní části podokna **návrháře aplikace logiky** vyberte tlačítko **Uložit** .

1. Nezapomeňte simulovat data senzorů tak, že přejdete do složky pro **připojení zařízení** v ukázce digitální vlákna v příkazovém okně a spustíte `dotnet run`.

Během několika minut byste měli začít dostávat e-mailová oznámení od tohoto Logic Apps prostředku. 

   [oznámení @no__t – 1Email](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Pokud chcete přestat přijímat tyto e-maily, na portálu klikněte na prostředek Logic Apps a vyberte podokno **Přehled** . Vyberte **Zakázat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přestat zkoumat digitální vlákna Azure v tomto okamžiku, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků vaše digitální vlákna a vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste narazili na potíže s odstraněním instance digitálního vlákna, aktualizovala se aktualizace služby s touto opravou. Zkuste prosím instanci znovu odstranit.

2. V případě potřeby odstraňte ukázkové aplikace v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak vizualizovat data senzorů, analyzovat trendy a odhalit anomálie, najdete v dalším kurzu:

> [!div class="nextstepaction"]
> [Kurz: vizualizace a analýza událostí z digitálních vláken Azure pomocí Time Series Insights](tutorial-facilities-analyze.md)

Můžete si taky přečíst další informace o grafech prostorové logiky a objektových modelech v části digitální vlákna Azure:

> [!div class="nextstepaction"]
> [Porozumění objektovým modelům a graf prostorové logiky digitálních vláken](concepts-objectmodel-spatialgraph.md)
