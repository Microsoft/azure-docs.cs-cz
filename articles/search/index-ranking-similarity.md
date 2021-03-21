---
title: Konfigurace algoritmu podobnosti
titleSuffix: Azure Cognitive Search
description: Naučte se, jak povolit BM25 u starších vyhledávacích služeb a jak se dají upravit parametry BM25, aby lépe vyhovovaly obsahu vašich indexů.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232830"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Konfigurace algoritmu řazení podobnosti v Azure Kognitivní hledání

Azure Kognitivní hledání podporuje dva algoritmy řazení podle podobnosti:

+ *Klasický algoritmus podobnosti* , který používají všechny služby vyhledávání až do 15. července 2020.
+ Implementace *Okapi BM25* algoritmu, který se používá ve všech vyhledávacích službách vytvořených po 15. červenci.

Hodnocení BM25 je nové výchozí nastavení, protože má za následek vytváření vyhledávacích výsledků, které jsou lépe zarovnané na očekávání uživatelů. Obsahuje [parametry](#set-bm25-parameters) pro ladění výsledků na základě faktorů, jako je například velikost dokumentu. 

Pro nové služby vytvořené po 15. červenci 2020 se BM25 používá automaticky a je jediným algoritmem podobnosti. Pokud se pokusíte nastavit podobnost na ClassicSimilarity na nové službě, vrátí se chyba HTTP 400, protože tento algoritmus není službou podporován.

Pro starší služby vytvořené před 15. července 2020 zůstane u klasické podobnosti výchozí algoritmus. Starší služby mohou upgradovat na BM25 podle indexu, jak je vysvětleno níže. Pokud přecházíte z klasického na BM25, můžete očekávat, že se vám zobrazí několik rozdílů, jak jsou seřazené výsledky hledání.

> [!NOTE]
> Sémantické hodnocení, které je v současné době ve verzi Preview pro standardní služby ve vybraných oblastech, je dalším krokem před tím, než se vytvářejí relevantnější výsledky. Na rozdíl od ostatních algoritmů se jedná o funkci doplňku, která se opakuje v existující sadě výsledků. Další informace najdete v tématu [Přehled sémantického vyhledávání](semantic-search-overview.md) a [sémantické hodnocení](semantic-ranking.md).

## <a name="enable-bm25-scoring-on-older-services"></a>Povolit bodování BM25 pro starší služby

Pokud používáte vyhledávací službu, která byla vytvořena před 15. července 2020, můžete povolit BM25 nastavením vlastnosti podobnosti na nových indexech. Vlastnost se zveřejňuje jenom na nových indexech, takže pokud chcete BM25 na stávajícím indexu, musíte vyřadit a [znovu sestavit index](search-howto-reindex.md) s novou vlastností podobnosti nastavenou na Microsoft. Azure. Search. BM25Similarity.

Jakmile index existuje s vlastností podobnosti, můžete přepínat mezi BM25Similarity nebo ClassicSimilarity. 

Následující odkazy popisují vlastnost podobnosti v sadách Azure SDK. 

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

## <a name="set-bm25-parameters"></a>Nastavení parametrů BM25

BM25 podobnost přináší dva uživatelsky přizpůsobitelné parametry pro řízení počítaného skóre relevance. Můžete nastavit parametry BM25 během vytváření indexu nebo jako aktualizaci indexu, pokud byl během vytváření indexu zadán algoritmus BM25.

| Vlastnost | Typ | Description |
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