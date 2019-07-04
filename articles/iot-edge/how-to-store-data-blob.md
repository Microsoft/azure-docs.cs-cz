---
title: Store objekty BLOB bloku v zařízeních – Azure IoT Edge | Dokumentace Microsoftu
description: Pochopit funkce výběru vrstvy a time-to-live, naleznete v tématu podporované blob storage operace a připojte se k účtu úložiště objektů blob.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: dabaa06e224c6498c0080c4546c04f40e3919bb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448535"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dat na hraničních zařízeních s Azure Blob Storage na hraničních zařízeních IoT (preview)

Poskytuje úložiště objektů Blob v Azure na hraničních zařízeních IoT [objektů blob bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) řešení úložiště na hraničních zařízeních. Modul úložiště objektů blob na vašem zařízení IoT Edge se chová jako služby objektů blob bloku Azure, ale objekty BLOB bloku se ukládají místně na vašem zařízení IoT Edge. Získat přístup pomocí stejných metod, sady SDK služby Azure storage BLOB nebo blokovat volání rozhraní API objektů blob, které jste už zvyklí.

Tento modul se dodává s **deviceToCloudUpload** a **deviceAutoDelete** funkce.
> [!NOTE]
> Azure Blob Storage na hraničních zařízeních IoT je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sledovat video o stručný úvod
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** se dají konfigurovat funkce, které vám umožní automaticky nahrát data z vaší místní blob storage do Azure podporující přerušované připojení k Internetu. To vám umožní:

- Zapnout nebo vypnout funkci deviceToCloudUpload.
- Vyberte pořadí, ve kterém se data kopírují do Azure, jako je NewestFirst nebo OldestFirst.
- Zadejte účet úložiště Azure, na který chcete nahrát data.
- Zadejte kontejnery, které chcete nahrát do Azure. Tento modul umožňuje zadat zdroj a cíl názvy kontejnerů.
- Vyberte možnost odstranit objekty BLOB, okamžitě po dokončení nahrávání do cloudového úložiště
- Úplné odeslání objektu blob (pomocí `Put Blob` operace) a nahrajte na úrovni bloku (pomocí `Put Block` a `Put Block List` operace).

Tento modul používá blokovat nahrávání úrovně, když objekt blob se skládá z bloků. Tady jsou některé běžné scénáře:

- Aplikace aktualizuje některé bloky dříve nahraném objektu blob, tento modul nahraje pouze aktualizovaná bloky a ne celý objekt blob.
- Modul je ukládání objektů blob a připojení k Internetu zmizet, je-li připojení zpět znovu že odešle jenom zbývající bloky a ne celý objekt blob.

Pokud dojde procesu pro neočekávané ukončení (např. výpadku) při odeslání objektu blob, všechny bloky, které byly termín pro nahrávání se nahraje znovu, když modul přejde do režimu online.

**deviceAutoDelete** se dají konfigurovat funkce, kde modulu automaticky odstraní objektů blob z místního úložiště. když vyprší platnost zadaná doba trvání (měří se v minutách). To vám umožní:

- Zapnout nebo vypnout funkci deviceAutoDelete.
- Zadejte dobu v minutách (deleteAfterMinutes), po který objekty BLOB se automaticky odstraní.
- Vyberte možnost zachovat objekt blob, zatímco je nahrávání, když vyprší platnost deleteAfterMinutes hodnotu.

Scénáře, kde data, jako jsou videa, obrázky, daty o financování, data nemocnici nebo všechna data, která musí být uložená místně, později, která by mohla zpracovat místně nebo do cloudu přenáší jsou dobrými příklady použití tohoto modulu.

Tento článek vysvětluje koncepty související s Azure Blob Storage na hraničních zařízeních IoT kontejneru, na kterém běží služba objektů blob na vašem zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

- Vývojovém počítači nebo virtuální počítač můžete použít jako zařízení IoT Edge podle pokynů v tomto rychlém startu pro [Linux](quickstart-linux.md) nebo [zařízení Windows](quickstart.md).

