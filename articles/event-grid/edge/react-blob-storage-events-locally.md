---
title: Reakce na události Blob Storage modulu – Azure Event Grid IoT Edge | Microsoft Docs
description: Reakce na události Blob Storage modulu
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87462016"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Kurz: reakce na události Blob Storage v IoT Edge (Preview)
V tomto článku se dozvíte, jak nasadit modul Azure Blob Storage do služby IoT, který by sloužil jako Event Grid Vydavatel k posílání událostí při vytváření objektů BLOB a odstraňování objektů blob do Event Grid.  

Přehled služby Azure Blob Storage v IoT Edge najdete v tématu [azure BLOB Storage na IoT Edge](../../iot-edge/how-to-store-data-blob.md) a jejích funkcích.

> [!WARNING]
> Azure Blob Storage on IoT Edge Integration with Event Grid je ve verzi Preview.

Aby bylo možné dokončit tento kurz, budete potřebovat:

* **Předplatné Azure** – Pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) . 
* **Zařízení Azure IoT Hub a IoT Edge** – postupujte podle kroků v rychlém startu pro zařízení se systémem [Linux](../../iot-edge/quickstart-linux.md) nebo [Windows](../../iot-edge/quickstart.md) , pokud ho ještě nemáte.

## <a name="deploy-event-grid-iot-edge-module"></a>Nasadit modul Event Grid IoT Edge

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge, a všechny z nich fungují pro Azure Event Grid na IoT Edge. Tento článek popisuje postup nasazení Event Grid IoT Edge z Azure Portal.

>[!NOTE]
> V tomto kurzu nasadíte modul Event Grid bez trvalosti. To znamená, že všechna témata a odběry, které v tomto kurzu vytvoříte, budou při opětovném nasazení modulu odstraněny. Další informace o tom, jak nastavit trvalost, najdete v následujících článcích: [trvalé uložení stavu v systému Linux](persist-state-linux.md) nebo [trvalého stavu ve Windows](persist-state-windows.md). Pro produkční úlohy doporučujeme nainstalovat modul Event Grid s persistencí.


