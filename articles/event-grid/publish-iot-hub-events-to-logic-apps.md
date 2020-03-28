---
title: Kurz – použití událostí služby IoT Hub k aktivaci aplikací Azure Logic Apps
description: Tento kurz ukazuje, jak používat službu směrování událostí služby Azure Event Grid, vytvářet automatizované procesy k provádění akcí Azure Logic Apps na základě událostí služby IoT Hub.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706441"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Kurz: Odesílání e-mailových oznámení o událostech služby Azure IoT Hub pomocí služby Event Grid a logic Apps

Azure Event Grid vám umožňuje reagovat na události ve službě IoT Hub aktivováním akcí v podnikových aplikacích ve směru server-klient.

Tento článek vás provede ukázkovou konfigurací, která používá služby IoT Hub a Event Grid. Na konci máte aplikaci logiky Azure nastavenou pro odesílání e-mailu s oznámením pokaždé, když se zařízení přidá do vašeho centra IoT Hub. 

## <a name="prerequisites"></a>Požadavky

* E-mailový účet od jakéhokoliv poskytovatele e-mailu podporovaného v Azure Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Tento e-mailový účet se používá k posílání oznámení o událostech. Úplný seznam podporovaných konektorů aplikace logiky najdete v článku [Přehled konektorů](https://docs.microsoft.com/connectors/).
* Aktivní účet Azure. Pokud ho nemáte, můžete [si vytvořit bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* IoT Hub v Azure. Pokud jste si ještě žádné nevytvořili, přečtěte si téma [Začínáme se službou IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md), kde najdete návod. 

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Nejprve vytvořte aplikaci logiky a přidejte aktivační událost mřížky událostí, která monitoruje skupinu prostředků pro váš virtuální počítač. 

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit prostředek**, zadejte do vyhledávacího pole "aplikace logiky" a vyberte return. Z výsledků vyberte **aplikaci logiky.**

   ![Vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na další obrazovce vyberte **Vytvořit**. 

1. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT. 

   ![Pole pro vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Vyberte **Vytvořit**.

1. Po vytvoření prostředku přejděte do aplikace logiky. Chcete-li to provést, vyberte **skupiny prostředků**a vyberte skupinu prostředků, kterou jste vytvořili pro tento kurz. Pak najděte aplikaci logiky v seznamu prostředků a vyberte ji. 

1. V Návrháři aplikací **logiky,** stránku dolů zobrazíte šablony . Zvolte **Prázdná aplikace logiky,** abyste mohli vytvořit aplikaci logiky od začátku.

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

1. Vyberte **Nový krok**. Otevře se okno pro **volbu Zvolte akci**.

1. Vyhledejte **E-mail**.

1. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. Tento kurz používá **Office 365 Outlook**. Kroky pro jiné poskytovatele e-mailu jsou podobné. 

   ![Výběr konektoru poskytovatele e-mailu](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Vyberte akci **Odeslat e-mail**. 

1. Pokud budete vyzváni, přihlaste se k e-mailovému účtu. 

1. Vytvořte šablonu e-mailu. 

   * **Komu**: Zadejte e-mailovou adresu, která bude dostávat e-maily s oznámeními. V tomto kurzu použijte e-mailový účet, který máte dostupný pro testování. 

   * **Předmět**: Vyplňte text předmětu. Když kliknete na textové pole Předmět, můžete vybrat dynamický obsah, který chcete zahrnout. Například tento kurz `IoT Hub alert: {event Type}`používá . Pokud dynamický obsah nevidíte, vyberte hypertextový odkaz **Přidat dynamický obsah** – tím se zapíná a vypíná.

   * **Text**: Napište text pro váš e-mail. Vyberte vlastnosti JSON z nástroje pro výběr, aby se zahrnul dynamický obsah na základě dat událostí. Pokud dynamický obsah nevidíte, vyberte pod textovým polem **Text** přidat hypertextový odkaz **Přidat dynamický obsah.** Pokud se nezobrazují požadovaná pole, klikněte na obrazovce Dynamický obsah na *další,* abyste pole z předchozí akce zahrnuli.

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

1. Na webu Azure Portal přejděte do svého centra IoT. Můžete to provést výběrem **skupin prostředků**, vyberte skupinu prostředků pro tento kurz a pak vyberte centrum IoT hub ze seznamu prostředků.

2. Vyberte **události**.

   ![Otevření podrobností Event Gridu](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Vyberte **odběr událostí**. 

   ![Vytvoření nového odběru události](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Vytvořte odběr události s následujícími hodnotami: 

   * **Podrobnosti o odběru událostí**: Zadejte popisný název a vyberte **schéma mřížky událostí**.

   * **Typy událostí**: Ve **filtru na typy událostí**odškrtnete všechny volby kromě **zařízení, které bylo vytvořeno**.

       ![typy událostí předplatného](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Podrobnosti o koncovém bodu**: Vyberte Typ koncového bodu jako **webový hák** a *vyberte koncový bod* a vložte adresu URL, kterou jste zkopírovali z aplikace logiky, a potvrďte výběr.

     ![Výběr adresy URL koncového bodu](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Po dokončení by mělo podokno vypadat jako v následujícím příkladu: 

    ![Ukázkový formulář odběru události](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Tady byste si mohli uložit odběr události a přijímat oznámení pro každé zařízení, které se vytvoří ve vašem centru IoT. Pro účely tohoto kurzu však použijeme volitelná pole k filtrování pro konkrétní zařízení. V horní části podokna vyberte **Filtry.**

6. Vyberte **Přidat nový filtr**. Vyplňte pole těmito hodnotami:

   * **Klíč**: `Subject`Vyberte .

   * **Operátor**: `String begins with`Vyberte .

   * **Hodnota**: `devices/Building1_` Zadáním filtrujete události zařízení v budově 1.
  
   Přidejte další filtr s těmito hodnotami:

   * **Klíč**: `Subject`Vyberte .

   * **Operátor**: `String ends with`Vyberte .

   * **Hodnota**: `_Temperature` Zadáním filtrujete události zařízení související s teplotou.

   Karta **Filtry** předplatného událostí by teď měla vypadat podobně jako na tomto obrázku:

   ![Přidání filtrů do odběru událostí](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Výběrem možnosti **Vytvořit** uložte odběr události.

## <a name="create-a-new-device"></a>Vytvoření nového zařízení

Otestujte si aplikaci logiky vytvořením nového zařízení, aby se aktivoval e-mail s oznámením události. 

1. V centru IoT vyberte **Zařízení IoT**. 

2. Vyberte **nový**.

3. Pro **ID zařízení** zadejte `Building1_Floor1_Room1_Light`.

4. Vyberte **Uložit**. 

5. Můžete přidat více zařízení s různými ID a otestovat filtry odběrů událostí. Zkuste tyto příklady: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Pokud jste přidali čtyři příklady, měl by seznam zařízení IoT vypadat jako na následujícím obrázku:

   ![Seznam zařízení centra IoT Hub](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Po přidání několika zařízení do centra IoT se podívejte do e-mailu, která z nich aktivovala aplikaci logiky. 

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Místo použití webu Azure Portal můžete provést kroky služby IoT Hub pomocí rozhraní příkazového řádku Azure CLI. Podrobnosti najdete na stránkách Azure CLI pro [vytvoření předplatného událostí](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) a vytvoření zařízení [IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. Po dokončení vyzkoušení kurzu a testování výsledků zakažte nebo odstraňte prostředky, které nechcete zachovat. 

Chcete-li odstranit všechny prostředky vytvořené v tomto kurzu, odstraňte skupinu prostředků. 

1. Vyberte **skupiny prostředků**a vyberte skupinu prostředků, kterou jste pro tento kurz vytvořili.

2. V podokně Skupina prostředků vyberte **Odstranit skupinu prostředků**. Budete vyzváni k zadání názvu skupiny prostředků a potom jej můžete odstranit. Všechny zdroje v něm obsažené jsou také odstraněny.

Pokud nechcete odebrat všechny prostředky, můžete je spravovat jeden po druhém. 

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