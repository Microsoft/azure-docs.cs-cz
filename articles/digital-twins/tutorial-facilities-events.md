---
title: 'Kurz: zachycení událostí zařízení z prostoru IoT – digitální vlákna Azure | Microsoft Docs'
description: Zjistěte, jak pomocí kroků v tomto kurzu dostávat oznámení z prostorů díky integraci služby Azure Digital Twins s Logic Apps.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 1cd617204bbc12a99b6ae9e3b55fbc59b0e0578a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75933705"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Kurz: příjem oznámení z digitálních vláken Azure pomocí Logic Apps

Po nasazení instance digitálního vlákna Azure můžete zřídit své prostory a implementovat vlastní funkce pro monitorování konkrétních podmínek. při výskytu monitorovaných podmínek můžete správce Office informovat e-mailem.

V [prvním kurzu](tutorial-facilities-setup.md)jste nakonfigurovali prostorový graf imaginární budovy. Místnost v budově obsahuje snímače pro pohyb, oxid uhličitý a teplotu. V [druhém kurzu](tutorial-facilities-udf.md)jste zřídili graf a uživatelsky definovanou funkci pro monitorování těchto hodnot senzorů a aktivovali oznámení v případě, že je místnost prázdná a teplota a oxid uhličitého jsou v pohodlném rozsahu. 

