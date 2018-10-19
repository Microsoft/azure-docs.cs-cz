---
title: Indexování služby Azure Table storage s Azure Search | Dokumentace Microsoftu
description: Naučte se indexovat data uložená ve službě Azure Table storage s Azure Search
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: 738518f94869a55cf80db1c87b8c74b167f5cce1
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406921"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Index Azure Table storage s Azure Search
Tento článek ukazuje, jak pomocí Azure Search index data uložená v úložišti tabulek Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Nastavení Azure Table storage indexování

Indexer Azure Table storage můžete nastavit pomocí těchto prostředků:

* [Azure Portal](https://ms.portal.azure.com)
* Služba Azure Search [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Služba Azure Search [sady .NET SDK](https://aka.ms/search-sdk)

Tady vám ukážeme tok pomocí rozhraní REST API. 

### <a name="step-1-create-a-datasource"></a>Krok 1: Vytvoření zdroje dat

Zdroj dat určuje, jaká data do indexu, přihlašovacích údajů potřebných pro přístup k data a zásady, které umožňují Azure Search efektivně identifikovat změny v datech.

Indexování tabulky zdroje dat musí mít následující vlastnosti:

- **název** je jedinečný název zdroje dat v rámci vaší vyhledávací služby.
- **typ** musí být `azuretable`.
- **přihlašovací údaje** parametr obsahuje připojovací řetězec účtu úložiště. Zobrazit [zadat přihlašovací údaje](#Credentials) podrobné informace.
- **kontejner** nastaví název tabulky a nepovinný dotaz.
    - Zadejte název tabulky s použitím `name` parametru.
    - Volitelně můžete zadat dotaz s použitím `query` parametru. 

> [!IMPORTANT] 
> Kdykoli je to možné, použijte filtr u PartitionKey pro zajištění lepšího výkonu. Jakýkoli jiný dotaz nemá ke skenování celé tabulky, výsledkem je nízký výkon pro rozsáhlé tabulky. Zobrazit [faktory ovlivňující výkon](#Performance) oddílu.


Chcete-li vytvořit zdroj dat:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Další informace o rozhraní API pro vytvoření zdroje dat, naleznete v tématu [vytvořit zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Způsoby, jak zadat přihlašovací údaje ####

Zadejte přihlašovací údaje pro tabulky v jednom z těchto způsobů: 

- **Připojovací řetězec účtu úložiště úplný přístup**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` připojovací řetězec můžete získat z webu Azure portal tak, že přejdete **okno účet úložiště** > **nastavení**  >  **Klíče** (pro účty úložiště classic) nebo **nastavení** > **přístupové klíče** (pro úložiště Azure Resource Manageru účty).
- **Účet úložiště sdíleného přístupu podpis připojovací řetězec**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` sdíleného přístupového podpisu musí mít v seznamu a oprávnění ke čtení u kontejnerů (tabulky v tomto případě) a objekty (řádky tabulky).
-  **Tabulka sdílený přístupový podpis**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` sdílený přístupový podpis by měl mít oprávnění pro dotaz (čtení) v tabulce.

Další informace o úložiště sdílené přístupové podpisy, naleznete v tématu [použití sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Pokud používáte přihlašovací údaje sdíleného přístupového podpisu, je potřeba pravidelně aktualizovat přihlašovací údaje zdroje dat s obnovené podpisy, aby se zabránilo vypršení jejich platnosti. Pokud vyprší platnost pověření sdíleného přístupového podpisu, indexeru se nezdaří s chybovou zprávou, která je podobná "V připojovacím řetězci zadané přihlašovací údaje jsou neplatné nebo vypršela platnost."  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributy, a zkušeností jiných objektů, které obrazce hledání.

K vytvoření indexu:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Další informace o vytváření indexů, naleznete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3: Vytvoření indexeru
Indexer propojuje zdroj dat s cílovým indexem vyhledávání a poskytuje plán pro automatizaci aktualizace dat. 

Po vytvoření indexu a zdroj dat, jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Indexer spouští každé dvě hodiny. (Interval plánování je nastavena na "PT2H"). Pokud chcete spustit indexer každých 30 minut, nastavte interval, který "PT30M". Nejkratší podporovaný interval je pět minut. Plán je volitelná. Pokud tento parametr vynechán, indexer se spustí pouze jednou, když je vytvořena. Můžete však spustit indexer na vyžádání v každém okamžiku.   

Další informace o rozhraní API pro vytvoření indexeru najdete v tématu [vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Řešení s názvy jiného pole
Názvy polí v existujícího indexu v některých případech se liší od názvů vlastností v tabulce. Mapování polí můžete použít k mapování názvů vlastností z tabulky na názvy polí v indexu vyhledávání. Další informace o mapování polí najdete v tématu [mapování polí indexeru Azure Search most rozdíly mezi zdroje dat a vyhledávací indexy](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Popisovač klíče dokumentů
Ve službě Azure Search je klíč dokumentu jednoznačně identifikuje dokumentu. Každý index vyhledávání musí mít přesně jeden klíčové pole typu `Edm.String`. Klíčové pole je povinné pro každý dokument, který je přidáván do indexu. (Ve skutečnosti je jediné povinné pole.)

Vzhledem k tomu, že řádky tabulky složený klíč, generuje Azure Search syntetické pole s názvem `Key` , která je zřetězením oddílu řádků klíče a hodnoty klíče. Například pokud je řádek PartitionKey je `PK1` a RowKey `RK1`, pak bude `Key` je hodnota pole `PK1RK1`.

> [!NOTE]
> `Key` Hodnota může obsahovat znaky, které nejsou platné v klíči dokumentu, jako je například spojovníky. Můžete vyřešit pomocí neplatné znaky `base64Encode` [pole mapování funkce](search-indexer-field-mappings.md#base64EncodeFunction). Pokud to uděláte, nezapomeňte také použít kódování Base64 adresu URL bezpečné při předávání klíče dokumentů v rozhraní API volá jako je například vyhledávání.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstranění duplicit
Při nastavování tabulky indexer spustit podle plánu se Reindexuje jenom nové nebo aktualizované řádky, vzhledem k řádku `Timestamp` hodnotu. Není nutné určit zásady detekce změn. Přírůstkové indexování se povolí automaticky.

K označení, je nutné odebrat některé dokumenty z indexu, můžete použít obnovitelné odstranění strategie. Nemusíte ho odstraňovat řádek, přidejte vlastnost umožňující označit, že má odstranit a nastavit zásadu obnovitelného odstranění detekce v objektu datasource. Například následující zásady domnívá, že se odstranění řádku, pokud řádek obsahuje vlastnosti `IsDeleted` s hodnotou `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Otázky výkonu

Ve výchozím nastavení používá Azure Search následující filtr dotazu: `Timestamp >= HighWaterMarkValue`. Tabulky Azure nemají sekundární index `Timestamp` pole, tento typ dotazu vyžaduje ke skenování celé tabulky a je proto pomalé pro velké tabulky.


Tady jsou dvě možné přístupy pro zlepšení výkonu indexování tabulky. Oba tyto přístupy spoléhají na použití oddíly tabulky: 

- Pokud vaše data můžete přirozeně rozdělená na více rozsahů oddílů, vytvořte zdroj dat a odpovídající indexer pro každý rozsah oddílu. Každý indexer musí nyní zpracovat pouze konkrétní oddíl rozsah, což vede k lepší výkon dotazů. Pokud má malý počet pevné oddíly, ještě více vylepšit data, která musí být indexován: každý indexer provádí pouze kontroly oddílu. Například pro vytvoření zdroje dat pro zpracování rozsah oddílu pomocí klíčů z `000` k `100`, dotaz následujícím způsobem: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Pokud vaše data jsou rozdělená podle času (například můžete vytvořit nový oddíl každý dne nebo týdne), zvažte následující postup: 
    - Pomocí dotazu ve formátu: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Monitorovat průběh indexeru pomocí [získání rozhraní API stavu Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)a pravidelně aktualizují `<TimeStamp>` podmínky dotazu na základě nejnovější úspěšné jako horní mez vysoké hodnoty. 
    - S tímto přístupem Pokud je potřeba aktivovat kompletní Reindexace, musíte obnovit zdroj dat dotazu, kromě resetování indexeru. 


## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte požadavky na funkce nebo nápady na vylepšení, odešlete je z našich [webu UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
