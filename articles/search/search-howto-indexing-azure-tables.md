---
title: Hledání v obsahu úložiště Azure Table
titleSuffix: Azure Cognitive Search
description: Naučte se indexovat data uložená v Azure Table Storage pomocí indexeru služby Azure Kognitivní hledání.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 2c67cd4d071660da2ca5714623695ca434329263
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91275179"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Indexování tabulek z Azure Table Storage pomocí Azure Kognitivní hledání

Tento článek popisuje, jak pomocí služby Azure Kognitivní hledání indexovat data uložená v úložišti tabulek Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Nastavení indexování služby Azure Table Storage

Indexer služby Azure Table Storage můžete nastavit pomocí těchto prostředků:

* [Azure Portal](https://ms.portal.azure.com)
* [REST API](/rest/api/searchservice/Indexer-operations) kognitivní hledání Azure
* Sada Azure Kognitivní hledání [.NET SDK](/dotnet/api/overview/azure/search)

V tomto příkladu předvádíme tok pomocí REST API. 

### <a name="step-1-create-a-datasource"></a>Krok 1: vytvoření zdroje dat

Zdroj dat určuje, která data se mají indexovat, přihlašovací údaje potřebné pro přístup k datům a zásady, které umožňují službě Azure Kognitivní hledání efektivně identifikovat změny v datech.

Pro indexování tabulek musí mít zdroj dat následující vlastnosti:

- **název** je jedinečný název zdroje dat v rámci vyhledávací služby.
- **typ** musí být `azuretable` .
- parametr **přihlašovacích údajů** obsahuje připojovací řetězec účtu úložiště. Podrobnosti najdete v části [zadání přihlašovacích údajů](#Credentials) .
- **kontejner** nastaví název tabulky a nepovinný dotaz.
    - Zadejte název tabulky pomocí `name` parametru.
    - Volitelně můžete zadat dotaz pomocí `query` parametru. 

> [!IMPORTANT] 
> Kdykoli je to možné, použijte filtr na PartitionKey pro lepší výkon. Jakýkoli jiný dotaz provede úplnou kontrolu tabulky, což má za následek špatný výkon pro velké tabulky. Viz část [požadavky na výkon](#Performance) .


Vytvoření zdroje dat:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   
```

Další informace o rozhraní API Create DataSource najdete v tématu [Create DataSource](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Způsoby zadání přihlašovacích údajů ####

Přihlašovací údaje pro tabulku můžete zadat jedním z těchto způsobů: 

- **Připojovací řetězec spravované identity**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` Tento připojovací řetězec nevyžaduje klíč účtu, ale musíte postupovat podle pokynů pro [nastavení připojení k účtu Azure Storage pomocí spravované identity](search-howto-managed-identities-storage.md).
- **Úplný přístup k účtu úložiště s úplným přístupem**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` připojovací řetězec můžete z Azure Portal získat tak, že v okně nastavení **účtu úložiště** zadáte  >    >  **klíče** pro účty úložiště (pro účty Classic) nebo   >  **přístupové klíče** nastavení (pro účty Azure Resource Manager úložiště).
- **Připojovací řetězec sdíleného přístupového podpisu účtu úložiště**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` sdílený přístupový podpis by měl mít oprávnění list a číst na kontejnerech (v tomto případě tabulky) a objekty (řádky tabulky).
-  **Sdílený přístupový podpis tabulky**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` sdílený přístupový podpis by měl mít v tabulce oprávnění Query (čtení).

Další informace o sdílených přístupových podpisech úložiště najdete v tématu [použití sdílených přístupových podpisů](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Pokud používáte přihlašovací údaje sdíleného přístupového podpisu, budete muset pravidelně aktualizovat přihlašovací údaje zdroje dat pomocí obnovených signatur, aby se předešlo jejich vypršení platnosti. Pokud platnost přihlašovacích údajů pro sdílený přístupový podpis vyprší, indexer se nezdařil s chybovou zprávou podobnou "přihlašovací údaje zadané v připojovacím řetězci jsou neplatné nebo vypršely."  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributy a další konstrukce, které prohledají možnosti vyhledávání.

Vytvoření indexu:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }
```

Další informace o vytváření indexů najdete v tématu [vytvoření indexu](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3: vytvoření indexeru
Indexer připojuje zdroj dat k cílovému vyhledávacímu indexu a poskytuje plán pro automatizaci aktualizace dat. 

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Tento indexer se spustí každé dvě hodiny. (Interval plánu je nastaven na "PT2H".) Pokud chcete indexer spustit každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je pět minut. Plán je nepovinný. je-li tento parametr vynechán, bude indexer spuštěn pouze jednou při jeho vytvoření. Indexer ale můžete kdykoli spustit na vyžádání.   

Další informace o rozhraní API pro vytváření indexerů najdete v tématu [Create indexer](/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Pracovat s různými názvy polí
V některých případech se názvy polí v existujícím indexu liší od názvů vlastností v tabulce. Mapování polí můžete použít k mapování názvů vlastností z tabulky na názvy polí v indexu vyhledávání. Další informace o mapování polí najdete v tématu [mapování polí služby Azure kognitivní hledání indexeru mostu jako rozdíl mezi zdroji dat a indexy vyhledávání](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Zpracování klíčů dokumentu
V Azure Kognitivní hledání klíč dokumentu jednoznačně identifikuje dokument. Každý index vyhledávání musí mít přesně jedno pole klíče typu `Edm.String` . Klíčové pole je vyžadováno pro každý dokument, který je přidán do indexu. (Ve skutečnosti je to jediné povinné pole.)

Vzhledem k tomu, že řádky tabulky mají složený klíč, Azure Kognitivní hledání generuje syntetické pole `Key` s názvem, které je zřetězení hodnot klíče oddílu a klíče řádku. Pokud je například PartitionKey řádku `PK1` a RowKey je `RK1` , `Key` hodnota pole je `PK1RK1` .

> [!NOTE]
> `Key`Hodnota může obsahovat znaky, které jsou v klíčích dokumentů neplatné, například pomlčky. Pomocí `base64Encode` [funkce mapování polí](search-indexer-field-mappings.md#base64EncodeFunction)můžete pracovat s neplatnými znaky. Pokud to uděláte, nezapomeňte použít kódování Base64 bezpečné pro adresy URL také při předávání klíčů dokumentů ve voláních rozhraní API, jako je Lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstraňování duplicit
Když nastavíte indexer tabulky tak, aby se spouštěl podle plánu, přeindexuje pouze nové nebo aktualizované řádky, které jsou určeny `Timestamp` hodnotou řádku. Nemusíte určovat zásady detekce změn. Přírůstkové indexování je povoleno automaticky.

Chcete-li určit, že některé dokumenty musí být z indexu odebrány, můžete použít strategii obnovitelného odstranění. Místo odstranění řádku přidejte vlastnost, která označuje, že je odstraněna, a nastavte zásady Detekce tichého odstranění pro zdroj dat. Následující zásada například předpokládá, že řádek bude odstraněn, pokud má řádek vlastnost `IsDeleted` s hodnotou `"true"` :

```http
    PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   
```

<a name="Performance"></a>
## <a name="performance-considerations"></a>Otázky výkonu

Ve výchozím nastavení používá Azure Kognitivní hledání následující filtr dotazu: `Timestamp >= HighWaterMarkValue` . Vzhledem k tomu, že tabulky Azure nemají sekundární index v `Timestamp` poli, vyžaduje tento typ dotazu úplnou kontrolu tabulky, a proto je pro velké tabulky pomalé.


Tady jsou dva možné přístupy pro zlepšení výkonu indexování tabulek. Oba tyto přístupy spoléhají na použití oddílů tabulky: 

- Pokud je možné data přirozeně rozdělit do několika rozsahů oddílů, vytvořte zdroj dat a odpovídající indexer pro každý rozsah oddílů. Každý indexer teď musí zpracovat jenom konkrétní rozsah oddílu, což vede k lepšímu výkonu dotazů. Pokud data, která mají být indexována, mají malý počet pevných oddílů, a to i v případě, že každý indexer provádí kontrolu oddílu. Chcete-li například vytvořit zdroj dat pro zpracování rozsahu oddílu s klíči z `000` na `100` , použijte dotaz podobný tomuto: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Pokud jsou vaše data rozdělená podle času (například nový oddíl vytvoříte každý den nebo týden), vezměte v úvahu následující postup: 
    - Použijte dotaz na formulář: `(PartitionKey ge <TimeStamp>) and (other filters)` . 
    - Sledovat průběh indexeru pomocí [rozhraní Get API stavu pro indexer](/rest/api/searchservice/get-indexer-status)a pravidelně aktualizovat `<TimeStamp>` podmínku dotazu na základě nejnovější úspěšné hodnoty vysokého množství. 
    - Pokud potřebujete s tímto přístupem aktivovat kompletní Reindexování, je potřeba resetovat dotaz DataSource a obnovit indexer. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám zdokonalit Azure Kognitivní hledání
Pokud máte žádosti o funkce nebo návrhy na vylepšení, odešlete je na náš [Web UserVoice](https://feedback.azure.com/forums/263029-azure-search/).