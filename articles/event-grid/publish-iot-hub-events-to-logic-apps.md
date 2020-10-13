---
title: Kurz – použití událostí IoT Hub k aktivaci Azure Logic Apps
description: V tomto kurzu se dozvíte, jak používat službu Směrování událostí Azure Event Grid, vytvářet automatizované procesy pro provádění Azure Logic Apps akcí na základě IoT Hubch událostí.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5092aa0b5b23f04af1f49933bca234815f03f454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604536"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Kurz: Posílání e-mailových oznámení o událostech služby Azure IoT Hub s využitím služeb Event Grid a Logic Apps

Azure Event Grid vám umožňuje reagovat na události ve službě IoT Hub aktivováním akcí v podnikových aplikacích ve směru server-klient.

Tento článek vás provede ukázkovou konfigurací, která používá IoT Hub a Event Grid. Na konci máte aplikaci logiky Azure nastavenou k odeslání e-mailového oznámení pokaždé, když se zařízení připojí nebo odpojí ke službě IoT Hub. Event Grid můžete použít k získání včasného oznámení o odpojení důležitých zařízení. Metriky a diagnostika mohou trvat několik (tj. 20 nebo více), ale minuty se zobrazí v protokolech a výstrahách. To může být nepřijatelné pro kritickou infrastrukturu.

## <a name="prerequisites"></a>Předpoklady

* Musíte mít aktivní předplatné Azure. Pokud předplatné nemáte, můžete si [vytvořit bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/).

* E-mailový účet od jakéhokoli poskytovatele e-mailu, který podporuje Azure Logic Apps, jako je například Office 365 Outlook nebo Outlook.com. Tento e-mailový účet se používá k posílání oznámení o událostech. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

Nové centrum IoT můžete rychle vytvořit pomocí Azure Cloud Shell terminálu na portálu.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 

