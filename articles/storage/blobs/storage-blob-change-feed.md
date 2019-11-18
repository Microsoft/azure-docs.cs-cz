---
title: Změnit informační kanál ve službě Azure Blob Storage (Preview) | Microsoft Docs
description: Přečtěte si o protokolech Change feed v Azure Blob Storage a o tom, jak je používat.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: f48c8712a2f4fbd69db7de5247e3293ad57ae1e6
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112836"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Změna podpory kanálu v Azure Blob Storage (Preview)

Účelem kanálu změn je poskytnout transakční protokoly všech změn, ke kterým dojde u objektů BLOB a metadat objektů BLOB ve vašem účtu úložiště. Kanál změny poskytuje **seřazené**, **zaručené**, **odolné**, **neměnné**, protokol jen **pro čtení** těchto změn. Klientské aplikace mohou tyto protokoly kdykoli číst, a to buď ve streamování, nebo v režimu dávky. Kanál změn umožňuje vytvářet efektivní a škálovatelná řešení, která zpracovávají události změny, ke kterým dochází v účtu Blob Storage za nízké náklady.

Kanál změn se ukládá jako [objekty blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) ve speciálním kontejneru v účtu úložiště za ceny standardního [objektu BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) . Dobu uchování těchto souborů můžete řídit podle vašich požadavků (viz [podmínky](#conditions) aktuální verze). Události změny se připojují ke kanálu změn jako záznamy ve specifikaci formátu [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) : kompaktní, rychlý a binární formát, který poskytuje bohatou datovou strukturu s vloženým schématem. Tento formát je široce používat v ekosystému Hadoop, Stream Analytics a Azure Data Factory.

Tyto protokoly můžete zpracovat asynchronně, přírůstkově nebo v plném rozsahu. Libovolný počet klientských aplikací může nezávisle číst kanál změn, paralelně a vlastním tempem. Analytické aplikace, jako je například [Apache](https://drill.apache.org/docs/querying-avro-files/) analytics nebo [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) , můžou využívat protokoly přímo jako soubory Avro, které vám umožní je zpracovat s vysokou propustností a bez nutnosti psát vlastní aplikaci.

Podpora změny kanálu je vhodná pro scénáře, které zpracovávají data na základě objektů, které se změnily. Například aplikace mohou:

  - Aktualizujte sekundární index, proveďte synchronizaci s mezipamětí, vyhledávacím modulem nebo jakýmkoli jiným scénářem správy obsahu.
  
  - Extrahujte přehledy obchodních analýz a metriky na základě změn, ke kterým dojde u vašich objektů, a to buď v datových proudech, nebo v režimu dávky.
  
  - Umožňuje ukládat, auditovat a analyzovat změny vašich objektů, a to za jakékoli časové období, kvůli zabezpečení, dodržování předpisů nebo inteligentní správě podnikových dat.

  - Sestavujte řešení pro zálohování, zrcadlení nebo replikaci stavu objektů ve vašem účtu pro správu havárií nebo dodržování předpisů.

  - Vytvářejte propojené kanály aplikací, které reagují na změny událostí nebo naplánujte provádění na základě vytvořeného nebo změněného objektu.

> [!NOTE]
> [Události BLOB Storage](storage-blob-event-overview.md) poskytují jednorázové události v reálném čase, které umožňují vašim Azure Functionsám nebo aplikacím reagovat na změny, ke kterým dojde v objektu BLOB. Kanál změn poskytuje trvalý a seřazený model protokolu změn. Změny v informačním kanálu jsou ve vašem kanálu změn k dispozici během několika minut od změny. Pokud vaše aplikace musí reagovat na události, které jsou mnohem rychlejší, zvažte místo toho použití [BLOB Storagech událostí](storage-blob-event-overview.md) . Události Blob Storage umožňují vašim Azure Functionsám nebo aplikacím reagovat na jednotlivé události v reálném čase.

## <a name="enable-and-disable-the-change-feed"></a>Povolení a zákaz kanálu změn

Aby bylo možné začít zachytávání změn, je nutné povolit kanál změn v účtu úložiště. Zakáže kanál změn, aby se zakázaly zachytávání změn. Změny můžete povolit a zakázat pomocí Azure Resource Manager šablon na portálu nebo PowerShellu.

Při povolování kanálu změn je potřeba mít na paměti několik věcí.

- V každém účtu úložiště, který je uložený v kontejneru **$blobchangefeed** , existuje jenom jeden kanál změn pro službu BLOB Service.

- Změny jsou zachyceny pouze na úrovni služby BLOB Service.

- Kanál změn zachycuje *všechny* změny pro všechny dostupné události, ke kterým došlo na účtu. Klientské aplikace mohou podle potřeby vyfiltrovat typy událostí. (Podívejte se na [podmínky](#conditions) aktuální verze).

- Kanál změn můžou povolit jenom účty úložiště GPv2 a BLOB Storage. Účty úložiště GPv1, účty Premium BlockBlobStorage a hierarchické účty s povoleným oborem názvů se momentálně nepodporují.

> [!IMPORTANT]
> Kanál změn je ve verzi Public Preview a je dostupný v oblastech **westcentralus** a **westus2** . Viz část [podmínky](#conditions) tohoto článku. Pokud se chcete zaregistrovat ve verzi Preview, přečtěte si část [registrace předplatného](#register) v tomto článku. Předtím, než budete moci povolit kanál změn v účtech úložiště, je nutné zaregistrovat své předplatné.

### <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Nasazení šablony pomocí Azure Portal:

1. V Azure Portal klikněte na možnost **vytvořit prostředek**.

2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.

3. Zvolte **template Deployment**, zvolte **vytvořit**a potom **v editoru zvolte sestavit vlastní šablonu**.

4. V editoru šablon vložte následující kód JSON. Zástupný text `<accountName>` nahraďte názvem svého účtu úložiště.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Zvolte tlačítko **Save (Uložit** ), zadejte skupinu prostředků účtu a pak zvolte tlačítko **koupit** a nasaďte šablonu a povolte kanál změn.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nasazení šablony pomocí prostředí PowerShell:

1. Nainstalujte nejnovější PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zavřete a znovu otevřete konzolu PowerShellu.

3. Nainstalujte modul pro zobrazení **AZ. Storage** Preview.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkaz a postupujte podle pokynů na obrazovce pokynů k ověření.

   ```powershell
   Connect-AzAccount
   ```

5. Povolte pro svůj účet úložiště kanál změn.

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

---

## <a name="understand-change-feed-organization"></a>Porozumění organizaci pro změnu kanálu

<a id="segment-index"></a>

### <a name="segments"></a>Segmenty

Kanál změn je protokol změn, které jsou uspořádány do **hodinových** *segmentů* , ale připojeny k a aktualizovány každých několik minut. Tyto segmenty jsou vytvořeny pouze v případě, že dojde k událostem změny objektů blob, ke kterým dojde v této hodinové Tím umožníte klientským aplikacím využívat změny, ke kterým dojde v určitém časovém rozsahu, aniž byste museli prohledávat celý protokol. Další informace najdete v tématu [specifikace](#specifications).

Dostupný hodinový segment kanálu změn je popsán v souboru manifestu, který určuje cesty k souborům kanálu změn pro daný segment. Výpis `$blobchangefeed/idx/segments/` virtuálního adresáře zobrazuje tyto segmenty seřazené podle času. Cesta segmentu popisuje začátek hodinového časového rozsahu, který segment představuje. Pomocí tohoto seznamu můžete odfiltrovat segmenty protokolů, které vás zajímají.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` se automaticky vytvoří, když povolíte kanál změn. Tento soubor můžete bezpečně ignorovat. Je to vždy prázdný inicializační soubor. 

Soubor manifestu segmentu (`meta.json`) zobrazuje cestu souborů kanálu změn pro daný segment ve vlastnosti `chunkFilePaths`. Zde je příklad souboru manifestu segmentu.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Pokud vytvoříte seznam kontejnerů v účtu úložiště, kontejner `$blobchangefeed` se zobrazí až po povolení funkce Change feed na vašem účtu. Než budete moct zobrazit kontejner, budete muset počkat několik minut, než se kanál změn povolí.

<a id="log-files"></a>

### <a name="change-event-records"></a>Změna záznamů událostí

Soubory kanálu změn obsahují řadu záznamů událostí změn. Každý záznam události změny odpovídá jedné změně v individuálním objektu BLOB. Záznamy jsou serializovány a zapsány do souboru pomocí specifikace formátu [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) . Záznamy lze číst pomocí specifikace formátu souboru Avro. K dispozici je několik knihoven pro zpracování souborů v tomto formátu.

Změny souborů kanálu se ukládají ve virtuálním adresáři `$blobchangefeed/log/` jako [doplňovací objekty blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). První soubor kanálu změny v každé cestě bude mít `00000` v názvu souboru (například `00000.avro`). Název každého dalšího souboru protokolu přidaného do této cesty se zvýší o 1 (například: `00001.avro`).

Tady je příklad záznamu události změny ze souboru změnového kanálu převedeného na JSON.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Popis jednotlivých vlastností najdete v tématu [Azure Event Grid schéma událostí pro Blob Storage](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Soubory kanálu změn pro segment se po vytvoření segmentu nezobrazují okamžitě. Doba zpoždění spadá do normálního intervalu latence při publikování kanálu změny, který je během několika minut od změny.

## <a name="consume-the-change-feed"></a>Využití kanálu změn

Kanál změn vytváří několik metadat a souborů protokolů. Tyto soubory se nacházejí v kontejneru **$blobchangefeed** účtu úložiště. 

> [!NOTE]
> V aktuální verzi není kontejner **$blobchangefeed** viditelný v Průzkumník služby Azure Storage nebo Azure Portal. V současné době nemůžete při volání rozhraní ListContainers API zobrazit kontejner $blobchangefeed, ale můžete volat rozhraní ListBlobs API přímo na kontejneru a zobrazit tak objekty blob.

Vaše klientské aplikace mohou používat kanál změn pomocí knihovny Change feed Processor, která je součástí sady SDK pro změnu informačního kanálu. 

Viz [protokol kanálu změny procesu v Azure Blob Storage](storage-blob-change-feed-how-to.md).

<a id="specifications"></a>

## <a name="specifications"></a>Specifikace

- Záznamy událostí změny se připojují jenom ke kanálu změn. Po připojení těchto záznamů jsou neměnné a umístění záznamů je stabilní. Klientské aplikace mohou spravovat svůj vlastní kontrolní bod na pozici pro čtení kanálu změn.

- Záznamy událostí změny se připojují během několika minut od změny. Klientské aplikace se mohou rozhodnout, že budou záznamy spotřebovávat, protože jsou připojeny k přístupu pro streamování nebo hromadně v jakémkoli čase.

- Záznamy událostí změn jsou seřazené podle pořadí změn **na objekt BLOB**. Pořadí změn napříč objekty blob není v Azure Blob Storage definované. Všechny změny v předchozím segmentu jsou před jakýmikoli změnami v následných segmentech.

- Záznamy událostí změn jsou serializovány do souboru protokolu pomocí specifikace formátu [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) .

- Změna záznamů událostí, kde `eventType` má hodnotu `Control` jsou interní systémové záznamy a neodrážejí změnu objektů ve vašem účtu. Tyto záznamy můžete bezpečně ignorovat.

- Hodnoty v kontejneru vlastností `storageDiagnonstics` jsou pouze pro interní použití a nejsou určeny pro použití vaší aplikací. Vaše aplikace by neměly mít na těchto datech smluvní závislost. Tyto vlastnosti můžete bezpečně ignorovat.

- Čas reprezentovaný segmentem je **přibližný** s mezemi po dobu 15 minut. Aby se zajistila spotřeba všech záznamů v určitou dobu, využijte po sobě jdoucí a další hodinový segment.

- Každý segment může mít jiný počet `chunkFilePaths`. Důvodem je interní dělení datového proudu protokolu pro správu propustnosti publikování. Protokolové soubory v každé `chunkFilePath` mají zaručit vzájemně se vylučující objekty BLOB a dají se používat a zpracovávat paralelně, aniž by došlo k porušení pořadí úprav na objekt BLOB během iterace.

- Segmenty jsou spouštěny ve stavu `Publishing`. Po dokončení připojení záznamů do segmentu bude `Finalized`. Soubory protokolu v jakémkoli segmentu, který je datován po datu vlastnosti `LastConsumable` v souboru `$blobchangefeed/meta/Segments.json`, by neměly být aplikací spotřebovány. Tady je příklad vlastnosti `LastConsumable`v souboru `$blobchangefeed/meta/Segments.json`:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>Registrace předplatného (Preview)

Protože je kanál změn jenom ve verzi Public Preview, budete muset zaregistrovat předplatné, aby se funkce používala.

### <a name="register-by-using-powershell"></a>Registrace pomocí PowerShellu

V konzole PowerShellu spusťte tyto příkazy:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registrace pomocí Azure CLI

V Azure Cloud Shell spusťte tyto příkazy:

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Podmínky a známé problémy (Preview)

Tato část popisuje známé problémy a podmínky v současnosti ve verzi Public Preview kanálu změn.
- Pro verzi Preview musíte nejdřív [zaregistrovat předplatné](#register) , abyste mohli povolit kanál změn pro svůj účet úložiště v oblastech westcentralus nebo westus2. 
- Kanál změn zachycuje pouze operace vytvoření, aktualizace, odstranění a kopírování. Aktualizace metadat nejsou aktuálně zachyceny ve verzi Preview.
- Změny záznamů událostí pro jednu změnu se můžou ve vašem kanálu změn objevit více než jednou.
- Ještě nemůžete spravovat dobu života souborů protokolu kanálu změn nastavením časových zásad uchovávání na základě času.
- Vlastnost `url` souboru protokolu je vždy prázdná.
- Vlastnost `LastConsumable` souboru segmentes. JSON neobsahuje seznam velmi prvního segmentu, který dokončí kanál změn. K tomuto problému dochází až po finalizaci prvního segmentu. Všechny následné segmenty po první hodiny jsou přesně zachyceny ve vlastnosti `LastConsumable`.

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Jaký je rozdíl mezi kanálem změny a protokolováním Analýza úložiště?
Kanál změn je optimalizován pro vývoj aplikací, protože v protokolu změn kanálu jsou zaznamenány pouze úspěšné události vytvoření, změny a odstranění objektu BLOB. Protokolování analýz zaznamenává všechny úspěšné a neúspěšné požadavky napříč všemi operacemi, včetně operací čtení a výpisu. Když použijete kanál změn, nemusíte se starat o filtrování šumu v protokolu u velkého účtu transakce a soustředit se jenom na události změny objektu BLOB.

### <a name="should-i-use-change-feed-or-storage-events"></a>Mám použít události změny kanálu nebo úložiště?
Obě funkce můžete využívat jako kanál změn a [události služby Blob Storage](storage-blob-event-overview.md) jsou podobné jako v podstatě a hlavním rozdílem je latence, řazení a ukládání záznamů událostí. Změna kanálu zapisuje záznamy do protokolu změn, které jsou hromadně zapisovány, a přitom zaručuje pořadí operací změny objektů BLOB. Události úložiště jsou vloženy v reálném čase a nemusí být seřazeny. Události změny kanálu jsou trvale uložené v rámci vašeho účtu úložiště, zatímco události úložiště jsou přechodné a spotřebované obslužnou rutinou události, pokud je neuložíte explicitně.

## <a name="next-steps"></a>Další kroky

- Podívejte se na příklad, jak číst kanál změn pomocí klientské aplikace .NET. Viz [protokol kanálu změny procesu v Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Přečtěte si, jak reagovat na události v reálném čase. Viz [reakce na události BLOB Storage](storage-blob-event-overview.md) .
- Přečtěte si další informace o podrobném protokolování pro úspěšné i neúspěšné operace pro všechny požadavky. Viz [protokolování analýzy Azure Storage](../common/storage-analytics-logging.md) .
