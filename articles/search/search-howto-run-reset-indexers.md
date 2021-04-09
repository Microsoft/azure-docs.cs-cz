---
title: Spustit nebo resetovat indexery
titleSuffix: Azure Cognitive Search
description: Resetujte indexer, dovednosti nebo jednotlivé dokumenty a aktualizujte vše nebo část indexu nebo úložiště znalostí.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667659"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Spuštění nebo resetování indexerů, dovedností nebo dokumentů

K provedení indexeru může dojít při prvním vytvoření [indexeru](search-indexer-overview.md), při spuštění indexeru na vyžádání nebo při nastavování indexeru podle plánu. Po prvním spuštění indexer udržuje přehled o tom, které dokumenty hledání byly indexovány prostřednictvím interní "horní meze". Značka se nikdy nezveřejňuje v rozhraní API, ale interně indexer ví, kde se indexování zastavilo, aby bylo možné pokračovat v příštím spuštění.

Horní značku můžete vymazat nastavením indexeru, pokud se chcete znovu zpracovat od začátku. Resetování rozhraní API jsou k dispozici v klesajících úrovních v hierarchii objektů:

+ Celé hledání corpus (použití [indexerů pro resetování](#reset-indexers))
+ Konkrétní dokument nebo seznam dokumentů (použití [resetování dokumentů – Preview](#reset-docs))
+ Konkrétní dovednosti nebo obohacení dokumentu (použití [resetování dovedností – Preview](#reset-skills))

Rozhraní API pro resetování se používají k aktualizaci obsahu v mezipaměti (použitelné ve scénářích [obohacení AI](cognitive-search-concept-intro.md) ) nebo k vymazání horních značek a sestavení indexu.

Resetování, následované spuštěním, může znovu zpracovat existující dokumenty a nové dokumenty, ale neodebere osamocené dokumenty hledání v indexu hledání, které byly vytvořeny v předchozích spuštěních. Další informace o odstranění najdete v tématu [Přidání, aktualizace nebo odstranění dokumentů](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Spustit indexery

[Vytvořit indexer](/rest/api/searchservice/create-indexer) vytvoří a spustí indexer, pokud ho nevytvoříte v zakázaném stavu ("zakázáno": true). První spuštění trvá trochu delší dobu, protože tvoří také jeho pokrývající se vytváření objektů.

[Spuštění indexeru](/rest/api/searchservice/run-indexer) detekuje a zpracovává jenom to, co je potřeba k synchronizaci indexu vyhledávání se zdrojem dat. Úložiště objektů BLOB má vestavěnou detekci změn. Další zdroje dat, jako je Azure SQL nebo Cosmos DB, musí být nakonfigurované pro detekci změn předtím, než může indexer číst jenom nové a aktualizované řádky.

Indexer můžete spustit pomocí kteréhokoli z těchto přístupů:

+ Azure Portal pomocí příkazu **Run** na stránce indexeru
+ [Spustit indexer (REST)](/rest/api/searchservice/run-indexer)
+ [Metoda RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) v sadě Azure .NET SDK (nebo použití ekvivalentní metody RunIndexer v jiné sadě SDK)

Provádění indexeru podléhá následujícím omezením:

+ Maximální počet úloh indexeru je 1 na replika žádné souběžné úlohy.

  Pokud je již vykonávání indexeru v kapacitě, obdržíte toto oznámení: "selhalo spuštění indexeru <indexer-název>", chyba: "v současnosti probíhá jiné volání indexeru; Souběžná volání nejsou povolena.

+ Maximální doba spuštění je 2 hodiny, pokud používáte dovednosti a 24 hodin bez. 

  Zpracování můžete roztáhnout vložením indexeru podle plánu. Úroveň Free má nižší časový limit běhu. Úplný seznam najdete v tématu [omezení indexerů](search-limits-quotas-capacity.md#indexer-limits) .

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Resetování indexeru

Resetování indexeru zahrnuje všechny. Ve vyhledávacím indexu je libovolný dokument hledání, který byl původně vyplněn indexerem, označen pro úplné zpracování. Všechny nové dokumenty nalezené v podkladovém zdroji budou přidány do indexu jako dokumenty pro hledání. Pokud je indexer nakonfigurovaný tak, aby používal dovednosti a [ukládání do mezipaměti](search-howto-incremental-index.md), dovednosti se znovu spustí a aktualizuje se mezipaměť.

Indexer můžete obnovit pomocí některého z těchto přístupů následovaný indexerem spuštěným pomocí jedné z metod popsaných výše.

+ Azure Portal pomocí příkazu **reset** na stránce indexeru
+ [Resetovat indexer (REST)](/rest/api/searchservice/reset-indexer)
+ [Metoda ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) v sadě Azure .NET SDK (nebo použití ekvivalentní metody RunIndexer v jiné sadě SDK)

Příznak resetování je po dokončení spuštění vymazán. Jakákoli pravidelná detekce změn, která je pro váš zdroj dat funkční, bude pokračovat při příštím spuštění, vyzvednutí všech dalších nových nebo aktualizovaných hodnot ve zbývající části sady dat.

> [!NOTE]
> Požadavek na obnovení určuje, co se má znovu zpracovat (indexer, dovednost nebo dokument), ale v opačném případě nemá vliv na chování modulu runtime indexeru. Pokud indexer obsahuje parametry času běhu, mapování polí, ukládání do mezipaměti, možnosti dávky atd., budou tato nastavení platit při spuštění indexeru po jeho resetování.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Resetování dovedností (Preview)

> [!IMPORTANT] 
> [Resetování dovedností](/rest/api/searchservice/preview-api/reset-skills) je ve verzi Public Preview dostupné jenom ve verzi Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

U indexerů, které mají dovednosti, můžete resetovat konkrétní dovednosti, aby se vynutilo zpracování této dovednosti a všech dovedností, které závisí na jeho výstupu. Také se aktualizují [rozšíření v mezipaměti](search-howto-incremental-index.md) . Resetování dovedností neověřuje výsledky dovedností uložených v mezipaměti, což je užitečné v případě, že je nasazená nová verze dovednosti a chcete, aby indexer pro všechny dokumenty znovu provedl tuto dovednost. 

[Obnova dovedností](/rest/api/searchservice/preview-api/reset-skills) je k dispozici prostřednictvím REST **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Můžete určit jednotlivé dovednosti, jak je uvedeno v příkladu výše, ale pokud kterákoli z těchto dovedností vyžaduje výstup z neuvedené dovednosti (#2 prostřednictvím #4), neuvedené dovednosti se spustí, dokud mezipaměť neposkytne potřebné informace. Aby to bylo pravdivé, musí rozšíření v mezipaměti pro dovednosti #2 přes #4 mít závislost na #1 (uvedená pro resetování).

Pokud nejsou zadané žádné dovednosti, spustí se celý dovednosti a pokud je mezipaměť povolená, aktualizuje se i mezipaměť.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Resetování dokumentů (Preview)

> [!IMPORTANT] 
> [Resetování dokumentů](/rest/api/searchservice/preview-api/reset-documents) je ve verzi Public Preview, dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Rozhraní API pro resetování dokumentů](/rest/api/searchservice/preview-api/reset-documents) přijímá seznam klíčů dokumentů, abyste mohli aktualizovat konkrétní dokumenty. Je-li tento parametr zadán, budou parametry obnovení jediným rozhodujícím významem, co bude zpracováno bez ohledu na jiné změny podkladových dat. Například když jste od posledního spuštění indexeru přidali nebo aktualizovali 20 objektů blob, ale jenom resetujete jenom jeden dokument, zpracuje se jenom jeden dokument.

Na základě jednotlivých dokumentů se všechna pole v tomto dokumentu hledání aktualizují o hodnoty ze zdroje dat. Nemůžete vybrat a vybrat, která pole se mají aktualizovat. 

Pokud je dokument obohacen prostřednictvím dovednosti a obsahuje data uložená v mezipaměti, vyvolá se dovednosti jenom pro zadané dokumenty a v mezipaměti se pro znovu zpracované dokumenty aktualizuje.

Při prvním testování tohoto rozhraní API vám následující rozhraní API pomůže ověřit a otestovat chování:

+ [Získá stav indexeru](/rest/api/searchservice/get-indexer-status) pomocí verze rozhraní API `2020-06-30-Preview` , aby se zkontroloval stav resetování a stav provádění. Na konci odpovědi na stav můžete najít informace o žádosti o obnovení.
+ [Obnovte dokumenty](/rest/api/searchservice/preview-api/reset-documents) pomocí rozhraní API verze `2020-06-30-Preview` , abyste určili, které dokumenty se mají zpracovat.
+ [Spusťte indexer](/rest/api/searchservice/run-indexer) a spusťte indexer (libovolnou verzi rozhraní API).
+ [Hledá v dokumentech](/rest/api/searchservice/search-documents) aktualizované hodnoty a také vrátí klíče dokumentu, pokud si nejste jisti hodnotou. Použijte, `"select": "<field names>"` Pokud chcete omezit, která pole se zobrazí v odpovědi.

### <a name="formulate-and-send-the-reset-request"></a>Formulace a odeslání žádosti o resetování

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

Klíče dokumentu zadané v požadavku jsou hodnoty z indexu hledání, které se mohou lišit od odpovídajících polí ve zdroji dat. Pokud si nejste jisti hodnotou klíče, [odešlete dotaz](search-query-create.md) , který vrátí hodnotu. Můžete použít `select` k vrácení pouze pole klíč dokumentu.

Pro objekty blob, které jsou analyzovány do více dokumentů hledání (například pokud jste jako režim analýzy použili [jsonLines nebo jsonArrays](search-howto-index-json-blobs.md)nebo [delimitedText](search-howto-index-csv-blobs.md)), klíč dokumentu generuje indexer a pro vás může být neznámý. V této situaci bude dotaz na klíč dokumentu instrumentovat při poskytování správné hodnoty.

Vícenásobné volání rozhraní API s různými klíči připojí nové klíče k seznamu resetování klíčů dokumentů. Volání rozhraní API s **`overwrite`** parametrem nastaveným na hodnotu true přepíše aktuální seznam klíčů dokumentů, které mají být obnoveny pomocí datové části požadavku:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Kontrolovat stav resetování

Chcete-li zjistit stav resetování a zjistit, které klíče dokumentů jsou zařazeny do fronty ke zpracování, použijte funkci [získat stav indexeru](/rest/api/searchservice/get-indexer-status) **`api-version=06-30-2020-Preview`** . Rozhraní API pro verzi Preview vrátí **`currentState`** oddíl, který najdete na konci odpovědi na stav získat indexer.

"Mode" bude **`indexingAllDocs`** pro dovednosti obnovení (protože potenciálně ovlivněny všechny dokumenty) pro pole, která jsou vyplněna prostřednictvím rozšíření AI.

V případě obnovení dokumentů je režim nastaven na hodnotu **`indexingResetDocs`** . Indexer tento stav zachová, dokud se nezpracují všechny klíče dokumentu, které se ve volání resetování dokumentů nezadaly, a během operace se nespustí žádné další úlohy indexeru. Hledání všech dokumentů v seznamu klíčů dokumentů vyžaduje, aby byl každý dokument vyhledán a spárován s klíčem, a pokud je datová sada velká, může to chvíli trvat. Pokud kontejner objektů BLOB obsahuje stovky objektů BLOB a dokumenty, které chcete resetovat, jsou na konci, indexer nenalezne vyhovující objekty blob, dokud nebudou nejdřív zkontrolováni všichni ostatní.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Po novém zpracování dokumentů indexer vrátí do **`indexingAllDocs`** režimu a při příštím spuštění zpracuje všechny nové nebo aktualizované dokumenty.

## <a name="next-steps"></a>Další kroky

Resetování rozhraní API slouží k informování rozsahu dalšího spuštění indexeru. Pro skutečné zpracování budete muset vyvolat spuštění indexeru na vyžádání nebo to, aby naplánovaná úloha mohla dokončit práci. Po dokončení běhu se indexer vrátí do normálního zpracování, ať už se jedná o plán nebo zpracování na vyžádání.

Po resetování a opětovném spuštění úloh indexeru můžete monitorovat stav z vyhledávací služby nebo získat podrobné informace prostřednictvím diagnostického protokolování.

+ [Operace indexeru (REST)](/rest/api/searchservice/indexer-operations)
+ [Sledovat stav indexeru hledání](search-howto-monitor-indexers.md)
+ [Shromažďování a analýza dat protokolu](search-monitor-logs.md)
+ [Naplánování indexeru](search-howto-schedule-indexers.md)