- Azure Blob Storage na modul IoT Edge podporuje následující konfigurace zařízení:

  | Operační systém | Architektura |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Enterprise | AMD64 |
  | Windows Server. 2019 | AMD64 |
  | Raspbian stretch | ARM32 |

Cloudové prostředky:

[IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Vlastnosti deviceToCloudUpload a deviceAutoDelete

Slouží k nastavení deviceToCloudUploadProperties a deviceAutoDeleteProperties požadované vlastnosti. Může být sadě během nasazování nebo později změnit úpravou dvojčete modulu, aniž by bylo nutné znovu nasadit. Doporučujeme zkontrolovat "Dvojčete modulu" pro `reported configuration` a `configurationValidation` k Ujistěte se, že se správně rozšíří hodnoty.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Název tohoto nastavení je `deviceToCloudUploadProperties`

| Pole | Možné hodnoty | Vysvětlení | Proměnná prostředí |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Ve výchozím nastavení nastavena na `false`, pokud chcete, chcete-li ho na hodnotu `true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst OldestFirst | Umožňuje vybrat pořadí, ve kterém se data kopírují do Azure. Ve výchozím nastavení nastavena na `OldestFirst`. Pořadí je určeno čas poslední změny objektu Blob | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` připojovací řetězec, který vám umožní určit účet Azure Storage, do kterého chcete data uložit. Zadejte `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Přidejte příslušné EndpointSuffix Azure kde bude nahrávaných dat, se mění globální Azure, Government Azure a Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umožňuje zadat názvy kontejnerů, které chcete nahrát do Azure. Tento modul umožňuje zadat zdroj a cíl názvy kontejnerů. Pokud nezadáte název cílového kontejneru, se automaticky přiřadí název kontejneru jako `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Můžete vytvořit šablony řetězce pro název cílového kontejneru, podívejte se na sloupec možných hodnot. <br>* %h -> název centra IoT (3 až 50 znaků). <br>* %d -> ID zařízení IoT Edge (1 až 129 znaků). <br>* %m -> název modulu (1 až 64 znaků). <br>* %c -> velikost zdrojového kontejneru (3 až 63 znaků). <br><br>Maximální velikost název kontejneru je 63 znaků při automaticky přiřadí název cílového kontejneru Pokud velikost kontejneru je větší než 63 znaků, které budou trim každý oddíl (IoTHubName IotEdgeDeviceID, název modulu, SourceContainerName) 15 znaky. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Ve výchozím nastavení nastavena na `false`. Když je nastaveno na `true`, automaticky odstraní data až po dokončení nahrávání do cloudového úložiště | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Název tohoto nastavení je `deviceAutoDeleteProperties`

| Pole | Možné hodnoty | Vysvětlení | Proměnná prostředí |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Ve výchozím nastavení nastavena na `false`, pokud chcete, chcete-li ho na hodnotu `true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Zadejte dobu v minutách. Modul se automaticky odstraní objektů blob z místního úložiště po vypršení této hodnoty | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Ve výchozím nastavení nastavena na `true`, a zachová objekt blob, zatímco je nahrání do cloudového úložiště, když vyprší platnost deleteAfterMinutes. Můžete nastavit na `false` a odstraní data, jakmile vyprší platnost deleteAfterMinutes. Poznámka: Pro tuto vlastnost na práci uploadOn měl být nastaven na hodnotu true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Konfigurace souborů protokolu

Informace o konfiguraci soubory protokolu pro modul, informace najdete v těchto [osvědčené postupy produkční](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Připojení k modulu úložiště objektů blob

Můžete použít název účtu a klíč účtu, že jste nakonfigurovali pro modul pro přístup k úložišti objektů blob na vašem zařízení IoT Edge.

Zadejte zařízení IoT Edge jako koncový bod objektu blob pro jakékoli úložiště požadavků, které můžete provádět. Je možné [vytvoření připojovacího řetězce pro koncový bod explicitního úložiště](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) pomocí informací o zařízení IoT Edge a název účtu, který jste nakonfigurovali.

- Pro moduly, které jsou nasazené na stejném zařízení jako se spuštěným systémem Azure Blob Storage na modul IoT Edge, je koncový bod služby blob: `http://<module name>:11002/<account name>`.
- Pro externích modulech nebo aplikace, která běží na jiném zařízení, než ve kterém je spuštěný Azure Blob Storage na modul IoT Edge, pak v závislosti na nastavení sítě tak, aby přenosy dat z externího modulu nebo aplikaci můžete spojit se zařízením zprovoznění Azure Blob Storage na modul IoT Edge, je koncový bod služby blob mezi:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Ukázky rychlý start Azure Blob Storage

Dokumentace ke službě Azure Blob Storage zahrnuje ukázkový kód tohoto rychlého startu v několika jazycích. Můžete spustit tyto ukázky k otestování Azure Blob Storage na hraničních zařízeních IoT tak, že změníte koncový bod objektu blob pro připojení k modulu Místní objekt blob úložiště.

Následující ukázky rychlý start použít jazyky, které jsou také podporovány pomocí IoT Edge, můžete je nasadit jako moduly IoT Edge spolu s modulu služby blob storage:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Připojení k místní úložiště pomocí Průzkumníka služby Azure Storage

Můžete použít [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) pro připojení k účtu místního úložiště.

1. Stažení a instalace Průzkumníka služby Azure Storage

1. Připojení ke službě Azure Storage pomocí připojovacího řetězce

1. Zadejte připojovací řetězec: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Projděte si kroky pro připojení.

1. Vytvoření kontejneru v účtu místní úložiště

1. Spusťte nahrávání souborů jako objekty BLOB bloku.
   > [!NOTE]
   > Tento modul nepodporuje objekty BLOB stránky.

1. Můžete připojit své účty úložiště Azure, pokud nahráváte data. Poskytuje jedno zobrazení pro účet místního úložiště a účet úložiště Azure

## <a name="supported-storage-operations"></a>Operace úložiště podporuje

Moduly úložiště objektů BLOB ve službě IoT Edge používat stejné sady SDK Azure Storage a jsou konzistentní s 2017-04-17 verzi rozhraní API služby Azure Storage pro koncové body objektu blob bloku. Pozdějších verzích jsou závislé na požadavky zákazníků.

Protože ne všechny operace úložiště objektů Blob v Azure jsou podporovány službou Azure Blob Storage na hraničních zařízeních IoT, tato část uvádí stav všech.

### <a name="account"></a>Účet

Podporovány:

- Výpis kontejnerů

Nepodporovaný:

- Získání a nastavení vlastností služby blob
- Žádost o předběžné objektů blob
- Získání statistiky služby blob
- Získejte informace o účtu

### <a name="containers"></a>Containers

Podporovány:

- Vytvoření a odstranění kontejneru
- Získání kontejneru vlastností a metadat
- Výpis objektů blob
- Získání a nastavení ACL kontejneru
- Metadata kontejneru sady

Nepodporovaný:

- Zapůjčení kontejneru

### <a name="blobs"></a>Objekty blob

Podporovány:

- Vložit, získání a odstranění objektu blob
- GET a set vlastnosti objektu blob
- Získání a nastavení metadata objektu blob

Nepodporovaný:

- Zapůjčení objektu blob
- Vytvoření snímku objektu blob
- Zkopírujte a přerušení objekt blob kopírování
- Obnovit objekt blob
- Nastavení úrovně objektu blob

### <a name="block-blobs"></a>Objekty blob bloku

Podporovány:

- Vložit blok
- Uvést a získat seznam blokovaných položek

Nepodporovaný:

- Vložit blok z adresy URL

## <a name="release-notes"></a>Poznámky k verzi

Tady jsou [poznámky v docker hubu](https://hub.docker.com/_/microsoft-azure-blob-storage) pro tento modul.

## <a name="feedback"></a>Váš názor

Váš názor je důležitý pro nás, aby tento modul a jeho funkcí užitečné a snadno se používá. Sdělte nám prosím svůj názor a dejte nám vědět, jak můžeme vylepšit.

Můžete oslovit nás na adrese absiotfeedback@microsoft.com

## <a name="next-steps"></a>Další postup

Další informace o [nasazení služby Azure Blob Storage na hraničních zařízeních IoT](how-to-deploy-blob.md)
