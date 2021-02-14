---
title: Řazení algoritmu podobnosti
titleSuffix: Azure Cognitive Search
description: Postup nastavení algoritmu podobnosti pro vyzkoušení nového algoritmu podobnosti pro řazení
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: e2caa09d41abb1842100ed8259e82ec411390ccb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520625"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmus hodnocení v Azure Kognitivní hledání

> [!IMPORTANT]
> Od 15. července 2020 nově vytvořené vyhledávací služby použijí funkci hodnocení BM25 automaticky, která se ve většině případů osvědčila pro poskytování vyhledávacích výsledků, které se lépe rovnají se očekáváním uživatelů, než je aktuální výchozí hodnocení. Kromě vynikajícího hodnocení umožňuje BM25 také možnosti konfigurace pro optimalizaci výsledků na základě faktorů, jako je například velikost dokumentu.  
>
> V této změně se pravděpodobně v pořadí výsledků hledání zobrazí mírné změny. Pro ty, kteří chtějí otestovat dopad této změny, je BM25 algoritmus dostupný v rozhraní API verze 2019-05-06-Preview a v 2020-06-30.  

Tento článek popisuje, jak můžete použít nový algoritmus řazení BM25 pro existující vyhledávací služby pro vytváření nových indexů a dotazování pomocí rozhraní API pro verzi Preview.

V rámci služby Azure Kognitivní hledání je potřeba přijmout oficiální implementaci Lucene BM25 algoritmu *BM25Similarity*, který nahradí dříve použitou implementaci *ClassicSimilarity* . Podobně jako u staršího algoritmu ClassicSimilarity je BM25Similarity funkce načítání TF-IDF, která jako proměnné používá frekvenci termínů (TF) a inverzní hustota dokumentů (IDF) jako proměnné pro výpočet skóre relevance pro jednotlivé páry dokumentů, které se pak použijí pro hodnocení. 

V koncepční podobě se starším algoritmem podobnosti BM25 převezme svůj kořen v pravděpodobnostní načítání informací, aby se na něm vylepšil. BM25 nabízí také pokročilé možnosti vlastního nastavení, jako je například umožnění, aby se uživatel rozhodl, jak se skóre relevance škáluje s termínem četnosti shodných podmínek.

## <a name="how-to-test-bm25-today"></a>Postup testování BM25 ještě dnes

Při vytváření nového indexu můžete nastavit vlastnost **podobnosti** a zadat algoritmus. Můžete použít `api-version=2019-05-06-Preview` , jak je znázorněno níže, nebo `api-version=2020-06-30` .

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

Vlastnost **podobnost** je užitečná v tomto přechodném období, pokud jsou oba algoritmy k dispozici, pouze u stávajících služeb. 

| Vlastnost | Popis |
|----------|-------------|
| podobnosti | Nepovinný parametr. Platné hodnoty zahrnují *"#Microsoft. Azure. Search. ClassicSimilarity"* nebo *"#Microsoft. Azure. Search. BM25Similarity"*. <br/> Vyžaduje `api-version=2019-05-06-Preview` nebo novější na vyhledávací službě vytvořenou před 15. července 2020. |

Pro nové služby vytvořené po 15. červenci 2020 se BM25 používá automaticky a je jediným algoritmem podobnosti. Pokud se pokusíte nastavit **podobnost** na `ClassicSimilarity` novou službu, bude vrácena chyba 400, protože tento algoritmus není podporován v nové službě.

Pro existující služby vytvořené před 15. července 2020 zůstane u klasické podobnosti výchozí algoritmus. Pokud je vlastnost **podobnosti** vynechána nebo je nastavena na hodnotu null, index používá klasický algoritmus. Pokud chcete použít nový algoritmus, budete muset nastavit **podobnost** , jak je popsáno výše.

## <a name="bm25-similarity-parameters"></a>BM25 – parametry podobnosti

BM25 podobnost přináší dva uživatelsky přizpůsobitelné parametry pro řízení počítaného skóre relevance.

### <a name="k1"></a>K1

Parametr *K1* řídí funkci škálování mezi pojmem frekvence každého odpovídajícího termínu a konečným skórem pro dvojici dokumentů a dotazů.

Hodnota nula představuje "binární model", kde je příspěvek jednoho shodného termínu stejný pro všechny odpovídající dokumenty, a to bez ohledu na to, kolikrát se termín v textu zobrazuje, zatímco větší hodnota K1 umožňuje, aby se skóre pokračovalo v navýšení, protože v dokumentu je nalezeno více instancí stejného termínu. Ve výchozím nastavení používá Azure Kognitivní hledání hodnotu 1,2 pro parametr K1. Použití vyšší hodnoty K1 může být důležité v případech, kdy očekáváme, že bude součástí vyhledávacího dotazu více podmínek. V těchto případech můžeme chtít upřednostnit dokumenty, které odpovídají mnoha různým vyhledávaným dotazům přes dokumenty, které odpovídají jenom jednomu jednomu, několikrát. Například při dotazování indexu pro dokumenty, které obsahují termíny "Apollo Spaceflight", můžeme chtít snížit skóre článku o řečtině Mythology, který obsahuje výraz "Apollo", a to v porovnání s jiným článkem, který výslovně zmiňuje "Spaceflight" i "Apollo", a to pouze Spaceflight. 
 
### <a name="b"></a>b

Parametr *b* řídí, jak délka dokumentu ovlivňuje skóre relevance.

Hodnota 0,0 znamená, že délka dokumentu nebude mít vliv na skóre, zatímco hodnota 1,0 znamená, že dopad této frekvence v hodnocení relevance bude normalizována o délku dokumentu. Výchozí hodnota používaná v Azure Kognitivní hledání pro parametr b je 0,75. Normalizace četnosti dokumentů podle délky dokumentu je užitečná v případech, kdy chceme postihnout dokumenty na delší dobu. V některých případech je pravděpodobnější, že delší dokumenty (například kompletní nové) obsahují mnoho nepodstatných podmínek v porovnání s mnohem kratšími dokumenty.

### <a name="setting-k1-and-b-parameters"></a>Nastavení parametrů K1 a b

Chcete-li přizpůsobit hodnoty b nebo K1, jednoduše je přidejte jako vlastnosti objektu podobnosti při použití BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Algoritmus podobnosti lze nastavit pouze v době vytváření indexu. To znamená, že používaný algoritmus podobnosti se nedá změnit pro existující indexy. Parametry *"b"* a *"K1"* lze upravit při aktualizaci existující definice indexu, která používá BM25. Když změníte tyto hodnoty v existujícím indexu, převezmou index v režimu offline aspoň několik sekund, což způsobí, že vaše požadavky na indexování a dotazy budou úspěšné. Z tohoto důvodu budete muset v řetězci dotazu žádosti o aktualizaci nastavit parametr "allowIndexDowntime = true":

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Viz také  

+ [Odkaz na REST API](/rest/api/searchservice/)
+ [Přidání profilů vyhodnocování do indexu](index-add-scoring-profiles.md)
+ [Vytvoření rozhraní API pro index](/rest/api/searchservice/create-index)
+ [Sada Azure Kognitivní hledání .NET SDK](/dotnet/api/overview/azure/search)