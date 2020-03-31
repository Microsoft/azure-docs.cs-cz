---
title: Pořadíobjednávekte události připojení zařízení fr Azure IoT Hub w/Azure Cosmos DB
description: Tento článek popisuje, jak objednat a zaznamenat události připojení zařízení z Azure IoT Hub pomocí Azure Cosmos DB udržovat nejnovější stav připojení
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954259"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Uspořádání událostí připojení zařízení z Azure IoT Hubu pomocí služby Azure Cosmos DB

Azure Event Grid vám pomůže vytvářet aplikace založené na událostech a snadno integrovat události IoT do vašich podnikových řešení. Tento článek vás provede nastavení, které lze použít ke sledování a ukládání nejnovější stav připojení zařízení v Cosmos DB. Použijeme pořadové číslo, které je k dispozici v událostech Připojeno k zařízení a Odpojeno zařízení, a uložíme nejnovější stav v Cosmos DB. Budeme používat uloženou proceduru, což je aplikační logika, která je spuštěna proti kolekci v Cosmos DB.

Pořadové číslo je řetězcová reprezentace šestnáctkového čísla. Můžete použít řetězec porovnat k identifikaci větší číslo. Pokud převádíte řetězec na hex, bude číslo 256bitové číslo. Pořadové číslo se striktně zvyšuje a poslední událost bude mít vyšší číslo než jiné události. To je užitečné, pokud máte časté zařízení připojí a odpojí a chcete zajistit, aby pouze nejnovější událost se používá k aktivaci akce příjem dat, jako Azure Event Grid nepodporuje řazení událostí.

## <a name="prerequisites"></a>Požadavky

