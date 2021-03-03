---
title: Konfigurovat algoritmus podobnosti řazení
titleSuffix: Azure Cognitive Search
description: Postup nastavení algoritmu podobnosti pro vyzkoušení nového algoritmu podobnosti pro řazení
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677779"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>Konfigurace algoritmů hodnocení v Azure Kognitivní hledání

Azure Kognitivní hledání podporuje dva algoritmy řazení podle podobnosti:

+ *Klasický algoritmus podobnosti* , který používají všechny služby vyhledávání až do 15. července 2020.
+ Implementace *Okapi BM25* algoritmu, který se používá ve všech vyhledávacích službách vytvořených po 15. červenci.

Hodnocení BM25 je nové výchozí nastavení, protože má za následek vytváření vyhledávacích výsledků, které jsou lépe zarovnané na očekávání uživatelů. Umožňuje taky možnosti konfigurace pro optimalizaci výsledků na základě faktorů, jako je například velikost dokumentu. Pro nové služby vytvořené po 15. červenci 2020 se BM25 používá automaticky a je jediným algoritmem podobnosti. Pokud se pokusíte nastavit podobnost na ClassicSimilarity na nové službě, vrátí se chyba HTTP 400, protože tento algoritmus není službou podporován.

Pro starší služby vytvořené před 15. července 2020 zůstane u klasické podobnosti výchozí algoritmus. Starší služby mohou nastavovat vlastnosti indexu vyhledávání pro vyvolání BM25, jak je vysvětleno níže. Pokud přecházíte z klasického na BM25, můžete očekávat, že se vám zobrazí několik rozdílů, jak jsou seřazené výsledky hledání.

> [!NOTE]
> Sémantické vyhledávání je dodatečný algoritmus pro nové sémantické přeřazení, který zúží mezeru mezi očekáváními a výsledky ještě více. Na rozdíl od ostatních algoritmů se jedná o funkci doplňku, která se opakuje v existující sadě výsledků. Chcete-li použít algoritmus sémantického hledání ve verzi Preview, je nutné vytvořit novou službu a je třeba zadat [sémantický typ dotazu](semantic-how-to-query-request.md). Další informace najdete v tématu [Přehled sémantického hledání](semantic-search-overview.md).

## <a name="create-a-search-index-for-bm25-scoring"></a>Vytvoření indexu vyhledávání pro bodování BM25

Pokud používáte vyhledávací službu, která byla vytvořena před 15. července 2020, můžete nastavit vlastnost podobnosti na hodnotu BM25Similarity nebo ClassicSimilarity v definici indexu. Pokud je vlastnost podobnosti vynechána nebo je nastavena na hodnotu null, index používá klasický algoritmus.

Algoritmus podobnosti lze nastavit pouze v době vytváření indexu. Jakmile je však index vytvořen pomocí BM25, můžete aktualizovat existující index a nastavit nebo upravit parametry BM25.

| Klientská knihovna | Vlastnost podobnosti |
|----------------|---------------------|
| .NET  | [SearchIndex. podobnost](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex. podobnost](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [vlastnost podobnosti na SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>Příklad REST

Můžete také použít [REST API](/rest/api/searchservice/create-index), jak ukazuje následující příklad:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="bm25-similarity-parameters"></a>BM25 – parametry podobnosti

BM25 podobnost přináší dva uživatelsky přizpůsobitelné parametry pro řízení počítaného skóre relevance. Můžete nastavit parametry BM25 během vytváření indexu nebo jako aktualizaci indexu, pokud byl během vytváření indexu zadán algoritmus BM25.

| Vlastnost | Typ | Popis |
|----------|------|-------------|
| K1 | číslo | Určuje funkci škálování mezi termínem každého odpovídajícího termínu, který odpovídá konečnému skóre relevance páru dokumentů a dotazů. Hodnoty jsou obvykle 0,0 až 3,0 a 1,2 jako výchozí. </br></br>Hodnota 0,0 představuje "binární model", kde je příspěvek jednoho shodného termínu stejný pro všechny odpovídající dokumenty, a to bez ohledu na to, kolikrát se termín v textu zobrazuje, zatímco větší hodnota K1 umožňuje, aby se skóre pokračovalo v navýšení, protože v dokumentu je nalezeno více instancí stejného termínu. </br></br>Použití vyšší hodnoty K1 může být důležité v případech, kdy očekáváme, že bude součástí vyhledávacího dotazu více podmínek. V těchto případech můžeme chtít upřednostnit dokumenty, které odpovídají mnoha různým vyhledávaným dotazům přes dokumenty, které odpovídají jenom jednomu jednomu, několikrát. Například při dotazování indexu pro dokumenty, které obsahují termíny "Apollo Spaceflight", můžeme chtít snížit skóre článku o řeckém Mythology, který obsahuje výraz "Apollo", a to v porovnání s jiným článkem, který explicitně zmiňuje "Spaceflight" a "Apollo", a to pouze Spaceflight. |
| b | číslo | Určuje, jak délka dokumentu ovlivňuje skóre relevance. Hodnoty jsou mezi 0 a 1 a výchozím nastavením 0,75. </br></br>Hodnota 0,0 znamená, že délka dokumentu nebude mít vliv na skóre, zatímco hodnota 1,0 znamená, že dopad této frekvence v hodnocení relevance bude normalizována o délku dokumentu. </br></br>Normalizace četnosti dokumentů podle délky dokumentu je užitečná v případech, kdy chceme postihnout dokumenty na delší dobu. V některých případech je pravděpodobnější, že delší dokumenty (například kompletní nové) obsahují mnoho nepodstatných podmínek v porovnání s mnohem kratšími dokumenty. |

### <a name="setting-k1-and-b-parameters"></a>Nastavení parametrů K1 a b

Chcete-li nastavit nebo upravit hodnoty b nebo K1, přidejte je do objektu BM25 podobnosti. Nastavení nebo změna těchto hodnot v existujícím indexu převezmou index v režimu offline nejméně několik sekund, což způsobí, že aktivní indexování a požadavky na dotazy budou neúspěšné. V důsledku toho byste měli nastavit parametr "allowIndexDowntime = true" žádosti o aktualizaci:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Viz také  

+ [Odkaz na REST API](/rest/api/searchservice/)
+ [Přidání profilů vyhodnocování do indexu](index-add-scoring-profiles.md)
+ [Vytvoření rozhraní API pro index](/rest/api/searchservice/create-index)
+ [Sada Azure Kognitivní hledání .NET SDK](/dotnet/api/overview/azure/search)