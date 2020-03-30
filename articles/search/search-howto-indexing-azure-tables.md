---
title: Hledání přes obsah úložiště Azure Table
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak indexovat data uložená v úložišti Azure Table pomocí indexeru Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112996"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Jak indexovat tabulky z úložiště Azure Table pomocí Azure Cognitive Search

Tento článek ukazuje, jak pomocí Azure Cognitive Search indexovat data uložená v úložišti Azure Table.

## <a name="set-up-azure-table-storage-indexing"></a>Nastavení indexování úložiště azure table

Indexer úložiště tabulky Azure můžete nastavit pomocí těchto prostředků:

* [Portál Azure](https://ms.portal.azure.com)
* [Rozhraní REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) azure kognitivního vyhledávání
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

Zde demonstrujeme tok pomocí rozhraní REST API. 

### <a name="step-1-create-a-datasource"></a>Krok 1: Vytvoření zdroje dat

Zdroj dat určuje, která data mají být indexována, pověření potřebná pro přístup k datům a zásady, které umožňují Azure Cognitive Search efektivně identifikovat změny v datech.

Pro indexování tabulek musí mít zdroj dat následující vlastnosti:

- **název** je jedinečný název zdroje dat v rámci vyhledávací služby.
- **typ** musí `azuretable`být .
- **parametr credentials** obsahuje připojovací řetězec účtu úložiště. Podrobnosti najdete v části [Zadat přihlašovací údaje.](#Credentials)
- **nastaví** název tabulky a volitelný dotaz.
    - Zadejte název tabulky `name` pomocí parametru.
    - Volitelně zadejte dotaz pomocí `query` parametru. 

> [!IMPORTANT] 
> Kdykoli je to možné, použijte filtr na PartitionKey pro lepší výkon. Jakýkoli jiný dotaz provádí úplnou prohledávání tabulky, což má za následek nízký výkon pro velké tabulky. Podívejte se na část [Důležité informace o výkonu.](#Performance)


Vytvoření zdroje dat:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Další informace o rozhraní API vytvoření zdroje dat naleznete v tématu [Vytvoření zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Způsoby zadání pověření ####

Pověření pro tabulku můžete zadat jedním z těchto způsobů: 

- **Připojovací řetězec účtu úložiště s úplným přístupem** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` : Připojovací řetězec můžete získat z portálu Azure tak, že přejdete na**klíče** **nastavení** >  **účtu** > úložiště (pro klasické účty úložiště) nebo**klíče přístupu k** **nastavení** > (pro účty úložiště Azure Resource Manager).
- **Připojovací řetězec sdíleného přístupového podpisu účtu úložiště**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` Sdílený přístupový podpis by měl mít oprávnění k seznamu a čtení u kontejnerů (v tomto případě tabulek) a objektů (řádků tabulky).
-  **Podpis sdíleného přístupu tabulky**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` Sdílený přístupový podpis by měl mít v tabulce oprávnění k dotazu (čtení).

Další informace o sdílených přístupových podpisech úložiště najdete v tématu [Použití sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Pokud používáte pověření sdíleného přístupového podpisu, budete muset pověření zdroje dat pravidelně aktualizovat obnovenými podpisy, abyste zabránili vypršení jejich platnosti. Pokud vyprší platnost pověření podpisu sdíleného přístupu, indexer se nezdaří s chybovou zprávou podobnou "Pověření uvedená v připojovacím řetězci jsou neplatná nebo vypršela jeho platnost."  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributy a další konstrukce, které utvářejí prostředí vyhledávání.

Vytvoření indexu:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Další informace o vytváření indexů naleznete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3: Vytvoření indexeru
Indexer propojí zdroj dat s cílovým indexem vyhledávání a poskytne plán pro automatizaci aktualizace dat. 

Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Tento indexer běží každé dvě hodiny. (Interval plánu je nastaven na "PT2H".) Chcete-li spustit indexer každých 30 minut, nastavte interval na "PT30M". Nejkratší podporovaný interval je pět minut. Plán je volitelný; pokud je vynechán, indexer spustí pouze jednou, když je vytvořen. Však můžete spustit indexer na vyžádání kdykoli.   

Další informace o rozhraní API vytvořit indexeru naleznete v [tématu Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Další informace o definování plánů indexeru naleznete v [tématu Jak naplánovat indexery pro Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Řešení různých názvů polí
Názvy polí v existujícím indexu se někdy liší od názvů vlastností v tabulce. Mapování polí můžete použít k mapování názvů vlastností z tabulky na názvy polí v indexu vyhledávání. Další informace o mapování polí, najdete [v tématu Azure Cognitive Search indexer pole mapování přemostění rozdíly mezi zdroje dat a indexy vyhledávání](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Zpracování klíčů dokumentu
V Azure Cognitive Search klíč dokumentu jednoznačně identifikuje dokument. Každý index vyhledávání musí mít přesně `Edm.String`jedno klíčové pole typu . Pole klíče je vyžadováno pro každý dokument, který je přidáván do indexu. (Ve skutečnosti je to jediné povinné pole.)

Vzhledem k tomu, že řádky tabulky mají složený `Key` klíč, Azure Cognitive Search generuje syntetické pole s názvem, které je zřetězení klíče oddílu a hodnoty klíče řádku. Například pokud je `PK1` řádek PartitionKey a RowKey je `Key` `PK1RK1` `RK1`, pak hodnota pole je .

> [!NOTE]
> Hodnota `Key` může obsahovat znaky, které jsou v klíčích dokumentu neplatné, například pomlčky. Neplatné znaky můžete řešit `base64Encode` pomocí [funkce mapování polí](search-indexer-field-mappings.md#base64EncodeFunction). Pokud to uděláte, nezapomeňte použít kódování Base64 bezpečné pro adresy URL také při předávání klíčů dokumentů ve voláních rozhraní API, jako je Lookup.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové zjišťování indexování a odstraňování
Když nastavíte indexer tabulky pro spuštění podle plánu, přeindexuje pouze nové nebo aktualizované `Timestamp` řádky, jak je určeno hodnotou řádku. Není nutné zadat zásady zjišťování změn. Přírůstkové indexování je pro vás povoleno automaticky.

Chcete-li označit, že některé dokumenty musí být odebrány z indexu, můžete použít strategii obnovitelného odstranění. Místo odstranění řádku přidejte vlastnost, která označuje, že je odstraněna, a nastavte zásadu detekce měkkého odstranění na zdroji dat. Následující zásady se například domnívají, že řádek je `IsDeleted` odstraněn, `"true"`pokud má řádek vlastnost s hodnotou :

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
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

Ve výchozím nastavení azure cognitive search `Timestamp >= HighWaterMarkValue`používá následující filtr dotazu: . Vzhledem k tomu, že tabulky Azure `Timestamp` nemají sekundární index v poli, tento typ dotazu vyžaduje úplné prohledávače tabulky a je proto pomalé pro velké tabulky.


Zde jsou dva možné přístupy ke zlepšení výkonu indexování tabulky. Oba tyto přístupy spoléhají na použití oddílů tabulky: 

- Pokud vaše data lze přirozeně rozdělit do několika rozsahů oddílů, vytvořte zdroj dat a odpovídající indexer pro každý rozsah oddílů. Každý indexer má nyní zpracovat pouze určitý rozsah oddílů, výsledkem je lepší výkon dotazu. Pokud data, která je třeba indexovat má malý počet pevných oddílů, ještě lepší: každý indexer pouze provede prohledávač oddílů. Chcete-li například vytvořit zdroj dat pro zpracování `000` `100`rozsahu oddílů s klíči od do , použijte dotaz, jako je tento: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Pokud jsou data rozdělena podle času (například každý den nebo týden vytvoříte nový oddíl), zvažte následující přístup: 
    - Použijte dotaz formuláře: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Sledování průběhu indexeru pomocí [rozhraní Get Indexer Status API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)a pravidelně aktualizovat `<TimeStamp>` podmínku dotazu na základě nejnovější úspěšné hodnoty vysoké vodoznaku. 
    - S tímto přístupem, pokud potřebujete spustit úplné přeindexování, je třeba obnovit dotaz zdroje dat kromě resetování indexeru. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám vylepšit Azure Cognitive Search
Pokud máte požadavky na funkce nebo nápady na vylepšení, odešlete je na náš [web UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
