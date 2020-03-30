---
title: Změna informačního kanálu v úložišti objektů Blob Azure (preview) | Dokumenty společnosti Microsoft
description: Přečtěte si o protokolech kanálu změn v Azure Blob Storage a o tom, jak je používat.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536883"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Podpora kanálu změn ve službě Azure Blob Storage (preview)

Účelem kanálu změn je poskytnout protokoly transakcí všech změn, ke kterým dochází k objektům BLOB a metadatům objektů blob v účtu úložiště. Kanál změn poskytuje **objednané**, **zaručené**, **trvanlivé**, **neměnné**, jen pro **čtení** protokolu těchto změn. Klientské aplikace mohou číst tyto protokoly kdykoli, buď v datovém proudu nebo v dávkovém režimu. Informační kanál změn umožňuje vytvářet efektivní a škálovatelná řešení, která zpracovávají události změn, ke kterým dochází v účtu úložiště objektů Blob s nízkými náklady.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Kanál změn se ukládá jako [objekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) ve speciálním kontejneru ve vašem účtu úložiště za standardní [ceny objektů blob.](https://azure.microsoft.com/pricing/details/storage/blobs/) Dobu uchování těchto souborů můžete řídit na základě vašich požadavků (viz [podmínky](#conditions) aktuální verze). Události změn jsou připojeny k kanálu změn jako záznamy ve specifikaci formátu [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) kompaktní, rychlý, binární formát, který poskytuje bohaté datové struktury s vložkovým schématem. Tento formát se široce používá v ekosystému Hadoop, Stream Analytics a Azure Data Factory.

Tyto protokoly můžete zpracovat asynchronně, postupně nebo v plném rozsahu. Libovolný počet klientských aplikací můžete nezávisle číst změny kanálu, paralelně a vlastním tempem. Analytické aplikace, jako je [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) nebo [Apache Spark,](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) mohou využívat protokoly přímo jako soubory Avro, které vám umožní je zpracovávat za nízkou cenu, s velkou šířkou pásma a bez nutnosti psát vlastní aplikaci.

Podpora kanálu změn je vhodná pro scénáře, které zpracovávají data na základě objektů, které se změnily. Aplikace mohou například:

  - Aktualizujte sekundární index, synchronizujte s mezipamětí, vyhledávačem nebo jinými scénáři správy obsahu.
  
  - Extrahujte přehledy a metriky obchodní analýzy na základě změn, ke kterým dochází u objektů, a to buď datovým proudem, nebo dávkovým režimem.
  
  - Ukládejte, auditujte a analyzujte změny svých objektů v libovolném časovém období, z důvodu zabezpečení, dodržování předpisů nebo informací pro správu podnikových dat.

  - Vytvářejte řešení pro zálohování, zrcadlení nebo replikaci stavu objektu ve vašem účtu pro správu havárií nebo dodržování předpisů.

  - Vytvořte připojené aplikační kanály, které reagují na události změny nebo plánují spuštění na základě vytvořeného nebo změněného objektu.

> [!NOTE]
> Kanál změn poskytuje trvalý, uspořádaný model protokolu změn, ke kterým dochází k objektu blob. Změny jsou zapsány a zpřístupněny v protokolu zdrojů změn v řádu několika minut od změny. Pokud vaše aplikace musí reagovat na události mnohem rychleji, než je tento, zvažte použití [událostí úložiště objektů blob](storage-blob-event-overview.md) místo. [Události úložiště objektů blob](storage-blob-event-overview.md) poskytuje jednorázové události v reálném čase, které umožňují vašim funkcím nebo aplikacím Azure rychle reagovat na změny, ke kterým dochází v objektu blob. 

## <a name="enable-and-disable-the-change-feed"></a>Povolení a zakázání kanálu změn

Chcete-li zahájit zachytávání a zaznamenávat změny, musíte povolit kanál změn v účtu úložiště. Zakažte kanál změn, abyste zastavili zachytávání změn. Změny můžete povolit a zakázat pomocí šablon Azure Resource Manager na portálu nebo powershellu.

Při povolujete zdroj změn na paměti několik věcí, které je třeba mít na paměti.

- Existuje jenom jeden kanál změn pro službu objektů blob v každém účtu úložiště a je uložený v **kontejneru $blobchangefeed.**

- Změny vytvoření, aktualizace a odstranění se zachycují pouze na úrovni služby objektů blob.

- Kanál změn zachycuje *všechny* změny pro všechny události, které jsou k dispozici v účtu. Klientské aplikace mohou podle potřeby odfiltrovat typy událostí. (Viz [podmínky](#conditions) aktuální verze).

- Kanál change můžou povolit jenom účty úložiště GPv2 a Blob. Účty Premium BlockBlobStorage a hierarchické účty s povoleným oborem názvů nejsou aktuálně podporovány. Účty úložiště GPv1 nejsou podporovány, ale lze je upgradovat na GPv2 bez prostojů, další informace najdete v [tématu Upgrade na účet úložiště GPv2.](../common/storage-account-upgrade.md)

> [!IMPORTANT]
> Kanál změn je ve verzi Public Preview a je k dispozici v oblastech **westcentralus** a **westus2.** Viz [část podmínky](#conditions) tohoto článku. Pokud se chcete zaregistrovat do verze Preview, [přečtěte si](#register) část Registrace předplatného v tomto článku. Před povolením kanálu změn na účtech úložiště je nutné zaregistrovat předplatné.

### <a name="portal"></a>[Portál](#tab/azure-portal)

Povolte kanál změn na svém účtu úložiště pomocí portálu Azure:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte na možnost **Ochrana dat** v části Služba **objektů blob**.

3. V části **Informační kanál pro změnu objektů blob** klikněte na **Možnost** povoleno.

4. Zvolte tlačítko **Uložit,** chcete-li potvrdit nastavení ochrany dat.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Povolení kanálu změn pomocí PowerShellu:

1. Nainstalujte nejnovější PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zavřete konzolu Powershell a znovu ji otevřete.

3. Nainstalujte modul **náhledu Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. Přihlaste se k `Connect-AzAccount` předplatnému Azure pomocí příkazu a postupujte podle pokynů na obrazovce k ověření.

   ```powershell
   Connect-AzAccount
   ```

5. Povolte kanál změn pro svůj účet úložiště.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Šablony](#tab/template)
Pomocí šablony Azure Resource Manager umožněte kanálu change na vašem stávajícím účtu úložiště prostřednictvím portálu Azure:

1. Na webu Azure Portal zvolte **Vytvořit prostředek**.

2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.

3. Zvolte **[Nasadit vlastní šablonu](https://portal.azure.com/#create/Microsoft.Template)** a pak **v editoru zvolte Vytvořit vlastní šablonu**.

4. V editoru šablon vložte následující json. Nahraďte `<accountName>` zástupný symbol názvem svého účtu úložiště.

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
    
5. Zvolte tlačítko **Uložit,** určete skupinu prostředků účtu a pak zvolte tlačítko **Koupit,** chcete-li šablonu nasadit a povolit informační kanál změn.

---

## <a name="consume-the-change-feed"></a>Konzumujte zdroj změn

Kanál změn vytváří několik metadat a souborů protokolu. Tyto soubory jsou umístěny v **kontejneru $blobchangefeed** účtu úložiště. 

> [!NOTE]
> V aktuální verzi **$blobchangefeed** kontejner u neviditelného v Průzkumníkovi úložiště Azure nebo na webu Azure Portal. V současné době nelze zobrazit $blobchangefeed kontejneru při volání ListContainers ROZHRANÍ API, ale jste schopni volat ListBlobs ROZHRANÍ API přímo v kontejneru zobrazit objekty BLOB.

Klientské aplikace mohou využívat kanál změn pomocí knihovny procesoru kanálu blob, která je k dispozici s sadou SDK kanálu změnit. 

Viz [Protokoly kanálu změn procesu v azure blob storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Principy organizace zdroje změn

<a id="segment-index"></a>

### <a name="segments"></a>Segmenty

Kanál změn je protokol změn, které jsou uspořádány do **hodinových** *segmentů,* ale jsou připojeny a aktualizovány každých několik minut. Tyto segmenty jsou vytvořeny pouze v případě, že existují události změny objektu blob, ke kterým dochází v této hodině. To umožňuje klientské aplikace využívat změny, ke kterým dochází v rámci určité časové rozsahy bez nutnosti prohledávat celý protokol. Další informace naleznete v [části Specifikace](#specifications).

Dostupný hodinový segment kanálu změn je popsán v souboru manifestu, který určuje cesty k souborům kanálu změn pro tento segment. Výpis virtuálního `$blobchangefeed/idx/segments/` adresáře zobrazuje tyto segmenty seřazené podle času. Cesta segmentu popisuje začátek hodinového časového rozsahu, který segment představuje. Tento seznam můžete použít k odfiltrování segmentů protokolů, které vás zajímají.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Automaticky `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` se vytvoří, když povolíte zdroj změn. Tento soubor můžete bezpečně ignorovat. Jedná se o vždy prázdný inicializační soubor. 

Soubor manifestu`meta.json`segmentu ( ) zobrazuje cestu k souborům kanálu změn pro tento segment ve vlastnosti. `chunkFilePaths` Tady je příklad souboru manifestu segmentu.

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
> Kontejner `$blobchangefeed` se zobrazí až poté, co ve svém účtu podáte funkci zdroje změn. Budete muset počkat několik minut po povolení kanálu změn, než budete moci vypsat objekty BLOB v kontejneru. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Změna záznamů událostí

Soubory kanálu změn obsahují řadu záznamů událostí změny. Každý záznam události změny odpovídá jedné změně jednotlivého objektu blob. Záznamy jsou serializovány a zapsány do souboru pomocí specifikace formátu [Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) Záznamy lze číst pomocí specifikace formátu souboru Avro. Pro zpracování souborů v tomto formátu je k dispozici několik knihoven.

Soubory kanálu změn jsou `$blobchangefeed/log/` uloženy ve virtuálním adresáři jako [objekty BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). První soubor kanálu změn pod `00000` každou cestou bude `00000.avro`mít název souboru (například ). Název každého následujícího souboru protokolu přidaného do této cesty `00001.avro`se zvýší o 1 (například: ).

Tady je příklad záznamu události změny ze souboru kanálu změn převedeného na Json.

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

Popis jednotlivých vlastností najdete v tématu [schéma událostí Azure Event Grid pro úložiště objektů blob](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties).

> [!NOTE]
> Soubory zdroje změn pro segment se po vytvoření segmentu okamžitě nezobrazí. Délka zpoždění je v normálním intervalu publikování latence kanálu změn, který je během několika minut od změny.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikace

- Záznamy událostí změn jsou připojeny pouze ke zdroji změn. Jakmile jsou tyto záznamy připojeny, jsou neměnné a pozice záznamu je stabilní. Klientské aplikace mohou udržovat vlastní kontrolní bod na pozici čtení kanálu změn.

- Záznamy událostí změny jsou připojeny v řádu několika minut od změny. Klientské aplikace se mohou rozhodnout využívat záznamy tak, jak jsou připojeny pro přístup k datovým proudům nebo hromadně kdykoli jindy.

- Záznamy událostí změny jsou seřazeny podle pořadí změn **na objekt blob**. Pořadí změn napříč objekty BLOB není definováno ve službě Azure Blob Storage. Všechny změny v předchozím segmentu jsou před všemi změnami v následujících segmentech.

- Záznamy událostí změny jsou serializovány do souboru protokolu pomocí specifikace formátu [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Změna záznamů událostí, `eventType` kde `Control` má hodnotu jsou interní systémové záznamy a neodrážejí změnu objektů ve vašem účtu. Tyto záznamy můžete bezpečně ignorovat.

- Hodnoty v `storageDiagnonstics` váčku vlastností jsou určeny pouze pro interní použití a nejsou určeny pro použití vaší aplikací. Vaše aplikace by neměly mít smluvní závislost na tato data. Tyto vlastnosti můžete bezpečně ignorovat.

- Čas reprezentovaný segmentem je **přibližný** s hranicemi 15 minut. Chcete-li zajistit spotřebu všech záznamů v určeném čase, spotřebovávat po sobě jdoucí segment předchozí a následující hodinu.

- Každý segment může mít `chunkFilePaths` jiný počet z důvodu vnitřní dělení datového proudu protokolu pro správu propustnost publikování. Soubory protokolu v `chunkFilePath` každém z nich jsou zaručeně obsahují vzájemně se vylučující objekty BLOB a mohou být konzumovány a zpracovány paralelně bez porušení řazení změn na objekt blob během iterace.

- Segmenty začínají ve `Publishing` stavu. Po dokončení připojení záznamů k segmentu bude `Finalized`. Soubory protokolu v libovolném segmentu, `LastConsumable` který je `$blobchangefeed/meta/Segments.json` datován po datu vlastnosti v souboru, by neměly být spotřebovány vaší aplikací. Tady je příklad vlastnosti `LastConsumable`v `$blobchangefeed/meta/Segments.json` souboru:

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

## <a name="register-your-subscription-preview"></a>Registrace předplatného (preview)

Vzhledem k tomu, že kanál změn je jenom ve verzi Public Preview, budete muset zaregistrovat předplatné, abyste tuto funkci používali.

### <a name="register-by-using-powershell"></a>Registrace pomocí PowerShellu

V konzole PowerShell spusťte tyto příkazy:

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Registrace pomocí azure cli

Ve službě Azure Cloud Shell spusťte tyto příkazy:

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>Podmínky a známé problémy (náhled)

Tato část popisuje známé problémy a podmínky v aktuálníverzi veřejné verze informačního kanálu o změnách. 
- Pro náhled, musíte [nejprve zaregistrovat předplatné](#register) před povolením kanálu změn pro váš účet úložiště v westcentralus nebo westus2 oblastech. 
- Kanál změn zachycuje pouze operace vytváření, aktualizace, odstraňování a kopírování. Aktualizace metadat nejsou aktuálně zachyceny ve verzi Preview.
- Změna záznamů událostí pro každou jednotlivou změnu se může ve zdroji změn zobrazit vícekrát.
- Ještě nemůžete spravovat životnost souborů protokolu kanálu změn nastavením zásad uchovávání informací podle času a nelze odstranit objekty BLOB 
- Vlastnost `url` souboru protokolu je nyní vždy prázdná.
- Vlastnost `LastConsumable` souboru segments.json neuvádí úplně první segment, který kanál změn dokončí. K tomuto problému dochází až po dokončení první segment. Všechny následující segmenty po první hodině jsou `LastConsumable` přesně zachyceny ve vlastnosti.
- V současné době nelze zobrazit **$blobchangefeed** kontejneru při volání ListContainers ROZHRANÍ API a kontejner nezobrazí na webu Azure Portal nebo Storage Explorer
- Účty úložiště, které dříve iniciovaly [převzetí služeb při selhání účtu,](../common/storage-disaster-recovery-guidance.md) mohou mít problémy s nezobrazovaným souborem protokolu. Jakékoli budoucí převzetí služeb při selhání účtu může také ovlivnit soubor protokolu během náhledu.

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Jaký je rozdíl mezi zdrojem změn a protokolováním služby Storage Analytics?
Protokoly Analytics mají záznamy o všech operacích čtení, zápisu, seznamu a odstraňování s úspěšnými a neúspěšnými požadavky ve všech operacích. Protokoly Analytics jsou nejlepší úsilí a žádné řazení je zaručeno.

Kanál změn je řešení, které poskytuje transakční protokol úspěšných mutací nebo změn vašeho účtu, jako je vytváření objektů blob, úpravy a odstranění. Kanál změn zaručuje, že všechny události, které mají být zaznamenány a zobrazeny v pořadí úspěšných změn na objekt blob, tedy není třeba odfiltrovat šum z obrovského objemu operací čtení nebo neúspěšných požadavků. Kanál změn je zásadně navržen a optimalizován pro vývoj aplikací, které vyžadují určité záruky.

### <a name="should-i-use-change-feed-or-storage-events"></a>Mám použít kanál change feed nebo události úložiště?
Můžete využít obě funkce jako změna kanálu a [události úložiště objektů Blob](storage-blob-event-overview.md) poskytují stejné informace se stejnou zárukou spolehlivosti doručení, přičemž hlavním rozdílem je latence, řazení a ukládání záznamů událostí. Kanál změn publikuje záznamy do protokolu během několika minut po změně a také zaručuje pořadí operací změny na objekt blob. Události úložiště jsou nabízeny v reálném čase a nemusí být objednány. Události kanálu změn jsou trvale uloženy uvnitř účtu úložiště jako stabilní protokoly jen pro čtení s vlastní definované uchovávání informací, zatímco události úložiště jsou přechodné, které mají být spotřebovány obslužnou rutinou události, pokud je explicitně neuložíte. Pomocí kanálu změnit libovolný počet aplikací můžete využívat protokoly na vlastní pohodlí pomocí objektů BLOB API nebo sady SDK. 

## <a name="next-steps"></a>Další kroky

- Podívejte se na příklad, jak číst zdroj změn pomocí klientské aplikace .NET. Viz [Protokoly kanálu změn procesu v azure blob storage](storage-blob-change-feed-how-to.md).
- Přečtěte si, jak reagovat na události v reálném čase. Viz [Reakce na události úložiště objektů blob](storage-blob-event-overview.md)
- Další informace o podrobných informacích o protokolování úspěšných i neúspěšných operací pro všechny požadavky. Podívejte se na [protokolování analýzy Azure Storage](../common/storage-analytics-logging.md)
