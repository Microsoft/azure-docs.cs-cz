---
title: Používání událostí služby IoT Hub k aktivaci Azure Logic Apps | Microsoft Docs
description: Pomocí služby směrování událostí služby Azure Event Grid vytvoříte automatizované procesy k provádění akcí Azure Logic Apps na základě událostí služby IoT Hub.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: kgremban
ms.openlocfilehash: 7c5030a80ead7e84526e01aa3a8a4a75ee2b276a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135011"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Kurz: Posílání e-mailových oznámení o událostech Azure IoT Hub pomocí Logic Apps

Azure Event Grid vám umožňuje reagovat na události ve službě IoT Hub aktivováním akcí v podnikových aplikacích ve směru server-klient.

Tento článek vás provede ukázkou konfigurace, která používá IoT Hub a Event Grid. Na konci budete mít logickou aplikaci Azure nastavenou na posílání e-mailového oznámení pokaždé, když se přidá zařízení do vašeho centra IoT (IoT Hub). 

## <a name="prerequisites"></a>Požadavky

* E-mailový účet od jakéhokoliv poskytovatele e-mailu podporovaného v Azure Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Tento e-mailový účet se používá k posílání oznámení o událostech. Úplný seznam podporovaných konektorů aplikace logiky najdete v článku [Přehled konektorů](https://docs.microsoft.com/connectors/).
* Aktivní účet Azure. Pokud žádný nemáte, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* IoT Hub v Azure. Pokud jste si ještě žádné nevytvořili, přečtěte si téma [Začínáme se službou IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md), kde najdete návod. 

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Napřed vytvořte aplikaci logiky a přidejte trigger služby Event Grid, který ve skupině prostředků monitoruje váš virtuální počítač. 

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. V [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek** > **integrace** > **aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT. 
3. Vyberte **Vytvořit**.

4. Jakmile je prostředek vytvořený, přejděte do aplikace logiky. 

5. Návrhář pro Logic Apps ukazuje šablon pro běžné vzory, můžete začít rychleji. V návrháři aplikace logiky v části **Šablony** zvolte **Prázdná aplikace logiky**, abyste mohli sestavit zcela novou aplikaci logiky.

### <a name="select-a-trigger"></a>Výběr triggeru

Trigger je konkrétní událost, která spustí aplikaci logiky. V tomto kurzu trigger, který spustí pracovní postup, přijímá žádost přes protokol HTTP.  

1. Do panelu hledání pro konektory a triggery zadejte **HTTP**.
2. Vyberte jako trigger **Žádost – Při přijetí požadavku HTTP**. 

   ![Výběr triggeru požadavku HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. 

   ![Výběr triggeru požadavku HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Do textového pole vložte následující ukázkový kód JSON a potom vyberte **Hotovo**:

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

5. Můžete se zobrazit automaticky otevírané okno s oznámením **Nezapomeňte do svého požadavku přidat hlavičku Content-Type nastavenou na application/json**. Tento návrh můžete v klidu ignorovat a přejít k další části. 

### <a name="create-an-action"></a>Vytvoření akce

Akce jsou všechny kroky, které se provádějí potom, co trigger spustí pracovní postup aplikace logiky. V tomto kurzu je akce poslání e-mailového oznámení z vašeho poskytovatele e-mailu. 

1. Vyberte **Nový krok**. Tím se otevře okno **Vybrat akci**.

2. Vyhledejte **E-mail**.

3. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Tento kurz používá **Outlook z Office 365**. Kroky pro jiné poskytovatele e-mailu jsou podobné. 

   ![Výběr konektoru poskytovatele e-mailu](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Vyberte akci **Odeslat e-mail**. 

5. Pokud budete vyzváni, přihlaste se k e-mailovému účtu. 

6. Vytvořte šablonu e-mailu. 
   * **K**: Zadejte e-mailovou adresu pro příjem oznámení e-mailů. V tomto kurzu použijte e-mailový účet, který máte dostupný pro testování. 
   * **Předmět** a **tělo**: Zápis textu e-mailu. Vyberte vlastnosti JSON z nástroje pro výběr, aby se zahrnul dynamický obsah na základě dat událostí.  

   Vaše e-mailová šablona může vypadat podobně jako tento příklad:

   ![Vyplnění informací e-mailu](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Uložte svou aplikaci logiky. 

### <a name="copy-the-http-url"></a>Zkopírujte adresu URL protokolu HTTP

Než odejdete z návrháře aplikace logiky, zkopírujte adresu URL, kterou vaše aplikace logiky sleduje kvůli triggeru. Pomocí této adresy URL nakonfigurujete Event Grid. 

1. Kliknutím rozbalte konfigurační pole triggeru **Při přijetí požadavku HTTP**. 
2. Tlačítkem vedle hodnoty **Adresa URL operace HTTP POST** tuto hodnotu zkopírujte. 

   ![Zkopírování adresy URL operace HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Adresu URL si uložte, abyste na ni mohli odkazovat v další části. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurace odběru událostí služby IoT Hub

V této části nakonfigurujete v IoT Hubu publikování událostí, když k nim dojde. 

1. Na webu Azure Portal přejděte do svého centra IoT. 
2. Vyberte **Události**.

   ![Otevření podrobností Event Gridu](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Vyberte **Odběr události**. 

   ![Vytvoření nového odběru události](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Vytvořte odběr události s následujícími hodnotami: 
    * **Typ události**: Zrušte zaškrtnutí políčka přihlásit k odběru pro všechny typy událostí a vyberte **zařízení vytvořit** z nabídky.
    * **Podrobnosti o koncovém bodu**: Vyberte typ koncového bodu jako **Webhook** a klikněte na Vybrat koncový bod a vložte adresu URL, kterou jste zkopírovali z aplikace logiky a potvrďte výběr.

    ![Výběr adresy URL koncového bodu](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

    * **Podrobnosti o předplatném události**: Zadejte popisný název a vyberte **schématu Event Grid**

  Až to budete mít hotové, měl by formulář vypadat asi jako v následujícím příkladu: 

    ![Ukázkový formulář odběru události](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Tady byste si mohli uložit odběr události a přijímat oznámení pro každé zařízení, které se vytvoří ve vašem centru IoT. V tomto kurzu ale použijeme volitelná pole k filtrování pro konkrétní zařízení. Vyberte **další funkce** v horní části formuláře. 

6. Vytvořte následující filtry:

  * **Předmět začíná**: Zadejte `devices/Building1_` Chcete-li filtrovat události zařízení v budově 1.
  * **Předmět končí**: Zadejte `_Temperature` pro filtrování zařízení události související s teploty.

5. Výběrem možnosti **Vytvořit** uložte odběr události.

## <a name="create-a-new-device"></a>Vytvoření nového zařízení

Otestujte si aplikaci logiky vytvořením nového zařízení, aby se aktivoval e-mail s oznámením události. 

1. V centru IoT vyberte **Zařízení IoT**. 
2. Vyberte **Přidat**.
3. Pro **ID zařízení** zadejte `Building1_Floor1_Room1_Temperature`.
4. Vyberte **Uložit**. 
5. Můžete přidat více zařízení s různými ID a otestovat filtry odběrů událostí. Zkuste tyto příklady: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Po přidání několika zařízení do centra IoT se podívejte do e-mailu, která z nich aktivovala aplikaci logiky. 

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Místo použití webu Azure Portal můžete provést kroky služby IoT Hub pomocí rozhraní příkazového řádku Azure CLI. Podrobnosti najdete na stránkách Azure CLI pro [vytváření odběru událostí](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) a [vytváření zařízení IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. Po dokončení tohoto zkušebního kurzu a otestování výsledků zakažte nebo odstraňte prostředky, které si nechcete nechat. 

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

## <a name="next-steps"></a>Další postup

* Další informace o [Reagování na události služby IoT Hub pomocí aktivace akcí mřížkou událostí](../iot-hub/iot-hub-event-grid.md)
* [Informace o uspořádání událostí připojení a odpojení zařízení](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Další informace o tom, co ještě můžete provést se službou [Event Grid](overview.md)


