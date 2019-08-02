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
ms.openlocfilehash: 5932d51ecaca3c827ae6de268711c7f4d1b28d0a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640650"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dat na hraničních zařízeních s Azure Blob Storage na hraničních zařízeních IoT (preview)

Poskytuje úložiště objektů Blob v Azure na hraničních zařízeních IoT [objektů blob bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) řešení úložiště na hraničních zařízeních. Modul BLOB Storage v zařízení IoT Edge se chová jako služba Azure Block BLOB Service, s výjimkou toho, že objekty blob bloku se ukládají místně na vaše IoT Edge zařízení. Získat přístup pomocí stejných metod, sady SDK služby Azure storage BLOB nebo blokovat volání rozhraní API objektů blob, které jste už zvyklí. V tomto článku se dozvíte o konceptech souvisejících s Azure Blob Storage v kontejneru IoT Edge, na kterém běží služba blob na zařízení IoT Edge.

Tento modul je užitečný ve scénářích, kdy je potřeba ukládat data místně, dokud je nebudete moct zpracovat nebo přenést do cloudu. Tato data můžou být videa, obrázky, finanční údaje, ústavní data nebo jiná nestrukturovaná data.

> [!NOTE]
> Azure Blob Storage na hraničních zařízeních IoT je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podívejte se na video s rychlým Úvodem
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

Tento modul se dodává s funkcemi **deviceToCloudUpload** a **deviceAutoDelete** .

**deviceToCloudUpload** je konfigurovatelná funkce. Tato funkce automaticky nahraje data z místního úložiště objektů blob do Azure s možností občasné podpory připojení k Internetu. Umožňuje:

- Zapněte nebo vypněte funkci deviceToCloudUpload.
- Vyberte pořadí, ve kterém se data zkopírují do Azure, jako je NewestFirst nebo OldestFirst.
- Zadejte účet Azure Storage, na který chcete data nahrála.
- Zadejte kontejnery, které chcete nahrát do Azure. Tento modul umožňuje zadat název zdrojového i cílového kontejneru.
- Volba možnosti Odstranit objekty blob hned po dokončení nahrávání do cloudového úložiště
- Proveďte úplné nahrání objektu BLOB `Put Blob` (pomocí operace) a nahrávání na úrovni `Put Block` bloku `Put Block List` (pomocí operací a).

Tento modul používá nahrávání na úrovni bloku, když se objekt BLOB skládá z bloků. Tady jsou některé z běžných scénářů:

- Vaše aplikace aktualizuje některé bloky dříve nahraného objektu blob, tento modul nahraje pouze aktualizované bloky, nikoli celý objekt BLOB.
- Modul odesílá objekt BLOB a připojení k Internetu se ukončí, když se znovu nahrává, nahraje jenom zbývající bloky a ne celý objekt BLOB.

Pokud během nahrávání objektu BLOB dojde k neočekávanému ukončení procesu (například výpadek napájení), všechny bloky, které byly v důsledku nahrávání, se nahrají znovu, až se modul vrátí do režimu online.

**deviceAutoDelete** je konfigurovatelná funkce. Tato funkce automaticky odstraní objekty BLOB z místního úložiště po uplynutí zadané doby trvání (měřeno v minutách). Umožňuje:

- Zapněte nebo vypněte funkci deviceAutoDelete.
- Zadejte dobu v minutách (deleteAfterMinutes), po jejímž uplynutí budou objekty blob automaticky odstraněny.
- Vyberte možnost zachovat objekt BLOB při jeho nahrávání, pokud hodnota deleteAfterMinutes vyprší.


## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

- Pomocí postupu v rychlém startu pro zařízení se systémem [Linux](quickstart-linux.md) nebo [Windows](quickstart.md)můžete použít vývojový počítač nebo virtuální počítač jako zařízení IoT Edge.

- Azure Blob Storage na modul IoT Edge podporuje následující konfigurace zařízení:

  | Operační systém | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian stretch | Ne | Ano | Ne |  
  | Ubuntu Server 16.04 | Ano | Ne | Ano |
  | Ubuntu Server 18.04 | Ano | Ne | Ano |
  | Windows 10 IoT Enterprise, Build 17763 | Ano | Ne | Ne |
  | Windows Server 2019, Build 17763 | Ano | Ne | Ne |
  

Cloudové prostředky:

[IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>vlastnosti deviceToCloudUpload a deviceAutoDelete

Pomocí požadovaných vlastností modulu nastavte **deviceToCloudUploadProperties** a **deviceAutoDeleteProperties**. Požadované vlastnosti lze nastavit nebo později změnit úpravou modulu bez nutnosti opětovného nasazení. Doporučujeme, abyste zkontrolovali, jestli je modul `reported configuration` " `configurationValidation` nevlákenný", a aby se správně rozšířily hodnoty.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Název tohoto nastavení je`deviceToCloudUploadProperties`

| Pole | Možné hodnoty | Vysvětlení | Proměnná prostředí |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Nastaveno na `false` výchozí hodnotu. Pokud chcete funkci zapnout, nastavte toto pole na `true`. | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Umožňuje zvolit pořadí, ve kterém se data zkopírují do Azure. Nastaveno na `OldestFirst` výchozí hodnotu. Pořadí určuje čas poslední změny objektu BLOB. | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`je připojovací řetězec, který umožňuje určit účet úložiště, na který chcete data nahráli. Zadejte `Azure Storage Account Name`, `Azure Storage Account Key`, .`End point suffix` Přidejte odpovídající EndpointSuffix z Azure, kam se budou data nahrávat, se liší pro globální Azure, státní správu Azure a Microsoft Azure Stack. <br><br> Sem můžete zadat připojovací řetězec SAS Azure Storage. Ale po vypršení platnosti musíte tuto vlastnost aktualizovat.  | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umožňuje zadat názvy kontejnerů, které chcete nahrát do Azure. Tento modul umožňuje zadat název zdrojového i cílového kontejneru. Pokud nezadáte název cílového kontejneru, automaticky se přiřadí název kontejneru jako `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Můžete vytvořit řetězce šablon pro název cílového kontejneru, podívejte se do sloupce možné hodnoty. <br>*% h-> IoT Hub název (3-50 znaků). <br>*% d – > IoT Edge ID zařízení (1 až 129 znaků). <br>*% m-> název modulu (1 až 64 znaků). <br>*% c-> název zdrojového kontejneru (3 až 63 znaků). <br><br>Maximální velikost názvu kontejneru je 63 znaků a při automatickém přiřazování názvu cílového kontejneru, pokud velikost kontejneru překročí 63 znaků, se všechny oddíly (IoTHubName, IotEdgeDeviceID, Module, SourceContainerName) oříznou na 15. písmena. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Nastaveno na `false` výchozí hodnotu. Když je nastavená na `true`, automaticky se odstraní data po dokončení nahrávání do cloudového úložiště. | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Název tohoto nastavení je`deviceAutoDeleteProperties`

| Pole | Možné hodnoty | Vysvětlení | Proměnná prostředí |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Nastaveno na `false` výchozí hodnotu. Pokud chcete funkci zapnout, nastavte toto pole na `true`. | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Zadejte čas v minutách. Modul automaticky odstraní objekty BLOB z místního úložiště, jakmile vyprší platnost této hodnoty. | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Ve výchozím nastavení se nastaví na `true`a při vypršení platnosti deleteAfterMinutes zůstane objekt BLOB při nahrávání do cloudového úložiště. Můžete ho nastavit na `false` a tato data budou odstraněna, jakmile deleteAfterMinutes vyprší platnost. Poznámka: Aby tato vlastnost fungovala uploadOn, musí být nastavená na true.| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="using-smb-share-as-your-local-storage"></a>Použití sdílené složky SMB jako místního úložiště
Pokud nasadíte kontejner Windows tohoto modulu na hostitele Windows, můžete jako cestu k místnímu úložišti zadat sdílenou složku SMB.
Můžete spustit `New-SmbGlobalMapping` příkaz prostředí PowerShell pro místní mapování sdílené složky SMB na zařízení IoT s Windows. Ujistěte se, že zařízení IoT může číst a zapisovat do vzdálené sdílené složky protokolu SMB.

Níže jsou uvedené kroky konfigurace:
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
Příklad: <br>
`$creds = Get-Credentials` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

Tento příkaz použije přihlašovací údaje k ověření u vzdáleného serveru SMB. Pak namapujte cestu vzdálené sdílené složky na G: písmeno jednotky (může to být jakékoli jiné dostupné písmeno jednotky). Zařízení IoT teď má datový svazek namapovaný na cestu na jednotce G:. 

Pro nasazení `<storage directory bind>` může být hodnota **G:/ContainerData: C:/BlobRoot**.

## <a name="configure-log-files"></a>Konfigurace souborů protokolu

Informace o konfiguraci souborů protokolu pro váš modul najdete v tématu věnovaném osvědčeným postupům pro [produkční](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)prostředí.

## <a name="connect-to-your-blob-storage-module"></a>Připojení k modulu úložiště objektů blob

Můžete použít název účtu a klíč účtu, že jste nakonfigurovali pro modul pro přístup k úložišti objektů blob na vašem zařízení IoT Edge.

Zadejte zařízení IoT Edge jako koncový bod objektu blob pro jakékoli úložiště požadavků, které můžete provádět. Je možné [vytvoření připojovacího řetězce pro koncový bod explicitního úložiště](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) pomocí informací o zařízení IoT Edge a název účtu, který jste nakonfigurovali.

- Pro moduly, které jsou nasazené na stejném zařízení, ve kterém je spuštěná služba Azure Blob Storage v IoT Edge modulu, je koncový `http://<module name>:11002/<account name>`bod objektu BLOB:.
- Pro moduly nebo aplikace běžící na jiném zařízení musíte zvolit správný koncový bod pro vaši síť. V závislosti na nastavení sítě vyberte formát koncového bodu tak, aby přenos dat z externího modulu nebo aplikace mohl kontaktovat zařízení, na kterém běží Azure Blob Storage v modulu IoT Edge. Koncový bod objektu BLOB pro tento scénář je jedním z těchto:
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

1. Můžete také připojit účty úložiště Azure v Průzkumník služby Storage. Tato konfigurace poskytuje jedno zobrazení pro účet místního úložiště i pro účet Azure Storage.

## <a name="supported-storage-operations"></a>Operace úložiště podporuje

Moduly BLOB Storage v IoT Edge používají sady Azure Storage SDK a jsou konzistentní s verzí 2017-04-17 rozhraní API Azure Storage pro koncové body objektů blob bloku. 

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

Přečtěte si, jak [nasadit Azure Blob Storage v IoT Edge](how-to-deploy-blob.md)