1. V pravém horním rohu stránky vyberte tlačítko Cloud Shell.

   ![Tlačítko Cloud Shell](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Spuštěním následujícího příkazu vytvoříte novou skupinu prostředků:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Spuštěním následujícího příkazu vytvořte centrum IoT:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Minimalizujte Cloud Shell terminálu. Později v tomto kurzu se vrátíte do prostředí.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Dále vytvořte aplikaci logiky a přidejte Trigger služby Event Grid, který zpracovává požadavky ze služby IoT Hub. 

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**, do vyhledávacího pole zadejte "Logic App" a vyberte Return (vrátit). Z výsledků vyberte **Aplikace logiky** .

   ![Vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na další obrazovce vyberte **vytvořit**. 

1. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT. 

   ![Pole pro vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Vyberte **Zkontrolovat a vytvořit**.

1. Ověřte nastavení a pak vyberte **vytvořit**.

1. Po vytvoření prostředku vyberte **Přejít k prostředku**. 

1. V Návrháři Logic Apps stránku dolů a zobrazte **šablony**. Vyberte **prázdná aplikace logiky** , abyste mohli vytvořit aplikaci logiky od začátku.

### <a name="select-a-trigger"></a>Výběr aktivační události

Trigger je konkrétní událost, která spustí aplikaci logiky. V tomto kurzu trigger, který spustí pracovní postup, přijímá žádost přes protokol HTTP.  

1. Do panelu hledání pro konektory a triggery zadejte **HTTP**.

1. Procházejte výsledky a vyberte **požadavek – když se jako aktivační událost přijme požadavek HTTP** . 

   ![Výběr triggeru požadavku HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. 

   ![Použít ukázkovou datovou část](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Do textového pole vložte JSON *schématu události připojené zařízení* a potom vyberte **Hotovo**:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Tato událost se publikuje, když je zařízení připojené ke IoT Hub.

> [!NOTE]
> Můžete se zobrazit automaticky otevírané okno s oznámením **Nezapomeňte do svého požadavku přidat hlavičku Content-Type nastavenou na application/json**. Tento návrh můžete v klidu ignorovat a přejít k další části. 

### <a name="create-an-action"></a>Vytvoření akce

Akce jsou všechny kroky, které se provádějí potom, co trigger spustí pracovní postup aplikace logiky. V tomto kurzu je akce poslání e-mailového oznámení z vašeho poskytovatele e-mailu. 

1. Vyberte **Nový krok**. Tím se otevře okno pro **Výběr akce**.

1. Vyhledejte **Outlook**.

1. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. V tomto kurzu se používá **Outlook.com**. Kroky pro jiné poskytovatele e-mailu jsou podobné. 

   ![Výběr konektoru poskytovatele e-mailu](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Vyberte akci **Odeslat e-mail (v2)** . 

1. Vyberte **Přihlásit** se a přihlaste se ke svému e-mailovému účtu. Vyberte **Ano** , pokud chcete aplikaci povolit přístup k vašim informacím.

1. Vytvořte e-mailovou šablonu. 

   * **Komu**: Zadejte e-mailovou adresu pro příjem e-mailů s oznámením. Pro účely tohoto kurzu použijte e-mailový účet, ke kterému máte při testování přístup. 

   * **Předmět**: Vyplňte text předmětu. Po kliknutí na textové pole Předmět můžete vybrat dynamický obsah, který chcete zahrnout. Tento kurz například používá `IoT Hub alert: {eventType}` . Pokud nevidíte dynamický obsah, vyberte hypertextový odkaz **Přidat dynamický obsah** – tím ho přepínáte a vypnuli.

   * **Text**: Napište text e-mailu. V nástroji pro výběr vyberte vlastnosti JSON, které budou zahrnovat dynamický obsah na základě dat události. Pokud nemůžete zobrazit dynamický obsah, v textovém poli text **zprávy** vyberte hypertextový odkaz **Přidat dynamický obsah** . Pokud se vám nezobrazují požadovaná pole, klikněte na tlačítko *Další* na obrazovce dynamický obsah a přidejte pole z předchozí akce.

   Vaše e-mailová šablona může vypadat podobně jako tento příklad:

   ![Vyplnění informací e-mailu](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. V Návrháři Logic Apps vyberte **Uložit** .  

### <a name="copy-the-http-url"></a>Zkopírujte adresu URL protokolu HTTP

Než odejdete z návrháře aplikace logiky, zkopírujte adresu URL, kterou vaše aplikace logiky sleduje kvůli triggeru. Pomocí této adresy URL nakonfigurujete Event Grid. 

1. Kliknutím rozbalte konfigurační pole triggeru **Při přijetí požadavku HTTP**. 

1. Tlačítkem vedle hodnoty **Adresa URL operace HTTP POST** tuto hodnotu zkopírujte. 

   ![Zkopírování adresy URL operace HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Adresu URL si uložte, abyste na ni mohli odkazovat v další části. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurace odběru událostí služby IoT Hub

V této části nakonfigurujete v IoT Hubu publikování událostí, když k nim dojde. 

1. Na webu Azure Portal přejděte do svého centra IoT. Můžete to udělat tak, že vyberete **skupiny prostředků**, pak vyberete skupinu prostředků tohoto kurzu a pak ze seznamu prostředků vyberete Centrum IoT.

1. Vyberte **události**.

   ![Otevření podrobností Event Gridu](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Vyberte **odběr události**. 

   ![Vytvoření nového odběru události](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Vytvořte odběr události s následujícími hodnotami: 

   1. V části **Podrobnosti odběru události** :
      1. Zadejte **název** odběru události. 
      2. Vyberte **Event Grid schéma** pro **schéma událostí**. 
   2. V části **Podrobnosti o tématu** :
      1. Potvrďte, že je **typ tématu** nastaven na **IoT Hub**. 
      2. Potvrďte, že název služby IoT Hub je nastaven jako hodnota pro pole **zdrojového prostředku** . 
      3. Zadejte název **systémového tématu** , které se vytvoří za vás. Další informace o systémových tématech najdete v tématu [Přehled systémových témat](system-topics.md).
   3. V části **typy událostí** :
      1. Vyberte rozevírací seznam **filtrovat typy událostí** .
      1. Zrušte zaškrtnutí políček **vytvořeno zařízení** a **odstraněné zařízení** **a nechte** zaškrtnuté políčko **Odpojeno zařízení a odpojit zařízení** .

         ![vybrat typy událostí předplatného](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. V části **Podrobnosti o koncovém bodu** : 
       1. Jako **webový Hook**vyberte **Typ koncového bodu** .
       2. Klikněte na **Vybrat koncový bod**, vložte adresu URL, kterou jste zkopírovali z aplikace logiky, a potvrďte výběr.

         ![Výběr adresy URL koncového bodu](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Po dokončení by mělo příslušné podokno vypadat podobně jako v následujícím příkladu: 

         ![Ukázkový formulář odběru události](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Vyberte **Vytvořit**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Simulace nového zařízení připojujícího a odesílaného telemetrie

Otestujte aplikaci logiky díky rychlému simulaci připojení zařízení pomocí Azure CLI. 

1. Kliknutím na tlačítko Cloud Shell znovu otevřete terminál.

1. Spuštěním následujícího příkazu vytvořte identitu simulovaného zařízení:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Spusťte následující příkaz, který simuluje připojení zařízení k IoT Hub a odesílání telemetrie:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Po připojení simulovaného zařízení k IoT Hub obdržíte e-mail s upozorněním na události "DeviceConnected".

1. Po dokončení simulace obdržíte e-mail s upozorněním na události "DeviceDisconnected". 

    ![Příklad e-mailové zprávy](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. Až skončíte s vyzkoušením kurzu a otestujete výsledky, zakažte nebo odstraňte prostředky, které nechcete zachovat. 

Pokud chcete odstranit všechny prostředky vytvořené v tomto kurzu, odstraňte skupinu prostředků. 

1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste pro tento kurz vytvořili.

2. V podokně skupina prostředků vyberte **Odstranit skupinu prostředků**. Zobrazí se výzva k zadání názvu skupiny prostředků a pak ho můžete odstranit. Odstraní se také všechny prostředky, které jsou v něm obsažené.

## <a name="next-steps"></a>Další kroky

* Další informace o [Reagování na události služby IoT Hub pomocí aktivace akcí mřížkou událostí](../iot-hub/iot-hub-event-grid.md)
* [Informace o uspořádání událostí připojení a odpojení zařízení](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Další informace o tom, co ještě můžete provést se službou [Event Grid](overview.md)

Úplný seznam podporovaných konektorů aplikace logiky najdete v tématu [Přehled konektorů](/connectors/).
