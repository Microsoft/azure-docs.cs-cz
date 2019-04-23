---
title: Řazení událostí připojení zařízení ze služby Azure IoT Hub pomocí služby Azure Cosmos DB | Dokumentace Microsoftu
description: Tento článek popisuje, jak uspořádat a zaznamenávat události připojení zařízení ze služby Azure IoT Hub pomocí služby Azure Cosmos DB k údržbě nejnovější stav připojení
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: ff8f8c6656c4cd095749b3e048c72572d113f1ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400310"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Objednat zařízení události připojení ze služby Azure IoT Hub pomocí služby Azure Cosmos DB

Azure Event Grid umožňuje vytvářet aplikace založené na událostech a jednoduchá integrace událostí IoT v business solutions. Tento článek vás provede instalací, který slouží ke sledování a uložit nejnovější stav připojení zařízení do služby Cosmos DB. Společnost Microsoft použije pořadové číslo, které jsou k dispozici v událostech zařízení připojené a odpojené zařízení a nejnovější stav uložený ve službě Cosmos DB. Budeme používat uložené procedury, která je aplikační logiky, která se provede na kolekce v databázi Cosmos DB.

Pořadové číslo je řetězcové vyjádření šestnáctkového čísla. Porovnání řetězců můžete použít k identifikaci většímu počtu. Při převodu řetězce do šestnáctkové soustavy, číslo bude počet bitů na 256. Pořadové číslo výhradně roste a nejnovější událost může mít větší číslo než jiné události. To je užitečné, pokud máte časté zařízení připojí a odpojí a mít jistotu, že pouze nejnovější událost se používá k aktivaci podřízené akce, jako je Azure Event Grid nepodporuje řazení událostí.

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud žádný nemáte, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Aktivní účet rozhraní SQL API služby Azure Cosmos DB. Pokud ještě jeden ještě nevytvořili, přečtěte si téma [vytvoření databázového účtu](../cosmos-db/create-sql-api-dotnet.md#create-an-azure-cosmos-db-account) návod.

* Kolekce v databázi. Zobrazit [přidat kolekci](../cosmos-db/create-sql-api-dotnet.md#add-a-database-and-a-collection) návod. Když vytvoříte kolekci, použijte `/id` pro klíč oddílu.

* IoT Hub v Azure. Pokud jste si ještě žádné nevytvořili, přečtěte si téma [Začínáme se službou IoT Hub](iot-hub-csharp-csharp-getstarted.md), kde najdete návod.

## <a name="create-a-stored-procedure"></a>Vytvoření uložené procedury

Nejprve vytvořte uloženou proceduru a nastavte ji do spustit logiku, která porovná čísla pořadí příchozích událostí a zaznamenává nejnovější událost za každou zařízení v databázi.

1. V rozhraní SQL API Cosmos DB vyberte **Průzkumník dat** > **položky** > **novou uloženou proceduru**.

   ![Vytvořit uloženou proceduru](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Zadejte **LatestDeviceConnectionState** pro ID uložené procedury a vložte následující **uložená procedura tělo**. Všimněte si, že tento kód by měl nahraďte existující kód v těle uloženou proceduru. Tento kód udržuje jeden řádek na ID zařízení a zaznamenává nejnovější stav připojení toto ID zařízení díky identifikaci nejvyšší pořadové číslo.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Uložte uložená procedura:

    ![uložit uložené procedury](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Napřed vytvořte aplikaci logiky a přidejte trigger služby Event Grid, který ve skupině prostředků monitoruje váš virtuální počítač.

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. V [webu Azure portal](https://portal.azure.com)vyberte **+ vytvořit prostředek**vyberte **integrace** a potom **aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT.

   ![Nové aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Vyberte **vytvořit** k vytvoření aplikace logiky.

   Právě jste vytvořili prostředek Azure pro vaši aplikaci logiky. Až Azure nasadí aplikaci logiky, v Návrháři pro Logic Apps se zobrazí šablony pro obvyklé scénáře, abyste mohli začít rychleji.

   > [!NOTE]
   > Chcete-li najít a znovu otevřete aplikaci logiky, vyberte **skupiny prostředků** a vyberte skupinu prostředků, kterou používáte pro tento návod. Pak vyberte svou novou aplikaci logiky. Otevře se návrhář aplikace logiky.

4. V návrháři aplikace logiky posuňte doprava, dokud se nezobrazí běžné triggery. V části **šablony**, zvolte **prázdná aplikace logiky** tak, že můžete vytvářet aplikace logiky úplně od začátku.

### <a name="select-a-trigger"></a>Výběr triggeru

Trigger je konkrétní událost, která spustí aplikaci logiky. V tomto kurzu trigger, který spustí pracovní postup, přijímá žádost přes protokol HTTP.

1. Konektory a triggery panelu hledání zadejte **HTTP** a stiskněte Enter.

2. Vyberte jako trigger **Žádost – Při přijetí požadavku HTTP**.

   ![Výběr triggeru požadavku HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**.

   ![K vygenerování schématu použít ukázkovou datovou část](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Do textového pole vložte následující ukázkový kód JSON a potom vyberte **Hotovo**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Vložte ukázkovou datovou část JSON](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Můžete se zobrazit automaticky otevírané okno s oznámením **Nezapomeňte do svého požadavku přidat hlavičku Content-Type nastavenou na application/json**. Tento návrh můžete v klidu ignorovat a přejít k další části.

### <a name="create-a-condition"></a>Vytvořit podmínku

V pracovního postupu aplikace logiky podmínek pomoci spustit konkrétní akce po předání této konkrétní podmínky. Když je splněna podmínka, lze definovat požadovanou akci. Pro účely tohoto kurzu je podmínka zkontroluje, jestli je typ eventType zařízení připojení nebo odpojení zařízení. Akce, budete moct spustit uloženou proceduru v databázi.

1. Vyberte **+ nový krok** pak **integrované**, vyhledejte a vyberte **podmínku**. Klikněte na tlačítko v **zvolit hodnotu** a objeví se pole zobrazují dynamický obsah – pole, která je možné vybrat. Vyplňte pole, jak je znázorněno níže na spustit pouze pro události zařízení připojené a odpojené zařízení:

   * Zvolte hodnotu: **eventType** – tuto možnost vyberte, pokud z polí v seznamu dynamického obsahu, které se zobrazí po kliknutí na toto pole.
   * Změna "je rovno" **končí**.
   * Zvolte hodnotu: **nected**.

     ![Zadejte podmínku](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. V **při hodnotě true** dialogového okna, klikněte na **přidat akci**.
  
   ![Přidání akce, pokud je true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Vyhledání služby Cosmos DB a výběr **Azure Cosmos DB – spustit uloženou proceduru**

   ![Vyhledávání pro službu cosmos DB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Vyplňte **cosmosdb-connection** pro **název připojení** a vyberte položku v tabulce a pak vyberte **vytvořit**. Zobrazí **spustit uloženou proceduru** panelu. Zadejte hodnoty pro pole:

   **ID databáze**: ToDoList

   **ID kolekce**: Items

   **Sproc ID**: LatestDeviceConnectionState

5. Vyberte **přidat nový parametr**. V rozevíracím seznamu, který se zobrazí, zaškrtněte políčka vedle **klíč oddílu** a **parametry pro uloženou proceduru**, klikněte kamkoli jinam na obrazovce, přidá pole pro hodnotu klíče oddílu a pole pro parametry pro uloženou proceduru.

   ![naplnění akce aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Teď zadejte hodnotu klíče oddílu a parametry jak vidíte níže. Je nutné umístit závorky a uvozovky jak je znázorněno. Budete muset kliknout na **Přidat dynamický obsah** získat platné hodnoty, můžete zde použít.

   ![naplnění akce aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. V horní části podokna kde říká **pro každou**v části **vybrat výstup z předchozího postupu**, ujistěte se, že ho **text** zaškrtnuto.

   ![naplnění pro každou aplikaci logiky](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Uložte svou aplikaci logiky.

### <a name="copy-the-http-url"></a>Zkopírujte adresu URL protokolu HTTP

Než odejdete z návrháře pro Logic Apps, zkopírujte adresu URL, kterou aplikace logiky naslouchá aktivační události. Pomocí této adresy URL nakonfigurujete Event Grid.

1. Kliknutím rozbalte konfigurační pole triggeru **Při přijetí požadavku HTTP**.

2. Tlačítkem vedle hodnoty **Adresa URL operace HTTP POST** tuto hodnotu zkopírujte.

   ![Zkopírování adresy URL operace HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Adresu URL si uložte, abyste na ni mohli odkazovat v další části.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurace odběru událostí služby IoT Hub

V této části nakonfigurujete v IoT Hubu publikování událostí, když k nim dojde.

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Vyberte **Události**.

   ![Otevření podrobností Event Gridu](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Vyberte **+ odběr události**.

   ![Vytvoření nového odběru události](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Vyplňte **Podrobnosti předplatného událostí**: Zadejte popisný název a vyberte **Event Grid schématu**.

5. Vyplňte **typy událostí** pole. Zrušte zaškrtnutí políčka **odběru pro všechny typy událostí** a vyberte **zařízení připojeno** a **odpojení zařízení** z nabídky.

   ![Nastavte typy událostí hledání](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Pro **podrobnosti o koncovém bodu**, vyberte typ koncového bodu jako **Webhook** a klikněte na Vybrat koncový bod a vložte adresu URL, kterou jste zkopírovali z aplikace logiky a potvrďte výběr.

   ![Vyberte adresu url koncového bodu](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

7. Formulář by teď měl vypadat podobně jako v následujícím příkladu:

   ![Ukázkový formulář odběru události](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Výběrem možnosti **Vytvořit** uložte odběr události.

## <a name="observe-events"></a>Sledovat události

Teď, když je nastavení odběru události, můžeme otestovat připojení zařízení.

### <a name="register-a-device-in-iot-hub"></a>Registrace zařízení ve službě IoT Hub

1. V centru IoT vyberte **Zařízení IoT**.

2. Vyberte **+ přidat** v horní části podokna.

3. Pro **ID zařízení** zadejte `Demo-Device-1`.

4. Vyberte **Uložit**.

5. Můžete přidat víc zařízení přes různé identifikátory zařízení.

   ![Zařízení do centra](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klikněte znovu; na zařízení Nyní se připojovací řetězce a klíče se nevyplní. Kopírovat **připojovací řetězec – primární klíč** pro pozdější použití.

   ![Připojovací řetězec pro zařízení](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

HostName=test-eventgrid-hub.azure-devices.net;DeviceId=Demo-Device-1;SharedAccessKey=cv8uPNixe7E2R9EHtimoY/PlJfBV/lOYCMajVOp/Cuw=

### <a name="start-raspberry-pi-simulator"></a>Spusťte simulátor Raspberry Pi

1. Umožňuje použít webový simulátor Raspberry Pi pro simulaci zařízení připojení.

[Spusťte simulátor Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Spustit ukázkovou aplikaci na webový simulátor Raspberry Pi

Tím se aktivuje událost připojené zařízení.

1. V oblasti kódování nahraďte zástupný text v řádku 15 služby Azure IoT Hub, připojovací řetězec zařízení, který jste uložili na konci předchozí části.

   ![Vložte připojovací řetězec zařízení](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Spusťte aplikaci tak, že vyberete **spustit**.

Vypadá podobně jako následující výstup, který zobrazuje data ze senzorů a zprávy, které se odesílají do služby IoT hub.

   ![Spouštění aplikace.](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klikněte na tlačítko **Zastavit** zastavit simulátor a aktivační události **odpojení zařízení** událostí.

Nyní jste spustili ukázkovou aplikaci shromažďovat data ze senzorů a odesílat je do služby IoT hub.

### <a name="observe-events-in-cosmos-db"></a>Sledovat události ve službě Cosmos DB

Zobrazí se výsledky provést uloženou proceduru v dokumentu Cosmos DB. Zde je, jak to vypadá. Každý řádek obsahuje nejnovější stav připojení zařízení na zařízení.

   ![Jak výsledku](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Namísto použití [webu Azure portal](https://portal.azure.com), můžete provést kroky IoT Hubu pomocí rozhraní příkazového řádku Azure. Podrobnosti najdete na stránkách rozhraní příkazového řádku Azure pro [vytvoření odběru událostí](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) a [vytvoření zařízení IoT](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. Po dokončení tohoto zkušebního kurzu a otestování výsledků zakažte nebo odstraňte prostředky, které si nechcete nechat.

Pokud nechcete přijít o práci na aplikaci logiky, místo odstranění ji zakažte.

1. Přejděte do aplikace logiky.

2. Na **přehled** okně vyberte **odstranit** nebo **zakázat**.

    Každý odběr může mít jedno bezplatné centrum IoT. Pokud jste vytvořili bezplatné centrum pro účely tohoto kurzu, tak ho nemusíte odstraňovat, aby se vám nic neúčtovalo.

3. Přejděte do svého centra IoT.

4. Na **přehled** okně vyberte **odstranit**.

    I když si centrum IoT necháte, bude vhodné odstranit odběr události, který jste vytvořili.

5. V centru IoT vyberte **Mřížka událostí**.

6. Vyberte odběr události, který chcete odebrat.

7. Vyberte **Odstranit**.

Pokud chcete odebrat účet služby Azure Cosmos DB na webu Azure Portal, klikněte pravým tlačítkem na název účtu a klikněte na tlačítko **odstranit účet**. Přečtěte si podrobné pokyny pro [odstranění účtu služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Další postup

* Další informace o [reakce na události služby IoT Hub s využitím služby Event Grid pro aktivaci akcí](../iot-hub/iot-hub-event-grid.md)

* [Projděte si kurz událostí služby IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Další informace o tom, co jiného vám pomůžou s [služby Event Grid](../event-grid/overview.md)