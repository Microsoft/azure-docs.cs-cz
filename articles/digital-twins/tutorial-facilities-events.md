---
title: Zachycení událostí z prostorem digitální dvojče Azure | Dokumentace Microsoftu
description: Zjistěte, jak pomocí kroků v tomto kurzu dostávat oznámení z prostorů díky integraci služby Azure Digital Twins s Logic Apps.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 994c864f04980dc963f9e7c4eb99b33382ad17e4
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614901"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Kurz: Dostávat oznámení od prostory vaší digitální dvojče Azure s využitím Logic Apps

Po zřízení prostory vaší nasazení vaší instance Azure digitální dvojče a implementujte vlastní funkce pro monitorování určitých podmínek, můžete upozornit správce office e-mailem při výskytu monitorovaných podmínky. 

V [první kurz](tutorial-facilities-setup.md), jste nakonfigurovali prostorový graf imaginární sestavování. Pokoje v budově obsahuje pohybu, oxidu uhličitého a teploty senzorů. V [druhé části kurzu](tutorial-facilities-udf.md), jste zřídili grafu a uživatelem definované funkce k monitorování těchto hodnot snímačů, a aktivovat oznámení při prostor je prázdný a teploty a oxidu uhličitého jsou v komfortní rozsah. 

V tomto kurzu se dozvíte, jak tato oznámení integrovat s Azure Logic Apps a odesílat e-maily, když bude taková místnost dostupná. Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům s rezervací zasedacích místností nejvíce podporujících produktivitu. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Integrace událostí pomocí služby Azure Event Grid.
> * Oznámení události s Logic Apps.
    
## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [nakonfigurovali](tutorial-facilities-setup.md) a [zřídili](tutorial-facilities-udf.md) vlastní systém Azure Digital Twins. Než budete pokračovat, ujistěte se, že máte následující:
- [Účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Spuštěná instance služby Digital Twins.
- Pracovní počítač se staženými a extrahovanými [ukázkami služby Digital Twins v jazyce C#](https://github.com/Azure-Samples/digital-twins-samples-csharp).
- [Sada .NET core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) na vývojovém počítači ke spuštění ukázky. Spustit `dotnet --version` ověření nainstalovanou správnou verzi. 
- Účet Office 365 pro odesílání e-mailových oznámení.

## <a name="integrate-events-with-event-grid"></a>Integrace událostí se službou Event Grid 
V této části nastavíte [služby Event Grid](../event-grid/overview.md) shromažďovat události z vaší instanci Azure digitální dvojče a přesměrovat je na [obslužná rutina události](../event-grid/event-handlers.md) například Logic Apps.

### <a name="create-an-event-grid-topic"></a>Vytvořit téma event gridu
[Téma event gridu](../event-grid/concepts.md#topics) poskytuje rozhraní pro směrování události generované modulem uživatelem definované funkce. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **vytvořit prostředek**. 

1. Vyhledejte a vyberte **Téma Event Gridu**. Vyberte **Vytvořit**.

1. Zadejte **Název** tématu Event Gridu a zvolte **Předplatné**. Vyberte **skupiny prostředků** , který můžete použít nebo vytvořit digitální dvojče instance a **umístění**. Vyberte **Vytvořit**. 

    ![Vytvořit téma event gridu](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Přejděte na téma event gridu od vaší skupiny prostředků, vyberte **přehled**a zkopírujte hodnotu **koncový bod tématu** do dočasného souboru. Bude nutné tuto adresu URL v další části. 

1. Vyberte **přístupové klíče**a zkopírujte **klíč 1** a **klíče 2** do dočasného souboru. Bude nutné tyto hodnoty a vytvořte koncový bod v další části.

    ![Event Grid klíče](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Vytvoření koncového bodu pro téma event gridu

1. V příkazovém okně, ujistěte se, že jste v **obsazení quickstart\src** složky digitální dvojče vzorku.

1. Otevřete soubor **actions\createEndpoints.yaml** v editoru Visual Studio Code. Ujistěte se, že má následující obsah:

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

1. Zástupný text `Event_Grid_Topic_Path` nahraďte cestou k tématu Event Gridu. Získat tuto cestu tak, že odeberete **https://** koncové cesty prostředku z a **koncový bod tématu** adresy URL. Cesta by měla mít přibližně tento formát: *yourEventGridName.yourLocation.eventgrid.azure.net*. 

    > [!IMPORTANT]
    > Všechny hodnoty zadávejte bez uvozovek. Ujistěte se, že existuje alespoň jeden znak po dvojtečky v souboru YAML. Můžete také ověřit váš obsah souboru YAML pomocí jakékoli online validátoru YAML [tento nástroj](https://onlineyamltools.com/validate-yaml).

1. Uložte soubor a zavřete ho. V příkazovém okně spusťte následující příkaz a po zobrazení výzvy se přihlaste. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Tento příkaz vytvoří koncový bod pro Event Grid. 

   ![Koncové body pro službu Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-apps"></a>Oznámení události s Logic Apps
Můžete použít [Azure Logic Apps](../logic-apps/logic-apps-overview.md) služby s cílem vytvořit automatizované úlohy pro událostí přijatých z jiné služby. V této části nastavíte Logic Apps vytvořit e-mailová oznámení pro události z prostorové senzorů, díky směrovat [téma event gridu](../event-grid/overview.md).

1. V levém podokně [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek**.

1. Vyhledejte a vyberte nový prostředek **Aplikace logiky**. Vyberte **Vytvořit**.

1. Zadejte **název** pro prostředek aplikace logiky a pak vyberte vaši **předplatné**, **skupiny prostředků**, a **umístění**. Vyberte **Vytvořit**.

    ![Vytvořte prostředek aplikace logiky](./media/tutorial-facilities-events/create-logic-app.png)

1. Otevřete váš prostředek služby Logic Apps při nasazení a pak otevřete **návrhář aplikace logiky** podokně. 

1. Vyberte trigger **Když dojde k události Event Gridu**. Přihlaste se k vašemu tenantovi pomocí svého účtu Azure, po zobrazení výzvy. Vyberte **povolit přístup** pro prostředek služby Event Grid po zobrazení výzvy. Vyberte **Pokračovat**.

1. V **při výskytu události prostředku (Preview)** okno: 
   
   a. Vyberte **předplatné** , který jste použili k vytvoření tématu event gridu.

   b. Vyberte **Microsoft.EventGrid.Topics** pro **typ prostředku**.

   c. Vyberte z rozevíracího seznamu pro prostředek služby Event Grid **název prostředku**.

   ![Podokno návrháře aplikace logiky](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Vyberte **nový krok** tlačítko.

1. V **zvolte akci** okno:
    
   a. Vyhledejte frázi **parsovat JSON** a vyberte akci **Parsovat JSON**.

   b. V **obsah** pole, vyberte **tělo** z **dynamický obsah** seznamu.

   c. Vyberte **ukázkový používání datovou část k vygenerování schématu**. Vložte následující datovou část JSON a potom vyberte **provádí**.

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
    
    ![Okno Parsovat JSON aplikace logiky pro Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Vyberte **nový krok** tlačítko.

1. V **zvolte akci** okno:

   a. Vyhledejte a vyberte **podmínky řízení** z **akce** seznamu. 

   b. V prvním **zvolit hodnotu** textového pole, vyberte **eventType** z **dynamický obsah** seznamu pro **Parsovat JSON** okna.

   c. Ve druhém **zvolit hodnotu** textové pole, zadejte **UdfCustom**.

   ![Vybrané podmínky](./media/tutorial-facilities-events/logic-app-condition.png)

1. V **při hodnotě true** okno:
   
   a. Vyberte **přidat akci**a vyberte **Office 365 Outlook**.

   b. Z **akce** seznamu vyberte **odeslat e-mailu**. Vyberte **přihlášení** a použijte svoje přihlašovací údaje účtu e-mailu. Vyberte **povolit přístup** po zobrazení výzvy.

   c. Do pole **Příjemce** zadejte ID svého e-mailu, abyste dostávali oznámení. V **subjektu**, zadejte text **digitální dvojče oznámení nízký air kvality v prostoru**. Potom vyberte **TopologyObjectId** z **dynamický obsah** seznamu pro **Parsovat JSON**.

   d. V části **tělo** ve stejném okně zadejte text podobný tomuto: **air špatná kvalita zjistil v místnosti, a teploty se musí upravit**. Nebojte se pracovalo za použití prvků z **dynamický obsah** seznamu.

   ![Výběr "Odeslat e-mail" aplikace logiky](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Vyberte **Uložit** tlačítko v horní části **návrhář aplikace logiky** podokně.

1. Ujistěte se, že simulace tak, že přejdete na data ze senzorů **připojení zařízení** složky digitální dvojče ukázku v příkazové okno a spuštění `dotnet run`.

Za pár minut měli byste začít získání e-mailová oznámení od tohoto prostředku aplikace logiky. 

   ![E-mailové oznámení](./media/tutorial-facilities-events/logic-app-notification.png)

Pokud chcete zastavit příjem těchto e-mailů, přejděte na váš prostředek služby Logic Apps na portálu a vyberte **přehled** podokně. Vyberte **zakázat**.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zastavit v tuto chvíli seznámení digitální dvojče Azure, bez obav odstraňte prostředky vytvořené v tomto kurzu:

1. V levé nabídce v [webu Azure portal](http://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků digitální dvojče a vyberte **odstranit**.
2. V případě potřeby odstraňte ukázkové aplikace na svém počítači práce. 


## <a name="next-steps"></a>Další postup

Informace o vizualizaci dat snímačů, analýze trendů a detekovat anomálie, přejděte k dalšímu kurzu: 
> [!div class="nextstepaction"]
> [Kurz: Vizualizace a analýza událostí z prostorů Azure Digital Twins s využitím služby Time Series Insights](tutorial-facilities-analyze.md)

Můžete také další informace o Prostorové řady grafů a objektové modely v digitální dvojče Azure: 
> [!div class="nextstepaction"]
> [Vysvětlení grafu prostorové inteligence a objektových modelů služby Digital Twins](concepts-objectmodel-spatialgraph.md)
