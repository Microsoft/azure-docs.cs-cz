---
title: Store objekty BLOB bloku v zařízeních – Azure IoT Edge | Dokumentace Microsoftu
description: Seznamte se s funkcemi pro zpracování vrstev a času na živé prostředí, najdete v tématu podporované operace služby Blob Storage a připojte se k účtu úložiště objektů BLOB.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c90a0351c8c71f4fcafa58a422cc3566a0b29b03
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850093"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dat na hraničních zařízeních s Azure Blob Storage na hraničních zařízeních IoT (preview)

Poskytuje úložiště objektů Blob v Azure na hraničních zařízeních IoT [objektů blob bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) řešení úložiště na hraničních zařízeních. Modul úložiště objektů blob na vašem zařízení IoT Edge se chová jako služby objektů blob bloku Azure, ale objekty BLOB bloku se ukládají místně na vašem zařízení IoT Edge. Získat přístup pomocí stejných metod, sady SDK služby Azure storage BLOB nebo blokovat volání rozhraní API objektů blob, které jste už zvyklí.

Tento modul se dodává s funkcemi **deviceToCloudUpload** a **deviceAutoDelete** .
> [!NOTE]
> Azure Blob Storage na hraničních zařízeních IoT je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podívejte se na video s rychlým Úvodem
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** je konfigurovatelná funkce, která umožňuje automaticky nahrát data z místního úložiště objektů blob do Azure s podporou připojení k Internetu. Umožňuje:

- Zapněte nebo vypněte funkci deviceToCloudUpload.
- Vyberte pořadí, ve kterém se data zkopírují do Azure, jako je NewestFirst nebo OldestFirst.
- Zadejte účet Azure Storage, na který chcete data nahrála.
- Zadejte kontejnery, které chcete nahrát do Azure. Tento modul umožňuje zadat název zdrojového i cílového kontejneru.
- Volba možnosti Odstranit objekty blob hned po dokončení nahrávání do cloudového úložiště
- Proveďte úplné nahrání objektu BLOB `Put Blob` (pomocí operace) a nahrávání na úrovni `Put Block` bloku `Put Block List` (pomocí operací a).

Tento modul používá nahrávání na úrovni bloku, když se objekt BLOB skládá z bloků. Tady jsou některé z běžných scénářů:

- Vaše aplikace aktualizuje některé bloky dříve nahraného objektu blob, tento modul nahraje pouze aktualizované bloky, nikoli celý objekt BLOB.
- Modul odesílá objekt BLOB a připojení k Internetu se ukončí, když se znovu nahrává, nahraje jenom zbývající bloky a ne celý objekt BLOB.

Pokud během nahrávání objektu BLOB dojde k neočekávanému ukončení procesu (například výpadku napájení), nahrají se znovu všechny bloky, které byly v důsledku nahrávání nahrány, když se modul vrátí do režimu online.

**deviceAutoDelete** je konfigurovatelná funkce, ve které modul automaticky odstraní objekty BLOB z místního úložiště po uplynutí zadané doby trvání (měřeno v minutách). Umožňuje:

- Zapněte nebo vypněte funkci deviceAutoDelete.
- Zadejte dobu v minutách (deleteAfterMinutes), po jejímž uplynutí budou objekty blob automaticky odstraněny.
- Vyberte možnost zachovat objekt BLOB při nahrávání, pokud hodnota deleteAfterMinutes vyprší.

Vhodné příklady pro použití tohoto modulu jsou situace, kdy data jako videa, obrázky, finanční data, ústavní data nebo libovolná data, která je třeba uložit místně, mohou být později zpracována místně nebo převedena do cloudu.

V tomto článku se dozvíte o konceptech souvisejících s Azure Blob Storage v kontejneru IoT Edge, na kterém běží služba blob na zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

- Pomocí postupu v rychlém startu pro zařízení se systémem [Linux](quickstart-linux.md) nebo [Windows](quickstart.md)můžete použít vývojový počítač nebo virtuální počítač jako zařízení IoT Edge.

