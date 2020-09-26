---
title: Objednat události připojení zařízení fr Azure IoT Hub w/Azure Cosmos DB
description: Tento článek popisuje, jak objednat a zaznamenat události připojení zařízení z Azure IoT Hub pomocí Azure Cosmos DB udržovat nejnovější stav připojení.
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: 815dc0a90d79323fb88c98867b5540105a6fa8cc
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356192"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Uspořádání událostí připojení zařízení z Azure IoT Hubu pomocí služby Azure Cosmos DB

Azure Event Grid vám pomůže sestavovat aplikace založené na událostech a snadno integrovat události IoT do obchodních řešení. Tento článek vás provede instalačním programem, který se dá použít ke sledování a ukládání nejnovějšího stavu připojení zařízení v Cosmos DB. Použijeme pořadové číslo dostupné v připojených zařízeních a událostech odpojených zařízení a uložíte nejnovější stav v Cosmos DB. Budeme používat uloženou proceduru, která je aplikační logikou spuštěnou pro kolekci v Cosmos DB.

Pořadové číslo je řetězcové vyjádření šestnáctkového čísla. K identifikaci většího čísla můžete použít porovnání řetězců. Pokud řetězec převádíte na šestnáctkovou hodnotu, bude číslo 256. Pořadové číslo se přesně zvyšuje a poslední událost bude mít vyšší číslo než jiné události. To je užitečné v případě, že se zařízení často připojuje a odpojuje a chcete zajistit, aby se k aktivaci navazující akce používala jenom poslední událost, protože Azure Event Grid nepodporuje řazení událostí.

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud ho nemáte, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Aktivní Azure Cosmos DB účet rozhraní SQL API. Pokud jste ho ještě nevytvořili, přečtěte si téma [vytvoření databázového účtu](../cosmos-db/create-sql-api-java.md#create-a-database-account) pro návod.

* Kolekce v databázi. Viz téma [Přidání kolekce](../cosmos-db/create-sql-api-java.md#add-a-container) pro návod. Při vytváření kolekce použijte `/id` pro klíč oddílu.

* IoT Hub v Azure. Pokud jste si ještě žádné nevytvořili, přečtěte si téma [Začínáme se službou IoT Hub](iot-hub-csharp-csharp-getstarted.md), kde najdete návod.

## <a name="create-a-stored-procedure"></a>Vytvoření uložené procedury

Nejdřív vytvořte uloženou proceduru a nastavte ji tak, aby běžela logika, která porovnává pořadová čísla příchozích událostí a zaznamenává poslední událost na zařízení v databázi.

1. V Cosmos DB rozhraní SQL API vyberte **Průzkumník dat**  >  **položky**  >  **Nová uložená procedura**.

   ![Vytvořit uloženou proceduru](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Jako ID uložené procedury zadejte **LatestDeviceConnectionState** a vložte následující **text do textu uložené procedury**. Všimněte si, že tento kód by měl nahradit libovolný existující kód v těle uložené procedury. Tento kód udržuje jeden řádek na ID zařízení a zaznamenává nejnovější stav připojení ID zařízení, a to tak, že identifikuje nejvyšší pořadové číslo.

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

3. Uložte uloženou proceduru:

    ![Uložit uloženou proceduru](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Napřed vytvořte aplikaci logiky a přidejte trigger služby Event Grid, který ve skupině prostředků monitoruje váš virtuální počítač.

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. V [Azure Portal](https://portal.azure.com)vyberte **+ vytvořit prostředek**, vyberte **integrace** a pak **Aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT.

   ![Nová aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Vyberte **vytvořit** a vytvořte aplikaci logiky.

   Právě jste vytvořili prostředek Azure pro vaši aplikaci logiky. Až Azure nasadí aplikaci logiky, v Návrháři pro Logic Apps se zobrazí šablony pro obvyklé scénáře, abyste mohli začít rychleji.

   > [!NOTE]
   > Chcete-li znovu vyhledat a otevřít aplikaci logiky, vyberte skupiny **prostředků** a vyberte skupinu prostředků, kterou používáte pro tento postup. Pak vyberte svou novou aplikaci logiky. Otevře se Návrhář aplikace logiky.

4. V návrháři aplikace logiky se posuňte doprava, dokud neuvidíte běžné aktivační události. V části **šablony**vyberte **prázdná aplikace logiky** , abyste mohli vytvořit aplikaci logiky od začátku.

### <a name="select-a-trigger"></a>Výběr aktivační události

Trigger je konkrétní událost, která spustí aplikaci logiky. V tomto kurzu trigger, který spustí pracovní postup, přijímá žádost přes protokol HTTP.

1. V panelu pro hledání konektorů a triggerů zadejte **http** a stiskněte ENTER.

2. Vyberte jako trigger **Žádost – Při přijetí požadavku HTTP**.

   ![Výběr triggeru požadavku HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**.

   ![Vytvoření schématu pomocí ukázkové datové části](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Vložit ukázkovou datovou část JSON](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Můžete se zobrazit automaticky otevírané okno s oznámením **Nezapomeňte do svého požadavku přidat hlavičku Content-Type nastavenou na application/json**. Tento návrh můžete v klidu ignorovat a přejít k další části.

### <a name="create-a-condition"></a>Vytvoření podmínky

V pracovním postupu aplikace logiky můžou podmínky po předání této konkrétní podmínky spustit konkrétní akce. Po splnění podmínky lze definovat požadovanou akci. V tomto kurzu je podmínka určena ke kontrole, jestli je typ eventType připojený k zařízení nebo zařízení odpojeno. Akce bude provádět uloženou proceduru ve vaší databázi.

1. Vyberte **+ Nový krok** a potom **předdefinované**a pak najděte a vyberte **podmínku**. Klikněte na **vybrat hodnotu** a pole se zobrazí, aby se zobrazil dynamický obsah – pole, která se dají vybrat. Vyplňte pole, jak je uvedeno níže, aby se spustilo pouze pro události připojené k zařízení a odpojené zařízení:

   * Zvolte hodnotu: **EventType** – vyberte tuto položku z polí v dynamickém obsahu, která se zobrazí po kliknutí na toto pole.
   * Změna "rovná se" na **končí**.
   * Vyberte hodnotu: **nected**.

     ![Vyplnit podmínku](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. V dialogu **při hodnotě true** klikněte na **přidat akci**.
  
   ![Přidat akci, pokud má hodnotu true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Vyhledejte Cosmos DB a vyberte **uloženou proceduru Azure Cosmos DB-Execute** .

   ![Hledat CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Vyplňte **cosmosdb – připojení** pro **název připojení** a vyberte položku v tabulce a pak vyberte **vytvořit**. Zobrazí se panel **Spustit uloženou proceduru** . Zadejte hodnoty pro pole:

   **ID databáze**: ToDoList

   **ID kolekce**: položky

   **ID sproc**: LatestDeviceConnectionState

5. Vyberte **Přidat nový parametr**. V rozevíracím seznamu, který se zobrazí, zaškrtněte políčka **klíč oddílu** a **parametry pro uloženou proceduru**a pak klikněte kamkoli jinam na obrazovku. Přidá pole pro hodnotu klíče oddílu a pole pro parametry pro uloženou proceduru.

   ![Snímek obrazovky zobrazuje položku Spustit uloženou proceduru s vybraným možnost Přidat nový parametr.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Nyní zadejte hodnotu a parametry klíče oddílu, jak je uvedeno níže. Nezapomeňte vložit do závorek a dvojité uvozovky, jak je znázorněno na obrázku. Možná budete muset kliknout na **Přidat dynamický obsah** a získat platné hodnoty, které zde můžete použít.

   ![Snímek obrazovky zobrazuje položku Spustit uloženou proceduru se zadanými parametry.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. V horní části **podokna, kde je uvedeno, v**části **vyberte výstup z předchozích kroků**se ujistěte, že je vybráno  **tělo** IT.

   ![naplnění aplikace logiky pro každý](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Uložte aplikaci logiky.

### <a name="copy-the-http-url"></a>Zkopírujte adresu URL protokolu HTTP

Než necháte návrháře Logic Apps, zkopírujte adresu URL, ke které vaše aplikace logiky naslouchá pro aktivační událost. Pomocí této adresy URL nakonfigurujete Event Grid.

1. Kliknutím rozbalte konfigurační pole triggeru **Při přijetí požadavku HTTP**.

2. Tlačítkem vedle hodnoty **Adresa URL operace HTTP POST** tuto hodnotu zkopírujte.

   ![Zkopírování adresy URL operace HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Adresu URL si uložte, abyste na ni mohli odkazovat v další části.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurace odběru událostí služby IoT Hub

V této části nakonfigurujete v IoT Hubu publikování událostí, když k nim dojde.

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Vyberte **události**.

   ![Otevření podrobností Event Gridu](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Vyberte **+ odběr události**.

   ![Vytvoření nového odběru události](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Vyplňte **Podrobnosti odběru události**: zadejte popisný název a vyberte **Event Grid schéma**.

5. Vyplňte pole **typy událostí** . V rozevíracím seznamu vyberte v nabídce možnost **připojené zařízení** a **zařízení odpojeno** . Kliknutím kamkoli jinam na obrazovce zavřete seznam a uložíte vybrané možnosti.

   ![Nastavte typy událostí, které se mají hledat.](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. V části **Podrobnosti o koncovém bodu**vyberte typ koncového bodu jako **Webhook** , klikněte na vybrat koncový bod a vložte adresu URL, kterou jste zkopírovali z aplikace logiky, a potvrďte výběr.

   ![Vyberte adresu URL koncového bodu.](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formulář by teď měl vypadat podobně jako v následujícím příkladu:

   ![Ukázkový formulář odběru události](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Vyberte **Vytvořit**, aby se odběr událostí uložil.

## <a name="observe-events"></a>Sledovat události

Teď, když je vaše předplatné událostí nastavené, otestujeme připojení zařízení.

### <a name="register-a-device-in-iot-hub"></a>Registrace zařízení v IoT Hub

1. Ve službě IoT Hub vyberte **zařízení IoT**.

2. V horní části podokna vyberte **+ Přidat** .

3. Jako **ID zařízení** zadejte `Demo-Device-1`.

4. Vyberte **Uložit**.

5. Můžete přidat několik zařízení s různými ID zařízení.

   ![Zařízení přidaná do centra](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Znovu klikněte na zařízení; Nyní se naplní řetězce a klíče pro připojení. Zkopírujte **připojovací řetězec – primární klíč** pro pozdější použití.

   ![ConnectionString pro zařízení](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Spustit simulátor malinu PI

Pojďme použít webový simulátor malinu pi k simulaci připojení zařízení.

[Spustit simulátor malinu PI](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Spustit ukázkovou aplikaci ve webovém simulátoru malinu PI

Tím se aktivuje událost připojená k zařízení.

1. V oblasti kódování nahraďte zástupný text na řádku 15 pomocí připojovacího řetězce zařízení Azure IoT Hub, který jste uložili na konci předchozí části.

   ![Vložit do připojovacího řetězce zařízení](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Spusťte aplikaci výběrem možnosti **Spustit**.

Vidíte něco podobného jako v následujícím výstupu, který zobrazuje data snímače a zprávy, které se odesílají do služby IoT Hub.

   ![Spouštění aplikace.](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kliknutím na tlačítko **zastavit** můžete simulátor zastavit a aktivovat událost **odpojeného zařízení** .

Teď jste spustili ukázkovou aplikaci, která shromáždí data ze senzorů a pošle ji do služby IoT Hub.

### <a name="observe-events-in-cosmos-db"></a>Sledování událostí v Cosmos DB

Výsledky spuštěné uložené procedury můžete zobrazit v dokumentu Cosmos DB. Takhle to vypadá. Každý řádek obsahuje nejnovější stav připojení zařízení na jedno zařízení.

   ![Postup pro výsledek](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Místo používání [Azure Portal](https://portal.azure.com)můžete IoT Hub kroky provést pomocí rozhraní příkazového řádku Azure CLI. Podrobnosti najdete na stránkách Azure CLI pro [Vytvoření odběru událostí](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) a [Vytvoření zařízení IoT](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. Až skončíte s vyzkoušením kurzu a otestujete výsledky, zakažte nebo odstraňte prostředky, které nechcete zachovat.

Pokud nechcete přijít o práci na aplikaci logiky, místo odstranění ji zakažte.

1. Přejděte do aplikace logiky.

2. V okně **Přehled** vyberte **Odstranit** nebo **Zakázat**.

    Každý odběr může mít jedno bezplatné centrum IoT. Pokud jste vytvořili bezplatné centrum pro účely tohoto kurzu, tak ho nemusíte odstraňovat, aby se vám nic neúčtovalo.

3. Přejděte do svého centra IoT.

4. V okně **Přehled** vyberte **Odstranit**.

    I když si centrum IoT necháte, bude vhodné odstranit odběr události, který jste vytvořili.

5. V centru IoT vyberte **Mřížka událostí**.

6. Vyberte odběr události, který chcete odebrat.

7. Vyberte **Odstranit**.

Pokud chcete účet Azure Cosmos DB z Azure Portal odebrat, klikněte pravým tlačítkem na název účtu a klikněte na **Odstranit účet**. Přečtěte si podrobné pokyny k [odstranění účtu Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o tom [, jak IoT Hub události pomocí Event Grid aktivovat akce](../iot-hub/iot-hub-event-grid.md) .

* [Vyzkoušejte kurz IoT Hub události](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Další informace o tom, co dalšího můžete dělat s [Event Grid](../event-grid/overview.md)
