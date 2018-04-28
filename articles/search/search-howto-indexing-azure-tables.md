---
title: Indexování úložiště tabulek Azure s Azure Search | Microsoft Docs
description: Naučte se indexovat data uložená ve službě Azure Table storage s Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: a171bdd11cd2de030937927eef34d5ad9e0507af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="index-azure-table-storage-with-azure-search"></a>Index úložiště tabulek Azure s Azure Search
Tento článek ukazuje, jak používat Azure Search k indexu data uložená ve službě Azure Table storage.

## <a name="set-up-azure-table-storage-indexing"></a>Nastavení Azure Table storage indexování

Indexer Azure Table storage můžete nastavit pomocí těchto prostředků:

* [Azure Portal](https://ms.portal.azure.com)
* Služba Azure Search [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Služba Azure Search [sady .NET SDK](https://aka.ms/search-sdk)

Zde ukážeme toku pomocí rozhraní REST API. 

### <a name="step-1-create-a-datasource"></a>Krok 1: Vytvoření zdroje dat

Zdroj dat určuje, která data do indexu, přihlašovací údaje potřebné pro přístup k data a zásady, které umožňují Azure Search efektivně identifikovat změny v datech.

Pro tabulku indexování, zdroje dat musí mít následující vlastnosti:

- **název** je jedinečný název zdroje dat v rámci služby search.
- **typ** musí být `azuretable`.
- **přihlašovací údaje** parametr obsahuje připojovací řetězec pro účet úložiště. Najdete v článku [zadejte přihlašovací údaje](#Credentials) podrobnosti.
- **kontejner** nastaví název tabulky a volitelné dotazů.
    - Zadejte název tabulky pomocí `name` parametr.
    - Volitelně můžete zadat pomocí dotazu `query` parametr. 

> [!IMPORTANT] 
> Kdykoli je to možné, použijte filtr na PartitionKey pro dosažení vyššího výkonu. Další dotaz nemá prohledání úplnou tabulky, výsledkem je nízký výkon pro rozsáhlé tabulky. Najdete v článku [faktory ovlivňující výkon](#Performance) části.


Vytvoření zdroje dat:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Další informace o rozhraní API vytvořit zdroj dat najdete v tématu [vytvořit zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Způsoby, jak zadat přihlašovací údaje ####

Zadejte pověření pro tabulky v jednom z těchto způsobů: 

- **Úplný přístup připojovací řetězce k účtu úložiště**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` z portálu Azure můžete získat připojovací řetězec přechodem na **okně účtu úložiště** > **nastavení**  >  **Klíče** (pro účty úložiště classic) nebo **nastavení** > **přístupové klíče** (pro úložiště Azure Resource Manager účty).
- **Účet úložiště sdíleného přístupu podpis připojovací řetězec**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` sdílený přístupový podpis by měla mít v seznamu a oprávnění ke čtení v kontejnery (tabulky v tomto případě) a objekty (řádky tabulky).
-  **Tabulka sdílený přístupový podpis**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` sdílený přístupový podpis musí mít oprávnění dotazu (čtení) v tabulce.

Další informace o sdílené úložiště přístup podpisy, najdete v části [pomocí sdílené přístupové podpisy](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Pokud používáte sdílený přístupový podpis pověření, musíte se pravidelně aktualizovat přihlašovací údaje zdroje dat s prodlouženou platností podpisy, aby se zabránilo vypršení jejich platnosti. Pokud přihlašovací údaje sdílený přístupový podpis vyprší, indexeru nezdaří a zobrazí se chybová zpráva "Zadané v připojovacím řetězci přihlašovací údaje jsou neplatné nebo vypršela platnost."  

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu
Index určuje pole v dokumentu, atributy, a jiných objektů, které utvářejí hledání prostředí.

Pokud chcete vytvořit index:

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

Další informace o vytváření indexů, najdete v části [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Krok 3: Vytvořením indexeru.
Indexer zdroje dat se připojí pomocí cílový index vyhledávání a poskytuje plán, který chcete automatizovat aktualizace dat. 

Po vytvoření indexu a zdroj dat, jste připraveni vytvořit indexeru:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Indexer se spustí každé dvě hodiny. (Interval plánování je nastavena na "PT2H"). Pokud chcete spustit indexer každých 30 minut, nastavte interval, který "PT30M". Nejkratší podporovaný interval je pět minut. Plán je volitelná. Pokud tento parametr vynechán, indexer se spustí pouze jednou, když je vytvořena. Indexer však můžete spustit na vyžádání kdykoli.   

Další informace o rozhraní API Indexer vytvořit, naleznete v části [vytvořit Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Řešení s názvy různých polí
V některých případech se liší od názvů vlastností v tabulce názvy polí v existující index. Mapování polí můžete použít k mapování názvů vlastností z tabulky na názvy polí v indexu vyhledávání. Další informace o mapování polí, najdete v části [mapování polí indexer Azure Search přemostění rozdíly mezi zdroje dat a vyhledávání indexů](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Popisovač dokumentu klíče
Ve službě Azure Search je klíč dokumentu jednoznačně identifikuje dokumentu. Každý index hledání musí mít přesně jeden klíčové pole typu `Edm.String`. Klíčové pole je povinné pro každý dokument, který je přidáván do indexu. (Ve skutečnosti je pouze požadované pole.)

Vzhledem k tomu, že řádky tabulky složené klíče, vygeneruje Azure Search syntetické pole s názvem `Key` , je tvořen oddílu klíč a řádek klíčové hodnoty. Například pokud řádek je klíč oddílu je `PK1` a RowKey `RK1`, pak se `Key` hodnotu pole je `PK1RK1`.

> [!NOTE]
> `Key` Hodnota může obsahovat znaky, které jsou v dokumentu klíče, jako je například pomlčky neplatné. Neplatné znaky můžete řešit pomocí `base64Encode` [pole mapování funkce](search-indexer-field-mappings.md#base64EncodeFunction). Pokud to uděláte, nezapomeňte použít kódování Base64 bezpečných adresy URL při předávání dokumentu klíče v rozhraní API volá například vyhledávání.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstranění duplicit
Při nastavování tabulky indexer spouštět podle plánu, ho Reindexuje pouze nové nebo aktualizované řádky určeného řádku `Timestamp` hodnotu. Nemáte zadejte zásady detekce změn. Přírůstkové indexování se povolí automaticky.

K označení, že určitých dokumentů je třeba odebrat z indexu, můžete použít strategie obnovitelného odstranění. Místo odstraněním řádku, přidání vlastnosti do znamenat, že se má odstranit a nastavit zásadu obnovitelného odstranění duplicit na zdroji dat. Například tyto zásady domnívá, že je-li vlastnost řádek odstranění řádku `IsDeleted` s hodnotou `"true"`:

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

Ve výchozím nastavení Azure Search používá následující filtr dotazu: `Timestamp >= HighWaterMarkValue`. Vzhledem k tomu tabulky Azure nemáte sekundárního indexu `Timestamp` pole, tento typ dotazu vyžaduje prohledání úplnou tabulky a je proto pomalé pro rozsáhlé tabulky.


Tady jsou dvě možné přístupy k zlepšení výkonu indexování tabulky. Oba tyto přístupy spoléhají na použití tabulky oddílů: 

- Pokud vaše data můžou být dělené přirozeně do několika oddílů rozsahy, vytvořte zdroj dat a odpovídající indexer pro každý oddíl rozsah. Každý indexer má nyní zpracovat pouze konkrétní oddíl rozsah, což vede k lepší výkon dotazů. Pokud data, která musí být indexován má malý počet pevné oddílů, ještě lepší: každý indexer pouze nemá kontrolu oddílu. Chcete-li například vytvořit zdroj dat pro zpracování rozsahu oddílu s klíči z `000` k `100`, použít dotaz takto: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Pokud vaše data je rozdělena na oddíly podle času (například můžete vytvořit nový oddíl každý den nebo týden), zvažte následující postup: 
    - Použít dotaz, ve formátu: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Indexer průběh sledovat pomocí [získání rozhraní indexeru stav API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)a pravidelně aktualizovat `<TimeStamp>` podmínky dotazu na základě nejnovější úspěšné horní mez hodnoty. 
    - Potřebujete s tímto přístupem, pokud je nutné aktivovat novou dokončení indexaci resetování dotazu zdroje dat kromě resetovat indexer. 


## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte žádosti o funkce nebo vylepšení nápady, odesílat je na našem [UserVoice lokality](https://feedback.azure.com/forums/263029-azure-search/).
