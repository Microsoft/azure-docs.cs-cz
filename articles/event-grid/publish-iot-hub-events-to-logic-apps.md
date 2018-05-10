---
title: Používání událostí služby IoT Hub na aktivaci Azure Logic Apps | Microsoft Docs
description: Pomocí služby Směrování událostí Azure událostí mřížky, vytvořte automatizované procesy k provádění akcí Azure Logic Apps na základě událostí služby IoT Hub.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 4fed42a45f8d291bd3ba1e4fd5d636b7d0b0fbfc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Odesílání e-mailová oznámení o událostech Azure IoT Hub pomocí Logic Apps

Azure mřížky událostí umožňuje reagování na události v IoT Hub pomocí spuštění akce v podřízené obchodních aplikací.

Tento článek vás provede ukázka konfigurace, která používá IoT Hub a událostí mřížky. Na konci budete mít Azure logiku aplikace nastavit na odeslání e-mailové oznámení pokaždé, když přidání zařízení do služby IoT hub. 

## <a name="prerequisites"></a>Požadavky

* E-mailový účet z jakékoli e-mailu zprostředkovatele, který podporuje Azure Logic Apps, jako je Office 365 Outlook, Outlook.com nebo z Gmailu. Tato e-mailový účet se používá k odesílání oznámení události. Úplný seznam podporovaných konektory aplikace logiky, najdete v článku [konektory – přehled](https://docs.microsoft.com/connectors/)
* Aktivní účet Azure. Pokud nemáte, můžete [vytvořit bezplatný účet](http://azure.microsoft.com/pricing/free-trial/).
* Centrum Iot v Azure. Pokud ještě jste nevytvořili jednu, přečtěte si téma [Začínáme se službou IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md) návod. 

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Nejprve vytvořte aplikaci logiky a přidejte mřížce aktivační událost, která monitoruje skupinu prostředků pro virtuální počítač. 

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky


1. V [portál Azure](https://portal.azure.com), vyberte **nový** > **Enterprise integrace** > **aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Pojmenujte svou aplikaci logiky jedinečný v rámci vašeho předplatného a pak vyberte stejné předplatné, skupinu prostředků a umístění jako služby IoT hub. 
3. Až budete připraveni, vyberte **připnout na řídicí panel**a zvolte **vytvořit**.

   Právě jste vytvořili prostředek Azure pro vaši aplikaci logiky. Až Azure nasadí aplikaci logiky, v Návrháři pro Logic Apps se zobrazí šablony pro obvyklé scénáře, abyste mohli začít rychleji.

   > [!NOTE] 
   > Když vyberete **připnout na řídicí panel**, aplikace logiky automaticky otevře v návrháři aplikace logiky. Jinak můžete ručně najít a otevřít aplikaci logiky.

4. V Návrháři logiku aplikace pod **šablony**, zvolte **prázdné aplikace logiky** tak, že můžete vytvořit svou aplikaci logiky od začátku.

## <a name="select-a-trigger"></a>Vyberte aktivační událost

Aktivační událost je konkrétní události, která se spouští aplikace logiky. V tomto kurzu je aktivační událost, která nastaví vypnout pracovní postup přijme požadavek přes protokol HTTP.  

1. Konektory a aktivační události panelu Hledat, zadejte **HTTP**.
2. Vyberte **požadavku - obdrží žádost HTTP při** jako aktivační událost. 

   ![Vyberte aktivační událost požadavku HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Vyberte **datová část ukázky použít ke generování schématu**. 

   ![Vyberte aktivační událost požadavku HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Vložte následující ukázkový kód JSON do textového pole a pak vyberte **provádí**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<IoT hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
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
       "deviceId": "LogicAppTestDevice",
       "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
       "opType": "DeviceCreated"
     },
     "dataVersion": "",
     "metadataVersion": "1"
   }]
   ```
5. Můžete obdržet oznámení automaticky otevírané okno s informacemi o tom, **nezapomeňte zadat hlavičku Content-Type nastavena na application/json ve vaší žádosti.** Můžete bezpečně ignorovat tento návrh a přejde k další části. 


### <a name="create-an-action"></a>Vytvoření akce

Akce jsou všechny kroky, které se provádějí až po aktivační událost spustí pracovní postup aplikace logiky. V tomto kurzu je akce k odeslání e-mailová oznámení od poskytovatele e-mailu. 

1. Vyberte **nový krok** pak **přidat akci**. 

   ![Nový krok, přidat akci](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. Vyhledejte **e-mailu**. 
3. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Tento kurz používá **Office 365 Outlook**. Kroky pro ostatní poskytovatele e-mailu jsou podobné. 

   ![Vyberte konektor poskytovatele e-mailu](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Vyberte **e-mailovou zprávu** akce. 
5. Pokud budete vyzváni, přihlaste se k e-mailový účet. 
6. Vytvoření šablony e-mailu. 
   * **K**: Zadejte e-mailovou adresu dostávat e-maily s oznámeními. V tomto kurzu použijte e-mailový účet, který je k dispozici pro testování. 
   * **Předmět** a **textu**: zápis textu e-mailu. Vyberte vlastnosti JSON z nástroje pro výběr zahrnout dynamický obsah na základě dat událostí.  

   E-mailovou šablonu může vypadat podobně jako tento příklad:

   ![Vyplňte informace e-mailu](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Uložte svou aplikaci logiky. 

### <a name="copy-the-http-url"></a>Zkopírujte adresu URL protokolu HTTP

Před odchodem z návrháře aplikace logiky, zkopírujte adresu URL, kterou aplikace logiky naslouchá pro aktivační událost. Pomocí této adresy URL ke konfiguraci událostí mřížky. 

1. Rozbalte **obdrží žádost HTTP při** pole Konfigurace aktivační událost kliknutím na. 
2. Zkopírujte hodnotu **adresy URL protokolu HTTP POST** výběrem vedle sebe tlačítko Kopírovat. 

   ![Zkopírujte adresu URL protokolu HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Uložte tato adresa URL, takže je můžete na něj odkazovat v další části. 

## <a name="publish-an-event-from-iot-hub"></a>Publikování události ze služby IoT Hub

V této části můžete nakonfigurovat službu IoT Hub publikovat události, kdy k nim dojde. 

1. Na portálu Azure přejděte do služby IoT hub. 
2. Vyberte **události**.

   ![Otevřete tak podrobnosti události mřížky](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Vyberte **předplatného události**. 

   ![Vytvoření nového odběru událostí](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Vytvoření odběru událostí s následujícími hodnotami: 
   * **Název**: Zadejte popisný název.
   * **Přihlášení k odběru pro všechny typy událostí**: výběr zaškrtávací políčko.
   * **Typy událostí**: vyberte **DeviceCreated**.
   * **Typ odběratele**: vyberte **webové háku**.
   * **Koncový bod odběratele**: vložte adresu URL, kterou jste zkopírovali z aplikace logiky. 

   Můžete uložit událost odběru a přijímat oznámení pro každé zařízení, která je vytvořena ve službě IoT hub. V tomto kurzu ale můžeme použít nepovinná pole pro filtrování pro konkrétní zařízení: 

   * **Předpony filtru**: Zadejte `devices/Building1_` pro filtrování zařízení události v budova 1.
   * **Přípona filtru**: Zadejte `_Temperature` k filtrování pro zařízení události související s teploty.

   Když jste hotovi, formuláře by měl vypadat jako v následujícím příkladu: 

   ![Ukázka události předplatné formuláře](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Vyberte **vytvořit** se uložení odběru událostí.

## <a name="create-a-new-device"></a>Vytvoření nového zařízení

Testovat svou aplikaci logiky vytvořením nové zařízení pro aktivaci e-mailové oznámení události. 

1. Ze služby IoT hub, vyberte **zařízení IoT**. 
2. Vyberte **Přidat**.
3. Pro **ID zařízení**, zadejte `Building1_Floor1_Room1_Temperature`.
4. Vyberte **Uložit**. 
5. Můžete přidat více zařízení s ID k testování filtrů odběrů událostí jiné zařízení. Zkuste tyto příklady: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Po přidání několik zařízení do služby IoT hub, zkontrolujte e-mailu zobrazit ty, které aktivuje aplikaci logiky. 

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Místo použití portálu Azure, můžete provést kroky IoT Hub pomocí rozhraní příkazového řádku Azure. Podrobnosti najdete v tématu stránky Azure CLI pro [vytváření odběru událostí](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) a [vytvoření zařízení IoT se systémem](https://docs.microsoft.com/cli/azure/iot/device)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz používá prostředky, které platit poplatky na vaše předplatné Azure. Po dokončení tohoto kurzu vyzkoušení a testování výsledky, zakázat nebo odstranit prostředky, které chcete zachovat. 

Pokud nechcete ztratit práci na svou aplikaci logiky, zakažte ho místo odstranění. 

1. Přejděte do aplikace logiky.
2. Na **přehled** okně vyberte **odstranit** nebo **zakázat**. 

Každý odběr může mít jeden bezplatné Centrum IoT. Pokud jste vytvořili bezplatné Centrum pro účely tohoto kurzu, pak nemusíte odstranit, aby vám nezačaly nabíhat poplatky.

1. Přejděte do služby IoT hub. 
2. Na **přehled** okně vyberte **odstranit**. 

I když necháte služby IoT hub, můžete odstranit odběr události, kterou jste vytvořili. 

1. Ve službě IoT hub, vyberte **událostí mřížky**.
2. Vyberte předplatné událostí, které chcete odebrat. 
3. Vyberte **Odstranit**. 

## <a name="next-steps"></a>Další postup

Další informace o [reaktivní událostí služby IoT Hub pomocí mřížky události pro aktivační událost akce](../iot-hub/iot-hub-event-grid.md).

Další informace o tom, co else můžete provést s [událostí mřížky](overview.md).


