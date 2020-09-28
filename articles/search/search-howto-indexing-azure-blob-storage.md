---
title: Konfigurace indexeru objektů BLOB
titleSuffix: Azure Cognitive Search
description: Nastavte indexer Azure Blob pro automatizaci indexování obsahu objektů BLOB pro operace fulltextového vyhledávání v Azure Kognitivní hledání.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403602"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>Jak nakonfigurovat indexer objektů BLOB v Azure Kognitivní hledání

V tomto článku se dozvíte, jak používat Azure Kognitivní hledání k indexování textových dokumentů (například PDF, systém Microsoft Office dokumentů a několika dalších běžných formátů) uložených v úložišti objektů BLOB v Azure. Nejprve se vysvětlují základy nastavení a konfigurace indexeru objektů BLOB. Potom nabízí hlubší průzkum chování a scénářů, se kterými se pravděpodobně setkáte.

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>Podporované formáty

Indexer objektů BLOB může extrahovat text z následujících formátů dokumentů:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>Nastavení indexování objektů BLOB

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
* * * přihlašovací údaje poskytují jako parametr připojovací řetězec účtu úložiště `credentials.connectionString` . Podrobnosti najdete v tématu [jak zadat přihlašovací údaje](#Credentials) níže.
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

#### <a name="how-to-specify-credentials"></a>Jak zadat přihlašovací údaje

Přihlašovací údaje pro kontejner objektů blob můžete zadat jedním z těchto způsobů:

* **Připojovací řetězec spravované identity**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  Tento připojovací řetězec nevyžaduje klíč účtu, ale je nutné postupovat podle pokynů pro [nastavení připojení k účtu Azure Storage pomocí spravované identity](search-howto-managed-identities-storage.md).

* **Připojovací řetězec pro úplný přístup k účtu úložiště**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  Připojovací řetězec můžete z Azure Portal získat tak, že přejdete do okna účtu úložiště > nastavení > klíče (pro účty klasického úložiště) nebo nastavení > přístupových klíčů (pro Azure Resource Manager účty úložiště).

* Připojovací řetězec **sdíleného přístupového podpisu** (SAS) účtu úložiště:`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  SAS by měl mít seznam a oprávnění číst pro kontejnery a objekty (v tomto případě objekty BLOB).

* **Sdílený přístupový podpis kontejneru**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  SAS by měl mít v kontejneru oprávnění list a Read.

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

Další informace najdete v tématu [vytvoření indexu (REST API)](/rest/api/searchservice/create-index).

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

Další informace najdete v tématu [Vytvoření indexeru (REST API)](/rest/api/searchservice/create-indexer). Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>Způsob indexování objektů BLOB

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

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definování klíčů dokumentu a mapování polí

V Azure Kognitivní hledání klíč dokumentu jednoznačně identifikuje dokument. Každý index vyhledávání musí mít přesně jedno pole klíče typu EDM. String. Klíčové pole je vyžadováno pro každý dokument, který je přidán do indexu (ve skutečnosti je to jediné povinné pole).  

Měli byste pečlivě zvážit, které extrahované pole by mělo být namapováno na pole klíče pro váš index. Kandidáti:

* ** \_ \_ název úložiště metadat** – může to být praktický kandidát, ale Všimněte si, že 1) názvy nemusí být jedinečné, protože v různých složkách můžete mít objekty BLOB se stejným názvem a 2) název může obsahovat znaky, které jsou v klíčích dokumentů neplatné, například pomlčky. Pomocí funkce mapování polí můžete pracovat s neplatnými znaky `base64Encode` [field mapping function](search-indexer-field-mappings.md#base64EncodeFunction) – Pokud to uděláte, nezapomeňte kódovat klíče dokumentů při jejich předávání v voláních rozhraní API, jako je například vyhledávání. (Například v .NET můžete k tomuto účelu použít [metodu UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) ).

* ** \_ \_ cesta úložiště metadat** – při použití úplné cesty je zajištěna jedinečnost, ale cesta má jednoznačně `/` znaky, které jsou [v klíči dokumentu neplatné](/rest/api/searchservice/naming-rules).  Jak je uvedeno výše, máte možnost kódování klíčů pomocí `base64Encode` [funkce](search-indexer-field-mappings.md#base64EncodeFunction).

* Třetí možností je přidat do objektů BLOB vlastnost vlastní metadata. Tato možnost ale vyžaduje, aby váš proces nahrání objektu BLOB přidal tuto vlastnost metadat do všech objektů BLOB. Vzhledem k tomu, že klíč je povinná vlastnost, všechny objekty blob, které tuto vlastnost nemají, nebudou indexovány.

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

Další informace najdete v tématu [mapování polí a transformace](search-indexer-field-mappings.md).

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

## <a name="index-by-file-type"></a>Index podle typu souboru

Můžete určit, které objekty blob budou indexovány a které se přeskočí.

### <a name="include-blobs-having-specific-file-extensions"></a>Zahrnout objekty BLOB s konkrétními příponami souborů

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

### <a name="exclude-blobs-having-specific-file-extensions"></a>Vyloučení objektů BLOB s konkrétními příponami souborů

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

## <a name="index-parts-of-a-blob"></a>Indexovat části objektu BLOB

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

## <a name="index-from-multiple-sources"></a>Index z více zdrojů

Můžete chtít "sestavovat" dokumenty z více zdrojů v indexu. Například můžete chtít sloučit text z objektů BLOB s jinými metadaty uloženými v Cosmos DB. Rozhraní API pro indexování nabízených oznámení můžete dokonce použít spolu s různými indexery k vytváření dokumentů hledání z více částí.

Aby to fungovalo, všechny indexery a další součásti musí souhlasit s klíčem dokumentu. Další podrobnosti o tomto tématu najdete v článku [indexování více zdrojů dat Azure](./tutorial-multiple-data-sources.md) nebo tohoto příspěvku na blogu v tématu [kombinování dokumentů s ostatními daty v Azure kognitivní hledání](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

## <a name="index-large-datasets"></a>Indexování velkých datových sad

Indexování objektů BLOB může být časově náročný proces. V případech, kdy máte v indexu miliony objektů blob, můžete urychlit indexování pomocí dělení dat a používání více indexerů k paralelnímu zpracování dat. Tady je postup, jak to můžete nastavit:

* Rozdělit data do více kontejnerů objektů BLOB nebo virtuálních složek

* Nastavte několik zdrojů dat Azure Kognitivní hledání, jeden pro každý kontejner nebo složku. Chcete-li odkazovat na složku objektů blob, použijte `query` parametr:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* Vytvořte odpovídající indexer pro každý zdroj dat. Všechny indexery mohou ukazovat na stejný index vyhledávání cíle.  

* Jedna jednotka pro hledání ve vaší službě může spustit jeden indexer v daném okamžiku. Vytvoření více indexerů, jak je popsáno výše, je užitečné pouze v případě, že jsou skutečně spouštěny paralelně. Pokud chcete spustit více indexerů paralelně, Škálujte vyhledávací službu tím, že vytvoříte příslušný počet oddílů a replik. Pokud například služba vyhledávání obsahuje 6 jednotek hledání (například 2 oddíly × 3 repliky), pak 6 indexerů může běžet současně, což má za následek zvětšení v propustnosti indexování po šesti rozložení. Další informace o škálování a plánování kapacity najdete v tématu [Úprava kapacity služby Azure kognitivní hledání](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>Ošetření chyb

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

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>Vlastnosti metadat specifických pro typ obsahu

Následující tabulka shrnuje zpracování pro jednotlivé formáty dokumentů a popisuje vlastnosti metadat extrahované službou Azure Kognitivní hledání.

| Formát dokumentu/typ obsahu | Extrahovaná metadata | Podrobnosti zpracování |
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

## <a name="see-also"></a>Viz také

* [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
* [Principy objektů BLOB pomocí AI](search-blob-ai-integration.md)
* [Přehled indexování objektů BLOB](search-blob-storage-integration.md)