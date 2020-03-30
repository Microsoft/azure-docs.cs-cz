---
title: Definování filtrů ve službě Azure Media Services
description: Toto téma popisuje, jak vytvořit filtry, aby je váš klient mohl použít k vysílání datového proudu určitých částí datového proudu. Media Services vytváří dynamické manifesty k dosažení tohoto selektivního streamování.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251463"
---
# <a name="filters"></a>Filtry

Při doručování obsahu zákazníkům (události živého streamování nebo video na vyžádání) může klient potřebovat větší flexibilitu, než je popsáno ve výchozím souboru manifestu datového zdroje. Azure Media Services nabízí [dynamické manifesty](filters-dynamic-manifest-overview.md) založené na předdefinovaných filtrech. 

Filtry jsou pravidla na straně serveru, která vašim zákazníkům umožňují dělat například: 

- Přehrání pouze části videa (namísto přehrávání celého videa). Například:
  - Zmenšete manifest tak, aby zobrazoval dílčí klip živé události ("filtrování dílčích klipů") nebo
  - Ořízněte začátek videa ("oříznutí videa").
- Doručujte pouze zadané interpretace nebo zadané jazykové skladby, které jsou podporovány zařízením, které se používá k přehrávání obsahu ("filtrování interpretací"). 
- Upravte prezentační okno (DVR) tak, aby poskytovalo omezenou délku okna DVR v přehrávači ("nastavení okna prezentace").

Mediální služby umožňují vytvářet **filtry účtů** a **filtry datových zdrojů** pro váš obsah. Kromě toho můžete předem vytvořené filtry přidružit k **lokátoru streamování**.

## <a name="defining-filters"></a>Definování filtrů

Existují dva typy filtrů: 