### <a name="select-your-iot-edge-device"></a>Vyberte zařízení IoT Edge

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejděte na IoT Hub.
1. V nabídce v části **Automatická správa zařízení** vyberte **IoT Edge** . 
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. Vyberte možnost **nastavit moduly**. Nechejte stránku otevřenou. Budete pokračovat kroky v další části.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení místo ručního vytváření dokumentu JSON.  Má tři kroky: **přidat moduly**, **zadat trasy**a **zkontrolovat nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **moduly nasazení** vyberte **Přidat** .
1. Z typů modulů v rozevíracím seznamu vyberte **IoT Edge modul** .
1. Zadejte název, obrázek a možnosti vytvoření kontejneru kontejneru:

   * **Název**: eventgridmodule
   * **Identifikátor URI image**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
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

 1. Klikněte na **Uložit**.
 1. Přejděte k další části a přidejte modul předplatitele Azure Event Grid před jejich nasazením dohromady.

    >[!IMPORTANT]
    > V tomto kurzu se naučíte nasadit modul Event Grid, aby se povolily požadavky HTTP/HTTPs, ale ověřování klientů je zakázané. Pro produkční úlohy doporučujeme povolit pouze žádosti a předplatitele HTTPs s povoleným ověřováním klienta. Další informace o tom, jak bezpečně nakonfigurovat Event Grid modul, najdete v tématu [zabezpečení a ověřování](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Nasazení IoT Edge modulu předplatitele Event Grid

V této části se dozvíte, jak nasadit jiný modul IoT, který by sloužil jako obslužná rutina události, do které se můžou události doručovat.

### <a name="add-modules"></a>Přidat moduly

1. V části **moduly nasazení** vyberte **Přidat** znovu. 
1. Z typů modulů v rozevíracím seznamu vyberte **IoT Edge modul** .
1. Zadejte název, obrázek a možnosti vytvoření kontejneru kontejneru:

   * **Název**: odběratel
   * **Identifikátor URI image**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Možnosti vytvoření kontejneru**: žádné
1. Klikněte na **Uložit**.
1. Pokračujte k další části a přidejte modul Azure Blob Storage.

## <a name="deploy-azure-blob-storage-module"></a>Nasazení modulu Azure Blob Storage

V této části se dozvíte, jak nasadit modul Blob Storage Azure, který se bude chovat jako Event Grid Publisher Publisher vytvořil a odstranil objekty blob.

### <a name="add-modules"></a>Přidat moduly

1. V části **moduly nasazení** vyberte **Přidat** .
2. Z typů modulů v rozevíracím seznamu vyberte **IoT Edge modul** .
3. Zadejte název, obrázek a možnosti vytvoření kontejneru kontejneru:

   * **Název**: azureblobstorageoniotedge
   * **Identifikátor URI image**: MCR.Microsoft.com/Azure-Blob-Storage:Latest
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
   > - Blob Storage modul může publikovat události pomocí protokolu HTTPS i HTTP. 
   > - Pokud jste povolili ověřování na základě klienta pro EventGrid, ujistěte se, že aktualizujete hodnotu EVENTGRID_ENDPOINT tak, aby povolovala https, například: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` .
   > - Přidejte také další proměnnou prostředí `AllowUnknownCertificateAuthority=true` do výše uvedeného JSON. Při komunikaci s EventGrid přes HTTPS umožňuje **AllowUnknownCertificateAuthority** modul úložiště důvěřovat certifikátům serveru EventGrid podepsaných svým držitelem.

4. Aktualizujte kód JSON, který jste zkopírovali, pomocí následujících informací:

   - Nahraďte `<your storage account name>` názvem, který si můžete pamatovat. Názvy účtů by měly mít délku 3 až 24 znaků a malými písmeny a číslicemi. Žádné mezery.

   - Nahraďte `<your storage account key>` klíčem base64 64-byte. Klíč můžete vygenerovat pomocí nástrojů, jako je [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Tyto přihlašovací údaje použijete pro přístup k úložišti objektů BLOB z jiných modulů.

   - Nahraďte `<event grid module name>` názvem vašeho modulu Event Grid.
   - Nahraďte `<storage mount>` v závislosti na vašem operačním systému vašeho kontejneru.
     - V případě kontejnerů Linux, **My-Volume:/blobroot**
     - Pro kontejnery Windows**Tento svazek: C:/BlobRoot**

5. Klikněte na **Uložit**.
6. Kliknutím na tlačítko **Další** pokračujte v části trasy.

    > [!NOTE]
    > Pokud jako hraniční zařízení používáte virtuální počítač Azure, přidejte pravidlo příchozího portu, které povolí příchozí provoz na portech hostitele používaných v tomto kurzu: 4438, 5888, 8080 a 11002. Pokyny k přidání pravidla najdete v tématu [Postup otevření portů pro virtuální počítač](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Nastavení tras

Ponechte výchozí trasy a vyberte **Další** , abyste pokračovali v části Kontrola.

### <a name="review-deployment"></a>Zkontrolovat nasazení

1. V části Kontrola se dozvíte, který manifest nasazení JSON byl vytvořen na základě vašich výběrů v předchozí části. Ověřte, že se zobrazí následující čtyři moduly: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **předplatitel** a **azureblobstorageoniotedge** , které se nasazují.
2. Zkontrolujte informace o svém nasazení a pak vyberte **Odeslat**.

## <a name="verify-your-deployment"></a>Ověření nasazení

1. Po odeslání nasazení se vrátíte na stránku IoT Edge služby IoT Hub.
2. Vyberte **zařízení IoT Edge** , na které cílíte nasazení, a otevřete jeho podrobnosti.
3. V podrobnostech o zařízení ověřte, že moduly eventgridmodule, předplatitelé a azureblobstorageoniotedge jsou uvedené **v části nasazení** a **nahlášené zařízením**.

   Může chvíli trvat, než se modul na zařízení spustí a pak se znovu oznámí IoT Hub. Aktualizujte stránku, aby se zobrazil aktualizovaný stav.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publikování událostí BlobCreated a BlobDeleted

1. Tento modul automaticky vytvoří téma **MicrosoftStorage**. Ověřte, že existuje
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
    > - V případě toku HTTPS, pokud je povoleno ověřování klienta prostřednictvím klíče SAS, by měl být dříve zadaný klíč SAS přidán jako záhlaví. Proto bude požadavek na kudrlinkou: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Pokud je v toku HTTPS povolený ověřování klienta prostřednictvím certifikátu, bude požadavek na kudrlinkou: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Předplatitelé se můžou zaregistrovat pro události publikované v tématu. K přijetí jakékoli události budete muset vytvořit předplatné Event Grid pro téma **MicrosoftStorage** .
    1. Vytvořte blobsubscription.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

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
       > Vlastnost **endpointType** určuje, že předplatitel je **Webhook**.  **EndpointUrl** Určuje adresu URL, na které předplatitel naslouchá pro události. Tato adresa URL odpovídá ukázce funkce Azure, kterou jste nasadili dříve.

    2. Spuštěním následujícího příkazu vytvořte odběr pro téma. Ověřte, že se zobrazí stavový kód HTTP `200 OK` .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - V případě toku HTTPS, pokud je povoleno ověřování klienta prostřednictvím klíče SAS, by měl být dříve zadaný klíč SAS přidán jako záhlaví. Proto bude požadavek na kudrlinkou: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Pokud je v toku HTTPS povolený ověřování klienta prostřednictvím certifikátu, bude požadavek na kudrlinkou:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Spusťte následující příkaz pro ověření, že se předplatné úspěšně vytvořilo. Měl by se vrátit stavový kód HTTP 200 OK.

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
       > - V případě toku HTTPS, pokud je povoleno ověřování klienta prostřednictvím klíče SAS, by měl být dříve zadaný klíč SAS přidán jako záhlaví. Proto bude požadavek na kudrlinkou: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Pokud je v toku HTTPS povolený ověřování klienta prostřednictvím certifikátu, bude požadavek na kudrlinkou: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Stáhněte si [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) a [Připojte ho k místnímu úložišti](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer) .

## <a name="verify-event-delivery"></a>Ověření doručení události

### <a name="verify-blobcreated-event-delivery"></a>Ověření doručení události BlobCreated

1. Nahrajte soubory jako objekty blob bloku do místního úložiště z Průzkumník služby Azure Storage a modul automaticky publikuje události vytváření. 
2. Podívejte se na protokol předplatitele pro událost vytvořit. Postupujte podle kroků a [Ověřte doručení událostí](pub-sub-events-webhook-local.md#verify-event-delivery) .

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

1. Odstraňte objekty BLOB z místního úložiště pomocí Průzkumník služby Azure Storage a modul automaticky publikuje události odstranění. 
2. Podívejte se na protokol předplatitele pro událost odstranění. Postupujte podle kroků a [Ověřte doručení událostí](pub-sub-events-webhook-local.md#verify-event-delivery) .

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

Gratulujeme! Dokončili jste tento kurz. Následující části obsahují podrobné informace o vlastnostech události.

### <a name="event-properties"></a>Vlastnosti události

Tady je seznam podporovaných vlastností události a jejich typy a popisy. 

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | object | Data události služby Blob Storage. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| api | řetězec | Operace, která aktivovala událost. Může to být jedna z následujících hodnot: <ul><li>BlobCreated – povolené hodnoty jsou: `PutBlob` a `PutBlockList`</li><li>BlobDeleted – povolené hodnoty jsou `DeleteBlob` `DeleteAfterUpload` a `AutoDelete` . <p>`DeleteAfterUpload`Událost se generuje, když se automaticky odstraní objekt blob, protože deleteAfterUpload požadovaná vlastnost je nastavená na true. </p><p>`AutoDelete` událost se generuje, když se automaticky odstraní objekt blob, protože vypršela platnost požadované hodnoty vlastnosti deleteAfterMinutes.</p></li></ul>|
| ID žádosti klienta | řetězec | ID požadavku pro rozhraní API úložiště poskytnuté klientem. Toto ID lze použít ke korelaci Azure Storage diagnostických protokolů pomocí pole "Client-Request-ID" v protokolech a lze je poskytnout v klientských požadavcích pomocí hlavičky x-MS-Client-Request-ID. Podrobnosti najdete v tématu [Formát protokolu](/rest/api/storageservices/storage-analytics-log-format). |
| Identifikátor | řetězec | ID žádosti generované službou pro operaci rozhraní API úložiště Dá se použít ke korelaci Azure Storage diagnostických protokolů pomocí pole "Request-ID-header" v protokolech a vrátí se z inicializace volání rozhraní API v hlavičce x-MS-Request-ID. Viz [Formát protokolu](/rest/api/storageservices/storage-analytics-log-format). |
| značk | řetězec | Hodnota, kterou můžete použít k podmíněnému provádění operací. |
| Třída | řetězec | Typ obsahu zadaný pro objekt BLOB. |
| contentLength | integer | Velikost objektu BLOB v bajtech |
| blobType | řetězec | Typ objektu BLOB Platné hodnoty jsou buď "BlockBlob" nebo "PageBlob". |
| url | řetězec | Cesta k objektu BLOB <br>Pokud klient používá REST API objektů blob, adresa URL má tuto strukturu: * \<storage-account-name\> . blob.Core.Windows.NET/ \<container-name\> / \<file-name\> *. <br>Pokud klient používá REST API Data Lake Storage, adresa URL má tuto strukturu: * \<storage-account-name\> . DFS.Core.Windows.NET/ \<file-system-name\> / \<file-name\> *. |


## <a name="next-steps"></a>Další kroky

Přečtěte si následující články v dokumentaci k Blob Storage:

- [Filtrovat události Blob Storage](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Doporučené postupy pro využívání Blob Storagech událostí](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

V tomto kurzu jste publikovali události vytvořením nebo odstraněním objektů BLOB v Blob Storage Azure. V dalších kurzech se dozvíte, jak předávané události do cloudu (Azure Event hub nebo Azure IoT Hub): 

- [Přesměrování událostí do Azure Event Gridu](forward-events-event-grid-cloud.md)
- [Přesměrování událostí do Azure IoT Hubu](forward-events-iothub.md)
