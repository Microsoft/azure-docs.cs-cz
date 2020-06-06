---
title: Kurz – použití událostí IoT Hub k aktivaci Azure Logic Apps
description: V tomto kurzu se dozvíte, jak používat službu Směrování událostí Azure Event Grid, vytvářet automatizované procesy pro provádění Azure Logic Apps akcí na základě IoT Hubch událostí.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: robinsh
ms.openlocfilehash: 2a0b25fc73bf0f549a199592d558c0097c2db8b8
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457061"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Kurz: odesílání e-mailových oznámení o událostech Azure IoT Hub pomocí Event Grid a Logic Apps

Azure Event Grid vám umožňuje reagovat na události ve službě IoT Hub aktivováním akcí v podnikových aplikacích ve směru server-klient.

Tento článek vás provede ukázkovou konfigurací, která používá IoT Hub a Event Grid. Na konci máte aplikaci logiky Azure nastavenou k odeslání e-mailu s oznámením pokaždé, když se do služby IoT Hub přidá zařízení. 

## <a name="prerequisites"></a>Požadavky

* Aktivní předplatné Azure. Pokud předplatné nemáte, můžete si [vytvořit bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/).

* E-mailový účet od jakéhokoli poskytovatele e-mailu, který podporuje Azure Logic Apps, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Tento e-mailový účet se používá k posílání oznámení o událostech. Úplný seznam podporovaných konektorů aplikace logiky najdete v tématu [Přehled konektorů](https://docs.microsoft.com/connectors/).

  > [!IMPORTANT]
  > Než použijete Gmail, ověřte, jestli máte obchodní účet G-Suite (e-mailová adresa s vlastní doménou) nebo účet uživatele Gmail (e-mailová adresa s @gmail.com nebo @googlemail.com ). Konektor Gmail můžou používat jenom obchodní účty G-Suite s jinými konektory bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete použít konektor Gmail s pouze konkrétními službami, které jsou schváleny pro Google, nebo můžete [vytvořit klientskou aplikaci Google, která bude použita pro ověřování](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* IoT Hub v Azure. Pokud jste si ještě žádné nevytvořili, přečtěte si téma [Začínáme se službou IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md), kde najdete návod.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Nejdřív vytvořte aplikaci logiky a přidejte Trigger služby Event Grid, který monitoruje skupinu prostředků pro váš virtuální počítač. 

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**, do vyhledávacího pole zadejte "Logic App" a vyberte Return (vrátit). Z výsledků vyberte **Aplikace logiky** .

   ![Vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na další obrazovce vyberte **vytvořit**. 

1. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT. 

   ![Pole pro vytvoření aplikace logiky](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Vyberte **Vytvořit**.

1. Po vytvoření prostředku přejděte do aplikace logiky. Provedete to tak, že vyberete **skupiny prostředků**a pak vyberete skupinu prostředků, kterou jste pro tento kurz vytvořili. Pak v seznamu prostředků Najděte aplikaci logiky a vyberte ji. 

1. V Návrháři Logic Apps stránku dolů a zobrazte **šablony**. Vyberte **prázdná aplikace logiky** , abyste mohli vytvořit aplikaci logiky od začátku.

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

1. Vyberte **Nový krok**. Tím se otevře okno pro **Výběr akce**.

1. Vyhledejte **E-mail**.

1. Vyhledejte a vyberte konektor odpovídající vašemu poskytovateli e-mailu. V tomto kurzu se používá **Office 365 Outlook**. Kroky pro jiné poskytovatele e-mailu jsou podobné. 

   ![Výběr konektoru poskytovatele e-mailu](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Vyberte akci **Odeslat e-mail**. 

1. Pokud budete vyzváni, přihlaste se k e-mailovému účtu. 

1. Vytvořte šablonu e-mailu. 

   * **Komu**: Zadejte e-mailovou adresu, která bude dostávat e-maily s oznámeními. V tomto kurzu použijte e-mailový účet, který máte dostupný pro testování. 

   * **Předmět**: Vyplňte text předmětu. Po kliknutí na textové pole Předmět můžete vybrat dynamický obsah, který chcete zahrnout. Tento kurz například používá `IoT Hub alert: {event Type}` . Pokud nevidíte dynamický obsah, vyberte hypertextový odkaz **Přidat dynamický obsah** – tím ho přepínáte a vypnuli.

   * **Tělo**: napište text pro váš e-mail. Vyberte vlastnosti JSON z nástroje pro výběr, aby se zahrnul dynamický obsah na základě dat událostí. Pokud nemůžete zobrazit dynamický obsah, v textovém poli text **zprávy** vyberte hypertextový odkaz **Přidat dynamický obsah** . Pokud se vám nezobrazují požadovaná pole, klikněte na tlačítko *Další* na obrazovce dynamický obsah a přidejte pole z předchozí akce.

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

1. Na webu Azure Portal přejděte do svého centra IoT. Můžete to udělat tak, že vyberete **skupiny prostředků**, pak vyberete skupinu prostředků tohoto kurzu a pak ze seznamu prostředků vyberete Centrum IoT.

2. Vyberte **události**.

   ![Otevření podrobností Event Gridu](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Vyberte **odběr události**. 

   ![Vytvoření nového odběru události](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Vytvořte odběr události s následujícími hodnotami: 

    1. V části **Podrobnosti odběru události** proveďte následující úlohy:
        1. Zadejte **název** odběru události. 
        2. Vyberte **Event Grid schéma** pro **schéma událostí**. 
   2. V části **Podrobnosti o tématu** proveďte následující úlohy:
       1. Potvrďte, že je **typ tématu** nastaven na **IoT Hub**. 
       2. Potvrďte, že název služby IoT Hub je nastaven jako hodnota pro pole **zdrojového prostředku** . 
       3. Zadejte název **systémového tématu** , které se vytvoří za vás. Další informace o systémových tématech najdete v tématu [Přehled systémových témat](system-topics.md).
   3. V části **typy událostí** proveďte následující úlohy: 
        1. Pro **Filtr na typy událostí**zrušte výběr všech možností s výjimkou **vytvořeného zařízení**.

           ![typy událostí předplatného](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   4. V části **Podrobnosti o koncovém bodu** proveďte následující úlohy: 
       1. Jako **webový Hook**vyberte **Typ koncového bodu** .
       2. Klikněte na **Vybrat koncový bod**, vložte adresu URL, kterou jste zkopírovali z aplikace logiky, a potvrďte výběr.

         ![Výběr adresy URL koncového bodu](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Až budete hotovi, podokno by mělo vypadat jako v následujícím příkladu: 

        ![Ukázkový formulář odběru události](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Tady byste si mohli uložit odběr události a přijímat oznámení pro každé zařízení, které se vytvoří ve vašem centru IoT. Pro tento kurz ale použijte volitelná pole k filtrování pro konkrétní zařízení. V horní části podokna vyberte **filtry** .

6. Vyberte **Přidat nový filtr**. Vyplňte pole těmito hodnotami:

   * **Klíč**: vyberte `Subject` .

   * **Operátor**: Select `String begins with` .

   * **Hodnota**: zadejte `devices/Building1_` , chcete-li vyfiltrovat události zařízení v budově 1.
  
   Přidejte další filtr s těmito hodnotami:

   * **Klíč**: vyberte `Subject` .

   * **Operátor**: Select `String ends with` .

   * **Hodnota**: Pokud `_Temperature` chcete vyfiltrovat události zařízení související s teplotou, zadejte.

   Karta **filtry** vašeho odběru událostí by teď měla vypadat podobně jako na tomto obrázku:

   ![Přidávání filtrů do odběru událostí](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Výběrem možnosti **Vytvořit** uložte odběr události.

## <a name="create-a-new-device"></a>Vytvoření nového zařízení

Otestujte si aplikaci logiky vytvořením nového zařízení, aby se aktivoval e-mail s oznámením události. 

1. V centru IoT vyberte **Zařízení IoT**. 

2. Vyberte **Nový**.

3. Pro **ID zařízení** zadejte `Building1_Floor1_Room1_Light`.

4. Vyberte **Uložit**. 

5. Můžete přidat více zařízení s různými ID a otestovat filtry odběrů událostí. Zkuste tyto příklady: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Pokud jste přidali čtyři příklady, váš seznam zařízení IoT by měl vypadat jako na následujícím obrázku:

   ![Seznam zařízení IoT Hub](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Po přidání několika zařízení do centra IoT se podívejte do e-mailu, která z nich aktivovala aplikaci logiky. 

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Místo použití webu Azure Portal můžete provést kroky služby IoT Hub pomocí rozhraní příkazového řádku Azure CLI. Podrobnosti najdete na stránkách Azure CLI pro [Vytvoření odběru událostí](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) a [Vytvoření zařízení IoT](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. Až skončíte s vyzkoušením kurzu a otestujete výsledky, zakažte nebo odstraňte prostředky, které nechcete zachovat. 

Pokud chcete odstranit všechny prostředky vytvořené v tomto kurzu, odstraňte skupinu prostředků. 

1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste pro tento kurz vytvořili.

2. V podokně skupina prostředků vyberte **Odstranit skupinu prostředků**. Zobrazí se výzva k zadání názvu skupiny prostředků a pak ho můžete odstranit. Odstraní se také všechny prostředky, které jsou v něm obsažené.

Pokud nechcete odebrat všechny prostředky, můžete je spravovat po jednom. 

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