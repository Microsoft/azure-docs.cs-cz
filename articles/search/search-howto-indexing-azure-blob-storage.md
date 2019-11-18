---
title: Hledání v obsahu služby Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Naučte se indexovat Blob Storage Azure a extrahovat text z dokumentů pomocí Azure Kognitivní hledání.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4f662df6692e03cf3eb948b0d8e2ae51002e815d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113020"
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
* [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) kognitivní hledání Azure
* Sada Azure Kognitivní hledání [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Některé funkce (například mapování polí) ještě nejsou k dispozici na portálu a je nutné je použít programově.
>

Tady předvádíme tok pomocí REST API.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Zdroj dat určuje, která data se mají indexovat, přihlašovací údaje potřebné pro přístup k datům a zásady pro efektivní identifikaci změn dat (nové, změněné nebo odstraněné řádky). Zdroj dat může použít více indexerů ve stejné vyhledávací službě.

Pro indexování objektů BLOB musí mít zdroj dat následující požadované vlastnosti:

* **název** je jedinečný název zdroje dat v rámci vyhledávací služby.
* **typ** musí být `azureblob`.
* **přihlašovací údaje** poskytují připojovací řetězec účtu úložiště jako parametr `credentials.connectionString`. Podrobnosti najdete v tématu [jak zadat přihlašovací údaje](#Credentials) níže.
* **kontejner** určuje kontejner v účtu úložiště. Ve výchozím nastavení jsou všechny objekty BLOB v kontejneru navýšené. Pokud chcete indexovat objekty blob pouze v konkrétním virtuálním adresáři, můžete tento adresář zadat pomocí volitelného parametru **dotazu** .

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

Další informace o rozhraní API Create DataSource najdete v tématu [Create DataSource](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Jak zadat přihlašovací údaje ####

Přihlašovací údaje pro kontejner objektů blob můžete zadat jedním z těchto způsobů:

- **Řetězec pro připojení k účtu úložiště s úplným přístupem**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` můžete z Azure Portal získat připojovací řetězec tak, že přejdete do okna účtu úložiště > Nastavení > klíče (pro účty klasického úložiště) nebo nastavení > přístupové klíče (pro účty úložiště Azure Resource Manager).
- Připojovací řetězec **sdíleného přístupového podpisu** (SAS) účtu úložiště: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` by měl mít v tomto případě v kontejnerech a objektech oprávnění list (objekty BLOB).
-  **Sdílený přístupový podpis kontejneru**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` by SAS měl mít v kontejneru oprávnění list a Read.

Další informace o sdílených přístupových podpisech úložiště najdete v tématu [použití sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Pokud používáte přihlašovací údaje SAS, budete muset pravidelně aktualizovat přihlašovací údaje ke zdroji dat pomocí obnovených signatur, aby se předešlo jejich vypršení platnosti. Pokud vyprší platnost přihlašovacích údajů SAS, indexer selže a zobrazí se chybová zpráva podobná `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributech a dalších konstrukcích, které prohledají možnosti vyhledávání.

Tady je postup vytvoření indexu s `content` polem, které lze prohledávat a který bude obsahovat text extrahovaný z objektů BLOB:   

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

Další informace o vytváření indexů najdete v tématu [vytvoření indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) .

### <a name="step-3-create-an-indexer"></a>Krok 3: vytvoření indexeru
Indexer připojuje zdroj dat k cílovému vyhledávacímu indexu a poskytuje plán pro automatizaci aktualizace dat.

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

Tento indexer se spustí každé dvě hodiny (časový interval je nastaven na "PT2H"). Pokud chcete indexer spustit každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je nepovinný – Pokud je vynechaný, indexer se při vytvoření spustí jenom jednou. Můžete ale kdykoli spustit indexer na vyžádání.   

Další informace o rozhraní API Create indexeru najdete v části [Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Jak Azure Kognitivní hledání indexuje objekty blob

V závislosti na [konfiguraci indexeru](#PartsOfBlobToIndex)může indexer objektů BLOB indexovat jenom metadata úložiště (užitečné v případě, že se jenom zajímáte o metadata a nepotřebujete indexovat obsah objektů BLOB), úložiště a metadata obsahu nebo metadata i textový obsah. Ve výchozím nastavení indexer extrahuje metadata i obsah.

> [!NOTE]
> Ve výchozím nastavení jsou objekty BLOB se strukturovaným obsahem, jako je JSON nebo CSV, indexované jako jeden blok textu. Pokud chcete indexovat objekty blob JSON a CSV strukturovaným způsobem, přečtěte si další informace v tématu indexování objektů BLOB [JSON](search-howto-index-json-blobs.md) a [indexování objektů BLOB ve formátu CSV](search-howto-index-csv-blobs.md) .
>
> Složený nebo vložený dokument (například archiv ZIP nebo dokument aplikace Word s vloženým e-mailem Outlooku obsahující přílohy) je také indexován jako jeden dokument.

* Textový obsah dokumentu se extrahuje do pole řetězce s názvem `content`.

> [!NOTE]
> Azure Kognitivní hledání omezuje počet vydaných textů v závislosti na cenové úrovni: 32 000 znaků pro úroveň Free, 64 000 pro Basic a 4 000 000 pro úrovně Standard, Standard S2 a Standard S3. V odpovědi na stav indexeru pro zkrácené dokumenty je k dispozici upozornění.  

* Uživatelsky definované vlastnosti metadat přítomné v objektu blob, pokud existují, jsou extrahovány do doslovného znění.
* Vlastnosti standardních metadat objektů BLOB jsou extrahovány do následujících polí:

  * **metadata\_úložiště\_název** (EDM. String) – název souboru objektu BLOB. Pokud máte například objekt BLOB/my-Container/My-Folder/subfolder/Resume.PDF, hodnota tohoto pole je `resume.pdf`.
  * **metadata\_úložiště\_cesta** (EDM. String) – úplný identifikátor URI objektu blob, včetně účtu úložiště. Například `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`.
  * **metadata\_úložiště\_obsahu\_typ** (EDM. String) – typ obsahu určený kódem, který jste použili k nahrání objektu BLOB. Například, `application/octet-stream`.
  * **metadata\_úložiště\_poslední\_upravil** (EDM. DateTimeOffset) – poslední změněné časové razítko pro objekt BLOB. Azure Kognitivní hledání používá toto časové razítko k identifikaci změněných objektů blob, aby nedocházelo k přeindexování všeho po počátečním indexování.
  * **metadata\_velikosti\_úložiště** (EDM. Int64) – velikost objektu BLOB v bajtech.
  * **metadata\_úložiště\_obsahu\_MD5** (EDM. String) – hodnota hash MD5 obsahu objektu blob, je-li k dispozici.
  * **metadata\_úložiště\_tokenu sas\_** (EDM. String) – dočasný token SAS, který mohou používat [vlastní dovednosti](cognitive-search-custom-skill-interface.md) k získání přístupu k objektu BLOB. Tento token by neměl být uložen pro pozdější použití, protože může vypršet jeho platnost.

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

* **metadata\_úložiště\_název** – může to být pohodlný kandidát, ale Všimněte si, že 1) názvy nemusí být jedinečné, protože objekty blob mají stejný název v různých složkách a 2) název může obsahovat znaky, které jsou v klíčích dokumentů neplatné, například pomlčky. S neplatnými znaky můžete pracovat pomocí [funkce mapování polí](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode` – Pokud to uděláte, nezapomeňte kódovat klíče dokumentů při jejich předávání v VOLÁNÍCH rozhraní API, jako je například vyhledávání. (Například v .NET můžete k tomuto účelu použít [metodu UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) ).
* **metadata\_úložiště\_cesta** – při použití úplné cesty je zajištěna jedinečnost, ale cesta má neomezeně `/` znaků, které jsou [v klíči dokumentu neplatné](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak je uvedeno výše, máte možnost kódování klíčů pomocí [funkce](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`.
* Pokud žádná z výše uvedených možností nefunguje za vás, můžete do objektů BLOB přidat vlastní vlastnost metadat. Tato možnost ale vyžaduje, aby váš proces nahrání objektu BLOB přidal tuto vlastnost metadat do všech objektů BLOB. Vzhledem k tomu, že klíč je povinná vlastnost, všechny objekty blob, které tuto vlastnost nemají, nebudou indexovány.

> [!IMPORTANT]
> Pokud pro klíčové pole v indexu neexistuje explicitní mapování, Azure Kognitivní hledání automaticky používá `metadata_storage_path` jako klíč a základní-64 kóduje hodnoty klíče (druhá možnost výše).
>
>

V tomto příkladu vybereme pole `metadata_storage_name` jako klíč dokumentu. Předpokládejme také, že váš index obsahuje klíčové pole s názvem `key` a pole `fileSize` pro uložení velikosti dokumentu. Pokud chcete, aby se při vytváření nebo aktualizaci indexeru vytvořily co nejvíce, zadejte následující mapování polí:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Pokud to chcete uvést dohromady, můžete přidat mapování polí a povolit kódování Base-64 klíčů pro existující indexer:

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
> Další informace o mapování polí najdete v [tomto článku](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Řízení indexovaných objektů BLOB
Můžete určit, které objekty blob budou indexovány a které se přeskočí.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexovat pouze objekty BLOB s určitými příponami souborů
Pomocí parametru konfigurace `indexedFileNameExtensions` indexeru můžete indexovat jenom objekty BLOB s příponami názvů souborů, které zadáte. Hodnota je řetězec obsahující čárkami oddělený seznam přípon souborů (s počáteční tečkou). Například chcete-li indexovat pouze. Soubory PDF a. Objekty blob DOCX:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Vyloučení objektů BLOB s určitými příponami souborů
Pomocí parametru konfigurace `excludedFileNameExtensions` můžete z indexování vyloučit objekty BLOB s určitými příponami názvů souborů. Hodnota je řetězec obsahující čárkami oddělený seznam přípon souborů (s počáteční tečkou). Například pro indexování všech objektů BLOB s výjimkou. PNG a. Rozšíření JPEG:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Pokud jsou přítomny parametry `indexedFileNameExtensions` i `excludedFileNameExtensions`, Azure Kognitivní hledání nejprve vyhledá `indexedFileNameExtensions`a pak na `excludedFileNameExtensions`. To znamená, že pokud se stejná Přípona souboru nachází v obou seznamech, bude vyloučena z indexování.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Řízení, které části objektu BLOB jsou indexované

Pomocí parametru konfigurace `dataToExtract` můžete určit, které části objektů BLOB se mají indexovat. Může mít následující hodnoty:

* `storageMetadata` – určuje, že se indexují jenom [standardní vlastnosti objektů BLOB a metadata zadaná uživatelem](../storage/blobs/storage-properties-metadata.md) .
* `allMetadata` – určuje, že se indexují metadata úložiště a [metadata specifická pro typ obsahu](#ContentSpecificMetadata) extrahovaná z obsahu objektu BLOB.
* `contentAndMetadata` – určuje, že se indexuje všechna metadata a textový obsah extrahovaný z objektu BLOB. Toto je výchozí hodnota.

Chcete-li například indexovat pouze metadata úložiště, použijte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Použití metadat objektů BLOB k řízení, jak jsou objekty blob indexovány

Výše popsané parametry konfigurace se vztahují na všechny objekty blob. V některých případech můžete chtít určit, jak jsou *jednotlivé objekty blob* indexovány. Můžete to udělat přidáním následujících vlastností a hodnot metadat objektu BLOB:

| Název vlastnosti | Hodnota vlastnosti | Vysvětlení |
| --- | --- | --- |
| AzureSearch_Skip |podmínka |Dá indexeru objektů BLOB úplný skok objektu BLOB. Nezkouší se žádná metadata ani extrakce obsahu. To je užitečné, když určitý objekt BLOB opakovaně selhává a přerušuje proces indexování. |
| AzureSearch_SkipContent |podmínka |Jedná se o ekvivalent `"dataToExtract" : "allMetadata"`ho nastavení popsaného [výše](#PartsOfBlobToIndex) v oboru určitého objektu BLOB. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Obchodování s chybami

Ve výchozím nastavení se indexer objektů BLOB zastaví, jakmile narazí na objekt BLOB s nepodporovaným typem obsahu (například obrázek). Můžete samozřejmě použít parametr `excludedFileNameExtensions` k přeskočení určitých typů obsahu. Je ale možné, že budete muset indexovat objekty blob bez znalosti všech možných typů obsahu předem. Pokud chcete pokračovat v indexování, když je nalezen nepodporovaný typ obsahu, nastavte parametr konfigurace `failOnUnsupportedContentType` na `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

U některých objektů BLOB nedokáže Azure Kognitivní hledání určit typ obsahu nebo nemůže zpracovat dokument jiného podporovaného typu obsahu. Chcete-li tento režim selhání ignorovat, nastavte parametr konfigurace `failOnUnprocessableDocument` na hodnotu false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Kognitivní hledání omezuje velikost indexovaných objektů BLOB. Tato omezení jsou popsaná v [omezení služby v Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Ve výchozím nastavení jsou objekty BLOB s příliš velikostí považovány za chyby. I když nastavíte parametr konfigurace `indexStorageMetadataOnlyForOversizedDocuments` na hodnotu true, stále ale můžete indexovat metadata úložiště objektů BLOB s více velikostmi: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Můžete také pokračovat v indexování, pokud dojde k chybám v jakémkoli okamžiku zpracování, při analýze objektů BLOB nebo při přidávání dokumentů do indexu. Chcete-li ignorovat určitý počet chyb, nastavte parametry konfigurace `maxFailedItems` a `maxFailedItemsPerBatch` na požadované hodnoty. Příklad:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstraňování duplicit
Když nastavíte indexer objektů BLOB tak, aby se spouštěl podle plánu, přeindexuje jenom změněné objekty blob, jak určuje časové razítko `LastModified` objektu BLOB.

> [!NOTE]
> Nemusíte určovat zásady detekce změn – přírůstkové indexování je pro vás povolené automaticky.

Pro podporu odstraňování dokumentů použijte přístup "obnovitelné odstranění". Pokud dojde k pravému odstranění objektů blob, odpovídající dokumenty nebudou odebrány z indexu vyhledávání. Místo toho použijte následující postup:  

1. Přidejte do objektu BLOB vlastnost vlastní metadata, která bude ukazovat na Azure Kognitivní hledání, že je logicky odstraněný.
2. Konfigurace zásad Detekce tichého odstranění ve zdroji dat
3. Jakmile indexer zpracuje objekt BLOB (jak je znázorněno v rozhraní API stavu indexeru), můžete objekt BLOB fyzicky odstranit.

Například následující zásady považují objekt blob, který se má odstranit, pokud má vlastnost metadata `IsDeleted` s hodnotou `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
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

Indexování objektů BLOB může být časově náročný proces. V případech, kdy máte v indexu miliony objektů blob, můžete urychlit indexování pomocí dělení dat a používání více indexerů k paralelnímu zpracování dat. Tady je postup, jak to můžete nastavit:

- Rozdělit data do více kontejnerů objektů BLOB nebo virtuálních složek
- Nastavte několik zdrojů dat Azure Kognitivní hledání, jeden pro každý kontejner nebo složku. Chcete-li odkazovat na složku objektů blob, použijte parametr `query`:

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

Aby to fungovalo, všechny indexery a další součásti musí souhlasit s klíčem dokumentu. Další podrobnosti k tomuto tématu najdete v článku [indexování více zdrojů dat Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Podrobný návod najdete v tomto externím článku: [kombinování dokumentů s ostatními daty v Azure kognitivní hledání](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexování prostého textu 

Pokud všechny objekty blob obsahují prostý text ve stejném kódování, můžete významně zvýšit výkon při indexování pomocí **režimu analýzy textu**. Chcete-li použít režim analýzy textu, nastavte vlastnost konfigurace `parsingMode` na `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Ve výchozím nastavení se předpokládá kódování `UTF-8`. Pokud chcete zadat jiné kódování, použijte vlastnost konfigurace `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Vlastnosti metadat specifických pro typ obsahu
Následující tabulka shrnuje zpracování pro jednotlivé formáty dokumentů a popisuje vlastnosti metadat extrahované službou Azure Kognitivní hledání.

| Formát dokumentu/typ obsahu | Vlastnosti metadat specifických pro typ obsahu | Podrobnosti zpracování |
| --- | --- | --- |
| HTML (text/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Obložení kódu HTML a extrakce textu |
| PDF (aplikace/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů (s výjimkou obrázků) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| DOC (Application/MSWord) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| DOCM (application/vnd. MS-Word. Document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extrakce textu, včetně vložených dokumentů |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Obložení kódu XML a extrakce textu |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Obložení kódu XML a extrakce textu |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| XLSM (application/vnd. MS-Excel. list. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extrakce textu, včetně vložených dokumentů |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extrakce textu, včetně vložených dokumentů |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrakce textu, včetně příloh |
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
