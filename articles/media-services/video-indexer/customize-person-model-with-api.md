---
title: Přizpůsobení modelu osoby pomocí Video Indexer API
titleSuffix: Azure Media Services
description: Naučte se, jak přizpůsobit model osoby pomocí rozhraní Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047016"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Přizpůsobení modelu osoby pomocí rozhraní Video Indexer API

Video Indexer podporuje rozpoznávání tváře a rozpoznávání celebrit pro obsah videa. Funkce rozpoznávání celebrit se zabývá přibližně 1 000 000 obličejemi na základě běžně vyžádaného zdroje dat, jako jsou IMDB, Wikipedii a nejvyšší vlivy na LinkedIn. Budou zjištěny plošky, které nejsou rozpoznány funkcí rozpoznávání celebrit, ale zůstanou nepojmenované. Po nahrání videa do Video Indexer a získání výsledků zpět můžete přejít zpátky a pojmenovat nerozpoznané obličeje. Po označení obličeje názvem se vytvoří obličej a název, který se přidá do modelu osoby vašeho účtu. Video Indexer pak tuto plošku rozpozná v budoucích videích a minulých videích.

Pomocí rozhraní Video Indexer API můžete upravit plošky zjištěné ve videu, jak je popsáno v tomto tématu. Můžete také použít Video Indexer web, jak je popsáno v tématu [Přizpůsobení modelu osoby pomocí webu video indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Správa modelů s více osobami

Video Indexer podporuje různé modely osob na účet. Tato funkce je aktuálně dostupná jenom prostřednictvím rozhraní API pro Video Indexer.

Pokud vaše účty využívají různé scénáře použití, můžete chtít vytvořit pro každý účet více modelů osob. Pokud je váš obsah například v souvislosti s sportem, můžete vytvořit samostatný pracovní model pro každý sport (fotbal, basketbalový, fotbal atd.).

Po vytvoření modelu je můžete použít k poskytnutí ID modelu konkrétního modelu osoby při nahrávání/indexování nebo přeindexování videa. Školení nové plochy pro video aktualizuje konkrétní vlastní model, ke kterému byl video přidruženo.

Každý účet má omezení 50 modelů osob. Pokud nepotřebujete podporu modelu více osob, při nahrávání/indexování nebo přeindexování nepřiřazujte své video ID modelu osoby. V takovém případě používá Video Indexer ve vašem účtu výchozí model vlastní osoby.

## <a name="create-a-new-person-model"></a>Vytvoření nového modelu osoby

Pokud chcete v zadaném účtu vytvořit nový model osoby, použijte rozhraní API pro [Vytvoření modelu person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) .

Odpověď poskytuje název a generovaný identifikátor ID modelu osoby, který jste právě vytvořili, podle formátu níže uvedeného příkladu.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Pak použijete hodnotu **ID** parametru **personModelId** při [nahrávání videa k indexování](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) nebo [přeindexování videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Odstranění modelu osoby

Pokud chcete odstranit vlastní pracovní model ze zadaného účtu, použijte rozhraní API pro [odstranění modelu person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) .

Po úspěšném odstranění modelu osoby se index vašich aktuálních videí, která používala odstraněný model, zůstane beze změny, dokud je znovu neindexujte. Po přeindexování nebude Video Indexer ve vašich aktuálních videích, které byly indexovány pomocí tohoto modelu, rozpoznány plošky, které byly pojmenovány v odstraněném modelu, ale plošky se budou i nadále detekovat. Vaše aktuální videa, která byla indexována pomocí odstraněného modelu, teď budou používat výchozí pracovní model vašeho účtu. Pokud se plošky z odstraněného modelu také pojmenují ve výchozím modelu vašeho účtu, budou tyto plošky i nadále rozpoznány ve videích.

Po úspěšném odstranění modelu osoby se nevrátí žádný obsah.

## <a name="get-all-person-models"></a>Získat modely všech osob

Pokud chcete získat všechny modely osob v zadaném účtu, použijte rozhraní API pro [získání modelu person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) .

Odpověď obsahuje seznam všech obchodních modelů v účtu (včetně výchozího modelu osoby v zadaném účtu) a každé z jejich názvů a ID podle formátu níže uvedeného příkladu.

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

Můžete zvolit model, který chcete použít pro video, a to pomocí `id` hodnoty model osoby pro `personModelId` parametr při [nahrávání videa do indexu nebo při](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) [přeindexování videa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aktualizovat obličej

Tento příkaz umožňuje aktualizovat obličej ve videu s názvem s použitím ID videa a ID obličeje. Tato akce pak aktualizuje model osoby, ke kterému byl video přidruženo při nahrávání, indexování nebo přeindexování. Pokud nebyl přiřazen žádný model osoby, aktualizuje se výchozí model osoby účtu.

Systém pak rozpoznává výskyty stejné plošky ve vašich dalších současných videích, která sdílejí stejný model Person. Rozpoznávání obličeje v ostatních aktuálních videích může nějakou dobu trvat, protože se jedná o dávkový proces.

Můžete aktualizovat obličej, který Video Indexer rozpoznat jako celebrit s novým názvem. Nový název, který udělíte, bude mít přednost před integrovaným rozpoznáváním celebrit.

K aktualizaci obličeje použijte rozhraní API pro [video vzhled](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) .

Názvy jsou pro pracovní modely jedinečné, takže pokud každému stejnému modelu udělíte dvě různé plošky, bude stejná `name` hodnota parametru video indexer zobrazení plošek jako stejná osoba a po přeindexaci videa je konvergovat.

## <a name="next-steps"></a>Další kroky

[Přizpůsobení modelu osoby pomocí Video Indexer webu](customize-person-model-with-website.md)