V tomto kurzu se dozvíte, jak tato oznámení integrovat s Azure Logic Apps a odesílat e-maily, když bude taková místnost dostupná. Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům s rezervací zasedacích místností nejvíce podporujících produktivitu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Integrujte události s Azure Event Grid.
> * Upozorněte události pomocí Logic Apps.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) vlastní systém Azure Digital Twins. Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins.
- [Ukázky digitálních vláken v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) stažených a extrahovaných v pracovním počítači.
- [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači pro spuštění ukázky. Spusťte `dotnet --version` , chcete-li ověřit, zda je nainstalovaná správná verze.
- Účet [Office 365](https://products.office.com/home) pro odesílání e-mailů s oznámením

> [!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance digitálního vlákna.

## <a name="integrate-events-with-event-grid"></a>Integrace událostí se službou Event Grid

V této části nastavíte [Event Grid](../event-grid/overview.md) pro shromažďování událostí z instance digitálního vlákna Azure a jejich přesměrování na [obslužnou rutinu události](../event-grid/event-handlers.md) , jako je například Logic Apps.

### <a name="create-an-event-grid-topic"></a>Vytvoření tématu Event gridu

[Téma Event Grid](../event-grid/concepts.md#topics) poskytuje rozhraní pro směrování událostí generovaných uživatelsky definovanou funkcí. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**. 

1. Vyhledejte a vyberte **Téma Event Gridu**. Vyberte **Vytvořit**.

1. Zadejte **Název** tématu Event Gridu a zvolte **Předplatné**. Vyberte **skupinu prostředků** , kterou jste použili nebo vytvořili pro instanci digitálního vlákna a **umístění**. Vyberte **Vytvořit**. 

    [![Vytvoření tématu Event gridu](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Přejděte do tématu Event Grid z vaší skupiny prostředků, vyberte **Přehled**a zkopírujte hodnotu pro **koncový bod tématu** do dočasného souboru. Tuto adresu URL budete potřebovat v další části. 

1. Vyberte **přístupové klíče**a zkopírujte **klíč 1** a **2** do dočasného souboru. Tyto hodnoty budete potřebovat pro vytvoření koncového bodu v další části.

    [![Event Grid klíče](./media/tutorial-facilities-events/tutorial-event-grid-keys.png)](./media/tutorial-facilities-events/tutorial-event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Vytvoření koncového bodu pro téma Event gridu

1. V okně příkazového řádku se ujistěte, že jste ve složce **Occupancy-quickstart\src** v ukázce digitálních vláken.

1. V editoru Visual Studio Code otevřete soubor **actions\createEndpoints.yaml**. Ujistěte se, že má následující obsah:

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

1. Zástupný text `<Primary connection string for your Event Grid>` nahraďte hodnotou **klíče 1**.

1. Zástupný text `<Secondary connection string for your Event Grid>` nahraďte hodnotou **klíče 2**.

1. Zástupný symbol pro **cestu** nahraďte cestou k tématu Event Grid. Získat tuto cestu odebráním **https://** a koncových cest prostředků z adresy URL **koncového bodu tématu** . Cesta by měla mít přibližně tento formát: *yourEventGridName.yourLocation.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Všechny hodnoty zadávejte bez uvozovek. Ujistěte se, že je alespoň jeden znak mezery za dvojtečkami v souboru YAML. Obsah souboru YAML můžete také ověřit pomocí libovolného ověřovacího modulu online YAML, jako je [Tento nástroj](https://onlineyamltools.com/validate-yaml).

1. Uložte soubor a zavřete ho. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Tento příkaz vytvoří koncový bod pro Event Grid. 

   [![Koncové body pro službu Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Oznamovat události pomocí Logic Apps

Službu [Azure Logic Apps](../logic-apps/logic-apps-overview.md) můžete použít k vytvoření automatizovaných úloh pro události obdržené z jiných služeb. V této části nastavíte Logic Apps k vytváření e-mailových oznámení pro události směrované z prostorových senzorů s využitím [tématu Event gridu](../event-grid/overview.md).

1. V levém podokně [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**.

1. Vyhledejte a vyberte nový prostředek **Aplikace logiky**. Vyberte **Vytvořit**.

1. Zadejte **název** prostředku aplikace logiky a pak vyberte své **předplatné**, **skupinu prostředků**a **umístění**. Vyberte **Vytvořit**.

    [![Vytvoření prostředku Logic Apps](./media/tutorial-facilities-events/tutorial-create-logic-app.png)](./media/tutorial-facilities-events/tutorial-create-logic-app.png#lightbox)

1. Otevřete prostředek Logic Apps, když se nasadí, a pak otevřete podokno **návrháře aplikace logiky** . 

1. Vyberte, kdy se má aktivovat **událost prostředku Event Grid** . Po zobrazení výzvy rozbalte možnost **Azure Event Grid** a přihlaste se ke svému klientovi pomocí svého účtu Azure. Pokud se zobrazí výzva, vyberte v případě potřeby přístup k prostředku Event Grid možnost **Povolení přístupu** . Vyberte **Pokračovat**.

1. V okně **při výskytu události prostředku** : 
   
   a. Vyberte **předplatné** , které jste použili k vytvoření tématu Event Grid.

   b. Pro **typ prostředku**vyberte **Microsoft. EventGrid. témata** .

   c. Z rozevíracího seznamu pro **název prostředku**vyberte prostředek Event Grid.

   [![Podokno návrháře aplikace logiky](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Vyberte tlačítko **Nový krok** .

1. V okně **zvolit akci** :

   a. Vyhledejte frázi **parsovat JSON** a vyberte akci **Parsovat JSON**.

   b. V poli **obsah** vyberte ze seznamu **dynamický obsah** položku **tělo** .

   c. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. Vložte následující datovou část JSON a potom vyberte **Hotovo**.

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

    Tato datová část obsahuje fiktivní hodnoty. Logic Apps používá tuto ukázkovou datovou část k vygenerování *schématu*.

    [![Logic Apps analyzovat okno JSON pro Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Vyberte tlačítko **Nový krok** .

1. V okně **zvolit akci** :

   a. V seznamu **akcí** vyberte **> podmínka** nebo **Podmínka** vyhledávání. 

   b. V prvním textovém poli **zvolit hodnotu** vyberte možnost **EventType** ze seznamu **dynamického obsahu** pro okno **analyzovat JSON** .

   c. V druhém textovém poli **zvolit hodnotu** zadejte `UdfCustom`.

   [![Vybrané podmínky](./media/tutorial-facilities-events/tutorial-logic-app-condition.png)](./media/tutorial-facilities-events/tutorial-logic-app-condition.png#lightbox)

1. V okně **if true** :

   a. Vyberte **přidat akci**a vyberte **Office 365 Outlook**.

   b. V seznamu **Akce** vyberte **Odeslat e-mail (v2)**. Vyberte **Přihlásit** se a použijte přihlašovací údaje k e-mailovým účtům. Pokud se zobrazí výzva, vyberte možnost **Povolení přístupu** .

   c. Do pole **Příjemce** zadejte ID svého e-mailu, abyste dostávali oznámení. V části **Předmět**zadejte text **digitální zdvojení oznámení pro špatnou kvalitu ovzduší v prostoru**. Pak vyberte **TopologyObjectId** ze seznamu **dynamického obsahu** pro **analyze JSON**.

   d. V části **tělo** v jednom okně zadejte text podobný následujícímu: **v místnosti se zjistila špatná kvalita vzduchu a je potřeba upravit teplotu**. Můžete si je klidně vypracovat pomocí prvků ze seznamu **dynamického obsahu** .

   [![Logic Apps výběru "Odeslat e-mail"](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png)](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png#lightbox)

1. V horní části podokna **návrháře aplikace logiky** vyberte tlačítko **Uložit** .

1. Zajistěte simulaci dat snímačů, a to tak, že v příkazovém okně přejdete do složky pro **připojení zařízení** v ukázce digitální vlákna `dotnet run`a spustíte.

Během několika minut byste měli začít dostávat e-mailová oznámení od tohoto Logic Apps prostředku. 

   [![E-mailové oznámení](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

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
> [Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights](tutorial-facilities-analyze.md)

Můžete si taky přečíst další informace o grafech prostorové logiky a objektových modelech v části digitální vlákna Azure:

> [!div class="nextstepaction"]
> [Vysvětlení grafu prostorové inteligence a objektových modelů služby Digital Twins](concepts-objectmodel-spatialgraph.md)
