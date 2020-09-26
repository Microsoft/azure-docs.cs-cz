---
title: Hledání v obsahu služby Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Naučte se indexovat dokumenty v Azure Blob Storage a extrahovat text z dokumentů pomocí Azure Kognitivní hledání.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 2ba511d3747ba308ae04ab1bbe3dcb89bca6a8a8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328288"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Postup indexování dokumentů v Azure Blob Storage s využitím Azure Kognitivní hledání

Tento článek popisuje, jak používat Azure Kognitivní hledání k indexování dokumentů (například souborů PDF, systém Microsoft Office dokumentů a několika dalších běžných formátů) uložených v úložišti objektů BLOB v Azure. Nejprve se vysvětlují základy nastavení a konfigurace indexeru objektů BLOB. Potom nabízí hlubší průzkum chování a scénářů, se kterými se pravděpodobně setkáte.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Podporované formáty dokumentů
Indexer objektů BLOB může extrahovat text z následujících formátů dokumentů:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Nastavení indexování objektů BLOB
Službu Azure Blob Storage indexer můžete nastavit pomocí:

* [Azure Portal](https://ms.portal.azure.com)
* [REST API](/rest/api/searchservice/Indexer-operations) kognitivní hledání Azure
* Sada Azure Kognitivní hledání [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> Některé funkce (například mapování polí) ještě nejsou k dispozici na portálu a je nutné je použít programově.
>

Tady předvádíme tok pomocí REST API.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Zdroj dat určuje, která data se mají indexovat, přihlašovací údaje potřebné pro přístup k datům a zásady pro efektivní identifikaci změn dat (nové, změněné nebo odstraněné řádky). Zdroj dat může použít více indexerů ve stejné vyhledávací službě.

Pro indexování objektů BLOB musí mít zdroj dat následující požadované vlastnosti:

* **název** je jedinečný název zdroje dat v rámci vyhledávací služby.
* **typ** musí být `azureblob` .
* **přihlašovací údaje** poskytují jako parametr připojovací řetězec účtu úložiště `credentials.connectionString` . Podrobnosti najdete v tématu [jak zadat přihlašovací údaje](#Credentials) níže.
* **kontejner** určuje kontejner v účtu úložiště. Ve výchozím nastavení jsou všechny objekty BLOB v kontejneru navýšené. Pokud chcete indexovat objekty blob pouze v konkrétním virtuálním adresáři, můžete tento adresář zadat pomocí volitelného parametru **dotazu** .

Vytvoření zdroje dat:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   
```

Další informace o rozhraní API Create DataSource najdete v tématu [Create DataSource](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak zadat přihlašovací údaje ####

Přihlašovací údaje pro kontejner objektů blob můžete zadat jedním z těchto způsobů:

- **Připojovací řetězec spravované identity**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` Tento připojovací řetězec nevyžaduje klíč účtu, ale musíte postupovat podle pokynů pro [nastavení připojení k účtu Azure Storage pomocí spravované identity](search-howto-managed-identities-storage.md).
- **Řetězec pro připojení k účtu úložiště s úplným přístupem**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` připojovací řetězec můžete z Azure Portal získat tak, že přejdete do okna účtu úložiště > nastavení > klíče (pro účty klasického úložiště), nebo nastavení > přístupových klíčů (pro Azure Resource Manager účty úložiště).
- Připojovací řetězec **sdíleného přístupového podpisu** (SAS) účtu úložiště: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS by měl mít v kontejnerech a objektech (v tomto případě objekty BLOB) oprávnění list a čtení.
-  **Sdílený přístupový podpis kontejneru**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS by měl mít v kontejneru oprávnění list a Read.

Další informace o sdílených přístupových podpisech úložiště najdete v tématu [použití sdílených přístupových podpisů](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Pokud používáte přihlašovací údaje SAS, budete muset pravidelně aktualizovat přihlašovací údaje ke zdroji dat pomocí obnovených signatur, aby se předešlo jejich vypršení platnosti. Pokud vyprší platnost přihlašovacích údajů SAS, indexer selže a zobrazí se chybová zpráva podobná této `Credentials provided in the connection string are invalid or have expired.` .  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributech a dalších konstrukcích, které prohledají možnosti vyhledávání.

Tady je postup, jak vytvořit index s `content` polem, které lze prohledávat a který ukládá text extrahovaný z objektů BLOB:   

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

Další informace o vytváření indexů najdete v tématu [vytvoření indexu](/rest/api/searchservice/create-index) .

### <a name="step-3-create-an-indexer"></a>Krok 3: vytvoření indexeru
Indexer připojuje zdroj dat k cílovému vyhledávacímu indexu a poskytuje plán pro automatizaci aktualizace dat.

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Tento indexer se spustí každé dvě hodiny (časový interval je nastaven na "PT2H"). Pokud chcete indexer spustit každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je nepovinný – Pokud je vynechaný, indexer se při vytvoření spustí jenom jednou. Můžete ale kdykoli spustit indexer na vyžádání.   

Další informace o rozhraní API Create indexeru najdete v části [Vytvoření indexeru](/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Jak Azure Kognitivní hledání indexuje objekty blob

V závislosti na [konfiguraci indexeru](#PartsOfBlobToIndex)může indexer objektů BLOB indexovat jenom metadata úložiště (užitečné v případě, že se jenom zajímáte o metadata a nepotřebujete indexovat obsah objektů BLOB), úložiště a metadata obsahu nebo metadata i textový obsah. Ve výchozím nastavení indexer extrahuje metadata i obsah.

> [!NOTE]
> Ve výchozím nastavení jsou objekty BLOB se strukturovaným obsahem, jako je JSON nebo CSV, indexované jako jeden blok textu. Pokud chcete indexovat objekty blob JSON a CSV strukturovaným způsobem, přečtěte si další informace v tématu indexování objektů BLOB [JSON](search-howto-index-json-blobs.md) a [indexování objektů BLOB ve formátu CSV](search-howto-index-csv-blobs.md) .
>
> Složený nebo vložený dokument (například archiv ZIP, dokument aplikace Word s vloženým e-mailem Outlooku obsahující přílohy nebo. Soubor MSG s přílohami) je také indexován jako jeden dokument. Například všechny obrázky extrahované z příloh. V poli normalized_images se vrátí soubor MSG.

* Textový obsah dokumentu se extrahuje do pole řetězce s názvem `content` .

> [!NOTE]
> Azure Kognitivní hledání omezuje množství využívaného textu v závislosti na cenové úrovni: 32 000 znaků pro úroveň Free, 64 000 pro Basic, 4 000 000 pro standard, 8 000 000 pro standard S2 a 16 000 000 pro standard S3. V odpovědi na stav indexeru pro zkrácené dokumenty je k dispozici upozornění.  

* Uživatelsky definované vlastnosti metadat přítomné v objektu blob, pokud existují, jsou extrahovány do doslovného znění. Všimněte si, že to vyžaduje, aby pole bylo definováno v indexu se stejným názvem jako klíč metadat objektu BLOB. Pokud má váš objekt BLOB například klíč metadat `Sensitivity` s hodnotou `High` , měli byste definovat pole s názvem `Sensitivity` v indexu vyhledávání a bude vyplněno hodnotou `High` .
* Vlastnosti standardních metadat objektů BLOB jsou extrahovány do následujících polí:

  * ** \_ \_ název úložiště metadat** (EDM. String) – název souboru objektu BLOB. Pokud máte například objekt BLOB/my-Container/My-Folder/subfolder/resume.pdf, hodnota tohoto pole je `resume.pdf` .
  * ** \_ \_ cesta úložiště metadat** (EDM. String) – úplný identifikátor URI objektu blob, včetně účtu úložiště. Například `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`.
  * ** \_ \_ \_ typ obsahu úložiště metadat** (EDM. String) – typ obsahu určený kódem, který jste použili k nahrání objektu BLOB. Například, `application/octet-stream`.
  * ** \_ \_ naposledy \_ upravená metadata úložiště** (EDM. DateTimeOffset) – poslední změněné časové razítko pro objekt BLOB. Azure Kognitivní hledání používá toto časové razítko k identifikaci změněných objektů blob, aby nedocházelo k přeindexování všeho po počátečním indexování.
  * ** \_ \_ velikost úložiště metadat** (EDM. Int64) – velikost objektu BLOB v bajtech
  * **Metadata \_ úložiště \_ \_ MD5** (EDM. String) – hash MD5 obsahu objektů blob, pokud je k dispozici.
  * ** \_ \_ \_ token SAS úložiště metadat** (EDM. String) – dočasný token SAS, který může být používán [vlastní dovedností](cognitive-search-custom-skill-interface.md) k získání přístupu k objektu BLOB. Tento token by neměl být uložen pro pozdější použití, protože může vypršet jeho platnost.

* Vlastnosti metadat specifické pro jednotlivé formáty dokumentů jsou extrahovány do [zde](#ContentSpecificMetadata)uvedených polí.

Nemusíte definovat pole pro všechny výše uvedené vlastnosti v indexu vyhledávání – stačí zachytit vlastnosti, které pro aplikaci potřebujete.

> [!NOTE]
> Názvy polí v existujícím indexu se často liší od názvů polí generovaných během extrakce dokumentu. **Mapování polí** můžete použít k mapování názvů vlastností, které poskytuje Azure kognitivní hledání, na názvy polí v indexu vyhledávání. Zobrazí se příklad použití mapování polí níže.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definování klíčů dokumentu a mapování polí
V Azure Kognitivní hledání klíč dokumentu jednoznačně identifikuje dokument. Každý index vyhledávání musí mít přesně jedno pole klíče typu EDM. String. Klíčové pole je vyžadováno pro každý dokument, který je přidán do indexu (ve skutečnosti je to jediné povinné pole).  

Měli byste pečlivě zvážit, které extrahované pole by mělo být namapováno na pole klíče pro váš index. Kandidáti:

* ** \_ \_ název úložiště metadat** – může to být praktický kandidát, ale Všimněte si, že 1) názvy nemusí být jedinečné, protože v různých složkách můžete mít objekty BLOB se stejným názvem a 2) název může obsahovat znaky, které jsou v klíčích dokumentů neplatné, například pomlčky. Pomocí funkce mapování polí můžete pracovat s neplatnými znaky `base64Encode` [field mapping function](search-indexer-field-mappings.md#base64EncodeFunction) – Pokud to uděláte, nezapomeňte kódovat klíče dokumentů při jejich předávání v voláních rozhraní API, jako je například vyhledávání. (Například v .NET můžete k tomuto účelu použít [metodu UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode?view=netframework-4.8) ).
* ** \_ \_ cesta úložiště metadat** – při použití úplné cesty je zajištěna jedinečnost, ale cesta má jednoznačně `/` znaky, které jsou [v klíči dokumentu neplatné](/rest/api/searchservice/naming-rules).  Jak je uvedeno výše, máte možnost kódování klíčů pomocí `base64Encode` [funkce](search-indexer-field-mappings.md#base64EncodeFunction).
* Pokud žádná z výše uvedených možností nefunguje za vás, můžete do objektů BLOB přidat vlastní vlastnost metadat. Tato možnost ale vyžaduje, aby váš proces nahrání objektu BLOB přidal tuto vlastnost metadat do všech objektů BLOB. Vzhledem k tomu, že klíč je povinná vlastnost, všechny objekty blob, které tuto vlastnost nemají, nebudou indexovány.

> [!IMPORTANT]
> Pokud pro klíčové pole v indexu neexistuje explicitní mapování, Azure Kognitivní hledání automaticky používá `metadata_storage_path` jako klíčová a základní-64 kódování hodnot klíče (druhá možnost výše).
>
>

V tomto příkladu vybereme `metadata_storage_name` pole jako klíč dokumentu. Předpokládejme také, že váš index obsahuje klíčové pole s názvem `key` a pole `fileSize` pro uložení velikosti dokumentu. Pokud chcete, aby se při vytváření nebo aktualizaci indexeru vytvořily co nejvíce, zadejte následující mapování polí:

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

Pokud to chcete uvést dohromady, můžete přidat mapování polí a povolit kódování Base-64 klíčů pro existující indexer:

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
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
```

> [!NOTE]
> Další informace o mapování polí najdete v [tomto článku](search-indexer-field-mappings.md).
>
>

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>Co když potřebujete kódovat pole, abyste ho mohli použít jako klíč, ale chcete ho také vyhledat?

Existují situace, kdy potřebujete jako klíč použít zakódovanou verzi pole, jako je metadata_storage_path, ale také je potřeba, aby toto pole bylo možné prohledávat (bez kódování). Chcete-li tento problém vyřešit, můžete jej namapovat do dvou polí. ten, který se použije pro klíč, a druhý, který se použije pro účely vyhledávání. V příkladu pod polem *Key* obsahuje kódovanou cestu, zatímco pole *cesta* není zakódované a bude použito jako vyhledávací pole v indexu.

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
<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Řízení indexovaných objektů BLOB
Můžete určit, které objekty blob budou indexovány a které se přeskočí.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexovat pouze objekty BLOB s určitými příponami souborů
Pomocí parametru konfigurace indexeru můžete indexovat pouze objekty BLOB s příponami názvů souborů, které zadáte `indexedFileNameExtensions` . Hodnota je řetězec obsahující čárkami oddělený seznam přípon souborů (s počáteční tečkou). Například chcete-li indexovat pouze. Soubory PDF a. Objekty blob DOCX:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-with-specific-file-extensions"></a>Vyloučení objektů BLOB s určitými příponami souborů
Můžete vyloučit objekty BLOB s konkrétní příponou názvu souboru z indexování pomocí `excludedFileNameExtensions` parametru konfigurace. Hodnota je řetězec obsahující čárkami oddělený seznam přípon souborů (s počáteční tečkou). Například pro indexování všech objektů BLOB s výjimkou. PNG a. Rozšíření JPEG:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

Pokud `indexedFileNameExtensions` `excludedFileNameExtensions` jsou přítomny parametry i a, Azure kognitivní hledání nejprve vyhledá `indexedFileNameExtensions` , a potom na `excludedFileNameExtensions` . To znamená, že pokud se stejná Přípona souboru nachází v obou seznamech, bude vyloučena z indexování.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Řízení, které části objektu BLOB jsou indexované

Můžete určit, které části objektů BLOB budou indexovány pomocí `dataToExtract` parametru konfigurace. Může mít následující hodnoty:

* `storageMetadata` – Určuje, že se indexují jenom [standardní vlastnosti objektů BLOB a metadata zadaná uživatelem](../storage/blobs/storage-blob-container-properties-metadata.md) .
* `allMetadata` – Určuje, že se indexují metadata úložiště a [metadata specifická pro typ obsahu](#ContentSpecificMetadata) extrahovaná z obsahu objektu BLOB.
* `contentAndMetadata` – Určuje, že se indexuje všechna metadata a textový obsah extrahovaný z objektu BLOB. Toto je výchozí hodnota.

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

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Použití metadat objektů BLOB k řízení, jak jsou objekty blob indexovány

Výše popsané parametry konfigurace se vztahují na všechny objekty blob. V některých případech můžete chtít určit, jak jsou *jednotlivé objekty blob* indexovány. Můžete to udělat přidáním následujících vlastností a hodnot metadat objektu BLOB:

| Název vlastnosti | Hodnota vlastnosti | Vysvětlení |
| --- | --- | --- |
| AzureSearch_Skip |podmínka |Dá indexeru objektů BLOB úplný skok objektu BLOB. Nezkouší se žádná metadata ani extrakce obsahu. To je užitečné, když určitý objekt BLOB opakovaně selhává a přerušuje proces indexování. |
| AzureSearch_SkipContent |podmínka |Jedná se o ekvivalent `"dataToExtract" : "allMetadata"` Nastavení popsaného [výše](#PartsOfBlobToIndex) na konkrétní objekt BLOB. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Obchodování s chybami

Ve výchozím nastavení se indexer objektů BLOB zastaví, jakmile narazí na objekt BLOB s nepodporovaným typem obsahu (například obrázek). Můžete samozřejmě použít `excludedFileNameExtensions` parametr k přeskočení určitých typů obsahu. Je ale možné, že budete muset indexovat objekty blob bez znalosti všech možných typů obsahu předem. Pokud chcete pokračovat v indexování, když je nalezen nepodporovaný typ obsahu, nastavte `failOnUnsupportedContentType` parametr konfigurace na `false` :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

U některých objektů BLOB nedokáže Azure Kognitivní hledání určit typ obsahu nebo nemůže zpracovat dokument jiného podporovaného typu obsahu. Chcete-li tento režim selhání ignorovat, nastavte `failOnUnprocessableDocument` parametr konfigurace na hodnotu false:

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Kognitivní hledání omezuje velikost indexovaných objektů BLOB. Tato omezení jsou popsaná v [omezení služby v Azure kognitivní hledání](./search-limits-quotas-capacity.md). Ve výchozím nastavení jsou objekty BLOB s příliš velikostí považovány za chyby. Pokud ale nastavíte `indexStorageMetadataOnlyForOversizedDocuments` parametr konfigurace na hodnotu true, pořád ale můžete indexovat metadata úložiště objektů BLOB s více velikostmi. 

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Můžete také pokračovat v indexování, pokud dojde k chybám v jakémkoli okamžiku zpracování, při analýze objektů BLOB nebo při přidávání dokumentů do indexu. Chcete-li ignorovat určitý počet chyb, nastavte `maxFailedItems` `maxFailedItemsPerBatch` parametry konfigurace a na požadované hodnoty. Příklad:

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstraňování duplicit

Když nastavíte indexer objektů BLOB tak, aby se spouštěl podle plánu, přeindexuje jenom změněné objekty blob, které určuje `LastModified` časové razítko objektu BLOB.

> [!NOTE]
> Nemusíte určovat zásady detekce změn – přírůstkové indexování je pro vás povolené automaticky.

Pro podporu odstraňování dokumentů použijte přístup "obnovitelné odstranění". Pokud dojde k pravému odstranění objektů blob, odpovídající dokumenty nebudou odebrány z indexu vyhledávání.

Existují dva způsoby, jak implementovat postup obnovitelného odstranění. Obě jsou popsány níže.

### <a name="native-blob-soft-delete-preview"></a>Obnovitelné odstranění nativního objektu BLOB (Preview)

> [!IMPORTANT]
> Podpora pro nativní odstranění objektu BLOB je ve verzi Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2020-06-30-Preview](./search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

> [!NOTE]
> Při použití zásad podmíněného odstranění v nativním objektu BLOB musí být klíče dokumentu pro dokumenty v indexu buď vlastnost objektu blob, nebo metadata objektu BLOB.

V této metodě použijete nativní funkci [obnovitelného odstranění objektů BLOB](../storage/blobs/soft-delete-blob-overview.md) nabízenou úložištěm Azure Blob Storage. Pokud je v účtu úložiště zapnutá možnost nativní odstranění nativního objektu blob, má zdroj dat nativní sadu zásad pro tiché odstranění a indexer nalezne objekt blob, který byl převeden na měkký odstraněný stav, indexer odebere tento dokument z indexu. Při indexování objektů BLOB z Azure Data Lake Storage Gen2 není podporováno nativní zásady podmíněného odstranění objektu BLOB.

Použijte k tomu následující postup:
1. Povolí [nativní obnovitelné odstranění pro úložiště objektů BLOB v Azure](../storage/blobs/soft-delete-blob-overview.md). Doporučujeme nastavit zásady uchovávání informací na hodnotu, která je mnohem vyšší než plán intervalu indexeru. Tímto způsobem, pokud dojde k potížím s indexerem nebo pokud máte velký počet dokumentů k indexování, je dostatek času, aby indexer mohl nakonec zpracovat obnovitelné odstraněné objekty blob. Indexery Azure Kognitivní hledání odstraní jenom dokument z indexu, pokud je objekt BLOB zpracovává, když je ve stavu, kdy je odstraněný.
1. Nakonfigurujte zásady detekce nativního odstranění objektů BLOB ve zdroji dat. Příklad najdete níže. Vzhledem k tomu, že je tato funkce ve verzi Preview, musíte použít REST API verze Preview.
1. Spusťte indexer nebo nastavte indexer tak, aby běžel podle plánu. Když indexer spustí a zpracuje objekt blob, dokument se odebere z indexu.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
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

Pokud odstraníte objekt BLOB ze služby Azure Blob Storage s povoleným nativním tlumeným odstraněním v účtu úložiště, bude se objekt BLOB přecházet na měkký odstraněný stav, který vám nabídne možnost zrušit odstranění tohoto objektu BLOB v rámci doby uchování. Pokud má zdroj dat služby Azure Kognitivní hledání nativní zásady pro tiché odstranění objektů BLOB a indexer zpracovává měkký odstraněný objekt blob, odebere tento dokument z indexu. Pokud je tento objekt BLOB později neodstraněn, indexer nebude vždy přeindexován do tohoto objektu BLOB. Důvodem je skutečnost, že indexer určuje, které objekty BLOB se mají indexovat na základě `LastModified` časového razítka objektu BLOB. Pokud se odstraněný objekt BLOB neodstraní `LastModified` , jeho časové razítko se neaktualizuje, takže pokud indexer už zpracovává objekty BLOB s `LastModified` časovými razítky novějšími než u neodstraněného objektu blob, nebude znovu indexovat neodstraněný objekt BLOB. Chcete-li se ujistit, že je znovu indexován neodstraněný objekt blob, bude nutné aktualizovat `LastModified` časové razítko objektu BLOB. Jedním ze způsobů, jak to provést, je uložení metadat tohoto objektu BLOB. Nemusíte měnit metadata, ale znovu uložíte metadata aktualizuje `LastModified` časové razítko objektu BLOB tak, aby indexer věděl, že musí tento objekt BLOB znovu indexovat.

### <a name="soft-delete-using-custom-metadata"></a>Obnovitelné odstranění pomocí vlastních metadat

V této metodě použijete metadata objektu BLOB k označení, kdy by měl být dokument odebrán z indexu vyhledávání.

Použijte k tomu následující postup:

1. Přidejte do objektu BLOB vlastní dvojici klíč-hodnota metadat, abyste označili Azure Kognitivní hledání, že se logicky odstraní.
1. Nakonfigurujte zásady detekce sloupce tichého odstranění ve zdroji dat. Příklad najdete níže.
1. Jakmile indexer zpracuje objekt BLOB a odstraní ho z indexu, můžete ho odstranit pro úložiště objektů BLOB v Azure.

Například následující zásady považují objekt blob, který se má odstranit, pokud má vlastnost metadata `IsDeleted` s hodnotou `true` :

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
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
```

#### <a name="reindexing-undeleted-blobs"></a>Přeindexování neodstraněných objektů BLOB

Pokud jste v zdroji dat nastavili zásady detekce nepodmíněného odstranění sloupce, přidejte vlastní metadata do objektu BLOB s hodnotou značky a potom spusťte indexer, indexer odstraní tento dokument z indexu. Pokud chcete tento dokument znovu indexovat, jednoduše změňte hodnotu metadat obnovitelného odstranění pro daný objekt BLOB a znovu spusťte indexer.

## <a name="indexing-large-datasets"></a>Indexování velkých datových sad

Indexování objektů BLOB může být časově náročný proces. V případech, kdy máte v indexu miliony objektů blob, můžete urychlit indexování pomocí dělení dat a používání více indexerů k paralelnímu zpracování dat. Tady je postup, jak to můžete nastavit:

- Rozdělit data do více kontejnerů objektů BLOB nebo virtuálních složek
- Nastavte několik zdrojů dat Azure Kognitivní hledání, jeden pro každý kontejner nebo složku. Chcete-li odkazovat na složku objektů blob, použijte `query` parametr:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Vytvořte odpovídající indexer pro každý zdroj dat. Všechny indexery mohou ukazovat na stejný index vyhledávání cíle.  

- Jedna jednotka pro hledání ve vaší službě může spustit jeden indexer v daném okamžiku. Vytvoření více indexerů, jak je popsáno výše, je užitečné pouze v případě, že jsou skutečně spouštěny paralelně. Pokud chcete spustit více indexerů paralelně, Škálujte vyhledávací službu tím, že vytvoříte příslušný počet oddílů a replik. Pokud například služba vyhledávání obsahuje 6 jednotek hledání (například 2 oddíly × 3 repliky), pak 6 indexerů může běžet současně, což má za následek zvětšení v propustnosti indexování po šesti rozložení. Další informace o škálování a plánování kapacity najdete v tématu [škálování úrovní prostředků pro dotazy a úlohy indexování v Azure kognitivní hledání](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexování dokumentů spolu se souvisejícími daty

Můžete chtít "sestavovat" dokumenty z více zdrojů v indexu. Například můžete chtít sloučit text z objektů BLOB s jinými metadaty uloženými v Cosmos DB. Rozhraní API pro indexování nabízených oznámení můžete dokonce použít spolu s různými indexery k vytváření dokumentů hledání z více částí. 

Aby to fungovalo, všechny indexery a další součásti musí souhlasit s klíčem dokumentu. Další podrobnosti k tomuto tématu najdete v článku [indexování více zdrojů dat Azure](./tutorial-multiple-data-sources.md). Podrobný návod najdete v tomto externím článku: [kombinování dokumentů s ostatními daty v Azure kognitivní hledání](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexování prostého textu 

Pokud všechny objekty blob obsahují prostý text ve stejném kódování, můžete významně zvýšit výkon při indexování pomocí **režimu analýzy textu**. Chcete-li použít režim analýzy textu, nastavte `parsingMode` vlastnost konfigurace na `text` :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Ve výchozím nastavení se `UTF-8` předpokládá kódování. Chcete-li zadat jiné kódování, použijte `encoding` vlastnost konfigurace: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Vlastnosti metadat specifických pro typ obsahu
Následující tabulka shrnuje zpracování pro jednotlivé formáty dokumentů a popisuje vlastnosti metadat extrahované službou Azure Kognitivní hledání.

| Formát dokumentu/typ obsahu | Vlastnosti metadat specifických pro typ obsahu | Podrobnosti zpracování |
| --- | --- | --- |
| HTML (text/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Obložení kódu HTML a extrakce textu |
| PDF (aplikace/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů (s výjimkou obrázků) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| DOC (Application/MSWord) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Obložení kódu XML a extrakce textu |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Obložení kódu XML a extrakce textu |
| XLSX (application/vnd. openxmlformats-officedocument. SpreadsheetML. list) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| XLSM (application/vnd. MS-Excel. list. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| PPTX (application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrahujte text, včetně textu extrahovaného z příloh. `metadata_message_to_email``metadata_message_cc_email`a `metadata_message_bcc_email` jsou kolekce řetězců, zbývající pole jsou řetězce.|
| ODT (application/vnd. Oasis. OpenDocument. text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| ODS (application/vnd. Oasis. OpenDocument. Spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| ODP (application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extrakce textu, včetně vložených dokumentů |
| ZIP (aplikace/ZIP) |`metadata_content_type` |Extrakce textu ze všech dokumentů v archivu |
| GZ (Application/gzip) |`metadata_content_type` |Extrakce textu ze všech dokumentů v archivu |
| EPUB (Application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrakce textu ze všech dokumentů v archivu |
| XML (Application/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Obložení kódu XML a extrakce textu |
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrakce textu<br/>Poznámka: Pokud potřebujete extrahovat více polí dokumentů z objektu BLOB JSON, přečtěte si podrobnosti v tématu [indexování objektů BLOB JSON](search-howto-index-json-blobs.md) . |
| EML (zpráva/RFC822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extrakce textu, včetně příloh |
| RTF (aplikace/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrakce textu|
| Prostý text (text/prostý) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrakce textu|


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám zdokonalit Azure Kognitivní hledání
Pokud máte žádosti o funkce nebo návrhy na vylepšení, dejte nám na našem [webu UserVoice](https://feedback.azure.com/forums/263029-azure-search/)informace.