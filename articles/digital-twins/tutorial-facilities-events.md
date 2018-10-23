---
title: Zachytávání událostí z prostoru Azure Digital Twins | Microsoft Docs
description: Zjistěte, jak pomocí kroků v tomto kurzu dostávat oznámení z prostorů díky integraci služby Azure Digital Twins s Logic Apps.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323204"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Kurz: Příjem oznámení z prostorů Azure Digital Twins pomocí Logic Apps

Po nasazení instance služby Azure Digital Twins, zřízení prostorů a implementaci vlastních funkcí pro monitorování určitých podmínek můžete e-mailem upozornit správce kanceláře v případě splnění monitorovaných podmínek. 

V [prvním kurzu](tutorial-facilities-setup.md) jste nakonfigurovali prostorový graf imaginární budovy s místností obsahující senzory pohybu, oxidu uhličitého a teploty. V [druhém kurzu](tutorial-facilities-udf.md) jste zřídili graf a vlastní funkci označovanou jako uživatelem definovaná funkce, která monitoruje hodnoty z těchto senzorů a aktivuje oznámení, když je místnost prázdná a teplota i úroveň oxidu uhličitého jsou v přípustném rozsahu. V tomto kurzu se dozvíte, jak tato oznámení integrovat s Azure Logic Apps a odesílat e-maily, když bude taková místnost dostupná. Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům s rezervací zasedacích místností nejvíce podporujících produktivitu. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Integrace událostí se službou Event Grid
> * Oznamování událostí pomocí aplikace logiky
    
## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) vlastní systém Azure Digital Twins. Než budete pokračovat, ujistěte se, že máte následující:
- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins.
- Pracovní počítač se staženými a extrahovanými [ukázkami služby Digital Twins v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- Počítač pro vývoj se sadou [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) pro spuštění ukázky. Spuštěním příkazu `dotnet --version` ověřte, že máte nainstalovanou správnou verzi. 
- Účet Office 365 pro odesílání e-mailových oznámení.

## <a name="integrate-events-with-event-grid"></a>Integrace událostí se službou Event Grid 
V této části nastavíte službu [Event Grid](../event-grid/overview.md) tak, aby shromažďovala události z vaší instance služby Digital Twins a přesměrovávala je do [obslužné rutiny události](../event-grid/event-handlers.md), jako je například Logic Apps.

### <a name="create-event-grid-topic"></a>Vytvoření tématu Event Gridu
[Témata Event Gridu](../event-grid/concepts.md#topics) poskytují rozhraní pro směrování událostí generovaných uživatelem definovanou funkcí. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Na levém navigačním panelu klikněte na **Vytvořit prostředek**. 

1. Vyhledejte a vyberte **Téma Event Gridu**. Klikněte na možnost **Vytvořit**.

1. Zadejte **Název** tématu Event Gridu a zvolte **Předplatné**. Vyberte **skupinu prostředků**, kterou jste použili nebo vytvořili po první instanci služby Digital Twins, a **Umístění**. Klikněte na možnost **Vytvořit**. 

    ![Vytvoření tématu Event Gridu](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Ze skupiny prostředků přejděte k tématu Event Gridu, klikněte na **Přehled** a zkopírujte hodnotu **Koncový bod tématu** do dočasného souboru. Tuto adresu URL budete potřebovat v následující části. 

1. Klikněte na **Přístupové klíče** a zkopírujte hodnoty **Klíč 1** a **Klíč 2** do dočasného souboru. Tyto hodnoty budete potřebovat k vytvoření koncového bodu v následující části.

    ![Klíče Event Gridu](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Vytvoření koncového bodu pro témat Event Gridu

1. V příkazovém okně se ujistěte, že se nacházíte ve složce **_occupancy-quickstart\src_** ukázky služby Digital Twins.

1. V editoru Visual Studio Code otevřete soubor **_actions\createEndpoints.yaml_**. Ujistěte se, že má následující obsah:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Zástupný text `Primary_connection_string_for_your_Event_Grid` nahraďte hodnotu **Klíč 1**. 

1. Zástupný text `Secondary_connection_string_for_your_Event_Grid` nahraďte hodnotu **Klíč 2**.

1. Zástupný text `Event_Grid_Topic_Path` nahraďte cestou k tématu Event Gridu. Tuto cestu získáte odebráním *https://* a koncových cest k prostředkům z adresy URL **koncového bodu tématu**. Cesta by měla mít přibližně tento formát: **yourEventGridName.yourLocation.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Všechny hodnoty zadávejte bez uvozovek. Ujistěte se, že za dvojtečkami v souboru *YAML* vždy následuje alespoň jeden znak mezery. Obsah souboru *YAML* můžete zkontrolovat také pomocí nějakého online validátoru YAML, jako je například [tento nástroj](https://onlineyamltools.com/validate-yaml).

1. Uložte soubor a zavřete ho. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Tento příkaz vytvoří koncový bod pro službu Event Grid. 

   ![Koncové body pro službu Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Oznamování událostí pomocí aplikace logiky
Služba [Azure Logic Apps](../logic-apps/logic-apps-overview.md) umožňuje vytvářet automatizované úlohy pro události přijaté z jiných služeb. V této části nastavíte službu Logic Apps tak, aby s využitím [tématu Event Gridu](../event-grid/overview.md) vytvářela e-mailová oznámení o událostech směrovaných z prostorových senzorů.

1. V levém navigačním podokně webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**.

1. Vyhledejte a vyberte nový prostředek **Aplikace logiky**. Klikněte na možnost **Vytvořit**.

1. Zadejte **Název** aplikace logiky a pak vyberte **Předplatné**, **Skupinu prostředků** a **Umístění**. Klikněte na možnost **Vytvořit**.

    ![Vytvoření aplikace logiky](./media/tutorial-facilities-events/create-logic-app.png)

1. Po nasazení otevřete aplikaci logiky a pak otevřete podokno **Návrhář aplikace logiky**. 

1. Vyberte trigger **Když dojde k události Event Gridu**. Po zobrazení výzvy se pomocí svého účtu Azure **přihlaste** k tenantu. Po zobrazení výzvy potvrďte **povolení přístupu** k vaší službě Event Grid. Klikněte na **Pokračovat**.

1. V okně **Když dojde k události prostředku (Preview)**: 
    1. Vyberte **Předplatné**, které jste předtím použili k vytvoření služby Event Grid.

    1. Jako **Typ prostředku** vyberte **Microsoft.EventGrid.Topics**.

    1. Jako **Název prostředku** v rozevíracím seznamu vyberte název vašeho prostředku služby Event Grid.

    ![Vytvoření aplikace logiky](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Klikněte na tlačítko **Nový krok**.

1. V okně **Vybrat akci**:
    1. Vyhledejte frázi *parsovat JSON* a vyberte akci **Parsovat JSON**.

    1. Klikněte do pole **Obsah** a v seznamu **Dynamický obsah** vyberte **Text**.

    1. Klikněte na **K vygenerování schématu použijte ukázkovou datovou část**. Vložte následující datovou část JSON a pak klikněte na **Hotovo**.

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
    
    Tato datová část obsahuje fiktivní hodnoty. Aplikace logiky použije tuto jednoduchou datovou část k vygenerování **schématu**.
    
    ![Parsování JSON v aplikaci logiky pro Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Klikněte na tlačítko **Nový krok**.

1. V okně **Vybrat akci**:
    1. Vyhledejte akci **Ovládání podmínek** a vyberte ji v seznamu **Akce**. 

    1. V okně **Parsovat JSON** klikněte do prvního textového pole **Zvolit hodnotu** a v seznamu **Dynamický obsah** vyberte **Typ události**.

    1. Klikněte do druhého textového pole **Zvolit hodnotu** a zadejte *UdfCustom*.

    ![Parsování JSON v aplikaci logiky pro Event Grid](./media/tutorial-facilities-events/logic-app-condition.png)

1. V okně **Pokud je true**:
    1. Klikněte na **Přidat akci** a vyberte *Office 365 Outlook*.

    1. V seznamu **Akce** vyberte **Odeslat e-mail**. Klikněte na **Přihlásit se** a použijte přihlašovací údaje ke svému e-mailovému účtu. Po zobrazení výzvy klikněte na **Povolit přístup**.

    1. Do pole **Příjemce** zadejte ID svého e-mailu, abyste dostávali oznámení. V okně **Parsovat JSON** do pole **Předmět** zadejte text *Oznámení služby Digital Twins na špatnou kvalitu vzduchu v prostoru* a pak v seznamu **Dynamický obsah** vyberte **ID objektu topologie**.

    1. Ve stejném okně do pole **Text** zadejte podobný text: *V místnosti se zjistila špatná kvalita vzduchu a je potřeba upravit teplotu*. Zprávu můžete rozvést použitím dalších elementů ze seznamu **Dynamický obsah**, jak je znázorněno níže.

    ![Odeslání e-mailu z aplikace logiky](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Klikněte na tlačítko **Uložit** v horní části podokna **Návrhář aplikace logiky**.

1. Nezapomeňte simulovat data ze snímačů tím, že v příkazovém okně přejdete do složky **_device-connectivity_** ukázky služby Digital Twin a spustíte příkaz `dotnet run`.

Během několika minut byste měli začít dostávat e-mailová oznámení z této aplikace logiky. 

   ![Odeslání e-mailu z aplikace logiky](./media/tutorial-facilities-events/logic-app-notification.png)

Pokud chcete zastavit příjem těchto e-mailů, přejděte ke své **aplikaci logiky** na portálu a vyberte podokno **Přehled**. Klikněte na **Zakázat**.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už službu Azure Digital Twins nechcete dál zkoumat, můžete prostředky vytvořené v rámci tohoto kurzu odstranit:

1. V levé nabídce na webu [Azure Portal](http://portal.azure.com) klikněte na **Všechny prostředky**, vyberte svou skupinu prostředků Digital Twins a **odstraňte** ji.
2. V případě potřeby můžete pokračovat odstraněním ukázkových aplikací i na pracovním počítači. 


## <a name="next-steps"></a>Další kroky

Můžete pokračovat k dalšímu kurzu, kde zjistíte, jak vizualizovat data ze snímačů, analyzovat trendy a detekovat anomálie. 
> [!div class="nextstepaction"]
> [Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights](tutorial-facilities-analyze.md)

Můžete si také přečíst další informace o grafech prostorové inteligence a objektových modelech ve službě Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Vysvětlení grafu prostorové inteligence a objektových modelů služby Digital Twins](concepts-objectmodel-spatialgraph.md)