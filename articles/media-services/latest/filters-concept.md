---
title: Definování filtrů v Azure Media Services
description: V tomto tématu se dozvíte, jak vytvořit filtry, aby je klient mohl používat ke streamování konkrétních oddílů datového proudu. Media Services vytvoří dynamické manifesty pro dosažení tohoto selektivního streamování.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: bb5561ced93c3f5a899c6e48fdab0f14e52914bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291544"
---
# <a name="filters"></a>Filtry

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Při doručování obsahu zákazníkům (živé události streamování nebo video na vyžádání) může váš klient potřebovat větší flexibilitu než popis, který je popsaný v souboru manifestu výchozího prostředku. Azure Media Services nabízí [dynamické manifesty](filters-dynamic-manifest-overview.md) založené na předem definovaných filtrech. 

Filtry jsou pravidla na straně serveru, která zákazníkům umožňují provádět například tyto akce: 

- Přehrát jenom část videa (místo přehrávání celého videa) Například:
  - Snižte manifest pro zobrazení dílčího klipu živé události ("filtrování dílčích klipů") nebo
  - Ořízne začátek videa ("ořezávání videa").
- Doručovat pouze zadané verze a/nebo zadané stopy jazyka podporované zařízením, které se používá k přehrání obsahu ("filtrování verzí"). 
- Upravte okno prezentace (DVR), aby se v přehrávači zajistila omezená délka okna DVR ("Úprava okna prezentace").

Media Services vám umožní vytvořit **filtry účtu** a **filtry assetů** pro váš obsah. Předem vytvořené filtry můžete navíc přidružit k **lokátoru streamování**.

## <a name="defining-filters"></a>Definování filtrů

Existují dva typy filtrů: 

* [Filtry účtu](/rest/api/media/accountfilters) (globální) – dá se použít pro všechny prostředky v účtu Azure Media Services, které mají dobu života účtu.
* [Filtry assetu](/rest/api/media/assetfilters) (místní) – lze použít pouze na prostředek, ke kterému byl filtr přidružen při vytváření, a má životnost assetu. 

**Filtry účtu** a typy **filtrů prostředků** mají přesně stejné vlastnosti pro definování/popis filtru. Kromě při vytváření **filtru assetů**je třeba zadat název assetu, ke kterému chcete filtr přidružit.

V závislosti na vašem scénáři se rozhodujete, jaký typ filtru je vhodnější (filtr prostředků nebo filtr účtu). Filtry účtu jsou vhodné pro profily zařízení (filtrování verzí), kde se dají filtry assetů použít k oříznutí konkrétního prostředku.

Pomocí následujících vlastností popíšete filtry. 

