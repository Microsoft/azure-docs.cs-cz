---
title: Indexování obsahu úložiště objektů Blob v Azure pro fulltextové vyhledávání – Azure Search
description: Zjistěte, jak index služby Azure Blob Storage a extrakci textu z dokumentů s Azure Search.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: d90a4272f843cc9f2b66aa202ddefe54e7b1a361
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632157"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexování dokumentů ve službě Azure Blob Storage pomocí služby Azure Search
Tento článek popisuje, jak používat Azure Search k indexování dokumentů (jako jsou soubory PDF, dokumentů Microsoft Office a několik dalších běžných formátů) uložené ve službě Azure Blob storage. Nejprve vysvětluje základy tohoto nastavení a konfigurace indexeru blob. Potom nabízí blíže zkoumat chování a scénáře se pravděpodobně dojde k.

## <a name="supported-document-formats"></a>Podporované formáty dokumentu
Indexování objektů blob můžete extrahovat text z následujících formátů dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Nastavení indexování objektů blob
Můžete nastavit pomocí indexeru Azure Blob Storage:

* [Azure Portal](https://ms.portal.azure.com)
* Služba Azure Search [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Služba Azure Search [sady .NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Některé funkce (například mapování polí) ještě nejsou k dispozici na portálu a musí být použity prostřednictvím kódu programu.
>
>

Tady vám ukážeme tok pomocí rozhraní REST API.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Zdroj dat určuje, jaká data do indexu, přihlašovací údaje potřebné pro přístup k datům a zásady, které efektivně identifikovat změny v datech (nové, změněné nebo odstraněné řádky). Zdroj dat můžete použít několik indexerů ve stejné službě search.

Indexování objektů blob, zdroj dat musí mít následující požadované vlastnosti:

* **název** je jedinečný název zdroje dat v rámci vaší vyhledávací služby.
* **typ** musí být `azureblob`.
* **přihlašovací údaje** obsahuje připojovací řetězec účtu úložiště jako `credentials.connectionString` parametru. Zobrazit [jak zadat přihlašovací údaje](#Credentials) níže podrobnosti.
* **kontejner** Určuje kontejner v účtu úložiště. Standardně jsou všechny objekty BLOB v kontejneru retrievable. Pokud chcete jenom k objektům BLOB indexu v konkrétní virtuální adresář, můžete určit tento adresář pomocí volitelného **dotazu** parametru.

Chcete-li vytvořit zdroj dat:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Další informace k rozhraní API pro vytvoření zdroje dat, naleznete v tématu [vytvořit zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak zadat přihlašovací údaje ####

Zadejte přihlašovací údaje pro kontejner objektů blob v jednom z těchto způsobů:

- **Připojovací řetězec účtu úložiště úplný přístup**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Připojovací řetězec můžete získat z webu Azure portal tak, že přejdete do okna účet úložiště > Nastavení > klíče (pro účty úložiště Classic) nebo nastavení > přístupové klíče (pro účty úložiště Azure Resource Manageru).
- **Sdílený přístupový podpis úložiště účtu** připojovací řetězec (SAS): `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` Musí mít v seznamu a oprávnění ke čtení u kontejnery a objekty sdíleného přístupového podpisu (v tomto případě objektů BLOB).
-  **Sdílený přístupový podpis kontejneru**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS by měl mít seznamu a oprávnění ke čtení v kontejneru.

Další informace o úložiště sdílené přístupové podpisy, naleznete v tématu [použití sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Pokud používáte pověření SAS, je potřeba pravidelně aktualizovat přihlašovací údaje zdroje dat s obnovené podpisy, aby se zabránilo vypršení jejich platnosti. Pokud vyprší platnost pověření SAS, indexeru se nezdaří s chybovou zprávou, která je podobná `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributy a jiných objektů, které obrazce hledání vyzkoušet.

Tady je postup pro vytvoření indexu s možností prohledávání `content` pole, které chcete uložit text extrahovaný z objektů blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Další informace o vytváření indexů, naleznete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Krok 3: Vytvoření indexeru
Indexer propojuje zdroj dat s cílovým indexem vyhledávání a poskytuje plán pro automatizaci aktualizace dat.

Po vytvoření index a zdroj dat jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Indexer se spustí každé dvě hodiny (interval plánování je nastavena na "PT2H"). Pokud chcete spustit indexer každých 30 minut, nastavte interval, který "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je volitelné - li tento parametr vynechán, indexer se spustí pouze jednou, když je vytvořena. Však můžete kdykoli spustit indexer na vyžádání.   

Podrobné informace o rozhraní API pro vytvoření indexeru, projděte si [vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Jak Azure Search indexování objektů BLOB

V závislosti na tom [konfigurace indexeru](#PartsOfBlobToIndex), indexeru blob můžete indexovat jenom metadata úložiště (užitečné, pokud vás zajímá pouze o metadata a není třeba indexovat obsah objektů BLOB), úložiště a obsah, metadata nebo oba metadata a textový obsah. Ve výchozím nastavení indexer extrahuje metadata a obsah.

> [!NOTE]
> Ve výchozím nastavení jsou indexovány objekty BLOB s Strukturovaný obsah, jako je JSON nebo CSV jako jediný neodkazovaný blok textu. Pokud chcete k indexování objektů BLOB JSON a sdíleného svazku clusteru strukturovaným způsobem, naleznete v tématu [JSON indexování objektů blob](search-howto-index-json-blobs.md) a [objekty BLOB sdíleného svazku clusteru indexování](search-howto-index-csv-blobs.md) funkce ve verzi preview.
>
> Složené nebo vložený dokument (například archiv ZIP nebo Wordový dokument s vložený Outlookový e-mail obsahující přílohy) jsou také indexována jako jeden dokument.

* Textový obsah dokumentu je extrahován do pole řetězce s názvem `content`.

> [!NOTE]
> Služba Azure Search omezuje množství textu extrahuje v závislosti na cenové úrovně: 32 000 znaků bezplatné vrstvy, 64 000 pro základní a 4 miliony úrovně Standard, Standard S2 a Standard S3. Upozornění je zahrnutý v odpovědi stav indexeru pro oříznutá dokumenty.  

* Metadata zadané uživatelem na objekt blob, pokud existuje, extrahují se vlastnosti znění.
* Extrahují se vlastnosti metadat objektu blob standardní do následujících polí:

  * **metadata\_úložiště\_název** (Edm.String) – název souboru objektu blob. Například pokud máte /my-container/my-folder/subfolder/resume.pdf objektů blob, hodnotou tohoto pole je `resume.pdf`.
  * **metadata\_úložiště\_cesta** (Edm.String) – úplný identifikátor URI objektu blob, včetně účtu úložiště. Například `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`.
  * **metadata\_úložiště\_obsah\_typ** (Edm.String) - obsah typu, jak je uvedeno v kódu, který jste použili k nahrání objektu blob. Například, `application/octet-stream`.
  * **metadata\_úložiště\_poslední\_upravit** (Edm.DateTimeOffset) - naposledy změněno časové razítko pro objekt blob. Služba Azure Search používá k identifikaci změněné objekty BLOB, aby se zabránilo přeindexování všechno, co po počáteční indexování tímto časovým razítkem.
  * **metadata\_úložiště\_velikost** (Edm.Int64) – objekt blob velikost v bajtech.
  * **metadata\_úložiště\_obsah\_md5** (Edm.String) – hodnoty hash MD5 obsah objektů blob, pokud je k dispozici.
* Metadata vlastnosti specifické pro každý dokument formátu jsou extrahovány do polí uvedených [tady](#ContentSpecificMetadata).

Není nutné definovat všechny výše uvedené vlastnosti polí v indexu vyhledávání – zachytit pouze vlastnosti, které potřebujete pro vaši aplikaci.

> [!NOTE]
> Názvy polí v indexu existující často, bude lišit od názvů polí vygenerovaných během extrakce dokumentu. Můžete použít **mapování polí** k mapování názvů vlastností na názvy polí v indexu vyhledávání k dispozici služba Azure Search. Zobrazí se pole, které mapování použijte následující příklad.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definování klíče dokumentů a mapování polí
Ve službě Azure Search je klíč dokumentu jednoznačně identifikuje dokumentu. Každý index vyhledávání musí mít přesně jeden klíčové pole typu Edm.String. Klíčové pole je povinné pro každý dokument, který je přidáván do indexu (je ve skutečnosti jediné povinné pole).  

Měli byste pečlivě zvážit, které extrahované pole by měla být mapována na pole klíče indexu. Kandidáty jsou:

* **metadata\_úložiště\_název** – to může být vhodné Release candidate, ale Všimněte si, že 1) názvy nemusí být unikátní, máte objekty BLOB se stejným názvem v různých složkách a (2) název může obsahovat znaky, které jsou Neplatné v klíči dokumentu, jako je například spojovníky. Můžete vyřešit pomocí neplatné znaky `base64Encode` [pole mapování funkce](search-indexer-field-mappings.md#base64EncodeFunction) – Pokud je to provedete, nezapomeňte při předávání v rozhraní API volá jako je například vyhledávání kódovat klíče dokumentu. (Například v rozhraní .NET můžete použít [UrlTokenEncode metoda](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) k tomuto účelu).
* **metadata\_úložiště\_cesta** – úplné cesty zajistí jedinečnost ale jednoznačně obsahuje cestu `/` znaky, které jsou [neplatné v klíči dokumentu](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak je uvedeno výše, máte možnost kódování klíče pomocí `base64Encode` [funkce](search-indexer-field-mappings.md#base64EncodeFunction).
* Pokud žádná z výše uvedených možností pro vás nejvhodnější, můžete přidat vlastnost vlastních metadat pro objekty BLOB. Tato možnost však vyžaduje váš proces nahrání objektu blob přidat tuto vlastnost metadat do všech objektů BLOB. Protože klíč se vyžaduje vlastnost, všechny objekty BLOB, které nemají tuto vlastnost se nepodaří indexovat.

> [!IMPORTANT]
> Pokud neexistuje žádné explicitní mapování pro klíčové pole v indexu, automaticky používá Azure Search `metadata_storage_path` jako klíč a base-64 kóduje hodnoty klíče (druhá možnost výše).
>
>

V tomto příkladu můžeme vybrat `metadata_storage_name` pole jako klíč dokumentu. Předpokládejme také indexu má pole klíče s názvem `key` a pole `fileSize` pro ukládání velikost dokumentu. Propojí věci podle potřeby, zadejte následující mapování polí při vytváření nebo aktualizaci indexer:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Aby to všechno dohromady, jak můžete přidat mapování polí a povolit kódování base-64 klíčů pro existujícího indexeru:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2017-11-11
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
> Další informace o mapování polí najdete v tématu [v tomto článku](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Řízení, které objekty BLOB se indexují.
Můžete řídit, které objekty BLOB se indexují a které se přeskočí.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexování pouze objektů BLOB pomocí konkrétní přípony souborů
Můžete indexovat pouze objektů BLOB pomocí přípony názvů souborů pomocí `indexedFileNameExtensions` parametr konfigurace indexeru. Hodnota je řetězec obsahující čárkou oddělený seznam přípon souborů (s úvodní tečky). Například pro index pouze. PDF a. Objekty BLOB DOCX, postupujte takto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Vyloučení objektů BLOB pomocí konkrétní přípony souborů
Můžete vyloučit indexování s využitím objektů BLOB pomocí konkrétní přípony názvů souborů `excludedFileNameExtensions` konfigurační parametr. Hodnota je řetězec obsahující čárkou oddělený seznam přípon souborů (s úvodní tečky). Například na všechny objekty BLOB s výjimkou souborů s index. PNG a. Rozšíření JPEG, postupujte takto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Pokud mají oba `indexedFileNameExtensions` a `excludedFileNameExtensions` parametry jsou k dispozici, Azure Search nejprve prohledá `indexedFileNameExtensions`, pak na `excludedFileNameExtensions`. To znamená, že pokud je k dispozici v obou seznamech stejnou příponu, bude vyloučena z indexování.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Řízení, které části objektu blob se indexují.

Můžete řídit, které části objektů BLOB jsou indexovány pomocí `dataToExtract` konfigurační parametr. To můžete provést následující hodnoty:

* `storageMetadata` -Určuje pouze [uživatelem zadaných metadat a vlastností objektu blob standardní](../storage/blobs/storage-properties-metadata.md) jsou indexovány.
* `allMetadata` -Určuje, že metadata úložiště a [metadata specifická pro typ obsahu](#ContentSpecificMetadata) extrahovat z objektu blob se indexují obsah.
* `contentAndMetadata` -Určuje, že všechna metadata a textový obsah extrahovat z objektu blob se indexují. Toto je výchozí hodnota.

Například chcete-li indexovat jenom metadata úložiště, použijte tento příkaz:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Možnost řídit, jak jsou indexovány objekty BLOB metadata objektu blob

Parametry konfigurace je popsáno výše se vztahují na všechny objekty BLOB. V některých případech můžete ovládací prvek jak *jednotlivých objektů blob* jsou indexovány. Můžete provést přidáním následující vlastnosti metadat objektu blob a hodnoty:

| Název vlastnosti | Hodnota vlastnosti | Vysvětlení |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Dává pokyn indexeru blob zcela přeskočení objektu blob. Dojde k pokusu o extrahování metadat ani obsah. To je užitečné, když konkrétní objekt blob opakovaně a přeruší indexovacím procesem. |
| AzureSearch_SkipContent |"true" |Jedná se o ekvivalent z `"dataToExtract" : "allMetadata"` nastavení popisu [nad](#PartsOfBlobToIndex) omezená na konkrétní objekt blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Práce s chybami

Ve výchozím nastavení indexeru blob zastaví, jakmile nalezne objekt blob se nepodporovaný typ obsahu (například obrázek). Samozřejmě můžete `excludedFileNameExtensions` parametru pro přeskočení určité typy obsahu. Ale budete muset index objekty BLOB bez znalosti předem všechny možné typy obsahu. Chcete-li pokračovat, indexování, když je zjištěn nepodporovaný typ obsahu, nastavte `failOnUnsupportedContentType` parametr konfigurace `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Pro některé objekty BLOB Azure Search nelze určit typ obsahu, nebo nelze zpracovat dokument jinak nepodporuje typ obsahu. Chcete-li ignorovat tento režim selhání, nastavte `failOnUnprocessableDocument` konfigurační parametr na hodnotu false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Služba Azure Search omezuje velikost objektů BLOB, která jsou indexována. Tato omezení jsou popsané v [omezení služby Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Nadměrné velikosti za objekty BLOB jsou považována za chyby ve výchozím nastavení. Ale můžete pořád indexovat metadata úložiště objektů BLOB nadměrné velikosti Pokud nastavíte `indexStorageMetadataOnlyForOversizedDocuments` konfigurační parametr na hodnotu true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Můžete také dále indexování v případě chyby v libovolném bodě zpracování, při analýze objektů BLOB nebo při přidávání dokumentů do indexu. Chcete-li ignorovat konkrétní počet chyb, nastavte `maxFailedItems` a `maxFailedItemsPerBatch` parametry konfigurace na požadované hodnoty. Příklad:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstranění duplicit
Při nastavování indexeru blob spustit podle plánu reindexes pouze změněné objekty BLOB, vzhledem k objektu blob `LastModified` časové razítko.

> [!NOTE]
> Není nutné určit zásady detekce změn – přírůstkové indexování se povolí automaticky za vás.

Pro podporu odstraňování dokumentů, použijte s přístupem "obnovitelné odstranění". Při odstranění objektů BLOB rovnou předplatit, neodeberou odpovídající dokumenty z indexu vyhledávání. Místo toho použijte následující kroky:  

1. Přidání vlastních metadat vlastnosti do objektu blob k označení do služby Azure Search je logicky odstraněna
2. Nakonfigurovat zásadu obnovitelného odstranění duplicit ve zdroji dat.
3. Po zpracování objektů blob (jak je vidět ve stavu indexer API) indexeru můžete fyzicky odstranit objekt blob

Například následující zásady bude považovat za objekt blob, která se má odstranit, pokud má vlastnost metadat `IsDeleted` s hodnotou `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Indexování velkých datových sad

Indexování objektů BLOB může být časově náročný proces. V případech, kdy máte milionů objektů BLOB k indexování můžete urychlit indexování dělení dat a zpracování dat v paralelní pomocí několik indexerů. Zde je, jak můžete nastavit toto:

- Svá data dělit do více kontejnerů objektů blob nebo virtuální složky
- Nastavte několik zdrojů dat Azure Search, jeden pro každý kontejner nebo složka. Chcete-li přejít do složky objektů blob, použijte `query` parametr:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Vytvořte odpovídající indexer pro každý datový zdroj. Všechny indexery může odkazovat na stejnou cílovým indexem vyhledávání.  

- Jedna jednotka služby search ve své službě můžete spustit indexerů v daném okamžiku. Vytváření několik indexerů, jak je popsáno výše je užitečná pouze pokud, skutečně běží paralelně. K paralelnímu spouštění více indexerů, horizontálně navýšit kapacitu vaší vyhledávací služby tak, že vytvoříte odpovídající počet oddílů a replik. Například pokud vaše vyhledávací služba má 6 jednotek vyhledávání (například 2 oddíly x 3 repliky), poté 6 indexery spouštět současně, což vede k six-fold nárůst propustnost indexování. Další informace o škálování a plánování kapacity najdete v tématu [úrovně škálování prostředků pro dotazy a indexování úloh ve službě Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexování dokumentů spolu s souvisejících dat

Můžete chtít "sestavení" dokumenty z více zdrojů v indexu. Chcete například sloučit text z objektů BLOB s další metadata uložená v databázi Cosmos DB. Nasdílení změn indexování rozhraní API spolu s různými indexery můžete použít i Vybudujte vyhledávání dokumentů z více částí. 

Aby to fungovalo všechny indexery a další součásti musí shodnout na klíč dokumentu. Podrobný návod najdete v článku externí: [Kombinovat dokumenty s ostatními daty ve službě Azure Search ](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexování prostý text 

Pokud všechny objekty BLOB obsahují prostý text ve stejném kódování, může výrazně zlepšit výkon indexování pomocí **text režim parsování**. Chcete-li použít režim parsování text, nastavte `parsingMode` konfigurační vlastnost `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Ve výchozím nastavení `UTF-8` považován za kódování. Pokud chcete zadat jiné kódování, použijte `encoding` vlastnost konfigurace: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Vlastnosti metadat obsahu specifické pro typ.
Následující tabulka shrnuje zpracování u každé formát dokumentu a popisuje vlastnosti metadata extrahovaná modulem Azure Search.

| Formát dokumentu / typ obsahu | Vlastnosti metadat konkrétní typ obsahu | Podrobnosti o zpracování |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Odstranit kód HTML a extrahovat text |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrahovat text, včetně vložené dokumenty (s výjimkou imagí) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahovat text, včetně vložené dokumenty |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrahovat text, včetně vložené dokumenty |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahovat text, včetně vložené dokumenty |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrahovat text, včetně vložené dokumenty |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahovat text, včetně vložené dokumenty |
| Chyba (aplikace a vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrahovat text, včetně vložené dokumenty |
| ZPRÁVA (aplikace/vnd.ms – outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahovat text, včetně příloh |
| ZIP (aplikace a PSČ) |`metadata_content_type` |Rozbalte text z všechny dokumenty v archivu |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Odstranit kód XML a extrahovat text |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Extrakce textu<br/>POZNÁMKA: Pokud je potřeba extrahovat více polí dokumentu z objektů blob JSON, naleznete v tématu [JSON indexování objektů blob](search-howto-index-json-blobs.md) podrobnosti |
| EML (zpráva/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrahovat text, včetně příloh |
| RTF (application/rtf). |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Extrakce textu|
| Prostý text (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Extrakce textu|


## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte požadavky na funkce nebo nápady na vylepšení, dejte nám vědět, na našem [webu UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
