---
title: Tutorial - Use IoT Hub events to trigger Azure Logic Apps
description: Tutorial - Using the event routing service of Azure Event Grid, create automated processes to perform Azure Logic Apps actions based on IoT Hub events.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 70ad74715446a54605a23a049ebc92a81d7ee673
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423777"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Send email notifications about Azure IoT Hub events using Event Grid and Logic Apps

Azure Event Grid vám umožňuje reagovat na události ve službě IoT Hub aktivováním akcí v podnikových aplikacích ve směru server-klient.

This article walks through a sample configuration that uses IoT Hub and Event Grid. At the end, you have an Azure logic app set up to send a notification email every time a device is added to your IoT hub. 

## <a name="prerequisites"></a>Předpoklady

* E-mailový účet od jakéhokoliv poskytovatele e-mailu podporovaného v Azure Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Tento e-mailový účet se používá k posílání oznámení o událostech. Úplný seznam podporovaných konektorů aplikace logiky najdete v článku [Přehled konektorů](https://docs.microsoft.com/connectors/).
* Aktivní účet Azure. Pokud žádný nemáte, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* IoT Hub v Azure. Pokud jste si ještě žádné nevytvořili, přečtěte si téma [Začínáme se službou IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md), kde najdete návod. 

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

First, create a logic app and add an event grid trigger that monitors the resource group for your virtual machine. 

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. In the [Azure portal](https://portal.azure.com), select **Create a resource**, then type "logic app" in the search box and select return. Select **Logic App** from the results.

   ![Vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. On the next screen, select **Create**. 

1. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT. 

   ![Fields for create logic app](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Vyberte **Create** (Vytvořit).

1. Once the resource is created, navigate to your logic app. To do this, select **Resource groups**, then select the resource group you created for this tutorial. Then find the logic app in the list of resources and select it. 

1. In the Logic Apps Designer, page down to see **Templates**. Choose **Blank Logic App** so that you can build your logic app from scratch.

### <a name="select-a-trigger"></a>Výběr triggeru

Trigger je konkrétní událost, která spustí aplikaci logiky. V tomto kurzu trigger, který spustí pracovní postup, přijímá žádost přes protokol HTTP.  

1. Do panelu hledání pro konektory a triggery zadejte **HTTP**.

1. Vyberte jako trigger **Žádost – Při přijetí požadavku HTTP**. 

   ![Výběr triggeru požadavku HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. 

   ![Výběr triggeru požadavku HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Do textového pole vložte následující ukázkový kód JSON a potom vyberte **Hotovo**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Můžete se zobrazit automaticky otevírané okno s oznámením **Nezapomeňte do svého požadavku přidat hlavičku Content-Type nastavenou na application/json**. Tento návrh můžete v klidu ignorovat a přejít k další části. 

### <a name="create-an-action"></a>Vytvoření akce

Akce jsou všechny kroky, které se provádějí potom, co trigger spustí pracovní postup aplikace logiky. V tomto kurzu je akce poslání e-mailového oznámení z vašeho poskytovatele e-mailu. 

1. Vyberte **Nový krok**. This opens a window to **Choose an action**.

1. Vyhledejte **E-mail**.

1. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Tento kurz používá **Outlook z Office 365**. Kroky pro jiné poskytovatele e-mailu jsou podobné. 

   ![Výběr konektoru poskytovatele e-mailu](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Vyberte akci **Odeslat e-mail**. 

1. Pokud budete vyzváni, přihlaste se k e-mailovému účtu. 

1. Vytvořte šablonu e-mailu. 

   * **Komu**: Zadejte e-mailovou adresu, která bude dostávat e-maily s oznámeními. V tomto kurzu použijte e-mailový účet, který máte dostupný pro testování. 

   * **Subject**: Fill in the text for the subject. When you click on the Subject text box, you can select dynamic content to include. For example, this tutorial uses `IoT Hub alert: {event Type}`. If you can't see Dynamic content, select the **Add dynamic content** hyperlink -- this toggles it on and off.

   * **Body**: Write the text for your email. Vyberte vlastnosti JSON z nástroje pro výběr, aby se zahrnul dynamický obsah na základě dat událostí. If you can't see the Dynamic content, select the **Add dynamic content** hyperlink under the **Body** text box. If it doesn't show you the fields you want, click *more* in the Dynamic content screen to include the fields from the previous action.

   Vaše e-mailová šablona může vypadat podobně jako tento příklad:

   ![Vyplnění informací e-mailu](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Uložte svou aplikaci logiky. 

### <a name="copy-the-http-url"></a>Zkopírujte adresu URL protokolu HTTP

Než odejdete z návrháře aplikace logiky, zkopírujte adresu URL, kterou vaše aplikace logiky sleduje kvůli triggeru. Pomocí této adresy URL nakonfigurujete Event Grid. 

1. Kliknutím rozbalte konfigurační pole triggeru **Při přijetí požadavku HTTP**. 

1. Tlačítkem vedle hodnoty **Adresa URL operace HTTP POST** tuto hodnotu zkopírujte. 

   ![Zkopírování adresy URL operace HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Adresu URL si uložte, abyste na ni mohli odkazovat v další části. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurace odběru událostí služby IoT Hub

V této části nakonfigurujete v IoT Hubu publikování událostí, když k nim dojde. 

1. Na webu Azure Portal přejděte do svého centra IoT. You can do this by selecting **Resource groups**, then select the resource group for this tutorial, and then select your IoT hub from the list of resources.

2. Vyberte **Události**.

   ![Otevření podrobností Event Gridu](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Vyberte **Odběr události**. 

   ![Vytvoření nového odběru události](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Vytvořte odběr události s následujícími hodnotami: 

   * **Event Subscription Details**: Provide a descriptive name and select **Event Grid Schema**.

   * **Event Types**: In the **Filter to Event Types**, uncheck all of the choices except **Device Created**.

       ![subscription event types](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Endpoint Details**: Select Endpoint Type as **Web Hook** and select *select an endpoint* and paste the URL that you copied from your logic app and confirm selection.

     ![Výběr adresy URL koncového bodu](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   When you're done, the pane should look like the following example: 

    ![Ukázkový formulář odběru události](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Tady byste si mohli uložit odběr události a přijímat oznámení pro každé zařízení, které se vytvoří ve vašem centru IoT. For this tutorial, though, let's use the optional fields to filter for specific devices. Select **Filters** at the top of the pane.

6. Select **Add new filter**. Fill in the fields with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String begins with`.

   * **Value**:  Enter `devices/Building1_` to filter for device events in building 1.
  
   Add another filter with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String ends with`.

   * **Value**: Enter `_Temperature` to filter for device events related to temperature.

   The **Filters** tab of your event subscription should now look similar to this image:

   ![Adding filters to event subscription](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Výběrem možnosti **Vytvořit** uložte odběr události.

## <a name="create-a-new-device"></a>Vytvoření nového zařízení

Otestujte si aplikaci logiky vytvořením nového zařízení, aby se aktivoval e-mail s oznámením události. 

1. V centru IoT vyberte **Zařízení IoT**. 

2. Vyberte **Nový**.

3. Pro **ID zařízení** zadejte `Building1_Floor1_Room1_Light`.

4. Vyberte **Save** (Uložit). 

5. Můžete přidat více zařízení s různými ID a otestovat filtry odběrů událostí. Zkuste tyto příklady: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   If you added the four examples, your list of IoT devices should look like the following image:

   ![IoT Hub device list](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Po přidání několika zařízení do centra IoT se podívejte do e-mailu, která z nich aktivovala aplikaci logiky. 

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Místo použití webu Azure Portal můžete provést kroky služby IoT Hub pomocí rozhraní příkazového řádku Azure CLI. For details, see the Azure CLI pages for [creating an event subscription](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) and [creating an IoT device](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. When you're finished trying out the tutorial and testing your results, disable or delete resources that you don't want to keep. 

To delete all of the resources created in this tutorial, delete the resource group. 

1. Select **Resource groups**, then select the resource group you created for this tutorial.

2. On the Resource group pane, select **Delete resource group**. You are prompted to enter the resource group name, and then you can delete it. All of the resources contained therein are also removed.

If you don't want to remove all of the resources, you can manage them one by one. 

Pokud nechcete přijít o práci na aplikaci logiky, místo odstranění ji zakažte. 

1. Přejděte do aplikace logiky.

2. V okně **Přehled** vyberte **Odstranit** nebo **Zakázat**. 

Každý odběr může mít jedno bezplatné centrum IoT. Pokud jste vytvořili bezplatné centrum pro účely tohoto kurzu, tak ho nemusíte odstraňovat, aby se vám nic neúčtovalo.

1. Přejděte do svého centra IoT. 

2. V okně **Přehled** vyberte **Odstranit**. 

I když si centrum IoT necháte, bude vhodné odstranit odběr události, který jste vytvořili. 

1. V centru IoT vyberte **Mřížka událostí**.

2. Vyberte odběr události, který chcete odebrat. 

3. Vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky

* Další informace o [Reagování na události služby IoT Hub pomocí aktivace akcí mřížkou událostí](../iot-hub/iot-hub-event-grid.md)
* [Informace o uspořádání událostí připojení a odpojení zařízení](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Další informace o tom, co ještě můžete provést se službou [Event Grid](overview.md)