- Azure Blob Storage na modul IoT Edge podporuje následující konfigurace zařízení:

  | Operační systém | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian stretch | Ne | Ano | Ne |  
  | Ubuntu Server 16.04 | Ano | Ne | Ano (k dispozici pro [instalaci](how-to-install-iot-edge-linux-arm.md#install-a-specific-version) s [Azure IoT Edge 1.0.8-RC1 a novější](https://github.com/Azure/azure-iotedge/releases)) |
  | Ubuntu Server 18.04 | Ano | Ne | Ano (k dispozici pro [instalaci](how-to-install-iot-edge-linux-arm.md#install-a-specific-version) s [Azure IoT Edge 1.0.8-RC1 a novější](https://github.com/Azure/azure-iotedge/releases)) |
  | Windows 10 IoT Enterprise, Build 17763 | Ano | Ne | Ne |
  | Windows Server 2019, Build 17763 | Ano | Ne | Ne |
  

Cloudové prostředky:

[IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>vlastnosti deviceToCloudUpload a deviceAutoDelete

Pomocí požadovaných vlastností nastavte deviceToCloudUploadProperties a deviceAutoDeleteProperties. Dají se nastavit během nasazování nebo později změnit úpravou modulu bez nutnosti opětovného nasazení. Doporučujeme, abyste zkontrolovali, jestli je modul `reported configuration` " `configurationValidation` nevlákenný", a aby se správně rozšířily hodnoty.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Název tohoto nastavení je`deviceToCloudUploadProperties`

| Pole | Možné hodnoty | Vysvětlení | Proměnná prostředí |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Ve výchozím nastavení je nastavená `false`na, pokud ho chcete zapnout v nastavení na`true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Umožňuje zvolit pořadí, ve kterém se data zkopírují do Azure. Ve výchozím nastavení je nastaveno na `OldestFirst`. Pořadí určuje čas poslední změny objektu BLOB. | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`je připojovací řetězec, který umožňuje určit účet Azure Storage, na který chcete data nahráli. Zadejte `Azure Storage Account Name`, `Azure Storage Account Key`, .`End point suffix` Přidejte odpovídající EndpointSuffix z Azure, kam se budou data nahrávat, se liší pro globální Azure, státní správu Azure a Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umožňuje zadat názvy kontejnerů, které chcete nahrát do Azure. Tento modul umožňuje zadat název zdrojového i cílového kontejneru. Pokud nezadáte název cílového kontejneru, automaticky se přiřadí název kontejneru jako `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Můžete vytvořit řetězce šablon pro název cílového kontejneru, podívejte se do sloupce možné hodnoty. <br>*% h-> IoT Hub název (3-50 znaků). <br>*% d – > IoT Edge ID zařízení (1 až 129 znaků). <br>*% m-> název modulu (1 až 64 znaků). <br>*% c-> název zdrojového kontejneru (3 až 63 znaků). <br><br>Maximální velikost názvu kontejneru je 63 znaků a při automatickém přiřazování názvu cílového kontejneru, pokud velikost kontejneru překročí 63 znaků, se všechny oddíly (IoTHubName, IotEdgeDeviceID, Module, SourceContainerName) oříznou na 15. písmena. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Ve výchozím nastavení je nastaveno na `false`. Když je nastavená na `true`, automaticky se odstraní data po dokončení nahrávání do cloudového úložiště. | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Název tohoto nastavení je`deviceAutoDeleteProperties`

| Pole | Možné hodnoty | Vysvětlení | Proměnná prostředí |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Ve výchozím nastavení je nastavená `false`na, pokud ho chcete zapnout v nastavení na`true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Zadejte čas v minutách. Modul automaticky odstraní objekty BLOB z místního úložiště, jakmile vyprší platnost této hodnoty. | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Ve výchozím nastavení se nastaví na `true`a při vypršení platnosti deleteAfterMinutes zůstane objekt BLOB při nahrávání do cloudového úložiště. Můžete ho nastavit na `false` a tato data budou odstraněna, jakmile deleteAfterMinutes vyprší platnost. Poznámka: Aby tato vlastnost fungovala uploadOn, musí být nastavená na true.| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Konfigurace souborů protokolu

Informace o konfiguraci souborů protokolu pro váš modul najdete v tématu věnovaném osvědčeným postupům pro [produkční](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)prostředí.

## <a name="connect-to-your-blob-storage-module"></a>Připojení k modulu úložiště objektů blob

Můžete použít název účtu a klíč účtu, že jste nakonfigurovali pro modul pro přístup k úložišti objektů blob na vašem zařízení IoT Edge.

Zadejte zařízení IoT Edge jako koncový bod objektu blob pro jakékoli úložiště požadavků, které můžete provádět. Je možné [vytvoření připojovacího řetězce pro koncový bod explicitního úložiště](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) pomocí informací o zařízení IoT Edge a název účtu, který jste nakonfigurovali.

- Pro moduly, které jsou nasazené na stejném zařízení, ve kterém je spuštěná služba Azure Blob Storage v IoT Edge modulu, je koncový `http://<module name>:11002/<account name>`bod objektu BLOB:.
- Pro externí moduly nebo aplikace, které jsou spuštěné na jiném zařízení, než na kterém běží Azure Blob Storage v IoT Edgem, pak v závislosti na nastavení sítě, aby datový přenos z externího modulu nebo aplikace mohl kontaktovat zařízení. spuštění služby Azure Blob Storage v modulu IoT Edge, koncový bod objektu BLOB je jedním z těchto:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Ukázky pro rychlý Start pro Azure Blob Storage

Dokumentace k Azure Blob Storage obsahuje ukázkový kód pro rychlý Start v několika jazycích. Tyto ukázky můžete použít k otestování Azure Blob Storage na IoT Edge změnou koncového bodu objektu BLOB tak, aby se připojil k vašemu místnímu modulu úložiště objektů BLOB.

Následující ukázky pro rychlý Start používají jazyky, které jsou podporované také nástrojem IoT Edge, takže je můžete nasadit jako IoT Edge moduly společně s modulem úložiště objektů BLOB:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Připojte se k místnímu úložišti pomocí Průzkumník služby Azure Storage

Pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) se můžete připojit k místnímu účtu úložiště.

1. Stažení a instalace Průzkumník služby Azure Storage

1. Připojení k Azure Storage pomocí připojovacího řetězce

1. Zadejte připojovací řetězec:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Projděte si postup, kterým se připojíte.

1. Vytvoření kontejneru v účtu místního úložiště

1. Začněte nahrávat soubory jako objekty blob bloku.
   > [!NOTE]
   > Tento modul nepodporuje objekty blob stránky.

1. Můžete se rozhodnout připojit účty služby Azure Storage, do kterých data nahráváte. Poskytuje jedno zobrazení pro účet místního úložiště i pro účet Azure Storage.

## <a name="supported-storage-operations"></a>Operace úložiště podporuje

Moduly BLOB Storage v IoT Edge používají stejné sady Azure Storage SDK a jsou konzistentní s verzí 2017-04-17 rozhraní API Azure Storage pro koncové body objektů blob bloku. Pozdějších verzích jsou závislé na požadavky zákazníků.

Vzhledem k tomu, že služba Azure Blob Storage v IoT Edge nepodporuje všechny operace Azure Blob Storage, v této části je uveden seznam jejich stavů.

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

- Blok vložení
- Uvést a získat seznam blokovaných položek

Nepodporovaný:

- Vložit blok z adresy URL

## <a name="release-notes"></a>Poznámky k verzi

Tady jsou [poznámky k verzi v Docker Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) pro tento modul.

## <a name="feedback"></a>Zpětná vazba

Váš názor je důležitý pro to, abychom tento modul a jeho funkce byly užitečné a snadno použitelné. Sdílejte prosím svůj názor a dejte nám vědět, jak můžeme vylepšit.

Můžete nás kontaktovat naabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Další postup

Další informace o [nasazení Azure Blob Storage v IoT Edge](how-to-deploy-blob.md)
