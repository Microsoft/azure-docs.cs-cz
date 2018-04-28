---
title: Indexování úložiště objektů Blob v Azure s Azure Search
description: Naučte se indexovat Azure Blob Storage a rozbalte text z dokumentů s Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 976b1c6b65036faeff3c4cc21e91ccf798eb0df3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indexování dokumentů v úložišti objektů Blob v Azure s Azure Search
Tento článek ukazuje, jak používat Azure Search k indexování dokumentů (například soubory PDF, dokumentů Microsoft Office a několik dalších běžných formátů) uložené v úložišti objektů Blob Azure. Nejprve vysvětluje základní informace o nastavení a konfiguraci indexer objektů blob. Poté nabízí podrobnější zkoumání chování, a scénáře, můžete se setkat.

## <a name="supported-document-formats"></a>Podporované formáty dokumentu
Indexer objektů blob můžete rozbalte text z následujících formátů dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Nastavení indexování objektů blob
Můžete nastavit indexer Azure Blob Storage pomocí:

* [Azure Portal](https://ms.portal.azure.com)
* Služba Azure Search [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Služba Azure Search [sady .NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Některé funkce (například mapování polí) ještě nejsou k dispozici na portálu a musí být použity prostřednictvím kódu programu.
>
>

Zde ukážeme toku pomocí rozhraní REST API.

### <a name="step-1-create-a-data-source"></a>Krok 1: Vytvoření zdroje dat
Zdroj dat určuje, která data do indexu, přihlašovací údaje potřebné pro přístup k datům a zásady, které efektivně identifikovat změny v datech (nové, modified nebo deleted řádky). Zdroj dat můžete použít několik indexerů v rámci stejné služby vyhledávání.

Pro objekt blob indexování, zdroj dat musí mít následující požadované vlastnosti:

* **název** je jedinečný název zdroje dat v rámci služby search.
* **typ** musí být `azureblob`.
* **přihlašovací údaje** poskytuje připojovacího řetězce úložiště účet jako `credentials.connectionString` parametr. V tématu [postupy pro zadání přihlašovacích údajů](#Credentials) níže podrobnosti.
* **kontejner** Určuje kontejner ve vašem účtu úložiště. Ve výchozím nastavení se dá načíst všechny objekty BLOB v kontejneru. Pokud chcete pouze na objekty BLOB index do konkrétního virtuálního adresáře, můžete zadat Tento adresář pomocí volitelné **dotazu** parametr.

Vytvoření zdroje dat:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Další informace o rozhraní API vytvořit zdroj dat, najdete v části [vytvořit zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Postupy pro zadání přihlašovacích údajů ####

Zadejte pověření pro kontejner objektů blob v jednom z těchto způsobů:

- **Úplný přístup připojovací řetězce k účtu úložiště**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Připojovací řetězec můžete získat z portálu Azure tak, že přejdete do okna účet úložiště > Nastavení > klíče (pro účty úložiště Classic) nebo nastavení > přístupové klíče (pro účty úložiště Azure Resource Manager).
- **Podpis sdíleného přístupu účtu úložiště** (SAS) připojovací řetězec: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` The SAS by měl mít v seznamu a oprávnění ke čtení v kontejnery a objekty (v tomto případě objektů BLOB).
-  **Sdílený přístupový podpis kontejneru**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` The SAS by měl mít v seznamu a oprávnění ke čtení v kontejneru.

Další informace o sdílené úložiště přístup podpisy, najdete v části [pomocí sdílené přístupové podpisy](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Pokud chcete použít přihlašovací údaje SAS, musíte se pravidelně aktualizovat pověření ke zdroji dat s prodlouženou platností podpisy, aby se zabránilo vypršení jejich platnosti. Pokud přihlašovací údaje SAS vyprší, indexeru se nezdaří s chybovou zprávou, která je podobná `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributy, a jiných objektů, které utvářejí hledání prostředí.

Zde je postup vytvoření indexu s vyhledávat `content` pole pro uložení textu extrahoval z objektů blob:   

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

Další informace o vytváření indexů najdete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Krok 3: Vytvořením indexeru.
Indexer zdroje dat se připojí pomocí cílový index vyhledávání a poskytuje plán, který chcete automatizovat aktualizace dat.

Jakmile byly vytvořeny index a zdroj dat, jste připraveni vytvořit indexeru:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Indexer se spustí každé dvě hodiny (interval plánování je nastavena na "PT2H"). Pokud chcete spustit indexer každých 30 minut, nastavte interval, který "PT30M". Nejkratší podporovaný interval je 5 minut. Plán je volitelné - li tento parametr vynechán, indexer se spustí pouze po jeho vytvoření. Indexer na vyžádání je však můžete spustit kdykoli.   

Další informace o rozhraní API Indexer vytvořit, podívejte se na [vytvořit Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Jak Azure Search indexuje objektů BLOB

V závislosti na [indexer konfigurace](#PartsOfBlobToIndex), indexer objekt blob může indexovat pouze metadata úložiště (užitečné, pokud se zajímáte pouze o metadata a nemusíte indexu obsah objektů BLOB), úložiště a obsah metadata nebo oba metadata a textový obsah. Ve výchozím nastavení extrahuje indexeru metadata a obsah.

> [!NOTE]
> Ve výchozím nastavení objektů BLOB s strukturovaných obsah, jako jsou JSON nebo CSV jsou uloženy jako jeden blok textu. Pokud chcete indexu objekty BLOB JSON a CSV strukturované, přečtěte si téma [objekty BLOB JSON indexování](search-howto-index-json-blobs.md) a [objekty BLOB indexování CSV](search-howto-index-csv-blobs.md) funkce verze preview.
>
> Složené nebo vloženého dokumentu (například archivu ZIP nebo dokument aplikace Word s embedded e-mailu aplikace Outlook obsahující přílohy) jsou také indexována jako jednotlivý dokument.

* Textový obsah dokumentu je extrahován do pole řetězce s názvem `content`.

> [!NOTE]
> Služba Azure Search omezuje, kolik text extrahuje v závislosti na cenové úrovně: 32 000 znaků zdarma vrstvy, 64 000 pro základní a 4 milionů na úrovních Standard, Standard S2 a úrovně Standard S3. Upozornění je zahrnutý v odpovědi stav indexeru pro zkrácený dokumenty.  

* Metadata zadaného uživatelem v objektu blob, pokud existuje, extrahují se vlastnosti doslovné.
* Extrahují se vlastnosti metadat standardní objekt blob do následujících polí:

  * **metadata\_úložiště\_název** (Edm.String) – název souboru objektu blob. Například pokud máte /my-container/my-folder/subfolder/resume.pdf objektů blob, hodnota tohoto pole je `resume.pdf`.
  * **metadata\_úložiště\_cesta** (Edm.String) - úplný identifikátor URI objektu blob, včetně účet úložiště. Například `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`.
  * **metadata\_úložiště\_obsah\_typu** (Edm.String) – typ podle specifikace kód, který jste použili k načtení objektu blob obsahu. Například, `application/octet-stream`.
  * **metadata\_úložiště\_poslední\_upravil** (Edm.DateTimeOffset) - poslední změny časové razítko pro tento objekt blob. Služba Azure Search používá k identifikaci změněné objekty BLOB, aby se zabránilo novou všechno indexaci po počáteční indexování toto časové razítko.
  * **metadata\_úložiště\_velikost** (Edm.Int64) – objekt blob velikost v bajtech.
  * **metadata\_úložiště\_obsah\_md5** (Edm.String) - hodnota hash MD5 obsahu objektu blob, pokud je k dispozici.
* Metadata vlastnosti specifické pro každý dokument formátu extrahují do polí uvedených [zde](#ContentSpecificMetadata).

Není nutné definovat všechny výše uvedené vlastnosti pole v indexu vyhledávání – stačí zaznamenat vlastnosti, které potřebujete pro vaši aplikaci.

> [!NOTE]
> Názvy polí v indexu existující často, bude jiné než názvy polí vygenerovaných během extrakce dokumentu. Můžete použít **pole mapování** mapovat názvy vlastností, které poskytuje Azure Search na názvy polí v indexu vyhledávání. Zobrazí se pole, které mapování použít následující příklad.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definování dokumentu klíče a mapování polí
Ve službě Azure Search je klíč dokumentu jednoznačně identifikuje dokumentu. Každý index hledání musí mít přesně jeden klíčové pole typu Edm.String. Klíčové pole je povinné pro každý dokument, který je přidáván do indexu (je ve skutečnosti jenom požadované pole).  

Měli pečlivě zvažte, které extrahované pole by měla být mapována na pole klíče pro indexu. Kandidáti:

* **metadata\_úložiště\_název** – to může být vhodné kandidátem, ale Všimněte si, že 1) názvy nemusí být jedinečné, jako je možné, že objekty BLOB se stejným názvem do jiné složky a 2) název může obsahovat znaky, které jsou v dokumentu klíče, jako je například pomlčky je neplatné. Neplatné znaky můžete řešit pomocí `base64Encode` [pole mapování funkce](search-indexer-field-mappings.md#base64EncodeFunction) – Pokud je to mějte na paměti, ke kódování dokumentu klíče při předávání je v rozhraní API volá například vyhledávání. (Například v rozhraní .NET můžete použít [UrlTokenEncode metoda](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) k tomuto účelu).
* **metadata\_úložiště\_cesta** – použití úplnou cestu zajišťuje jedinečnost, ale cesta výborný obsahuje `/` znaky, které jsou [neplatný klíč dokumentu](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Jak je uvedeno výše, máte možnost kódování klíče pomocí `base64Encode` [funkce](search-indexer-field-mappings.md#base64EncodeFunction).
* Pokud žádná z výše uvedených možností fungovat pro vás, můžete přidat vlastnost vlastních metadat pro objekty BLOB. Tuto možnost, ale vyžadovat váš proces odesílání blob přidání vlastnosti metadat do všech objektů BLOB. Vzhledem k tomu, že klíč se vyžaduje vlastnost, všech objektů BLOB, které nemají tuto vlastnost nelze indexovat.

> [!IMPORTANT]
> Pokud žádné explicitní mapování pro klíčové pole v indexu Azure Search automaticky použije `metadata_storage_path` jako klíč a kódování base-64 kóduje hodnoty klíče (druhá možnost výše).
>
>

V tomto příkladu vytvoříme `metadata_storage_name` pole jako klíč dokumentu. Také Předpokládejme indexu má klíčové pole s názvem `key` a pole `fileSize` pro ukládání velikost dokumentu. Chcete-li propojit věcí si podle potřeby, zadejte následující mapování polí při vytváření nebo aktualizaci indexer:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Aby to všechny společně, zde je, jak můžete přidat mapování polí a povolit kódování base-64 klíčů pro existujícího indexeru:

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
> Další informace o mapování polí, najdete v části [v tomto článku](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Řízení, které objekty BLOB jsou uloženy.
Můžete řídit, které objekty BLOB jsou indexované a který se přeskočí.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Index pouze objekty BLOB s konkrétní přípony souborů
Může indexovat pouze objekty BLOB s příponami názvů souborů, zadejte pomocí `indexedFileNameExtensions` indexer konfigurační parametr. Hodnota je řetězec obsahující oddělený čárkami seznam přípon souborů (s úvodní tečky). Například do indexu pouze. PDF a. Objekty BLOB DOCX, postupujte takto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Vyloučení objektů BLOB s konkrétní přípony souborů
Objekty BLOB s konkrétní přípony názvů souborů můžete vyloučit z indexování pomocí `excludedFileNameExtensions` konfigurační parametr. Hodnota je řetězec obsahující oddělený čárkami seznam přípon souborů (s úvodní tečky). Například do indexu všech objektů BLOB s výjimkou těch, které se. PNG a. Rozšíření JPEG, postupujte takto:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Pokud oba `indexedFileNameExtensions` a `excludedFileNameExtensions` dostupné parametry, Azure Search nejdřív zjistí `indexedFileNameExtensions`, pak na `excludedFileNameExtensions`. To znamená, že pokud stejnou příponu souboru nachází v obou seznamů, bude vyloučena z indexu.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Řízení, které části objektu blob jsou uloženy.

Můžete řídit, které části objekty BLOB jsou indexované pomocí `dataToExtract` konfigurační parametr. Může trvat následující hodnoty:

* `storageMetadata` -Určuje pouze [vlastnosti standardní objektů blob a zadat uživatele metadata](../storage/blobs/storage-properties-metadata.md) jsou indexovány.
* `allMetadata` -Určuje, že metadata úložiště a [konkrétních metadat typu obsahu](#ContentSpecificMetadata) extrahovat z objektu blob jsou indexované obsah.
* `contentAndMetadata` -Určuje, že jsou indexován všechna metadata a textový obsah extrahoval z objektu blob. Toto je výchozí hodnota.

Například indexovat pouze metadata úložiště, použijte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Pomocí metadata objektu blob řídit, jak jsou indexované objektů BLOB

Výše uvedených parametrů konfigurace platí pro všechny objekty BLOB. V některých případech můžete k řízení způsobu *jednotlivé objekty BLOB* jsou indexovány. Můžete provést přidáním následující vlastnosti metadata objektu blob a hodnoty:

| Název vlastnosti | Hodnota vlastnosti | Vysvětlení |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Dá pokyn indexeru blob zcela přeskočit objektu blob. Extrakce metadat ani obsahu dojde k pokusu o. To je užitečné, když konkrétní objekt blob opakovaně a přerušení proces indexování. |
| AzureSearch_SkipContent |"true" |Toto je ekvivalentní `"dataToExtract" : "allMetadata"` nastavení popsané [výše](#PartsOfBlobToIndex) omezená na konkrétní objekt blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Plánování práce s chybami

Ve výchozím nastavení zastaví indexeru objektů blob při výskytu objekt blob se nepodporovaný typ obsahu (například obrázek). Samozřejmě můžete použít `excludedFileNameExtensions` parametr tak, aby přeskočil určité typy obsahu. Může ale muset objekty BLOB index bez znalosti všechny možné typy obsahu předem. Chcete-li pokračovat, indexování při zjistil se nepodporovaný typ obsahu, nastavte `failOnUnsupportedContentType` konfigurace parametru `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Pro některé objekty BLOB Azure Search nelze určit typ obsahu nebo se nepodařilo zpracovat doklad o jinak podporovaná typ obsahu. Chcete-li tento režim selhání ignorovat, nastavte `failOnUnprocessableDocument` konfiguračního parametru na hodnotu false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Služba Azure Search omezuje velikost objekty BLOB, které jsou uloženy. Tato omezení jsou dokumentovány v článku [omezení služby ve službě Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Příliš velký objekty BLOB jsou považovány za chyby ve výchozím nastavení. Ale můžete pořád indexovat metadata úložiště objektů BLOB příliš velký Pokud nastavíte `indexStorageMetadataOnlyForOversizedDocuments` konfiguračního parametru na hodnotu true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Můžete také pokračovat indexování Pokud chyby v libovolném bodě zpracování, při analýze objektů BLOB nebo při přidávání dokumentů do indexu. Chcete-li ignorovat konkrétní počet chyb, nastavte `maxFailedItems` a `maxFailedItemsPerBatch` parametry konfigurace pro požadované hodnoty. Příklad:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstranění duplicit
Při nastavování objektu blob indexer spouštět podle plánu, znovu indexovat pouze změněné objekty BLOB, určeného objektu blob `LastModified` časové razítko.

> [!NOTE]
> Nemáte zadejte zásady detekce změn – přírůstkové indexování se povolí automaticky.

Pro podporu odstraňování dokumentů, použijte s "obnovitelného odstranění". Pokud odstraníte přímý objekty BLOB, odpovídající dokumenty nebudou odebrané z indexu vyhledávání. Místo toho použijte následující postup:  

1. Přidání vlastních metadat vlastnosti do objektu blob a informuje Azure Search je logicky odstraněna
2. Umožňuje nakonfigurovat zásadu obnovitelného odstranění detekce ve zdroji dat
3. Po zpracování objektu blob (jak je znázorněno podle stavu indexer rozhraní API) indexeru fyzicky odstraníte objektu blob

Například následující zásady zvažuje objekt blob odstranit, pokud má vlastnost metadat `IsDeleted` s hodnotou `true`:

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

## <a name="indexing-large-datasets"></a>Indexování rozsáhlých datových sad

Indexování objekty BLOB může být zdlouhavý proces. V případech, kdy máte miliony objektů BLOB k indexu můžete urychlit indexování dělení dat a použitím několik indexerů zpracování dat paralelně. Zde je, jak můžete nastavit tuto možnost:

- Oddílu data do více kontejnery objektů blob nebo virtuální složek
- Nastavte několik zdrojů dat Azure Search, jeden pro každý kontejner nebo složky. Chcete-li přejít do složky objektů blob, použijte `query` parametr:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Vytvořte odpovídající indexer pro každý datový zdroj. Všechny indexery může ukazovat na stejný cílový index vyhledávání.  

- Jedna jednotka vyhledávání ve službě můžete spustit jeden indexer v daném okamžiku. Vytvoření více indexery, jak je popsáno výše je pouze užitečné, pokud je ve skutečnosti spuštěna paralelně. Pokud chcete spustit několik indexerů paralelně, škálovat vytvořením odpovídající počet oddílů a repliky vaši službu vyhledávání. Například pokud vaši službu vyhledávání má 6 jednotek služby vyhledávání (například 2 oddíly x 3 repliky), pak 6 indexery můžete spustit současně, což vede k six-fold zvýšení propustnost indexování. Další informace o škálování a plánování kapacity najdete v tématu [škálovat prostředek úrovně pro dotaz a indexování úlohy ve službě Azure Search](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexování dokumentů společně s související data

Můžete chtít "sestavte" dokumenty z více zdrojů v indexu. Například můžete sloučit text z objektů BLOB s další metadata uloženy v databázi Cosmos. Můžete dokonce nabízené indexování API společně s různými indexery vybudovat vyhledávání dokumentů z více částí. 

Tato možnost fungovala všechny indexery a další součásti muset odsouhlasení klíč dokumentu. Podrobný návod, najdete v části v tomto článku externí: [dokumenty kombinovat s jinými daty ve službě Azure Search ](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexování prostý text 

Pokud všechny objekty BLOB obsahují stejné kódování prostý text, může výrazně zlepšit výkon indexování pomocí **text analýza režimu**. Chcete-li použít text analýza režimu, nastavte `parsingMode` vlastnosti konfigurace `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Ve výchozím nastavení `UTF-8` kódování se předpokládá. Chcete-li zadat jiné kódování, použijte `encoding` vlastnosti konfigurace: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Vlastnosti metadata specifická pro typ obsahu
Následující tabulka shrnuje zpracování provedeno pro každý dokument formátu a popisuje vlastnosti metadat extrahovat ve službě Azure Search.

| Dokument formátu / typ obsahu | Vlastnosti metadat konkrétní typ obsahu | Podrobnosti o zpracování |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Odstranit kód HTML a rozbalte text |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Rozbalte text, včetně vložených dokumentů (s výjimkou bitové kopie) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Rozbalte text, včetně embedded dokumenty |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Rozbalte text, včetně embedded dokumenty |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Rozbalte text, včetně embedded dokumenty |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Rozbalte text, včetně embedded dokumenty |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Rozbalte text, včetně embedded dokumenty |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Rozbalte text, včetně embedded dokumenty |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Rozbalte text, včetně příloh |
| ZIP (aplikace nebo zip) |`metadata_content_type` |Rozbalte text z všechny dokumenty v archivu |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Odstranit kód XML a rozbalte text |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Rozbalte text<br/>Poznámka: Pokud potřebujete k extrakci více polí dokumentu z objektu blob JSON, přečtěte si [objekty BLOB JSON indexování](search-howto-index-json-blobs.md) podrobnosti |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Rozbalte text, včetně příloh |
| RTF (aplikace/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Rozbalte text|
| Prostý text (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Rozbalte text|


## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte žádosti o funkce nebo vylepšení nápady, dejte nám vědět na našem [UserVoice lokality](https://feedback.azure.com/forums/263029-azure-search/).
