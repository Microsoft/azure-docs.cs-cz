---
title: Konfigurace indexeru objektů BLOB
titleSuffix: Azure Cognitive Search
description: Nastavte indexer Azure Blob pro automatizaci indexování obsahu objektů BLOB pro operace fulltextového vyhledávání v Azure Kognitivní hledání.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 6f70ae726cf41395e46760dc5cf7da5b4d61478a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802892"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Jak nakonfigurovat indexování objektů BLOB v Kognitivní hledání

Indexer objektů BLOB se používá k ingestování obsahu z úložiště objektů BLOB v Azure do indexu Kognitivní hledání. Indexery objektů BLOB se často používají při [obohacení AI](cognitive-search-concept-intro.md), kde připojené [dovednosti](cognitive-search-working-with-skillsets.md) přidávají image a zpracování přirozeného jazyka pro vytváření prohledávatelných obsahu. Můžete ale také použít indexery objektů BLOB bez rozšíření AI a ingestovat obsah z textových dokumentů, jako jsou soubory PDF, systém Microsoft Office dokumenty a formáty souborů.

V tomto článku se dozvíte, jak nakonfigurovat indexer objektů BLOB pro kterýkoli scénář. Pokud neznáte koncepty indexeru, začněte s [indexery ve službě Azure kognitivní hledání](search-indexer-overview.md) a [vytvořte indexer vyhledávání](search-howto-create-indexers.md) před začnete do indexování objektů BLOB.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Podporované formáty dokumentů

Indexer objektů BLOB v Azure Kognitivní hledání může extrahovat text z následujících formátů dokumentů:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Definice zdrojů dat

Hlavním rozdílem mezi indexerem objektů BLOB a jakýmkoli jiným indexerem je definice zdroje dat, která je přiřazená indexeru. Definice zdroje dat určuje typ zdroje dat ("Type": "azureblobu") a další vlastnosti pro ověřování a připojení k obsahu, který se má indexovat.

Definice zdroje dat objektu BLOB vypadá podobně jako v následujícím příkladu:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