* [Filtry účtů](https://docs.microsoft.com/rest/api/media/accountfilters) (globální) – lze použít na libovolný prostředek v účtu Azure Media Services, mají životnost účtu.
* [Filtry majetku](https://docs.microsoft.com/rest/api/media/assetfilters) (místní) - lze použít pouze na datový zdroj, ke kterému byl filtr přidružen při vytvoření, mají životnost majetku. 

**Typy filtrů účtů** a **filtrů majetku** mají přesně stejné vlastnosti pro definování nebo popis filtru. S výjimkou vytváření **filtru majetku**je třeba zadat název majetku, ke kterému chcete filtr přidružit.

V závislosti na scénáři se rozhodnete, jaký typ filtru je vhodnější (filtr majetku nebo filtr účtu). Filtry účtů jsou vhodné pro profily zařízení (filtrování interpretací), kde lze filtry datových zdrojů použít k oříznutí konkrétního datového zdroje.

K popisu filtrů se používají následující vlastnosti. 

|Name (Název)|Popis|
|---|---|
|firstQuality|První kvalitní přenosová rychlost filtru.|
|prezentaceTimeRange|Časový rozsah prezentace. Tato vlastnost se používá pro filtrování počátečních a koncových bodů manifestu, délky okna prezentace a pozice živého zahájení. <br/>Další informace naleznete [v tématu PresentationTimeRange](#presentationtimerange).|
|Skladby|Sleduje podmínky výběru. Další informace naleznete v [tématu](#tracks)|

### <a name="presentationtimerange"></a>prezentaceTimeRange

Tuto vlastnost použijte s **filtry datových zdrojů**. Nedoporučuje se nastavit vlastnost pomocí **filtrů účtů**.

|Name (Název)|Popis|
|---|---|
|**endTimestamp**|Platí pro video na vyžádání (VoD).<br/>Pro živé streamování prezentace je tiše ignorována a použita při ukončení prezentace a datový proud se stane VoD.<br/>Toto je dlouhá hodnota, která představuje absolutní koncový bod prezentace zaokrouhlený na nejbližší další začátek gop. Jednotka je časová osa, takže endTimestamp 1800000000 by bylo po dobu 3 minut.<br/>Pomocí startTimestamp a endTimestamp ořízněte fragmenty, které budou v seznamu stop (manifestu).<br/>Například startTimestamp=40000000 a endTimestamp=100000000 pomocí výchozí časové osy vygeneruje seznam stop, který obsahuje fragmenty od 4 sekund do 10 sekund prezentace VoD. Pokud fragment rozkročí hranice, celý fragment bude zahrnuta do manifestu.|
|**forceEndTimestamp**|Platí pouze pro živé vysílání.<br/>Označuje, zda musí být přítomna vlastnost endTimestamp. Pokud true, endTimestamp musí být zadán nebo je vrácen chybný kód požadavku.<br/>Povolené hodnoty: false, true.|
|**liveBackoffDuration**|Platí pouze pro živé vysílání.<br/> Tato hodnota definuje nejnovější živé pozice, které může klient hledat.<br/>Pomocí této vlastnosti můžete zpozdit pozici živého přehrávání a vytvořit vyrovnávací paměť na straně serveru pro hráče.<br/>Jednotka pro tuto vlastnost je časová osa (viz níže).<br/>Maximální doba trvání živého vypnutí je 300 sekund (3000000000).<br/>Například hodnota 2000000000znamená, že nejnovější dostupný obsah je zpožděn o 20 sekund od skutečného živého okraje.|
|**presentationWindowDuration**|Platí pouze pro živé vysílání.<br/>Použití presentationWindowDuration použít posuvné okno fragmentů zahrnout do seznamu stop.<br/>Jednotka pro tuto vlastnost je časová osa (viz níže).<br/>Například nastavte presentationWindowDuration=1200000000 použít dvouminutové posuvné okno. Média do 2 minut od živého okraje budou zahrnuta do seznamu stop. Pokud fragment rozkročí hranice, celý fragment bude zahrnuta do seznamu stop. Minimální doba trvání prezentačního okna je 60 sekund.|
|**startTimerazítko**|Platí pro video na vyžádání (VoD) nebo živé vysílání.<br/>Toto je dlouhá hodnota, která představuje absolutní počáteční bod datového proudu. Hodnota se zaokrouhluje na nejbližší další začátek gop. Jednotka je časová osa, takže startTimestamp 150000000 by bylo po dobu 15 sekund.<br/>Pomocí startTimestamp a endTimestampp ořízněte fragmenty, které budou v seznamu stop (manifestu).<br/>Například startTimestamp=40000000 a endTimestamp=100000000 pomocí výchozí časové osy vygeneruje seznam stop, který obsahuje fragmenty od 4 sekund do 10 sekund prezentace VoD. Pokud fragment rozkročí hranice, celý fragment bude zahrnuta do manifestu.|
|**Časové osy**|Platí pro všechna časová razítka a doby trvání v časovém rozsahu prezentace, určené jako počet přírůstků za jednu sekundu.<br/>Výchozí hodnota je 100000000 - deset milionů přírůstků za jednu sekundu, kde každý přírůstek bude 100 nanosekund.<br/>Například pokud chcete nastavit startTimestamp na 30 sekund, použijete hodnotu 300000000 při použití výchozí časové osy.|

### <a name="tracks"></a>Skladby

Zadáte seznam podmínek vlastností stopy filtru (FilterTrackPropertyConditions), na základě kterého by měly být stopy datového proudu (živé streamování nebo video na vyžádání) zahrnuty do dynamicky vytvořeného manifestu. Filtry jsou kombinovány pomocí logické operace **AND** a **OR.**

Podmínky vlastností sledování filtru popisují typy stop, hodnoty (popsané v následující tabulce) a operace (Equal, NotEqual). 

|Name (Název)|Popis|
|---|---|
|**Datový tok**|Pro filtrování použijte přenosovou rychlost stopy.<br/><br/>Doporučená hodnota je rozsah přenosových rychlostí v bitech za sekundu. Například "0-2427000".<br/><br/>Poznámka: Zatímco můžete použít určitou hodnotu datového toku, například 250000 (bitů za sekundu), tento přístup se nedoporučuje, protože přesné přenosové rychlosti mohou kolísat mezi aktivy.|
|**Fourcc**|Pro filtrování použijte hodnotu FourCC stopy.<br/><br/>Hodnota je prvním prvkem formátu kodeků, jak je uvedeno v [RFC 6381](https://tools.ietf.org/html/rfc6381). V současné době jsou podporovány následující kodeky: <br/>Pro video: "avc1", "hev1", "hvc1"<br/>Pro zvuk: "mp4a", "ec-3"<br/><br/>Chcete-li určit FourCC hodnoty pro stopy v Asset, získat a prozkoumat soubor manifestu.|
|**Jazyk**|Pro filtrování použijte jazyk stopy.<br/><br/>Hodnota je značka jazyka, který chcete zahrnout, jak je uvedeno v rfc 5646. Například "en".|
|**Název**|Pro filtrování použijte název stopy.|
|**Typ**|Pro filtrování použijte typ stopy.<br/><br/>Jsou povoleny následující hodnoty: "video", "audio" nebo "text".|

### <a name="example"></a>Příklad

Následující příklad definuje filtr živého streamování: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Přisuzování filtrů pomocí lokátoru streamování

V lokátoru [streamování](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)můžete zadat seznam [datových zdrojů nebo filtrů účtů](filters-concept.md) . [Dynamická služba packager](dynamic-packaging-overview.md) použije tento seznam filtrů spolu s filtry, které klient určí v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v adrese URL + filtrech, které zadáte v lokátoru streamování. 

Podívejte se na následující příklady:

* [Přidružení filtrů k lokátoru streamování - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Přidružení filtrů k lokátoru streamování – CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aktualizace filtrů
 
**Lokátory streamování** nelze aktualizovat, zatímco filtry lze aktualizovat. 

Nedoporučuje se aktualizovat definici filtrů přidružených k aktivně publikovanému **lokátoru streamování**, zejména pokud je povolena síť CDN. Servery datových proudů a sítě CDN mohou mít interní mezipaměti, které mohou vést k vrácení zastaralých dat uložených v mezipaměti. 

Pokud je třeba změnit definici filtru, zvažte vytvoření nového filtru a jeho přidání do adresy URL **lokátoru streamování** nebo publikování nového **lokátoru streamování,** který odkazuje na filtr přímo.

## <a name="next-steps"></a>Další kroky

Následující články ukazují, jak programově vytvářet filtry.  

- [Vytváření filtrů pomocí rest API](filters-dynamic-manifest-rest-howto.md)
- [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Vytváření filtrů pomocí cli](filters-dynamic-manifest-cli-howto.md)

