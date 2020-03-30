---
title: Přizpůsobení modelu osoby pomocí rozhraní API pro indexování videa
titleSuffix: Azure Media Services
description: Přečtěte si, jak přizpůsobit model osoby pomocí rozhraní API videoindexeru.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127894"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Přizpůsobení modelu osoby pomocí rozhraní API videoindexeru

Video Indexer podporuje rozpoznávání obličeje a rozpoznávání celebrit pro video obsah. Funkce rozpoznávání celebrit pokrývá asi milion tváří založených na běžně požadovaném zdroji dat, jako je IMDB, Wikipedia a top LinkedIn influencers. Tváře, které nejsou rozpoznány funkcí rozpoznávání celebrit, jsou detekovány, ale zůstanou nepojmenované. Po nahrání videa do videoindexeru a získání výsledků zpět se můžete vrátit zpět a pojmenovat tváře, které nebyly rozpoznány. Jakmile obličej označíte jménem, obličej a jméno se přidají do modelu osoby vašeho účtu. Video Indexer pak rozpozná tuto tvář ve vašich budoucích videích a minulých videích.

Rozhraní API videoindexeru můžete použít k úpravám tváří, které byly zjištěny ve videu, jak je popsáno v tomto tématu. Můžete také použít video Indexer webové stránky, jak je popsáno v [Přizpůsobit osoba modelu pomocí webu Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Správa více modelů osob

Video Indexer podporuje více modelů osob na účet. Tato funkce je v současné době k dispozici pouze prostřednictvím rozhraní API video indexeru.

Pokud váš účet vyhovuje různým scénářům použití, můžete vytvořit více modelů osob na účet. Pokud například váš obsah souvisí se sportem, můžete pro každý sport (fotbal, basketbal, fotbal a tak dále) vytvořit samostatný model osoby.

Po vytvoření modelu jej můžete použít poskytnutím ID modelu konkrétního modelu osoby při nahrávání/indexování nebo přeindexování videa. Školení nové tváře pro video aktualizuje konkrétní vlastní model, ke kterému bylo video přidruženo.

Každý účet má limit 50 osob modelů. Pokud nepotřebujete podporu modelu více osob, nepřiřazujte id modelu osoby k videu při nahrávání nebo indexování nebo přeindexování. V takovém případě používá video indexer ve vašem účtu výchozí vlastní model osoby.

## <a name="create-a-new-person-model"></a>Vytvoření nového modelu osoby

Chcete-li vytvořit nový model osoby v zadaném účtu, použijte rozhraní API [modelu vytvořit osobu.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)

Odpověď poskytuje název a generované ID modelu osoby modelu, který jste právě vytvořili podle formátu níže uvedeného příkladu.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Potom použijete **id** hodnotu pro **personModelId** parametr [při nahrávání videa indexovat](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo [reindexing video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Odstranění modelu osoby

Chcete-li odstranit vlastní model osoby ze zadaného účtu, použijte rozhraní API [modelu odstranění osoby.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)

Po úspěšném odstranění modelu osoba, index aktuální videa, které byly pomocí odstraněného modelu zůstane beze změny, dokud je přeindexovat. Po přeindexování nebudou tváře, které byly pojmenovány v odstraněném modelu, rozpoznány indexerem videa v aktuálních videích, která byla indexována pomocí tohoto modelu, ale tváře budou stále rozpoznány. Vaše aktuální videa, která byla indexována pomocí odstraněného modelu, budou nyní používat výchozí model osoby vašeho účtu. Pokud jsou tváře z odstraněného modelu také pojmenovány ve výchozím modelu vašeho účtu, budou tyto tváře ve videích nadále rozpoznány.

Neexistuje žádný vrácený obsah při úspěšném odstranění modelu osoby.

## <a name="get-all-person-models"></a>Získejte všechny modely osob

Chcete-li získat všechny modely osoba mise v zadaném účtu, použijte [získat osoby modelu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) ROZHRANÍ API.

Odpověď obsahuje seznam všech modelů osob ve vašem účtu (včetně výchozího modelu osoby v zadaném účtu) a každého z jejich jmen a ID podle formátu následujícího příkladu.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Můžete zvolit model, který chcete použít pro `id` video pomocí hodnoty modelu `personModelId` Osoba pro parametr při [nahrávání videa indexovat](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo [reindexovat video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aktualizace plochy

Tento příkaz umožňuje aktualizovat tvář ve videu názvem pomocí ID videa a ID obličeje. Tato akce pak aktualizuje model osoby, ke kterému bylo video přidruženo při nahrávání nebo indexování nebo přeindexování. Pokud nebyl přiřazen žádný model osoby, aktualizuje výchozí model osoby účtu.

Systém pak rozpozná výskyty stejné tváře v ostatních aktuálních videích, která sdílejí stejný model osoby. Rozpoznání obličeje v ostatních aktuálních videích může nějakou dobu trvat, než se projeví, protože se jedná o dávkový proces.

Můžete aktualizovat tvář, kterou video indexer rozpoznal jako celebritu s novým názvem. Nový název, který uvedete, bude mít přednost před vestavěným rozpoznáváním celebrit.

Chcete-li aktualizovat tvář, použijte [aktualizaci rozhraní API pro obličej videa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)

Názvy jsou jedinečné pro modely osob, takže pokud dáte dvěma různým tvářím ve stejném modelu osoba stejnou `name` hodnotu parametru, Video Indexer zobrazí tváře jako stejnou osobu a sblíží je po přeindexování videa.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu osoby pomocí webu Video Indexer](customize-person-model-with-website.md)