`"credentials"`Vlastnost může být připojovací řetězec, jak je znázorněno v předchozím příkladu, nebo jeden z alternativních přístupů popsaných v následující části. `"container"`Vlastnost poskytuje umístění obsahu v rámci Azure Storage a `"query"` slouží k určení podsložky v kontejneru. Další informace o definicích zdrojů dat najdete v tématu [Vytvoření zdroje dat (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Přihlašovací údaje

Přihlašovací údaje pro kontejner objektů blob můžete zadat jedním z těchto způsobů:

**Připojovací řetězec spravované identity**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Tento připojovací řetězec nevyžaduje klíč účtu, ale je nutné postupovat podle pokynů pro [nastavení připojení k účtu Azure Storage pomocí spravované identity](search-howto-managed-identities-storage.md).

**Připojovací řetězec pro úplný přístup k účtu úložiště**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

Připojovací řetězec můžete z Azure Portal získat tak, že přejdete do okna účtu úložiště > nastavení > klíče (pro účty klasického úložiště) nebo nastavení > přístupových klíčů (pro Azure Resource Manager účty úložiště).

Připojovací řetězec **sdíleného přístupového podpisu** (SAS) účtu úložiště:`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

SAS by měl mít seznam a oprávnění číst pro kontejnery a objekty (v tomto případě objekty BLOB).

**Sdílený přístupový podpis kontejneru**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS by měl mít v kontejneru oprávnění list a Read. Další informace o sdílených přístupových podpisech úložiště najdete v tématu [použití sdílených přístupových podpisů](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Pokud používáte přihlašovací údaje SAS, budete muset pravidelně aktualizovat přihlašovací údaje ke zdroji dat pomocí obnovených signatur, aby se předešlo jejich vypršení platnosti. Pokud vyprší platnost přihlašovacích údajů SAS, indexer selže s chybovou zprávou podobnou "přihlašovací údaje zadané v připojovacím řetězci jsou neplatné nebo vypršely".  

## <a name="index-definitions"></a>Definice indexu

Index určuje pole v dokumentu, atributech a dalších konstrukcích, které prohledají možnosti vyhledávání. Všechny indexery vyžadují, abyste zadali definici indexu hledání jako cíl. Následující příklad vytvoří jednoduchý index pomocí [indexu Create (REST API)](/rest/api/searchservice/create-index). 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

Definice indexu vyžadují, aby jedno pole v `"fields"` kolekci fungovalo jako klíč dokumentu. Definice indexu by měly také zahrnovat pole pro obsah a metadata.

**`content`** Pole je běžné pro obsah objektu BLOB. Obsahuje text extrahovaný z objektů BLOB. Vaše definice tohoto pole může vypadat podobně jako v předchozím příkladu. Tento název nemusíte používat, ale umožňuje využít výhod mapování implicitních polí. Indexer objektů BLOB může odeslat obsah objektu blob do pole Content EDM. String v indexu, aniž by se vyžadovalo mapování polí.

Můžete také přidat pole pro všechna metadata objektů blob, která chcete v indexu. Indexer může číst vlastnosti vlastních metadat, standardní vlastnosti [metadat](#indexing-blob-metadata) a vlastnosti [metadat specifické pro obsah](search-blob-metadata-properties.md) . Další informace o indexech najdete v tématu [vytvoření indexu](search-what-is-an-index.md).

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definování klíčů dokumentu a mapování polí

V indexu vyhledávání klíč dokumentu jednoznačně identifikuje každý dokument. Pole, které zvolíte, musí být typu `Edm.String` . V případě obsahu objektů BLOB jsou v objektu BLOB vlastnosti metadat nejlepší kandidáti pro klíč dokumentu.

+ **`metadata_storage_name`** – Tato vlastnost je kandidát, ale pouze v případě, že jsou názvy jedinečné ve všech kontejnerech a složkách, které indexuje. Bez ohledu na umístění objektu BLOB je konečným výsledkem to, že klíč dokumentu (název) musí být v indexu vyhledávání jedinečný po indexování veškerého obsahu. 

  Dalším možným problémem s názvem úložiště je, že může obsahovat znaky, které jsou pro klíče dokumentu neplatné, například pomlčky. Pomocí `base64Encode` [funkce mapování polí](search-indexer-field-mappings.md#base64EncodeFunction)můžete zpracovat neplatné znaky. Pokud to uděláte, nezapomeňte při jejich předávání v voláních rozhraní API, jako je například [vyhledávací dokument (REST)](/rest/api/searchservice/lookup-document), zakódovat klíče dokumentů. V rozhraní .NET můžete použít [metodu UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) ke kódování znaků.

+ **`metadata_storage_path`** -použití úplné cesty zaručuje jedinečnost, ale cesta má jednoznačně `/` znaky, které jsou [v klíči dokumentu neplatné](/rest/api/searchservice/naming-rules). Jak je uvedeno výše, můžete použít `base64Encode` [funkci](search-indexer-field-mappings.md#base64EncodeFunction) ke kódování znaků.

+ Třetí možností je přidat do objektů BLOB vlastnost vlastní metadata. Tato možnost vyžaduje, aby váš proces nahrání objektu BLOB přidal tuto vlastnost metadat do všech objektů BLOB. Vzhledem k tomu, že klíč je povinná vlastnost, všechny objekty blob, u kterých chybí hodnota, nebudou indexovány.

> [!IMPORTANT]
> Pokud pro klíčové pole v indexu neexistuje explicitní mapování, Azure Kognitivní hledání automaticky používá `metadata_storage_path` jako klíčová a základní-64 kódování hodnot klíče (druhá možnost výše).
>

#### <a name="example"></a>Příklad

Následující příklad znázorňuje `metadata_storage_name` klíč dokumentu. Předpokládat, že index obsahuje klíčové pole s názvem `key` a jiné pole s názvem `fileSize` pro uložení velikosti dokumentu. [Mapování polí](search-indexer-field-mappings.md) v přidružení polí definice indexeru vytvoří a `metadata_storage_name` má [ `base64Encode` funkci mapování polí](search-indexer-field-mappings.md#base64EncodeFunction) , která zpracovává nepodporované znaky.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Jak nastavit zakódované pole "prohledávatelné"

K dispozici jsou situace, kdy potřebujete použít zakódovanou verzi pole jako `metadata_storage_path` klíč, ale také je potřeba, aby toto pole bylo možné prohledávat (bez kódování) v indexu vyhledávání. Aby bylo možné podporovat oba případy použití, lze namapovat `metadata_storage_path` na dvě pole; jeden pro klíč (kódovaný) a druhý pro pole cesty, které můžeme předpokládat, má ve schématu indexu označení "prohledávatelné". Následující příklad ukazuje dvě mapování polí pro `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Obsah a metadata indexu

Objekty blob obsahují obsah a metadata. Můžete určit, které části objektů BLOB budou indexovány pomocí `dataToExtract` parametru konfigurace. Může mít následující hodnoty:

+ `contentAndMetadata` – Určuje, že se indexuje všechna metadata a textový obsah extrahovaný z objektu BLOB. Toto je výchozí hodnota.

+ `storageMetadata` – Určuje, že se indexují jenom [standardní vlastnosti objektů BLOB a metadata zadaná uživatelem](../storage/blobs/storage-blob-container-properties-metadata.md) .

+ `allMetadata` – Určuje, že standardní vlastnosti objektů BLOB a všechna [metadata pro nalezené typy obsahu](search-blob-metadata-properties.md) se extrahují z obsahu objektu BLOB a indexovaného.

Chcete-li například indexovat pouze metadata úložiště, použijte:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Indexování obsahu objektů BLOB

Ve výchozím nastavení jsou objekty BLOB se strukturovaným obsahem, jako je JSON nebo CSV, indexované jako jeden blok textu. Pokud ale dokumenty JSON nebo CSV mají interní strukturu (oddělovače), můžete přiřadit režimy analýzy pro generování jednotlivých dokumentů hledání pro každý řádek nebo prvek. Další informace najdete v tématu [indexování](search-howto-index-json-blobs.md) objektů BLOB JSON a [indexování objektů BLOB ve formátu CSV](search-howto-index-csv-blobs.md).

Složený nebo vložený dokument (například archiv ZIP, dokument aplikace Word s vloženým e-mailem Outlooku obsahující přílohy nebo. Soubor MSG s přílohami) je také indexován jako jeden dokument. Například všechny obrázky extrahované z příloh. V poli normalized_images se vrátí soubor MSG.

Textový obsah dokumentu se extrahuje do pole řetězce s názvem `content` .

  > [!NOTE]
  > Azure Kognitivní hledání v závislosti na cenové úrovni omezuje množství textu, který extrahuje. Aktuální [omezení služby](search-limits-quotas-capacity.md#indexer-limits) jsou 32 000 znaků pro úroveň Free, 64 000 pro Basic, 4 000 000 pro standard, 8 000 000 pro standard S2 a 16 000 000 pro standard S3. V odpovědi na stav indexeru pro zkrácené dokumenty je k dispozici upozornění.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Indexování metadat objektů BLOB

Indexery můžou také indexovat metadata objektů BLOB. Nejprve mohou být všechny uživatelsky definované vlastnosti metadat extrahovány do doslovného znění. Chcete-li získat hodnoty, je nutné definovat pole v indexu hledání typu se `Edm.String` stejným názvem jako klíč metadat objektu BLOB. Pokud má například objekt BLOB klíč metadat `Sensitivity` s hodnotou `High` , měli byste definovat pole s názvem `Sensitivity` v indexu vyhledávání a bude vyplněno hodnotou `High` .

Druhý, vlastnosti standardních metadat objektů BLOB se dají extrahovat do níže uvedených polí. Indexer objektů BLOB automaticky vytvoří mapování interních polí pro tyto vlastnosti metadat objektů BLOB. Stále musíte přidat pole, do kterých chcete definici indexu použít, ale můžete vynechat vytváření mapování polí v indexeru.

  + **metadata_storage_name** ( `Edm.String` ) – název souboru objektu BLOB. Pokud máte například objekt BLOB/my-Container/My-Folder/subfolder/resume.pdf, hodnota tohoto pole je `resume.pdf` .

  + **metadata_storage_path** ( `Edm.String` ) – úplný identifikátor URI objektu blob, včetně účtu úložiště. Například `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`.

  + **metadata_storage_content_type** ( `Edm.String` ) – typ obsahu určený kódem, který jste použili k nahrání objektu BLOB. Například, `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` ) – časové razítko posledního upraveného objektu BLOB. Azure Kognitivní hledání používá toto časové razítko k identifikaci změněných objektů blob, aby nedocházelo k přeindexování všeho po počátečním indexování.

  + **metadata_storage_size** ( `Edm.Int64` ) – velikost objektu BLOB v bajtech

  + **metadata_storage_content_md5** ( `Edm.String` ) – hodnota hash MD5 obsahu objektu blob, je-li k dispozici.

  + **metadata_storage_sas_token** ( `Edm.String` ) – dočasný token SAS, který mohou používat [vlastní dovednosti](cognitive-search-custom-skill-interface.md) k získání přístupu k objektu BLOB. Tento token by neměl být uložen pro pozdější použití, protože může vypršet jeho platnost.

A konečně všechny vlastnosti metadat specifické pro formát dokumentu objektů blob, které indexuje, můžete také znázornit ve schématu indexu. Další informace o metadatech specifických pro obsah najdete v tématu [vlastnosti metadat obsahu](search-blob-metadata-properties.md).

Je důležité, abyste odkazovali na to, že nemusíte definovat pole pro všechny výše uvedené vlastnosti v indexu vyhledávání – stačí zachytit vlastnosti, které pro vaši aplikaci potřebujete.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Jak řídit indexování indexovaných objektů

Můžete určit, které objekty blob budou indexovány a které jsou vynechány typem souboru objektu BLOB nebo nastavením vlastností samotného objektu blob, což způsobí, že indexer bude přeskočí.

### <a name="include-specific-file-extensions"></a>Zahrnout konkrétní přípony souborů

Slouží `indexedFileNameExtensions` k zadání čárkami odděleného seznamu přípon souborů, které se mají indexovat (s počáteční tečkou). Například chcete-li indexovat pouze. Soubory PDF a. Objekty blob DOCX:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Vyloučit konkrétní přípony souborů

Slouží `excludedFileNameExtensions` k zadání čárkami odděleného seznamu přípon souborů, které se mají přeskočit (znovu s počáteční tečkou). Například pro indexování všech objektů BLOB s výjimkou. PNG a. Rozšíření JPEG:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Pokud `indexedFileNameExtensions` `excludedFileNameExtensions` jsou přítomny parametry a a, indexer se nejprve vyhledá a `indexedFileNameExtensions` potom na `excludedFileNameExtensions` . Pokud je stejná Přípona souboru v obou seznamech, bude vyloučena z indexování.

### <a name="add-skip-metadata-the-blob"></a>Přidat "Přeskočit" metadata objektu BLOB

Parametry konfigurace indexeru se vztahují na všechny objekty BLOB v kontejneru nebo složce. V některých případech je třeba určit, jak jsou *jednotlivé objekty blob* indexovány. To můžete provést přidáním následujících vlastností a hodnot metadat do objektů BLOB v úložišti objektů BLOB. Když indexer narazí na tyto vlastnosti, přeskočí objekt BLOB nebo jeho obsah v běhu indexování.

| Název vlastnosti | Hodnota vlastnosti | Vysvětlení |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Dá indexeru objektů BLOB úplný skok objektu BLOB. Nezkouší se žádná metadata ani extrakce obsahu. To je užitečné, když určitý objekt BLOB opakovaně selhává a přerušuje proces indexování. |
| `AzureSearch_SkipContent` |`"true"` |Jedná se o ekvivalent `"dataToExtract" : "allMetadata"` Nastavení popsaného [výše](#PartsOfBlobToIndex) na konkrétní objekt BLOB. |

## <a name="index-large-datasets"></a>Indexování velkých datových sad

Indexování objektů BLOB může být časově náročný proces. V případech, kdy máte v indexu miliony objektů blob, můžete urychlit indexování pomocí dělení dat a používání více indexerů k [paralelnímu zpracování dat](search-howto-large-index.md#parallel-indexing). Tady je postup, jak to můžete nastavit:

+ Rozdělit data do více kontejnerů objektů BLOB nebo virtuálních složek

+ Nastavte několik zdrojů dat, jeden pro každý kontejner nebo složku. Chcete-li odkazovat na složku objektů blob, použijte `query` parametr:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Vytvořte odpovídající indexer pro každý zdroj dat. Všechny indexery by měly ukazovat na stejný index vyhledávání cíle.  

+ Jedna jednotka pro hledání ve vaší službě může spustit jeden indexer v daném okamžiku. Vytvoření více indexerů, jak je popsáno výše, je užitečné pouze v případě, že jsou skutečně spouštěny paralelně.

  Pokud chcete spustit více indexerů paralelně, Škálujte vyhledávací službu tím, že vytvoříte příslušný počet oddílů a replik. Pokud například služba vyhledávání obsahuje 6 jednotek hledání (například 2 oddíly × 3 repliky), pak 6 indexerů může běžet současně, což má za následek zvětšení v propustnosti indexování po šesti rozložení. Další informace o škálování a plánování kapacity najdete v tématu [Úprava kapacity služby Azure kognitivní hledání](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Zpracování chyb

K chybám, které se běžně vyskytují během indexování, patří nepodporované typy obsahu, chybějící obsah nebo objekty BLOB s větší velikostí.

Ve výchozím nastavení se indexer objektů BLOB zastaví, jakmile narazí na objekt BLOB s nepodporovaným typem obsahu (například obrázek). Pomocí `excludedFileNameExtensions` parametru můžete přeskočit určité typy obsahu. Můžete ale chtít indexovat, aby pokračoval i v případě, že dojde k chybám, a pak později ladit jednotlivé dokumenty. Další informace o chybách indexeru najdete v tématu [řešení běžných potíží indexerů](search-indexer-troubleshooting.md) a [chyb a upozornění indexerů](cognitive-search-common-errors-warnings.md).

### <a name="respond-to-errors"></a>Reakce na chyby

Existují čtyři vlastnosti indexeru, které ovládají odpověď indexeru, když dojde k chybám. Následující příklady znázorňují způsob nastavení těchto vlastností v definici indexeru. Pokud indexer již existuje, můžete tyto vlastnosti přidat úpravou definice na portálu.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` a `"maxFailedItemsPerBatch"`

Pokud dojde k chybám v jakémkoli okamžiku zpracování, při analýze objektů BLOB nebo při přidávání dokumentů do indexu můžete pokračovat v indexování. Nastavte tyto vlastnosti na počet přijatelných chyb. Hodnota `-1` umožňuje zpracování bez ohledu na to, kolik chyb nastane. V opačném případě je hodnota kladné celé číslo.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` a `"failOnUnprocessableDocument"` 

U některých objektů BLOB nedokáže Azure Kognitivní hledání určit typ obsahu nebo nemůže zpracovat dokument jiného podporovaného typu obsahu. Pokud chcete tyto chybové podmínky ignorovat, nastavte parametry konfigurace na `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>Uvolnit omezení indexeru

Můžete také nastavit [Vlastnosti konfigurace objektů BLOB](/rest/api/searchservice/create-indexer#blob-configuration-parameters) , které účinně určují, jestli existuje chybový stav. Následující vlastnost může zmírnit omezení a potlačit chyby, k nimž by jinak mohlo dojít.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` indexování metadat úložiště pro obsah objektu blob, který je příliš velký pro zpracování. Ve výchozím nastavení jsou objekty BLOB s příliš velikostí považovány za chyby. Omezení velikosti objektů BLOB najdete v tématu [omezení služby](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Viz také

+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Vytvoření indexeru](search-howto-create-indexers.md)
+ [Přehled objektů pro obohacení AI nad objekty blob](search-blob-ai-integration.md)
+ [Přehled hledání objektů BLOB](search-blob-storage-integration.md)