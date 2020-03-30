---
title: Reakce na události modulu úložiště objektů blob – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Reakce na události modulu úložiště objektů Blob
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086604"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Kurz: Reakce na události úložiště objektů Blob na IoT Edge (preview)
Tento článek ukazuje, jak nasadit azure blob storage na ioT modulu, který by fungovat jako vydavatel sítě událostí k odesílání událostí na vytvoření objektu blob a odstranění objektů blob do event gridu.  

Přehled úložiště objektů blob Azure na IoT Edge najdete v článku [Azure Blob Storage na IoT Edge](../../iot-edge/how-to-store-data-blob.md) a jeho funkce.

> [!WARNING]
> Azure Blob Storage na IoT Edge integrace s Event Grid je ve verzi Preview

Chcete-li dokončit tento výukový program, budete potřebovat:

* **Předplatné Azure** – vytvořte [bezplatný účet,](https://azure.microsoft.com/free) pokud ho ještě nemáte. 
* **Azure IoT Hub a zařízení IoT Edge** – postupujte podle pokynů v rychlém startu pro [Linux](../../iot-edge/quickstart-linux.md) nebo [windows zařízení,](../../iot-edge/quickstart.md) pokud ještě nemáte.

## <a name="deploy-event-grid-iot-edge-module"></a>Nasadit modul IoT Edge sítě událostí

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge a všechny z nich fungují pro Azure Event Grid na IoT Edge. Tento článek popisuje kroky nasazení Event Grid na IoT Edge z webu Azure Portal.

>[!NOTE]
> V tomto kurzu nasadíte modul Mřížka událostí bez trvalosti. To znamená, že všechna témata a odběry, které vytvoříte v tomto kurzu budou odstraněny při opětovném nasazení modulu. Další informace o nastavení trvalosti naleznete v následujících článcích: [Zachovat stav v Linuxu](persist-state-linux.md) nebo [Zachovat stav v systému Windows](persist-state-windows.md). Pro produkční úlohy doporučujeme nainstalovat modul Event Grid s trvalostí.


### <a name="select-your-iot-edge-device"></a>Výběr zařízení IoT Edge

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Přejděte do svého iot hubu.
1. V nabídce v části **Automatická správa zařízení** vyberte **IoT Edge.** 
1. Klikněte na ID cílového zařízení ze seznamu zařízení
1. Vyberte **možnost Nastavit moduly**. Nechte stránku otevřenou. Budete pokračovat kroky v další části.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Portál Azure má průvodce, který vás provede vytvořením manifestu nasazení, namísto ručního sestavení dokumentu JSON.  Má tři kroky: **Přidat moduly**, **Zadat trasy**a **Zkontrolovat nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **Moduly nasazení** vyberte **Přidat**
1. Z typů modulů v rozevíracím seznamu vyberte **Modul IoT Edge**
1. Zadejte název, bitovou kopii, možnosti vytvoření kontejneru:

   * **Název**: eventgridmodule
   * **Identifikátor URI obrázku**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Možnosti vytvoření kontejneru**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Klikněte na **Uložit.**
 1. Pokračujte k další části a před nasazením je přidáte modul odběratele mřížky událostí Azure.

    >[!IMPORTANT]
    > V tomto kurzu se naučíte nasadit modul Event Grid, který umožní oba požadavky HTTP/HTTPs, ověřování klienta je zakázáno. Pro produkční úlohy doporučujeme povolit pouze požadavky HTTP a předplatitele s povoleným ověřováním klientů. Další informace o bezpečné konfiguraci modulu Event Grid naleznete v [tématu Zabezpečení a ověřování](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Nasadit modul IoT Edge odběratele sítě událostí

Tato část ukazuje, jak nasadit jiný modul IoT, který by fungovat jako obslužná rutina události, do kterého mohou být doručovány události.

### <a name="add-modules"></a>Přidat moduly

1. V části **Moduly nasazení** vyberte **Přidat** znovu. 
1. Z typů modulů v rozevíracím seznamu vyberte **Modul IoT Edge**
1. Zadejte možnosti vytvoření názvu, bitové kopie a kontejneru kontejneru:

   * **Jméno**: odběratel
   * **Identifikátor URI obrázku**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Možnosti vytvoření kontejneru:** Žádné
1. Klikněte na **Uložit.**
1. Pokračujte k další části a přidejte modul Úložiště objektů blob Azure

## <a name="deploy-azure-blob-storage-module"></a>Nasazení modulu úložiště objektů blob Azure

Tato část ukazuje, jak nasadit modul Azure Blob Storage, který by fungovat jako vydavatel event grid publikování objektů Blob vytvořené a odstraněné události.

### <a name="add-modules"></a>Přidat moduly

1. V části **Moduly nasazení** vyberte **Přidat**
2. Z typů modulů v rozevíracím seznamu vyberte **Modul IoT Edge**
3. Zadejte možnosti vytvoření názvu, bitové kopie a kontejneru kontejneru:

   * **Název**: azureblobstorageoniotedge
   * **Identifikátor URI obrázku**: mcr.microsoft.com/azure-blob-storage:latest
   * **Možnosti vytvoření kontejneru**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Modul úložiště objektů blob můžete publikovat události pomocí protokolu HTTPS i HTTP. 
   > - Pokud jste povolili ověřování na základě klienta pro EventGrid, ujistěte se, `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`že aktualizovat hodnotu EVENTGRID_ENDPOINT povolit https, takto: .
   > - Také přidat další `AllowUnknownCertificateAuthority=true` proměnnou prostředí na výše uvedené Json. Při komunikaci s EventGrid přes PROTOKOL HTTPS umožňuje **AllowUnknownCertificateAuthority** modulu úložiště důvěřovat certifikátům serveru EventGrid podepsaných svým držitelem.

4. Aktualizujte json, který jste zkopírovali, pomocí následujících informací:

   - Nahraďte `<your storage account name>` název, který si pamatujete. Názvy účtů by měly mít dlouhý počet 3 až 24 znaků s velkými písmeny a čísly. Žádné mezery.

   - Nahraďte `<your storage account key>` 64bajtova základnou64. Můžete vygenerovat klíč s nástroji, jako [je GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Tato pověření použijete k přístupu k úložišti objektů blob z jiných modulů.

   - Nahraďte `<event grid module name>` se názvem modulu Event Grid.
   - Vyměňte `<storage mount>` podle operačního systému kontejneru.
     - Pro linuxové kontejnery **můj svazek:/blobroot**
     - Pro kontejnery Windows**můj svazek:C:/BlobRoot**

5. Klikněte na **Uložit.**
6. Chcete-li pokračovat v úseku trasy, klepněte na tlačítko **Další.**

    > [!NOTE]
    > Pokud používáte virtuální počítač Azure jako hraniční zařízení, přidejte pravidlo příchozího portu, které povolí příchozí provoz na hostitelských portech použitých v tomto kurzu: 4438, 5888, 8080 a 11002. Pokyny k přidání pravidla najdete v tématu [Jak otevřít porty k virtuálnímu počítači](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Nastavení tras

Zachovat výchozí trasy a výběrem možnosti **Další** pokračovat do oddílu revize

### <a name="review-deployment"></a>Zkontrolovat nasazení

1. Část revize zobrazuje manifest nasazení JSON, který byl vytvořen na základě vašich výběrů v předchozí části. Potvrďte, že se zobrazí následující čtyři moduly: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **odběratel** a **azureblobstorageoniotedge,** které jsou všechny nasazeny.
2. Zkontrolujte informace o nasazení a vyberte **Odeslat**.

## <a name="verify-your-deployment"></a>Ověření nasazení

1. Po odeslání nasazení se vrátíte na stránku IoT Edge vašeho centra IoT hub.
2. Vyberte **zařízení IoT Edge,** na které jste cílí s nasazením otevřít jeho podrobnosti.
3. V podrobnostech o zařízení ověřte, zda jsou moduly eventgridmodule, subscriber a azureblobstorageoniotedge uvedeny jako **zadané v nasazení** a hlášené **zařízením**.

   Může trvat několik okamžiků, než se modul spustí na zařízení a pak se ohlásí zpět do ioT hubu. Aktualizujte stránku, abyste viděli aktualizovaný stav.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publikování událostí BlobCreated a BlobDeleted

1. Tento modul automaticky vytvoří téma **MicrosoftStorage**. Ověření, zda existuje
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Ukázkový výstup:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Pro tok HTTPS, pokud je ověřování klienta povoleno pomocí klíče SAS, měl by být jako záhlaví přidán dříve zadaný klíč SAS. Proto bude žádost o zvlnění:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Pro tok HTTPS, pokud je ověřování klienta povoleno prostřednictvím certifikátu, bude požadavek curl:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Odběratelé se mohou zaregistrovat pro události publikované na téma. Chcete-li obdržet jakoukoli událost, budete muset vytvořit předplatné služby Event Grid pro téma **MicrosoftStorage.**
    1. Vytvořte soubor blobsubscription.json s následujícím obsahem. Podrobnosti o datové části naleznete v naší [dokumentaci k rozhraní API](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Vlastnost **endpointType** určuje, že odběratel je **Webhook**.  **EndpointUrl** určuje adresu URL, na které odběratel naslouchá události. Tato adresa URL odpovídá ukázce funkce Azure, kterou jste nasadili dříve.

    2. Chcete-li vytvořit odběr pro toto téma, spusťte následující příkaz. Zkontrolujte, zda se zobrazí `200 OK`stavový kód HTTP .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Pro tok HTTPS, pokud je ověřování klienta povoleno pomocí klíče SAS, měl by být jako záhlaví přidán dříve zadaný klíč SAS. Proto bude žádost o zvlnění:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Pro tok HTTPS, pokud je ověřování klienta povoleno prostřednictvím certifikátu, bude požadavek curl:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Spusťte následující příkaz k ověření, že odběr byl úspěšně vytvořen. Měl by být vrácen stavový kód HTTP 200 OK.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Ukázkový výstup:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Pro tok HTTPS, pokud je ověřování klienta povoleno pomocí klíče SAS, měl by být jako záhlaví přidán dříve zadaný klíč SAS. Proto bude žádost o zvlnění:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Pro tok HTTPS, pokud je ověřování klienta povoleno prostřednictvím certifikátu, bude požadavek curl:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Stáhněte si [Průzkumníka azure a](https://azure.microsoft.com/features/storage-explorer/) [připojte ho k místnímu úložišti](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Ověřit doručení události

### <a name="verify-blobcreated-event-delivery"></a>Ověřit doručení události BlobCreated

1. Nahrajte soubory jako objekty BLOB bloku do místního úložiště z Průzkumníka azure storage a modul automaticky publikuje události vytvoření. 
2. Podívejte se na protokoly odběratele pro vytvoření události. Podle pokynů [ověřte doručení události.](pub-sub-events-webhook-local.md#verify-event-delivery)

    Ukázkový výstup:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Ověření doručení události BlobDeleted

1. Odstraňte objekty BLOB z místního úložiště pomocí Průzkumníka úložiště Azure a modul automaticky publikuje události odstranění. 
2. Podívejte se na protokoly odběratele pro odstranění události. Podle pokynů [ověřte doručení události.](pub-sub-events-webhook-local.md#verify-event-delivery)

    Ukázkový výstup:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Blahopřejeme! Dokončili jste kurz. V následujících částech jsou uvedeny podrobnosti o vlastnostech události.

### <a name="event-properties"></a>Vlastnosti události

Tady je seznam podporovaných vlastností událostí a jejich typů a popisů. 

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí úložiště objektů blob. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| rozhraní api | řetězec | Operace, která spustila událost. Může to být jedna z následujících hodnot: <ul><li>BlobCreated - povolené `PutBlob` hodnoty jsou: a`PutBlockList`</li><li>BlobDeleted - povolené `DeleteBlob` `DeleteAfterUpload` hodnoty `AutoDelete`jsou a . <p>Událost `DeleteAfterUpload` je generována při blob je automaticky odstraněn, protože deleteAfterUpload požadovaná vlastnost je nastavena na hodnotu true. </p><p>`AutoDelete`událost je generována při automatickém odstranění objektu blob, protože vypršela platnost požadované hodnoty vlastnosti deleteAfterMinutes.</p></li></ul>|
| klientRequestId | řetězec | ID požadavku poskytnutého klientem pro operaci rozhraní API úložiště. Toto ID lze použít ke korelaci s diagnostickými protokoly úložiště Azure pomocí pole "client-request-id" v protokolech a může být poskytnuto v požadavcích klientů pomocí hlavičky "x-ms-client-request-id". Podrobnosti naleznete v [tématu Log Format](/rest/api/storageservices/storage-analytics-log-format). |
| Requestid | řetězec | ID požadavku generované službou pro operaci rozhraní API úložiště. Slouží ke korelaci s diagnostickými protokoly úložiště Azure pomocí pole "request-id-header" v protokolech a je vrácena z zahájení volání rozhraní API v záhlaví 'x-ms-request-id'. Viz [Formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | řetězec | Hodnota, kterou můžete použít k podmíněnému provádění operací. |
| Contenttype | řetězec | Typ obsahu zadaný pro objekt blob. |
| Contentlength | celé číslo | Velikost objektu blob v bajtů. |
| objekt blobType | řetězec | Typ objektu blob. Platné hodnoty jsou buď "BlockBlob" nebo "PageBlob". |
| url | řetězec | Cesta k objektu blob. <br>Pokud klient používá rozhraní BLOB REST API, pak adresa URL má tuto strukturu: * \<název účtu\>úložiště .blob.core.windows.net/\<název\>/\<\>souboru .* <br>Pokud klient používá rozhraní REST API úložiště datového jezera, pak adresa URL má tuto strukturu: * \<název účtu úložiště\>.dfs.core.windows.net/\<název\>/\<\>souboru-název souboru*. |


## <a name="next-steps"></a>Další kroky

Podívejte se na následující články z dokumentace úložiště objektů blob:

- [Události úložiště objektů blob filtru](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Doporučené postupy pro náročné události úložiště objektů Blob](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

V tomto kurzu jste publikovali události vytvořením nebo odstraněním objektů BLOB v úložišti objektů blob Azure. Další kurzy se dozvíte, jak předávat události do cloudu (Azure Event Hub nebo Azure IoT Hub): 

- [Přesměrování událostí do Azure Event Gridu](forward-events-event-grid-cloud.md)
- [Přesměrování událostí do Azure IoT Hubu](forward-events-iothub.md)
