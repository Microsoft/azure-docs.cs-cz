---
title: Změnit informační kanál v Azure Blob Storage | Microsoft Docs
description: Přečtěte si o protokolech Change feed v Azure Blob Storage a o tom, jak je používat.
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: c3348356561ea74bb5e0b5bc46fccee1ada82755
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568230"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Změna podpory kanálu v Azure Blob Storage

Účelem kanálu změn je poskytnout transakční protokoly všech změn, ke kterým dojde u objektů BLOB a metadat objektů BLOB ve vašem účtu úložiště. Kanál změny poskytuje **seřazené**, **zaručené**, **odolné**, **neměnné**, protokol jen **pro čtení** těchto změn. Klientské aplikace mohou tyto protokoly kdykoli číst, a to buď ve streamování, nebo v režimu dávky. Kanál změn umožňuje vytvářet efektivní a škálovatelná řešení, která zpracovávají události změny, ke kterým dochází v účtu Blob Storage za nízké náklady.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

Kanál změn se ukládá jako [objekty blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) ve speciálním kontejneru v účtu úložiště za ceny standardního [objektu BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) . Dobu uchování těchto souborů můžete řídit podle vašich požadavků (viz [podmínky](#conditions) aktuální verze). Události změny se připojují ke kanálu změn jako záznamy ve specifikaci formátu [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) : kompaktní, rychlý a binární formát, který poskytuje bohatou datovou strukturu s vloženým schématem. Tento formát se běžně používá v ekosystému Hadoop, Stream Analytics a Azure Data Factory.

Tyto protokoly můžete zpracovat asynchronně, přírůstkově nebo v plném rozsahu. Libovolný počet klientských aplikací může nezávisle číst kanál změn, paralelně a vlastním tempem. Analytické aplikace, jako je například [Apache](https://drill.apache.org/docs/querying-avro-files/) analytics nebo [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) , můžou využívat protokoly přímo jako soubory Avro, které vám umožní je zpracovat s vysokou propustností a bez nutnosti psát vlastní aplikaci.

Podpora změny kanálu je vhodná pro scénáře, které zpracovávají data na základě objektů, které se změnily. Například aplikace mohou:

  - Aktualizace sekundárního indexu, provádění synchronizací s mezipamětí, vyhledávání pomocí vyhledávacího webu nebo jakýkoli jiný scénář správy obsahu
  
  - Extrakce přehledů obchodních analýz a metrik na základě změn objektů, a to buď v datových proudech, nebo v dávkovém režimu
  
  - Ukládání, auditování a analýza změn objektů za libovolné časové období, a to za účelem zabezpečení, dodržování předpisů nebo informací při správě podnikových dat

  - Sestavujte řešení pro zálohování, zrcadlení nebo replikaci stavu objektů ve vašem účtu pro správu havárií nebo dodržování předpisů.

  - Vytvářejte propojené kanály aplikací, které reagují na změny událostí nebo naplánujte provádění na základě vytvořeného nebo změněného objektu.
  
Změna kanálu je požadovaná funkce pro [replikaci objektů](object-replication-overview.md) a [obnovení k určitému bodu v čase pro objekty blob bloku](point-in-time-restore-overview.md).

> [!NOTE]
> Změna kanálu poskytuje trvalý a seřazený model protokolu změn, ke kterým došlo u objektu BLOB. Změny se zapisují a zpřístupňují v protokolu kanálu změn během několika minut od změny. Pokud vaše aplikace musí reagovat na události, které jsou mnohem rychlejší, zvažte místo toho použití [BLOB Storagech událostí](storage-blob-event-overview.md) . [Události BLOB Storage](storage-blob-event-overview.md) poskytují jednorázové události v reálném čase, které umožňují vašim Azure Functionsám nebo aplikacím rychle reagovat na změny, ke kterým dojde v objektu BLOB. 

## <a name="enable-and-disable-the-change-feed"></a>Povolení a zákaz kanálu změn

Aby bylo možné začít zachytávání a zaznamenávání změn, je nutné povolit kanál změn v účtu úložiště. Zakáže kanál změn, aby se zakázaly zachytávání změn. Změny můžete povolit a zakázat pomocí Azure Resource Manager šablon na portálu nebo PowerShellu.

Při povolování kanálu změn je potřeba mít na paměti několik věcí.

- V každém účtu úložiště je k dispozici jenom jeden kanál změn pro službu BLOB Service a je uložený v kontejneru **$blobchangefeed** .

- Změny vytváření, aktualizace a odstraňování se zaznamenávají jenom na úrovni služby BLOB Service.

- Kanál změn zachycuje *všechny* změny pro všechny dostupné události, ke kterým došlo na účtu. Klientské aplikace mohou podle potřeby vyfiltrovat typy událostí. (Podívejte se na [podmínky](#conditions) aktuální verze).

- Kanál změn můžou povolit jenom účty úložiště GPv2 a BLOB Storage. Účty Premium BlockBlobStorage a hierarchické účty s povoleným oborem názvů se momentálně nepodporují. Účty úložiště GPv1 se nepodporují, ale dají se upgradovat na GPv2 bez výpadků, další informace najdete v tématu [upgrade na účet úložiště GPv2](../common/storage-account-upgrade.md) .

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Povolte na svém účtu úložiště změnu kanálu pomocí Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště.

2. Přejděte do možnosti **Ochrana dat** v části **BLOB Service**.

3. Klikněte na možnost **povoleno** v části **objekt pro změnu datového kanálu**.

4. Klikněte na tlačítko **Uložit** a potvrďte nastavení **ochrany dat** .

    ![Snímek obrazovky zobrazující nastavení ochrany dat](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Povolit kanál změn pomocí prostředí PowerShell:

1. Nainstalujte nejnovější PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zavřete a znovu otevřete konzolu PowerShellu.

3. Nainstalujte verzi 2.5.0 nebo novější z modulu **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkazu a podle pokynů na obrazovce proveďte ověření.

   ```powershell
   Connect-AzAccount
   ```

5. Povolte pro svůj účet úložiště kanál změn.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Šablona](#tab/template)
Pomocí šablony Azure Resource Manager můžete povolit kanál změn na svém stávajícím účtu úložiště prostřednictvím Azure Portal:

1. V Azure Portal klikněte na možnost **vytvořit prostředek**.

2. V **části Hledat na Marketplace**zadejte **šablonu Deployment**a potom stiskněte **ENTER**.

3. Zvolte **[nasadit vlastní šablonu](https://portal.azure.com/#create/Microsoft.Template)** a pak **v editoru zvolte sestavit vlastní šablonu**.

4. V editoru šablon vložte následující kód JSON. `<accountName>`Zástupný symbol nahraďte názvem vašeho účtu úložiště.

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

---

## <a name="consume-the-change-feed"></a>Využití kanálu změn

Kanál změn vytváří několik metadat a souborů protokolů. Tyto soubory se nacházejí v kontejneru **$blobchangefeed** účtu úložiště. 

> [!NOTE]
> V aktuální verzi není kontejner **$blobchangefeed** viditelný v Průzkumník služby Azure Storage nebo Azure Portal. V současné době nemůžete při volání rozhraní ListContainers API zobrazit kontejner $blobchangefeed, ale můžete volat rozhraní ListBlobs API přímo na kontejneru a zobrazit tak objekty blob.

Vaše klientské aplikace mohou používat kanál změn pomocí knihovny Change feed Processor, která je součástí sady SDK pro změnu informačního kanálu. 

Viz [protokol kanálu změny procesu v Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Porozumění organizaci pro změnu kanálu

<a id="segment-index"></a>

### <a name="segments"></a>Segmenty

Kanál změn je protokol změn, které jsou uspořádány do **hodinových** *segmentů* , ale byly připojeny k a aktualizovány každých několik minut. Tyto segmenty jsou vytvořeny pouze v případě, že dojde k událostem změny objektů blob, ke kterým dojde v této hodinové Tím umožníte klientským aplikacím využívat změny, ke kterým dojde v určitém časovém rozsahu, aniž byste museli prohledávat celý protokol. Další informace najdete v tématu [specifikace](#specifications).

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
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`Automaticky se vytvoří při povolení kanálu změn. Tento soubor můžete bezpečně ignorovat. Je to vždy prázdný inicializační soubor. 

Soubor manifestu segmentu ( `meta.json` ) zobrazuje cestu souborů kanálu změn pro tento segment ve `chunkFilePaths` Vlastnosti. Zde je příklad souboru manifestu segmentu.

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
> `$blobchangefeed`Kontejner se zobrazí až po povolení funkce změny kanálu na vašem účtu. Než budete moct vytvořit seznam objektů BLOB v kontejneru, budete muset několik minut počkat, než kanál změn povolíte. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Změna záznamů událostí

Soubory kanálu změn obsahují řadu záznamů událostí změn. Každý záznam události změny odpovídá jedné změně v individuálním objektu BLOB. Záznamy jsou serializovány a zapsány do souboru pomocí specifikace formátu [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) . Záznamy lze číst pomocí specifikace formátu souboru Avro. K dispozici je několik knihoven pro zpracování souborů v tomto formátu.

Změny souborů kanálu se ukládají ve `$blobchangefeed/log/` virtuálním adresáři jako [doplňovací objekty blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). První soubor kanálu změny v každé cestě bude mít `00000` název souboru (například `00000.avro` ). Název každého dalšího souboru protokolu přidaného do této cesty se zvýší o 1 (například: `00001.avro` ).

V záznamech kanálu změn jsou zachyceny následující typy událostí:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

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

Popis jednotlivých vlastností najdete v tématu [Azure Event Grid schéma událostí pro Blob Storage](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). Události BlobPropertiesUpdated a BlobSnapshotCreated jsou aktuálně exkluzivní pro změnu kanálu a ještě nejsou podporované pro události Blob Storage.

> [!NOTE]
> Soubory kanálu změn pro segment se po vytvoření segmentu nezobrazují okamžitě. Doba zpoždění spadá do normálního intervalu latence při publikování kanálu změny, který je během několika minut od změny.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikace

- Záznamy událostí změny se připojují jenom ke kanálu změn. Po připojení těchto záznamů jsou neměnné a umístění záznamů je stabilní. Klientské aplikace mohou spravovat svůj vlastní kontrolní bod na pozici pro čtení kanálu změn.

- Záznamy událostí změny se připojují během několika minut od změny. Klientské aplikace se mohou rozhodnout, že budou záznamy spotřebovávat, protože jsou připojeny k přístupu pro streamování nebo hromadně v jakémkoli čase.

- Záznamy událostí změn jsou seřazené podle pořadí změn **na objekt BLOB**. Pořadí změn napříč objekty blob není v Azure Blob Storage definované. Všechny změny v předchozím segmentu jsou před jakýmikoli změnami v následných segmentech.

- Záznamy událostí změn jsou serializovány do souboru protokolu pomocí specifikace formátu [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) .

- Změny záznamů událostí, kde `eventType` má hodnota, `Control` jsou interní systémové záznamy a neodrážejí změnu objektů ve vašem účtu. Tyto záznamy můžete bezpečně ignorovat.

- Hodnoty v `storageDiagnonstics` kontejneru objektů a dat jsou určené pouze pro interní použití a nejsou určeny pro použití vaší aplikací. Vaše aplikace by neměly mít na těchto datech smluvní závislost. Tyto vlastnosti můžete bezpečně ignorovat.

- Čas reprezentovaný segmentem je **přibližný** s mezemi po dobu 15 minut. Aby se zajistila spotřeba všech záznamů v určitou dobu, využijte po sobě jdoucí a další hodinový segment.

- Každý segment může mít jiný počet z `chunkFilePaths` důvodu interního rozdělování datového proudu protokolu pro správu propustnosti publikování. Soubory protokolu v každé z nich `chunkFilePath` jsou zaručené tak, aby obsahovaly vzájemně se vylučující objekty BLOB a je možné je používat a zpracovávat paralelně, aniž by došlo k porušení pořadí úprav na objekt BLOB během iterace.

- Segmenty jsou spouštěny ve `Publishing` stavu. Jakmile se připojí záznamy do segmentu, bude se jednat o `Finalized` . Soubory protokolu v jakémkoli segmentu, který je datován po datu `LastConsumable` vlastnosti v `$blobchangefeed/meta/Segments.json` souboru, by neměly být spotřebované vaší aplikací. Tady je příklad `LastConsumable` vlastnosti v `$blobchangefeed/meta/Segments.json` souboru:

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

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Podmínky a známé problémy

Tato část popisuje známé problémy a podmínky v aktuální verzi kanálu změn. 

- Změny záznamů událostí pro jednu změnu se můžou ve vašem kanálu změn objevit více než jednou.
- Ještě nemůžete spravovat dobu života souborů protokolu kanálu změn nastavením časových zásad uchovávání na základě času a objekty blob nemůžete odstranit.
- `url`Vlastnost souboru protokolu je nyní vždy prázdná.
- `LastConsumable`Vlastnost segments.jsv souboru neobsahuje seznam velmi prvního segmentu, který dokončí kanál změny. K tomuto problému dochází až po finalizaci prvního segmentu. Všechny následné segmenty po první hodiny jsou přesně zachyceny ve `LastConsumable` Vlastnosti.
- V současné době nemůžete při volání rozhraní ListContainers API zobrazit kontejner **$blobchangefeed** a kontejner se nezobrazuje Azure Portal nebo Průzkumník služby Storage. Obsah můžete zobrazit pomocí volání rozhraní ListBlobs API přímo v kontejneru $blobchangefeed.
- Účty úložiště, které dříve iniciovaly [převzetí služeb při selhání účtu](../common/storage-disaster-recovery-guidance.md) , můžou mít problémy se souborem protokolu, který se nezobrazuje. U každého budoucího převzetí služeb při selhání může být soubor protokolu ovlivněný.

## <a name="faq"></a>Časté otázky

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Jaký je rozdíl mezi kanálem změny a protokolováním Analýza úložiště?
Protokoly analýz mají záznamy o všech operacích čtení, zápisu, seznamu a odstraňování s úspěšnými a neúspěšnými žádostmi napříč všemi operacemi. Protokoly analýz jsou nejlepší úsilí a není zaručeno žádné řazení.

Změna kanálu je řešení, které poskytuje transakční protokol úspěšných mutací nebo změny účtu, jako je vytváření, úpravy a odstraňování objektů BLOB. Kanál změn zaručuje, že všechny události se mají zaznamenávat a zobrazovat v pořadí úspěšných změn na objekt blob, takže nemusíte odfiltrovat šum z obrovských operací čtení nebo neúspěšných žádostí. Kanál změn je v podstatě navržený a optimalizovaný pro vývoj aplikací, které vyžadují určité záruky.

### <a name="should-i-use-change-feed-or-storage-events"></a>Mám použít události změny kanálu nebo úložiště?
Pomocí obou funkcí můžete jako události změny kanálu a [BLOB Storage](storage-blob-event-overview.md) poskytovat stejné informace se stejnou zárukou pro spolehlivost doručení, přičemž hlavní rozdíl spočívá v latenci, řazení a ukládání záznamů událostí. Změna kanálu publikuje záznamy do protokolu během několika minut od změny a také garantuje pořadí operací změny na objekt BLOB. Události úložiště jsou vloženy v reálném čase a nemusí být seřazeny. Události změny kanálu jsou trvale uložené v rámci vašeho účtu úložiště jako stabilní protokoly jen pro čtení s vlastním definovaným uchováváním, zatímco události úložiště jsou přechodné, aby je mohla zpracovat obslužná rutina události, pokud je neuložíte explicitně. Pomocí kanálu změn může libovolný počet aplikací spotřebovat protokoly vlastním pohodlím pomocí rozhraní API objektů BLOB nebo sad SDK. 

## <a name="next-steps"></a>Další kroky

- Podívejte se na příklad, jak číst kanál změn pomocí klientské aplikace .NET. Viz [protokol kanálu změny procesu v Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Přečtěte si, jak reagovat na události v reálném čase. Viz [reakce na události BLOB Storage](storage-blob-event-overview.md) .
- Přečtěte si další informace o podrobném protokolování pro úspěšné i neúspěšné operace pro všechny požadavky. Viz [protokolování analýzy Azure Storage](../common/storage-analytics-logging.md) .