|Název|Popis|
|---|---|
|firstQuality|První přenosová rychlost filtru.|
|presentationTimeRange|Časový rozsah prezentace. Tato vlastnost se používá pro filtrování počátečních a koncových bodů, délky okna prezentace a živé počáteční pozice. <br/>Další informace najdete v tématu [PresentationTimeRange](#presentationtimerange).|
|určený|Sleduje podmínky výběru. Další informace najdete v tématu [sledování](#tracks) .|

### <a name="presentationtimerange"></a>presentationTimeRange

Tuto vlastnost použijte s **filtry assetů**. Nedoporučuje se nastavovat vlastnost s **filtry účtu**.

|Název|Popis|
|---|---|
|**endTimestamp**|Platí pro video na vyžádání (VoD).<br/>V případě prezentace živého streamování se netichě ignoruje a použije se při ukončení prezentace a datový proud se VoD.<br/>Jedná se o dlouhou hodnotu, která představuje absolutní koncový bod prezentace, zaokrouhlený na nejbližší další skupinu GOP Start. Jednotka je časové měřítko, takže endTimestamp 1800000000 bude trvat 3 minuty.<br/>Pomocí startTimestamp a endTimestamp ořízněte fragmenty, které budou v seznamu testů (manifest).<br/>Například startTimestamp = 40000000 a endTimestamp = 100000000 pomocí výchozí časové osy vygeneruje seznam testů, který obsahuje fragmenty ze 4 sekund a 10 sekund z prezentace VoD. Pokud fragment přechází na hranici, bude v manifestu obsažen celý fragment.|
|**forceEndTimestamp**|Platí jenom pro živé streamování.<br/>Určuje, zda musí být přítomna vlastnost endTimestamp. Pokud je nastaveno na true, musí se zadat endTimestamp nebo se vrátí špatný kód žádosti.<br/>Povolené hodnoty: false, true.|
|**liveBackoffDuration**|Platí jenom pro živé streamování.<br/> Tato hodnota definuje nejnovější živou pozici, na kterou může klient hledat.<br/>Pomocí této vlastnosti můžete zpozdit živou pozici přehrávání a vytvořit vyrovnávací paměť na straně serveru pro přehrávače.<br/>Jednotka pro tuto vlastnost je časová osa (viz níže).<br/>Maximální doba trvání živého zálohování je 300 sekund (3000000000).<br/>Například hodnota 2000000000 znamená, že nejnovější dostupný obsah je na 20 sekund zpožděn od reálné živé hrany.|
|**presentationWindowDuration**|Platí jenom pro živé streamování.<br/>Pomocí presentationWindowDuration můžete použít posuvné okno fragmentů, které chcete zahrnout do seznamu testů.<br/>Jednotka pro tuto vlastnost je časová osa (viz níže).<br/>Například nastavte presentationWindowDuration = 1200000000, aby se použili posuvné okno o dvou minutách. Do seznamu stop budou vložena média do 2 minut živého okraje. Pokud fragment přechází na hranici, bude v seznamu uveden celý fragment. Minimální doba trvání okna prezentace je 60 sekund.|
|**startTimestamp**|Platí pro video na vyžádání (VoD) nebo živé streamování.<br/>Jedná se o dlouhou hodnotu, která představuje absolutní počáteční bod datového proudu. Hodnota se zaokrouhlí na nejbližší další skupinu GOP Start. Jednotka je časové měřítko, takže startTimestamp 150000000 bude po dobu 15 sekund.<br/>Pomocí startTimestamp a endTimestampp ořízněte fragmenty, které budou v seznamu testů (manifest).<br/>Například startTimestamp = 40000000 a endTimestamp = 100000000 pomocí výchozí časové osy vygeneruje seznam testů, který obsahuje fragmenty ze 4 sekund a 10 sekund z prezentace VoD. Pokud fragment přechází na hranici, bude v manifestu obsažen celý fragment.|
|**měřítk**|Platí pro všechna časová razítka a trvání v časovém intervalu prezentace, která je zadána jako počet přírůstků za jednu sekundu.<br/>Výchozí hodnota je 10000000 – 10 000 000 přírůstcích za sekundu, přičemž každý přírůstek by byl 100 nanosekund dlouhé.<br/>Například pokud chcete nastavit startTimestamp na 30 sekund, použijete při použití výchozího časového měřítka hodnotu 300000000.|

### <a name="tracks"></a>Určený

V závislosti na tom, které stopy streamu (živé streamování nebo video na vyžádání) se mají zahrnout do dynamicky vytvořeného manifestu, zadejte seznam stavů filtru sledování (FilterTrackPropertyConditions). Filtry jsou kombinovány pomocí logických operací **and** a **or** .

Filtrovat podmínky vlastností sledování popisují typy sledování, hodnoty (popsané v následující tabulce) a operace (EQUAL, NotEqual). 

|Název|Popis|
|---|---|
|**Rychlostí**|Použijte přenosovou rychlost stopy pro filtrování.<br/><br/>Doporučená hodnota je rozsah přenosové rychlosti v bitech za sekundu. Například "0-2427000".<br/><br/>Poznámka: když můžete použít určitou hodnotu přenosové rychlosti, třeba 250000 (bity za sekundu), tento přístup se nedoporučuje, protože přesné přenosové rychlosti můžou kolísat od jednoho Assetu k druhému.|
|**FourCC**|Použijte hodnotu FourCC stopy pro filtrování.<br/><br/>Hodnota je prvním prvkem formátu kodeků, jak je uvedeno v [dokumentu RFC 6381](https://tools.ietf.org/html/rfc6381). V současné době jsou podporovány následující kodeky: <br/>Video: "avc1", "hev1", "hvc1"<br/>Pro zvuk: "mp4a", "ES-3"<br/><br/>Chcete-li určit hodnoty FourCC pro stopy v Assetu, Získejte a prověřte soubor manifestu.|
|**Jazyk**|Použijte jazyk sledování pro filtrování.<br/><br/>Hodnota je značka jazyka, který chcete zahrnout, jak je uvedeno v dokumentu RFC 5646. Například "en".|
|**Název**|Použijte název stopy pro filtrování.|
|**Typ**|Použijte typ stopy pro filtrování.<br/><br/>Jsou povoleny následující hodnoty: "video", "audio" nebo "text".|

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

## <a name="associating-filters-with-streaming-locator"></a>Přidružení filtrů k lokátoru streamování

Můžete určit seznam [filtrů Asset nebo Account](filters-concept.md) na [lokátoru streamování](/rest/api/media/streaminglocators/create#request-body). [Dynamický balíček](dynamic-packaging-overview.md) používá tento seznam filtrů společně s nastavením, které klient ZADÁ v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v URL + filtry, které zadáte na lokátoru streamování. 

Podívejte se na následující příklady:

* [Přidružit filtry k lokátoru streamování – .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Přidružení filtrů k lokátoru streamování – CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aktualizují se filtry
 
Pokud se filtry dají aktualizovat, **Lokátory streamování** se nedají aktualizovat. 

Nedoporučujeme aktualizovat definici filtrů přidružených k aktivně publikovaným **lokátorům streamování**, zejména pokud je povolená síť CDN. Servery streamování a sítě CDN můžou mít interní mezipaměti, které můžou vést k vrácení zastaralých dat uložených v mezipaměti. 

Pokud je potřeba změnit definici filtru, zvažte vytvoření nového filtru a jeho přidání do adresy URL **lokátoru streamování** nebo publikování nového **lokátoru streamování** , který přímo odkazuje na filtr.

## <a name="next-steps"></a>Další kroky

Následující články ukazují, jak vytvořit filtry prostřednictvím kódu programu.  

- [Vytváření filtrů pomocí rozhraní REST API](filters-dynamic-manifest-rest-howto.md)
- [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Vytváření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)
