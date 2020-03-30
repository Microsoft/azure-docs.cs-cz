---
title: Hledání přes obsah úložiště objektů Blob Azure
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak indexovat Azure Blob Storage a extrahovat text z dokumentů pomocí Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5df1198e6681431738f886eb7c3ad549936eab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067650"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Jak indexovat dokumenty v Azure Blob Storage pomocí Azure Cognitive Search

Tento článek ukazuje, jak pomocí Azure Cognitive Search indexovat dokumenty (například PDF, dokumenty Microsoft Office a několik dalších běžných formátů) uložené v úložišti objektů Blob Azure. Nejprve vysvětluje základy nastavení a konfigurace indexeru objektů blob. Pak nabízí hlubší zkoumání chování a scénáře, se kterými se pravděpodobně setkáte.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Podporované formáty dokumentů
Indexer objektů blob může extrahovat text z následujících formátů dokumentů:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Nastavení indexování objektů blob
Indexer úložiště objektů blob Azure můžete nastavit pomocí:

* [Portál Azure](https://ms.portal.azure.com)
* [Rozhraní REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) azure kognitivního vyhledávání
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Některé funkce (například mapování polí) ještě nejsou na portálu k dispozici a musí být použity programově.
>

Zde demonstrujeme tok pomocí rozhraní REST API.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Zdroj dat určuje, která data mají být indexována, pověření potřebná pro přístup k datům a zásady pro efektivní identifikaci změn v datech (nové, upravené nebo odstraněné řádky). Zdroj dat může používat více indexerů ve stejné vyhledávací službě.

Pro indexování objektů blob musí mít zdroj dat následující požadované vlastnosti:

* **název** je jedinečný název zdroje dat v rámci vyhledávací služby.
* **typ** musí `azureblob`být .
* **pověření** poskytuje připojovací řetězec `credentials.connectionString` účtu úložiště jako parametr. Podrobnosti naleznete v tématu [Jak zadat přihlašovací údaje](#Credentials) níže.
* **kontejner** určuje kontejner v účtu úložiště. Ve výchozím nastavení jsou všechny objekty BLOB v kontejneru retrievable. Pokud chcete indexovat objekty BLOB pouze v určitém virtuálním adresáři, můžete tento adresář určit pomocí parametru optional **query.**

Vytvoření zdroje dat:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Další informace o rozhraní CREATE Datasource API naleznete v [tématu Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak zadat pověření ####

Pověření pro kontejner objektů blob můžete zadat jedním z těchto způsobů:

- **Připojovací řetězec účtu úložiště s úplným přístupem**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Připojovací řetězec můžete získat z portálu Azure tak, že přejdete na okno účtu úložiště > Nastavení > klíčů (pro klasické účty úložiště) nebo Nastavení klíčů > přístupu (pro účty úložiště Azure Resource Manager).
- **Účet úložiště sdílený přístup ový podpis** `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` (SAS) připojovací řetězec: SAS by měl mít seznam a oprávnění ke čtení kontejnerů a objektů (objekty BLOB v tomto případě).
-  **Podpis sdíleného přístupu kontejneru**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS by měl mít seznam a oprávnění ke čtení v kontejneru.

Další informace o sdílených přístupových podpisech úložiště najdete v tématu [Použití sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Pokud používáte pověření SAS, budete muset pravidelně aktualizovat přihlašovací údaje zdroje dat s obnovenými podpisy, aby se zabránilo jejich vypršení platnosti. Pokud vyprší platnost pověření SAS, indexer se nezdaří s chybovou zprávou podobnou `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributy a další konstrukce, které utvářejí prostředí vyhledávání.

Tady je postup, jak vytvořit index `content` s prohledávatelným polem pro uložení textu extrahovaného z objektů BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Další informace o vytváření indexů najdete v tématu [Vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Krok 3: Vytvoření indexeru
Indexer propojí zdroj dat s cílovým indexem vyhledávání a poskytne plán pro automatizaci aktualizace dat.

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Tento indexer bude spuštěn každé dvě hodiny (interval plánu je nastaven na "PT2H"). Chcete-li spustit indexer každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je volitelný - pokud je vynechán, indexer se spustí pouze jednou, když je vytvořen. Indexer však můžete spustit indexer na vyžádání kdykoli.   

Další podrobnosti o rozhraní API vytvořit indexeru najdete v části [Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexeru naleznete v [tématu Jak naplánovat indexery pro Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Jak Azure Cognitive Search indexuje objekty BLOB

V závislosti na [konfiguraci indexeru](#PartsOfBlobToIndex)může indexer objektů blob indexovat pouze metadata úložiště (užitečné pouze v případě, že se zajímáte pouze o metadata a nepotřebujete indexovat obsah objektů BLOB), metadat a metadat obsahu úložiště a obsahu nebo metadat i textového obsahu. Ve výchozím nastavení indexer extrahuje metadata i obsah.

> [!NOTE]
> Ve výchozím nastavení jsou objekty BLOB se strukturovaným obsahem, například JSON nebo CSV, indexovány jako jeden blok textu. Pokud chcete indexovat objekty BLOB JSON a CSV strukturovaným způsobem, další informace najdete v [tématu Indexování objektů BLOB JSON](search-howto-index-json-blobs.md) a [indexování objektů BLOB CSV.](search-howto-index-csv-blobs.md)
>
> Složený nebo vložený dokument (například archiv ZIP nebo dokument aplikace Word s vloženým e-mailem aplikace Outlook obsahujícím přílohy) je také indexován jako jeden dokument.

* Textový obsah dokumentu je extrahován do pole `content`řetězce s názvem .

> [!NOTE]
> Azure Cognitive Search omezuje, kolik textu extrahuje v závislosti na cenové úrovni: 32 000 znaků pro úroveň Free, 64 000 pro základní, 4 miliony pro Standard, 8 milionů pro Standard S2 a 16 milionů pro Standard S3. Upozornění je součástí odpovědi na stav indexeru pro zkrácené dokumenty.  

* Uživatelem zadané vlastnosti metadat, které jsou v objektu blob, pokud existují, jsou extrahovány doslovně. Všimněte si, že to vyžaduje pole, které mají být definovány v indexu se stejným názvem jako klíč metadat objektu blob. Pokud má například objekt blob klíč `Sensitivity` metadat `High`s hodnotou , `Sensitivity` měli byste definovat pole pojmenované v `High`indexu hledání a bude naplněno hodnotou .
* Standardní vlastnosti metadat objektu blob se extrahují do následujících polí:

  * **název\_úložiště\_metadat** (Edm.String) - název souboru objektu blob. Pokud například máte objekt blob /my-container/my-folder/subfolder/resume.pdf, hodnota `resume.pdf`tohoto pole je .
  * **cesta\_úložiště\_metadat** (Edm.String) - úplný identifikátor URI objektu blob, včetně účtu úložiště. Například `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`.
  * **typ\_obsahu\_\_úložiště metadat** (Edm.String) - typ obsahu určený kódem, který jste použili k nahrání objektu blob. Například, `application/octet-stream`.
  * **\_úložiště\_metadat\_naposledy změněno** (Edm.DateTimeOffset) - poslední upravené časové razítko pro objekt blob. Azure Cognitive Search používá toto časové razítko k identifikaci změněných objektů BLOB, aby se zabránilo přeindexování vše po počáteční indexování.
  * **velikost\_úložiště\_metadat** (Edm.Int64) - velikost objektu blob v bajtů.
  * **obsah\_\_úložiště\_metadat md5** (Edm.String) - Hash hash MD5 obsahu objektu blob, pokud je k dispozici.
  * **token\_sas\_\_úložiště metadat** (Edm.String) – dočasný token SAS, který lze použít vlastní [dovednosti](cognitive-search-custom-skill-interface.md) k získání přístupu k objektu blob. Tento token by neměl být uložen pro pozdější použití, protože může vypršet.

* Vlastnosti metadat specifické pro každý formát dokumentu jsou extrahovány do polí uvedených [zde](#ContentSpecificMetadata).

Není nutné definovat pole pro všechny výše uvedené vlastnosti v indexu vyhledávání – stačí zachytit vlastnosti, které potřebujete pro vaši aplikaci.

> [!NOTE]
> Názvy polí v existujícím indexu se často budou lišit od názvů polí generovaných během extrakce dokumentu. Pomocí mapování polí můžete mapovat názvy vlastností poskytované Azure Cognitive Search na **názvy** polí v indexu vyhledávání. Níže uvidíte příklad mapování polí.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definování klíčů dokumentů a mapování polí
V Azure Cognitive Search klíč dokumentu jednoznačně identifikuje dokument. Každý index vyhledávání musí mít přesně jedno klíčové pole typu Edm.String. Pole klíče je vyžadováno pro každý dokument, který je přidáván do indexu (ve skutečnosti je to jediné povinné pole).  

Měli byste pečlivě zvážit, které extrahované pole by mělo být mapováno na klíčové pole pro váš index. Uchazeči jsou:

* **název\_úložiště\_metadat** – může to být vhodný kandidát, ale všimněte si, že 1) názvy nemusí být jedinečné, protože můžete mít objekty BLOB se stejným názvem v různých složkách a 2) název může obsahovat znaky, které jsou v klíčích dokumentu neplatné, například pomlčky. Neplatné znaky můžete řešit `base64Encode` pomocí [funkce mapování polí](search-indexer-field-mappings.md#base64EncodeFunction) – pokud tak učiníte, nezapomeňte kódovat klíče dokumentu při jejich předávání v volání rozhraní API, jako je vyhledávání. (Například v rozhraní .NET můžete pro tento účel použít [metodu UrlTokenEncode).](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)
* **cesta\_k\_úložišti metadat** - použití úplné cesty zajišťuje `/` jedinečnost, ale cesta rozhodně obsahuje znaky, které jsou [v klíči dokumentu neplatné](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak je uvedeno výše, máte možnost kódování `base64Encode` kláves pomocí [funkce](search-indexer-field-mappings.md#base64EncodeFunction).
* Pokud žádná z výše uvedených možností nefunguje pro vás, můžete přidat vlastní vlastnost metadat do objektů BLOB. Tato možnost však vyžaduje proces nahrávání objektů blob k přidání této vlastnosti metadat do všech objektů BLOB. Vzhledem k tomu, že klíč je požadovaná vlastnost, všechny objekty BLOB, které tuto vlastnost nemají, se nepodaří indexovat.

> [!IMPORTANT]
> Pokud neexistuje žádné explicitní mapování pro klíčové pole v indexu, Azure Cognitive Search automaticky používá `metadata_storage_path` jako klíč a base-64 kóduje hodnoty klíče (druhá možnost výše).
>
>

V tomto příkladu vyberte `metadata_storage_name` pole jako klíč dokumentu. Předpokládejme také, že index má `key` klíčové pole `fileSize` s názvem a pole pro ukládání velikosti dokumentu. Chcete-li při vytváření nebo aktualizaci indexeru aktualizovat položky podle potřeby, zadejte následující mapování polí:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Chcete-li to všechno spojit, můžete přidat mapování polí a povolit kódování klíčů base-64 pro existující indexer:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Další informace o mapování polí naleznete v [tomto článku](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Řízení indexů blob, které jsou indexovány
Můžete určit, které objekty BLOB jsou indexovány a které jsou přeskočeny.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexovat pouze objekty BLOB s určitými příponami souborů
Pomocí parametru konfigurace indexeru `indexedFileNameExtensions` můžete indexovat pouze objekty BLOB s příponami názvů souborů, které zadáte. Hodnota je řetězec obsahující seznam přípon souborů oddělených čárkami (s úvodní tečkou). Chcete-li například indexovat pouze soubor . PDF a . DOCX objekty BLOB, udělejte toto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Vyloučení objektů BLOB s určitými příponami souborů
Objekty BLOB s určitými příponami názvů `excludedFileNameExtensions` souborů můžete vyloučit z indexování pomocí parametru konfigurace. Hodnota je řetězec obsahující seznam přípon souborů oddělených čárkami (s úvodní tečkou). Chcete-li například indexovat všechny objekty BLOB s výjimkou objektů s . PNG a . JPEG rozšíření, udělejte toto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Pokud `indexedFileNameExtensions` jsou `excludedFileNameExtensions` k dispozici oba a parametry, Azure Cognitive Search nejprve se podíváme na `indexedFileNameExtensions`. `excludedFileNameExtensions` To znamená, že pokud je v obou seznamech k dispozici stejná přípona souboru, bude vyloučena z indexování.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Řízení, které části objektu blob jsou indexovány

Můžete určit, které části objektů BLOB jsou `dataToExtract` indexovány pomocí parametru konfigurace. Může trvat následující hodnoty:

* `storageMetadata`- určuje, že jsou indexovány pouze [standardní vlastnosti objektu blob a metadata zadaná uživatelem.](../storage/blobs/storage-properties-metadata.md)
* `allMetadata`- určuje, že metadata úložiště a [metadata specifická pro daný obsah](#ContentSpecificMetadata) extrahovaná z obsahu objektu blob jsou indexována.
* `contentAndMetadata`- určuje, že jsou indexována všechna metadata a textový obsah extrahovaný z objektu blob. Toto je výchozí hodnota.

Chcete-li například indexovat pouze metadata úložiště, použijte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Použití metadat objektu blob k řízení indexování objektů BLOB

Výše popsané parametry konfigurace platí pro všechny objekty BLOB. Někdy můžete chtít řídit, jak jsou indexovány *jednotlivé objekty BLOB.* To lze provést přidáním následujících vlastností a hodnot metadat objektu blob:

| Název vlastnosti | Hodnota vlastnosti | Vysvětlení |
| --- | --- | --- |
| AzureSearch_Skip |"pravda" |Instruuje indexer objektů blob, aby objekt blob zcela přeskočil. Nepokoušíse se o extrakci metadat ani obsahu. To je užitečné, když konkrétní objekt blob selže opakovaně a přeruší proces indexování. |
| AzureSearch_SkipContent |"pravda" |To je `"dataToExtract" : "allMetadata"` ekvivalentní nastavení popsané [výše](#PartsOfBlobToIndex) vymezené na konkrétní objekt blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Řešení chyb

Ve výchozím nastavení se indexer objektů blob zastaví, jakmile narazí na objekt blob s nepodporovaným typem obsahu (například obrázek). Parametr můžete samozřejmě `excludedFileNameExtensions` použít k přeskočení určitých typů obsahu. Však může být nutné indexovat objekty BLOB bez znalosti všech možných typů obsahu předem. Chcete-li pokračovat v indexování, když je `failOnUnsupportedContentType` zjištěn nepodporovaný typ obsahu, nastavte parametr konfigurace na `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

U některých objektů BLOB není Azure Cognitive Search schopen určit typ obsahu nebo nemůže zpracovat dokument jiného podporovaného typu obsahu. Chcete-li tento režim `failOnUnprocessableDocument` selhání ignorovat, nastavte parametr konfigurace na hodnotu false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Cognitive Search omezuje velikost objektů BLOB, které jsou indexované. Tato omezení jsou dokumentována v [limitech služeb v Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Nadrozměrné objekty BLOB jsou ve výchozím nastavení považovány za chyby. Pokud však nastavíte `indexStorageMetadataOnlyForOversizedDocuments` parametr konfigurace na hodnotu true, můžete stále indexovat metadata úložiště objektů BLOB nadměrné velikosti: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Indexování můžete také pokračovat, pokud dojde k chybám v libovolném bodě zpracování, a to buď při analýzě objektů BLOB nebo při přidávání dokumentů do indexu. Chcete-li ignorovat určitý počet `maxFailedItems` chyb, nastavte parametry a `maxFailedItemsPerBatch` na požadované hodnoty. Například:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové zjišťování indexování a odstraňování

Když nastavíte indexer objektů blob pro spuštění podle plánu, přeindexuje pouze změněné objekty `LastModified` BLOB, jak je určeno časovým razítkem objektu blob.

> [!NOTE]
> Není nutné zadat zásady zjišťování změn – přírůstkové indexování je povoleno automaticky.

Chcete-li podpořit odstranění dokumentů, použijte přístup "obnovitelného odstranění". Pokud odstraníte objekty BLOB úplně, odpovídající dokumenty nebudou odebrány z indexu vyhledávání.

Existují dva způsoby, jak implementovat přístup obnovitelného odstranění. Oba jsou popsány níže.

### <a name="native-blob-soft-delete-preview"></a>Nativní odstranění měkkým odstraněním objektu blob (náhled)

> [!IMPORTANT]
> Podpora nativního odstranění pomocí objektu blob je ve verzi Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](https://docs.microsoft.com/azure/search/search-api-preview) poskytuje tuto funkci. V současné době neexistuje žádná podpora portálu nebo sady .NET SDK.

> [!NOTE]
> Při použití nativní objekt blob obnovitelné odstranění zásady dokumentu klíče pro dokumenty v indexu musí být buď vlastnost objektu blob nebo metadata objektu blob.

V této metodě budete používat [nativní funkce odstranění objektů blob,](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) kterou nabízí úložiště objektů blob Azure. Pokud je nativní odstranění objektu blob obnovitelné odstranění povoleno ve vašem účtu úložiště, zdroj dat má nativní sadu zásad obnovitelného odstranění a indexer najde objekt blob, který byl převeden do obnovitelného odstraněného stavu, indexer tento dokument z indexu odebere. Nativní zásady odstranění objektů blob nejsou podporovány při indexování objektů BLOB z Azure Data Lake Storage Gen2.

Použijte k tomu následující postup:
1. Povolte [nativní obnovitelné odstranění pro úložiště objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete). Doporučujeme nastavit zásady uchovávání informací na hodnotu, která je mnohem vyšší než plán intervalu indexeru. Tímto způsobem, pokud je problém se systémem indexeru nebo pokud máte velký počet dokumentů k indexování, je spousta času pro indexer nakonec zpracovat obnovitelné odstraněné objekty BLOB. Indexery Azure Cognitive Search odstraní dokument z indexu jenom v případě, že zpracuje objekt blob, zatímco je v obnovitelném odstraněném stavu.
1. Nakonfigurujte nativní zásady detekce odstranění objektů blob ve zdroji dat. Příklad najdete níže. Vzhledem k tomu, že tato funkce je ve verzi Preview, musíte použít rozhraní PREVIEW REST API.
1. Spusťte indexer nebo nastavte indexer spustit podle plánu. Při spuštění indexeru a zpracuje objekt blob dokument bude odebrán z indexu.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>Přeindexování neodstraněných objektů BLOB

Pokud odstraníte objekt blob z úložiště objektů blob Azure s nativním měkkým odstraněním povoleným na vašem účtu úložiště, objekt blob přejde do obnovitelného odstraněného stavu, což vám dává možnost tento objekt blob obnovit během doby uchovávání. Když zdroj dat Azure Cognitive Search má nativní zásady odstranění objektů blob a indexer zpracovává měkký odstraněný objekt blob, odebere tento dokument z indexu. Pokud tento objekt blob je později undeleted indexer nebude vždy přeindexovat objekt blob. Důvodem je, že indexer určuje, které objekty BLOB `LastModified` indexovat na základě časového razítka objektu blob. Při odstranění obnovitelného odstraněného `LastModified` objektu blob jeho časové razítko se neaktualizuje, takže `LastModified` pokud indexer již zpracoval objekty BLOB s časovými razítky novějšími než neodstraněný objekt blob, nebude neindexovat neodstraněný objekt blob. Chcete-li se ujistit, že je neodstraněný objekt blob přeindexován, budete muset aktualizovat `LastModified` časové razítko objektu blob. Jedním ze způsobů, jak to provést, je opětovné uložení metadat tohoto objektu blob. Není nutné měnit metadata, ale opětovné uložení metadat aktualizuje `LastModified` časové razítko objektu blob tak, aby indexer ví, že je třeba přeindexovat tento objekt blob.

### <a name="soft-delete-using-custom-metadata"></a>Obnovitelné odstranění pomocí vlastních metadat

V této metodě použijete metadata objektu blob k označení, kdy má být dokument odebrán z indexu vyhledávání.

Použijte k tomu následující postup:

1. Přidejte do objektu blob vlastní dvojici klíč-hodnota metadat, která do Azure Cognitive Search označí, že se logicky odstraní.
1. Nakonfigurujte zásadu detekce sloupců s měkkým odstraněním ve zdroji dat. Příklad najdete níže.
1. Jakmile indexer zpracuje objekt blob a odstraní dokument z indexu, můžete odstranit objekt blob pro úložiště objektů Blob Azure.

Například následující zásady považuje objekt blob, který má být `IsDeleted` odstraněn, `true`pokud má vlastnost metadat s hodnotou :

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }

#### <a name="reindexing-undeleted-blobs"></a>Přeindexování neodstraněných objektů BLOB

Pokud nastavíte zásady detekce měkkých odstranění sloupců ve zdroji dat, pak přidáte vlastní metadata do objektu blob s hodnotou značky a pak spustíte indexer, indexer tento dokument z indexu odebere. Pokud chcete tento dokument přeindexovat, jednoduše změňte hodnotu metadat obnovitelného odstranění pro tento objekt blob a znovu spusťte indexer.

## <a name="indexing-large-datasets"></a>Indexování velkých datových sad

Indexování objektů BLOB může být časově náročný proces. V případech, kdy máte miliony objektů BLOB k indexování, můžete urychlit indexování rozdělením dat a použitím více indexerů ke zpracování dat paralelně. Zde je návod, jak to můžete nastavit:

- Rozdělení dat do více kontejnerů objektů blob nebo virtuálních složek
- Nastavte několik zdrojů dat Azure Cognitive Search, jeden na kontejner nebo složku. Chcete-li překážet na `query` složku objektů blob, použijte parametr:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Vytvořte odpovídající indexer pro každý zdroj dat. Všechny indexery mohou překážet na stejný cílový index vyhledávání.  

- Jedna vyhledávací jednotka ve vaší službě může spustit jeden indexer v daném okamžiku. Vytvoření více indexerů, jak je popsáno výše, je užitečné pouze v případě, že skutečně spustit paralelně. Chcete-li spustit více indexerů paralelně, horizontální navýšení kapacity vyhledávací služby vytvořením odpovídající počet oddílů a replik. Pokud má například vyhledávací služba 6 vyhledávacích jednotek (například 2 oddíly x 3 repliky), může být současně spuštěno 6 indexerů, což vede k šestinásobnému zvýšení propustnosti indexování. Další informace o škálování a plánování kapacity najdete v [tématu Škálování úrovní prostředků pro úlohy dotazů a indexování v Azure Cognitive Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexování dokumentů spolu se souvisejícími daty

Můžete chtít "sestavit" dokumenty z více zdrojů v indexu. Můžete například chtít sloučit text z objektů BLOB s jinými metadaty uloženými v Cosmos DB. Můžete dokonce použít rozhraní API pro indexování nabízených tisku spolu s různými indexery k vytvoření vyhledávacích dokumentů z více částí. 

Aby to fungovalo, všechny indexery a další součásti musí dohodnout na klíč dokumentu. Další podrobnosti k tomuto tématu najdete v článku [Index více zdrojů dat Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Podrobný návod najdete v tomto externím článku: [Zkombinujte dokumenty s dalšími daty v Azure Cognitive Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexování prostého textu 

Pokud všechny objekty BLOB obsahují prostý text ve stejném kódování, můžete výrazně zlepšit výkon indexování pomocí **režimu analýzy textu**. Chcete-li použít režim analýzy `parsingMode` textu, `text`nastavte vlastnost konfigurace na :

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Ve výchozím `UTF-8` nastavení se předpokládá kódování. Chcete-li zadat jiné kódování, použijte vlastnost `encoding` konfigurace: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Vlastnosti metadat specifických pro daný typ obsahu
Následující tabulka shrnuje zpracování provedené pro každý formát dokumentu a popisuje vlastnosti metadat extrahované azure kognitivní vyhledávání.

| Formát dokumentu / typ obsahu | Vlastnosti metadat specifické pro typ obsahu | Podrobnosti zpracování |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Odstranění značek HTML a extrahování textu |
| PDF (přihláška/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahování textu včetně vložených dokumentů (kromě obrázků) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahování textu včetně vložených dokumentů |
| DOC (aplikace/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahování textu včetně vložených dokumentů |
| DOCM (aplikace/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahování textu včetně vložených dokumentů |
| WORD XML (aplikace/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Odstranění značek XML a extrahování textu |
| WORD 2003 XML (aplikace/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Odstranění značek XML a extrahování textu |
| XLSX (aplikace/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahování textu včetně vložených dokumentů |
| XLS (aplikace/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahování textu včetně vložených dokumentů |
| XLSM (aplikace/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahování textu včetně vložených dokumentů |
| PPTX (aplikace/vnd.openxmlformats-officedocument.presentationml.presentation.presentation.presentation. |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahování textu včetně vložených dokumentů |
| PPT (aplikace/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahování textu včetně vložených dokumentů |
| PPTM (aplikace/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahování textu včetně vložených dokumentů |
| MSG (aplikace/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahovat text, včetně příloh. `metadata_message_to_email`a `metadata_message_cc_email` `metadata_message_bcc_email` jsou kolekce řetězců, zbytek polí jsou řetězce.|
| ODT (aplikace/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahování textu včetně vložených dokumentů |
| ODS (aplikace/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahování textu včetně vložených dokumentů |
| ODP (aplikace/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrahování textu včetně vložených dokumentů |
| ZIP (aplikace/zip) |`metadata_content_type` |Extrahování textu ze všech dokumentů v archivu |
| GZ (aplikace/gzip) |`metadata_content_type` |Extrahování textu ze všech dokumentů v archivu |
| EPUB (aplikace/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrahování textu ze všech dokumentů v archivu |
| XML (aplikace/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Odstranění značek XML a extrahování textu |
| JSON (aplikace/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrakce textu<br/>Poznámka: Pokud potřebujete extrahovat více polí dokumentu z objektu blob JSON, naleznete [v tématu indexování objektů BLOB JSON](search-howto-index-json-blobs.md) podrobnosti |
| EML (zpráva/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahování textu včetně příloh |
| RTF (aplikace/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrakce textu|
| Prostý text (text/prostý) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrakce textu|


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám vylepšit Azure Cognitive Search
Pokud máte požadavky na funkce nebo nápady na vylepšení, dejte nám vědět na našich [stránkách UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