* Aktivní účet Azure. Pokud ho nemáte, můžete [si vytvořit bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Aktivní účet SQL API Azure Cosmos DB. Pokud jste ho ještě nevytvořili, přečtěte si o [návodu v tématu Vytvoření databázového účtu.](../cosmos-db/create-sql-api-java.md#create-a-database-account)

* Kolekce v databázi. Viz [Přidání kolekce](../cosmos-db/create-sql-api-java.md#add-a-container) pro návod. Při vytváření kolekce, `/id` použijte klíč oddílu.

* IoT Hub v Azure. Pokud jste si ještě žádné nevytvořili, přečtěte si téma [Začínáme se službou IoT Hub](iot-hub-csharp-csharp-getstarted.md), kde najdete návod.

## <a name="create-a-stored-procedure"></a>Vytvoření uložené procedury

Nejprve vytvořte uloženou proceduru a nastavte ji tak, aby spouštěla logiku, která porovnává pořadová čísla příchozích událostí a zaznamenává nejnovější událost na zařízení v databázi.

1. Ve svém rozhraní SQL API Cosmos DB vyberte**položky datového** >  **průzkumníka** > **Nové uložené procedury**.

   ![Vytvořit uloženou proceduru](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Zadejte **LatestDeviceConnectionState** pro ID uložené procedury a vložte následující do **těla uložené procedury**. Všimněte si, že tento kód by měl nahradit všechny existující kód v těle uložené procedury. Tento kód udržuje jeden řádek na ID zařízení a zaznamenává nejnovější stav připojení tohoto ID zařízení identifikací nejvyšší pořadové číslo.

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

3. Uložit uloženou proceduru:

    ![uložit uloženou proceduru](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Napřed vytvořte aplikaci logiky a přidejte trigger služby Event Grid, který ve skupině prostředků monitoruje váš virtuální počítač.

### <a name="create-a-logic-app-resource"></a>Vytvořte prostředek aplikace logiky

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **+Vytvořit prostředek**, vyberte **Integrace** a potom **aplikace Logika**.

   ![Vytvoření aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Pojmenujte svoji aplikaci logiky jedinečným názvem v rámci vašeho předplatného a potom vyberte stejné předplatné, skupinu prostředků a umístění, jako má vaše centrum IoT.

   ![Nová aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Chcete-li vytvořit aplikaci logiky, vyberte **vytvořit.**

   Právě jste vytvořili prostředek Azure pro vaši aplikaci logiky. Až Azure nasadí aplikaci logiky, v Návrháři pro Logic Apps se zobrazí šablony pro obvyklé scénáře, abyste mohli začít rychleji.

   > [!NOTE]
   > Chcete-li znovu najít a otevřít aplikaci **logiky,** vyberte skupiny prostředků a vyberte skupinu prostředků, kterou používáte pro tento postup. Pak vyberte novou aplikaci logiky. Tím se otevře Návrhář aplikace logiky.

4. V Návrháři aplikace logiky posuňte doprava, dokud se nezobrazí běžné aktivační události. V části **Šablony**zvolte **Blank Logic App,** abyste mohli vytvořit aplikaci logiky od začátku.

### <a name="select-a-trigger"></a>Výběr triggeru

Trigger je konkrétní událost, která spustí aplikaci logiky. V tomto kurzu trigger, který spustí pracovní postup, přijímá žádost přes protokol HTTP.

1. V panelu hledání konektorů a aktivačních událostí zadejte **http** a stiskněte klávesu Enter.

2. Vyberte jako trigger **Žádost – Při přijetí požadavku HTTP**.

   ![Výběr triggeru požadavku HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Vyberte **K vygenerování schématu použijte ukázkovou datovou část**.

   ![Použití ukázkové datové části ke generování schématu](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Vložit ukázku datové části JSON](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Můžete se zobrazit automaticky otevírané okno s oznámením **Nezapomeňte do svého požadavku přidat hlavičku Content-Type nastavenou na application/json**. Tento návrh můžete v klidu ignorovat a přejít k další části.

### <a name="create-a-condition"></a>Vytvoření podmínky

V pracovním postupu aplikace logiky podmínky pomáhají spustit konkrétní akce po předání této konkrétní podmínky. Jakmile je podmínka splněna, lze definovat požadovanou akci. Pro účely tohoto kurzu je podmínkou zkontrolovat, zda eventType je připojen o zařízení nebo odpojeno. Akce bude provést uloženou proceduru v databázi.

1. Vyberte **+ Nový krok** a pak **Vestavěný**, pak najděte a vyberte **Podmínka**. Klikněte v **části Vyberte hodnotu** a objeví se pole s dynamickým obsahem – poli, která lze vybrat. Vyplňte pole, jak je znázorněno níže, a proveďte pouze události připojeného k zařízení a odpojené zařízení:

   * Zvolte hodnotu: **eventType** -- vyberte tuto položku z polí v dynamickém obsahu, která se zobrazí po kliknutí na toto pole.
   * Změna "je rovna" na **konec .**
   * Zvolte hodnotu: **nected**.

     ![Podmínka vyplnění](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. V dialogovém okně **pokud true** klikněte na **Přidat akci**.
  
   ![Přidat akci, pokud je true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Hledání Cosmos DB a vyberte **Azure Cosmos DB – spusťte uloženou proceduru**

   ![Hledat CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Vyplňte **cosmosdb-connection** pro **název připojení** a vyberte položku v tabulce a pak vyberte **Vytvořit**. Zobrazí se panel **Execute stored procedure.** Zadejte hodnoty polí:

   **ID databáze**: todolista

   **ID kolekce**: Položky

   **ID sproc**: LatestDeviceConnectionState

5. Vyberte **Přidat nový parametr**. V rozevíracím seznamu, který se zobrazí, zaškrtněte políčka vedle **klávesy Partition** a **parametry pro uloženou proceduru**a klikněte na libovolné místo na obrazovce; přidá pole pro hodnotu klíče oddílu a pole pro parametry pro uloženou proceduru.

   ![naplnění akce aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Nyní zadejte hodnotu klíče oddílu a parametry, jak je znázorněno níže. Ujistěte se, že jste vložili závorky a dvojité uvozovky, jak je znázorněno na obrázku. Možná budete muset kliknout na **Přidat dynamický obsah,** abyste získali platné hodnoty, které zde můžete použít.

   ![naplnění akce aplikace logiky](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. V horní části podokna, kde je uvedeno **pro každý**, v části **Vyberte výstup z předchozích kroků**, ujistěte se, že je vybrán **text.**

   ![naplnit aplikaci logiky pro každý](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Uložte svou aplikaci logiky.

### <a name="copy-the-http-url"></a>Zkopírujte adresu URL protokolu HTTP

Před opuštěním Návrháře aplikací logiky zkopírujte adresu URL, kterou vaše aplikace logiky naslouchá aktivační události. Pomocí této adresy URL nakonfigurujete Event Grid.

1. Kliknutím rozbalte konfigurační pole triggeru **Při přijetí požadavku HTTP**.

2. Tlačítkem vedle hodnoty **Adresa URL operace HTTP POST** tuto hodnotu zkopírujte.

   ![Zkopírování adresy URL operace HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Adresu URL si uložte, abyste na ni mohli odkazovat v další části.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurace odběru událostí služby IoT Hub

V této části nakonfigurujete v IoT Hubu publikování událostí, když k nim dojde.

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Vyberte **události**.

   ![Otevření podrobností Event Gridu](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Vyberte **+ Odběr událostí**.

   ![Vytvoření nového odběru události](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Vyplňte **podrobnosti o odběru událostí**: Zadejte popisný název a vyberte schéma **mřížky událostí**.

5. Vyplňte pole **Typy událostí.** V rozevíracím seznamu vyberte z nabídky pouze **připojené zařízení** a **odpojené zařízení.** Kliknutím na libovolné místo na obrazovce seznam zavřete a uložte výběry.

   ![Nastavení typů událostí, které chcete vyhledat](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. V **části Podrobnosti koncového bodu**vyberte Typ koncového bodu jako webový **hák** a klikněte na vybrat koncový bod a vložte adresu URL, kterou jste zkopírovali z aplikace logiky, a potvrďte výběr.

   ![Vybrat adresu URL koncového bodu](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formulář by nyní měl vypadat podobně jako v následujícím příkladu:

   ![Ukázkový formulář odběru události](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Výběrem možnosti **Vytvořit** uložte odběr události.

## <a name="observe-events"></a>Sledování událostí

Teď, když je vaše předplatné události nastavené, otestujte připojením zařízení.

### <a name="register-a-device-in-iot-hub"></a>Registrace zařízení v centru IoT Hub

1. V centru IoT vyberte **Zařízení IoT**.

2. V horní části podokna vyberte **+Přidat.**

3. Pro **ID zařízení** zadejte `Demo-Device-1`.

4. Vyberte **Uložit**.

5. Můžete přidat více zařízení s různými ID zařízení.

   ![Zařízení přidaná do rozbočovače](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klikněte na zařízení znovu; nyní budou vyplněny připojovací řetězce a klíče. Zkopírujte **připojovací řetězec – primární klíč** pro pozdější použití.

   ![ConnectionString pro zařízení](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Spuštění simulátoru Raspberry Pi

Použijeme webový simulátor Raspberry Pi k simulaci připojení zařízení.

[Spuštění simulátoru Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Spuštění ukázkové aplikace na webovém simulátoru Raspberry Pi

Tím se spustí událost připojená k zařízení.

1. V oblasti kódování nahraďte zástupný symbol na lince 15 připojovacím řetězcem zařízení Azure IoT Hub, který jste uložili na konci předchozí části.

   ![Vložit do připojovacího řetězce zařízení](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Spusťte aplikaci výběrem **možnosti Spustit**.

Zobrazí se něco podobného následujícímu výstupu, který zobrazuje data senzoru a zprávy, které jsou odesílány do centra IoT hub.

   ![Spouštění aplikace.](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klepnutím na **tlačítko Zastavit** zastavíte simulátor a spusťte událost **Odpojeno od zařízení.**

Nyní jste spouštěli ukázkovou aplikaci pro shromažďování dat senzorů a jejich odeslání do služby IoT hub.

### <a name="observe-events-in-cosmos-db"></a>Sledování událostí v Cosmos DB

Výsledky provedené uložené procedury můžete zobrazit v dokumentu Cosmos DB. Takhle to vypadá. Každý řádek obsahuje nejnovější stav připojení zařízení na zařízení.

   ![Jak k výsledku](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Místo použití [portálu Azure](https://portal.azure.com)můžete provést kroky služby IoT Hub pomocí příkazového příkazového příkazu k webu Azure. Podrobnosti najdete na stránkách Azure CLI pro [vytvoření předplatného událostí](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) a vytvoření zařízení [IoT](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz využívá prostředky, za které vám můžou být v předplatném Azure účtovány poplatky. Po dokončení vyzkoušení kurzu a testování výsledků zakažte nebo odstraňte prostředky, které nechcete zachovat.

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

Pokud chcete odebrat účet Azure Cosmos DB z portálu Azure, klikněte pravým tlačítkem myši na název účtu a klikněte na **Odstranit účet**. Podívejte se na podrobné pokyny pro [odstranění účtu Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Další kroky

* Další informace o [reakci na události služby IoT Hub pomocí služby Event Grid ke spuštění akcí](../iot-hub/iot-hub-event-grid.md)

* [Vyzkoušejte kurz událostí ioT hubu](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Přečtěte si, co dalšího můžete dělat s [Event Grid](../event-grid/overview.md)