---
title: Strukturování sémantické odpovědi
titleSuffix: Azure Cognitive Search
description: Popisuje algoritmus sémantického hodnocení v Kognitivní hledání a způsob strukturování sémantických odpovědí a sémantických popisků ze sady výsledků dotazu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679412"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Sémantické hodnocení a reakce v Azure Kognitivní hledání

> [!IMPORTANT]
> Algoritmus sémantického hodnocení a sémantické odpovědi a popisky jsou ve verzi Public Preview dostupné jenom v rámci verze Preview REST API. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sémantické hodnocení zlepšuje přesnost výsledků hledání změnou pořadí hlavních shod pomocí modelu sémantického hodnocení vyškolený pro dotazy vyjádřené v přirozeném jazyce na rozdíl od klíčových slov.

Tento článek popisuje algoritmus sémantického hodnocení a způsob, jakým je tato sémantická odezva ve tvaru. Odpověď obsahuje popisky, v prostém textu i s nejzajímavější a odpovědi (volitelné).

+ Sémantické titulky jsou textové pasáže relevantní pro dotaz extrahovaný z výsledků hledání. Můžou přispět k sumarizaci výsledků, když jsou jednotlivá pole obsahu pro stránku výsledků moc velká. Popisy funkcí jsou sémantické světla, které uživatelům umožňují rychle skim výsledky dotazů a získat tak nejdůležitější dokumenty a tím zlepšit celkové uživatelské prostředí.

+ Sémantické odpovědi využívají modely strojového učení z Bingu a formulují odpovědi na dotazy, které vypadají jako otázky. Odpovědi jsou vybrány ze seznamu pasáží, které jsou relevantní pro dotaz, jak extrahováno z hlavních dokumentů v sadě výsledků dotazu. Odpovědi se vrátí jako nezávislá objekt nejvyšší úrovně v datové části odpovědi na dotaz, který můžete zvolit pro vykreslení na stránkách hledání, společně s výsledky hledání na straně.

## <a name="prerequisites"></a>Požadavky

+ Dotazy, které jsou formulovány pomocí sémantického typu dotazu. Další informace najdete v tématu [Vytvoření sémantického dotazu](semantic-how-to-query-request.md).

## <a name="understanding-a-semantic-response"></a>Princip sémantické odpovědi

Sémantická odezva obsahuje nové vlastnosti pro skóre, popisy a odpovědi. Sémantická odezva je sestavena ze standardní odpovědi pomocí prvních 50 výsledků vrácených [fulltextovým vyhledávacím modulem](search-lucene-query-architecture.md), který je pak znovu seřazen pomocí sémantického hodnocení. Pokud je zadáno více než 50, budou vráceny další výsledky, ale nebudou sémanticky přehodnoceny.

Stejně jako u všech dotazů se odpověď skládá ze všech polí označených jako načístelné nebo pouze těch polí uvedených v příkazu SELECT. Obsahuje také pole odpověď a titulky.

+ Pro každý sémantický výsledek je ve výchozím nastavení k dispozici jedna odpověď, která se zobrazí jako samostatné pole, které můžete vybrat pro vykreslení na stránce vyhledávání. Můžete zadat až pět. Formulace odpovědi je automatizovaná: probíhá čtení všech dokumentů v počátečních výsledcích, spuštění extrakce, následuje porozumění počítači a nakonec zvýšení úrovně přímé odpovědi na dotaz uživatele v poli Answer.

+ "Titulkem" je shrnutí obsahu dokumentu založeného na extrakci, vráceno v prostém textu nebo s nejzajímavější. Popisky jsou zahrnuty automaticky a nelze je potlačit. K určení, které řetězce by měly být zvýrazněny, se použijí světla pomocí porozumění strojovým čtením. Zvýrazní pozornost nejdůležitějších pasáží, abyste mohli rychle vyhledat stránku výsledků, abyste našli správný dokument.

Sémanticky přeřazená sada výsledků seřadí výsledky podle @search.rerankerScore hodnoty. Pokud přidáte klauzuli OrderBy, vrátí se chyba HTTP 400, protože tato klauzule není v sémantickém dotazu podporována.

## <a name="example"></a>Příklad

@search.rerankerScoreExistuje společně se standardem @search.score a slouží k přeřazení výsledků.

S ohledem na následující dotaz:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Můžete očekávat, že se zobrazí následující výsledek, což je zástupce sémantické odpovědi. Tyto výsledky zobrazují pouze první tři odpovědi, seřazené podle " @search.rerankerScore ". Všimněte si, jak je teď v Oceanside řazení. V rámci výchozího pořadí " @search.score " bude tento výsledek vrácen v druhém místě po ukončení stopy.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Další kroky

+ [Přehled sémantického hledání](semantic-search-overview.md)
+ [Algoritmus podobnosti](index-ranking-similarity.md)
+ [Vytvořit sémantický dotaz](semantic-how-to-query-request.md)
+ [Vytvoření základního dotazu](search-query-create.md)