---
title: 'Kurz: Zachyťte události zařízení z prostoru IoT – digitální dvojčata Azure| Dokumenty společnosti Microsoft'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933705"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Kurz: Dostávat oznámení z prostorů Azure Digital Twins pomocí logic apps

Po nasazení instance Azure Digital Twins, zřizování prostorů a implementaci vlastních funkcí pro monitorování konkrétních podmínek můžete upozornit správce kanceláře e-mailem, když nastanou sledované podmínky.

V [prvním kurzu](tutorial-facilities-setup.md)jste nakonfigurovali prostorový graf imaginární budovy. Místnost v budově obsahuje senzory pohybu, oxidu uhličitého a teploty. Ve [druhém kurzu](tutorial-facilities-udf.md)jste zřídit graf a uživatelem definovanou funkci pro sledování těchto hodnot senzorů a spouštění oznámení, když je místnost prázdná, a teplota a oxid uhličitý jsou v pohodlném rozsahu. 

V tomto kurzu se dozvíte, jak tato oznámení integrovat s Azure Logic Apps a odesílat e-maily, když bude taková místnost dostupná. Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům s rezervací zasedacích místností nejvíce podporujících produktivitu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Integrujte události s Azure Event Grid.
> * Upozorněte události pomocí logicových aplikací.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) vlastní systém Azure Digital Twins. Než budete pokračovat, ujistěte se, že máte následující:

- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins.
- [Digitální dvojčata C # ukázky](https://github.com/Azure-Samples/digital-twins-samples-csharp) stáhnout a extrahovat na vašem pracovním počítači.
- [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači spustit ukázku. Spusťte `dotnet --version` a ověřte, zda je nainstalována správná verze.
- Účet [Office 365](https://products.office.com/home) pro odesílání e-mailů s oznámením

> [!TIP]
> Pokud zřizujete novou instanci, použijte jedinečný název instance Digitální dvojčata.

## <a name="integrate-events-with-event-grid"></a>Integrace událostí se službou Event Grid

V této části nastavíte [Event Grid](../event-grid/overview.md) pro shromažďování událostí z instance Azure Digital Twins a přesměrovat je na [obslužnou rutinu události,](../event-grid/event-handlers.md) jako jsou logic apps.

### <a name="create-an-event-grid-topic"></a>Vytvoření tématu mřížky událostí

[Téma mřížky událostí](../event-grid/concepts.md#topics) poskytuje rozhraní pro směrování událostí generovaných uživatelem definovanou funkcí. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **Vytvořit prostředek**. 

1. Vyhledejte a vyberte **Téma Event Gridu**. Vyberte **Vytvořit**.

1. Zadejte **Název** tématu Event Gridu a zvolte **Předplatné**. Vyberte **skupinu prostředků,** kterou jste použili nebo vytvořili pro instanci Digitální dvojčata, a **umístění**. Vyberte **Vytvořit**. 

    [![Vytvoření tématu mřížky událostí](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Přejděte k tématu mřížky událostí ze skupiny prostředků, vyberte **Přehled**a zkopírujte hodnotu **koncového bodu tématu** do dočasného souboru. Tuto adresu URL budete potřebovat v další části. 

1. Vyberte **přístupové klávesy**a zkopírujte **klíč 1** a **klíč 2** do dočasného souboru. Tyto hodnoty budete potřebovat k vytvoření koncového bodu v další části.

    [![Tlačítka mřížky událostí](./media/tutorial-facilities-events/tutorial-event-grid-keys.png)](./media/tutorial-facilities-events/tutorial-event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Vytvoření koncového bodu pro téma mřížky událostí

1. V příkazovém okně se ujistěte, že jste ve složce **obsazenost-quickstart\src** ukázky digitálnídvojčata.

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

1. Nahraďte `<Primary connection string for your Event Grid>` zástupný symbol hodnotou **klíče 1**.

1. Nahraďte `<Secondary connection string for your Event Grid>` zástupný symbol hodnotou **klíče 2**.

1. Nahraďte zástupný symbol **cesty** cestou tématu mřížky událostí. Tuto cestu získáte odebráním **https://** a cest koncových prostředků z adresy URL **koncového bodu tématu.** Cesta by měla mít přibližně tento formát: *yourEventGridName.yourLocation.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Všechny hodnoty zadávejte bez uvozovek. Ujistěte se, že za dvojtečkami v souboru YAML je alespoň jeden znak mezery. Můžete také ověřit obsah souboru YAML pomocí libovolného online validátoru YAML, jako je [tento nástroj](https://onlineyamltools.com/validate-yaml).

1. Uložte soubor a zavřete ho. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Tento příkaz vytvoří koncový bod pro mřížku událostí. 

   [![Koncové body pro službu Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Upozornit události pomocí aplikací logiky

Službu [Azure Logic Apps](../logic-apps/logic-apps-overview.md) můžete použít k vytvoření automatizovaných úloh pro události přijaté z jiných služeb. V této části nastavíte Logic Apps pro vytváření e-mailových oznámení pro události směrované z prostorových senzorů pomocí [tématu mřížky událostí](../event-grid/overview.md).

1. V levém podokně [portálu Azure](https://portal.azure.com)vyberte **Vytvořit prostředek**.

1. Vyhledejte a vyberte nový prostředek **Aplikace logiky**. Vyberte **Vytvořit**.

1. Zadejte **název** prostředku aplikace Logika a vyberte **předplatné**, **skupinu prostředků**a **umístění**. Vyberte **Vytvořit**.

    [![Vytvoření prostředku aplikace logiky](./media/tutorial-facilities-events/tutorial-create-logic-app.png)](./media/tutorial-facilities-events/tutorial-create-logic-app.png#lightbox)

1. Otevřete prostředek logic apps, když se nasazuje, a pak otevřete podokno **návrháře aplikace logiky.** 

1. Vyberte **aktivační událost Při výskytu prostředku mřížky událostí.** Rozbalte možnost **Azure Event Grid** a po zobrazení výzvy se přihlaste ke svému tenantovi pomocí svého účtu Azure. Pokud se zobrazí výzva, vyberte **povolit přístup** k prostředku mřížky událostí. Vyberte **Pokračovat**.

1. V okně **Při výskytu události prostředku:** 
   
   a. Vyberte **předplatné,** které jste použili k vytvoření tématu mřížky událostí.

   b. Vyberte **microsoft.eventgrid.témata** pro **typ prostředku**.

   c. Vyberte prostředek mřížky událostí z rozevíracího pole **Název prostředku**.

   [![Podokno návrháře aplikace logiky](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Vyberte tlačítko **Nový krok.**

1. V okně **Zvolte akci:**

   a. Vyhledejte frázi **parsovat JSON** a vyberte akci **Parsovat JSON**.

   b. V poli **Obsah** vyberte **text** ze seznamu **Dynamický obsah.**

   c. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. Vložte následující datovou část JSON a pak vyberte **Hotovo**.

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

    Tato datová část obsahuje fiktivní hodnoty. Logic Apps používá tuto ukázkovou datovou část ke generování *schématu*.

    [![Okno Analýzy aplikací logiky JSON pro mřížku událostí](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Vyberte tlačítko **Nový krok.**

1. V okně **Zvolte akci:**

   a. Ze seznamu **Akce** vyberte **Možnost Řídit > podmínku** nebo Vyhledat **podmínku.** 

   b. V prvním textovém poli **Zvolte hodnotu** vyberte **eventType** ze seznamu **Dynamický obsah** pro okno **Analyzovat JSON.**

   c. Do druhého Textového pole **zvolte hodnotu** zadejte `UdfCustom`.

   [![Vybrané podmínky](./media/tutorial-facilities-events/tutorial-logic-app-condition.png)](./media/tutorial-facilities-events/tutorial-logic-app-condition.png#lightbox)

1. V okně **Pokud je to pravda:**

   a. Vyberte **Přidat akci**a vyberte **Office 365 Outlook**.

   b. V seznamu **Akce** vyberte **Odeslat e-mail (V2).** Vyberte **Přihlásit se** a použít přihlašovací údaje svého e-mailového účtu. Vyberte **Povolit přístup,** pokud se zobrazí výzva.

   c. Do pole **Příjemce** zadejte ID svého e-mailu, abyste dostávali oznámení. V **předmětu**zadejte text **Oznámení digitálních dvojčat pro špatnou kvalitu ovzduší ve vesmíru**. Pak vyberte **TopologyObjectId** ze seznamu **dynamický obsah** pro **Parse JSON**.

   d. Do pole **Tělo** ve stejném okně zadejte text podobný následujícímu: **Špatná kvalita vzduchu zjištěná v místnosti a je třeba upravit teplotu**. Nebojte se vypracovat pomocí prvků ze seznamu **dynamický obsah.**

   [![Výběry aplikací logiky "Odeslat e-mail"](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png)](./media/tutorial-facilities-events/tutorial-logic-app-send-email.png#lightbox)

1. V horní části podokna **Návrhář aplikace Logika** vyberte tlačítko **Uložit.**

1. Data senzoru simulujte procházením složky **připojení zařízení** ukázky Digitální dvojčata `dotnet run`v příkazovém okně a spuštěním aplikace .

Během několika minut byste měli začít dostávat e-mailová oznámení z tohoto prostředku Logic Apps. 

   [![E-mailové oznámení](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Pokud chcete tyto e-maily ukončit, přejděte na portál u zdroje logic apps a vyberte podokno **Přehled.** Vyberte **zakázat**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete v tomto okamžiku přestat zkoumat Azure Digital Twins, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V levé nabídce na [portálu Azure](https://portal.azure.com)vyberte **Všechny prostředky**, vyberte skupinu prostředků Digitální dvojčata a vyberte **Odstranit**.

    > [!TIP]
    > Pokud došlo k potížím s odstraněním instance Digitální dvojčata, byla s opravou zavedena aktualizace služby. Opakujte odstranění instance.

2. V případě potřeby odstraňte ukázkové aplikace v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak vizualizovat data ze senzorů, analyzovat trendy a rozpoznat anomálie, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights](tutorial-facilities-analyze.md)

Další informace o grafech prostorové inteligence a objektových modelech najdete v Azure Digital Twins:

> [!div class="nextstepaction"]
> [Vysvětlení grafu prostorové inteligence a objektových modelů služby Digital Twins](concepts-objectmodel-spatialgraph.md)
