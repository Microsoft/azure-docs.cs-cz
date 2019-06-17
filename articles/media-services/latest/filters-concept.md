---
title: Definování filtrů ve službě Azure Media Services
description: Toto téma popisuje, jak vytvářet filtry, takže klient může používat na určité části datový proud stream. Služba Media Services vytvoří dynamických manifestů k selektivní Streamovat.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66225411"
---
# <a name="filters"></a>Filtry

Při doručování obsahu zákazníkům (živě streamovaných událostí nebo Video na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services nabízí [dynamických manifestů](filters-dynamic-manifest-overview.md) na základě předdefinovaných filtrů. 

Filtry jsou pravidla na straně serveru, které umožňují zákazníkům provádět například následující akce: 

- Přehrávání pouze část videa (namísto přehrání videa celý). Příklad:
  - Snižte manifest zobrazíte dílčích klipů živé události ("dílčích klipů filtrování"), nebo
  - Ořízněte úvodní video ("ořezávání video").
- Dodávejte pouze zadaný interpretace nebo zadané jazykové stopy, které podporuje zařízení, která se používá k přehrávání obsahu ("verze filtrování"). 
- Upravte prezentace okno (DVR) aby bylo možné poskytovat omezenou délku okna DVR v přehrávači ("okno s úpravou prezentace").

Služba Media Services vám umožní vytvořit **účtu filtry** a **Asset filtry** pro váš obsah. Kromě toho můžete přidružit s předem vytvořeným filtry **Lokátor streamování**.

## <a name="defining-filters"></a>Definování filtrů

Existují dva typy filtrů: 

* [Účet filtry](https://docs.microsoft.com/rest/api/media/accountfilters) (globální) – lze použít u všech prostředků v účtu Azure Media Services, mají životnost účtu.
* [Filtry Asset](https://docs.microsoft.com/rest/api/media/assetfilters) (místní) - může používat jedině pro určitý prostředek, pomocí kterého byl tento filtr k při vytvoření, mají životnost assetu. 

**Účet filtry** a **Asset filtry** typy mají stejné vlastnosti pro definování/popis filtru. Kromě případů, kdy se vytváří **Asset filtr**, je třeba zadat název assetu, se kterým se má přidružit filtru.

V závislosti na scénáři rozhodněte, jaký typ filtru je vhodnější (Asset filtru nebo účet). Filtrů účtů jsou vhodné pro profily zařízení (interpretace filtrování), kde se používal Asset filtry k trim konkrétní prostředek.

Následující vlastnosti použít k popisu filtry. 

|Název|Popis|
|---|---|
|firstQuality|První přenosové rychlosti kvality filtru.|
|presentationTimeRange|Prezentace časový rozsah. Tato vlastnost se používá k filtrování manifestu počáteční nebo koncové body, délka okna prezentace a živé počáteční pozice. <br/>Další informace najdete v tématu [PresentationTimeRange](#presentationtimerange).|
|stop|Podmínky výběr stopy. Další informace najdete v tématu [stopy](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Pomocí této vlastnosti se **Asset filtry**. Nedoporučuje se nastavit vlastnost s **filtrů účtů**.

|Name|Popis|
|---|---|
|**endTimestamp**|Vztahuje se na Video na vyžádání (VoD).<br/>Pro prezentaci živého streamování je tiše ignorováno a použít jakmile skončí prezentace a datový proud změní videa na vyžádání.<br/>To je dlouhou hodnotu, která představuje absolutní koncový bod prezentace zaokrouhlí na nejbližší další GOP start. Jednotka je na časové ose, takže endTimestamp 1800000000 bude po dobu 3 minut.<br/>Použijte startTimestamp a endTimestamp oříznout fragmenty, které budou v seznamu testů (manifest).<br/>Například startTimestamp = 40000000 a endTimestamp = 100000000 pomocí časové osy výchozí vygeneruje seznam testů, který obsahuje fragmenty mezi 4 sekundami a 10 sekund prezentace videa na vyžádání. Pokud fragment přechází na hranici, bude celý fragment součástí manifestu.|
|**forceEndTimestamp**|Platí pro živé streamování.<br/>Určuje, zda vlastnost endTimestamp musí být k dispozici. Při hodnotě true se endTimestamp musí být zadán nebo je vrácen chybný požadavek kód.<br/>Povolené hodnoty: false, true.|
|**liveBackoffDuration**|Platí pro živé streamování.<br/> Tato hodnota určuje nejnovější živé pozice, který může klient vyhledat.<br/>Pomocí této vlastnosti, můžete zpoždění přehrávání živé pozice a vytvořit vyrovnávací paměti na straně serveru pro hráče.<br/>Jednotka pro tuto vlastnost je časová osa (viz níže).<br/>Maximální délka živé regresi doby trvání je 300 sekund (3000000000).<br/>Například hodnota 2000000000 prostředky, které se nejnovější dostupný obsah je 20 sekund zpožděné od skutečné živé okraje.|
|**presentationWindowDuration**|Platí pro živé streamování.<br/>Použijte presentationWindowDuration posuvné okno fragmentů mají být zahrnuty seznamu testů.<br/>Jednotka pro tuto vlastnost je časová osa (viz níže).<br/>Například nastavte presentationWindowDuration = 1200000000 použít dvouminutového posuvného okna. Média během 2 minut za provozu edge se zahrne seznam stop. Pokud fragment přechází na hranici, bude celý fragment součástí seznamu stop. Doba trvání okna minimální prezentace je 60 sekund.|
|**startTimestamp**|Vztahuje se na Video na vyžádání (VoD) nebo živého streamování.<br/>To je dlouhou hodnotu, která reprezentuje absolutní počáteční bod datového proudu. Získá hodnotu zaokrouhlí na nejbližší další GOP Start. Jednotka je na časové ose, takže startTimestamp 150000000 bude po dobu 15 sekund.<br/>Použijte startTimestamp a endTimestampp oříznout fragmenty, které budou v seznamu testů (manifest).<br/>Například startTimestamp = 40000000 a endTimestamp = 100000000 pomocí časové osy výchozí vygeneruje seznam testů, který obsahuje fragmenty mezi 4 sekundami a 10 sekund prezentace videa na vyžádání. Pokud fragment přechází na hranici, bude celý fragment součástí manifestu.|
|**timescale**|Platí pro všechna časová razítka a doby trvání prezentace časový rozsah, zadaný jako počet kroků v jedné sekundy.<br/>Výchozí hodnota je 10000000 – deset milionů přírůstky v jedné sekundy, kde každý přírůstek by 100 nanosekund dlouho.<br/>Například pokud chcete nastavit startTimestamp na 30 sekund, můžete využít hodnotu 300000000 při použití výchozí časový rámec.|

### <a name="tracks"></a>stop

Můžete zadat seznam podmínek vlastností sledování filtru (FilterTrackPropertyConditions) podle, na kterém sleduje váš datový proud (živého streamování a Video na vyžádání) by měly být zahrnuty do dynamicky generovaný manifest. Filtry jsou kombinovat pomocí logické **a** a **nebo** operace.

Podmínky pro vlastnost sledování filtru popisují typy stop, hodnoty (popsané v následující tabulce) a operací (rovná, NotEqual). 

|Name|Popis|
|---|---|
|**Bitrate**|Použijte přenosové rychlosti jeden směr určený pro filtrování.<br/><br/>Doporučená hodnota je rozsah přenosových rychlostí v bitech za sekundu. Například "0-2427000".<br/><br/>Poznámka: když konkrétní s přenosovou rychlostí s hodnotou, jako třeba částku 250 000 jedné (bity za sekundu), můžete použít tento přístup se nedoporučuje, protože přesné přenosových rychlostí může kolísat z jednoho prostředku do jiného.|
|**FourCC**|Použijte hodnotu FourCC jeden směr určený pro filtrování.<br/><br/>Hodnota je první prvek kodeky formát, jak je uvedeno v [RFC 6381](https://tools.ietf.org/html/rfc6381). V současné době jsou podporovány následující kodeky: <br/>Video: "Avc1", "hev1", "hvc1"<br/>Pro zvuk: "Mp4a", "ES-3"<br/><br/>K určení hodnoty FourCC sleduje v Assetu, získejte a zkontrolujte soubor manifestu.|
|**Jazyk**|Použijte jazyk jeden směr určený pro filtrování.<br/><br/>Hodnota je značka jazyka, které chcete zahrnout jako uvedené v RFC 5646. Například "en".|
|**Název**|Použijte název jeden směr určený pro filtrování.|
|**Typ**|Použijte jeden směr určený pro filtrování.<br/><br/>Jsou povoleny následující hodnoty: "video", "zvuku" nebo "text".|

### <a name="example"></a>Příklad:

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

## <a name="associating-filters-with-streaming-locator"></a>Filtry přidružení Lokátor streamování

Můžete zadat seznam [asset nebo účet filtrů](filters-concept.md) na vaše [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). [Dynamické Packager](dynamic-packaging-overview.md) platí tento seznam filtrů společně s ty klientem v adrese URL. Tato kombinace generuje [dynamické Manifest](filters-dynamic-manifest-overview.md), která je založena na filtry v adrese URL a filtry, které jste zadali na Lokátor streamování. 

Podívejte se na následující příklady:

* [Filtry přidružit Lokátor streamování – .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Filtry přidružit Lokátor streamování – rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aktualizuje se filtry
 
**Lokátory streamování** nejsou uvedena aktualizovat filtry je možné aktualizovat. 

Nedoporučuje se aktualizovat definici filtrů přidružených ke aktivně publikované **Lokátor streamování**, zejména v případě, že je povolená síť CDN. Streamování servery a CDN může mít vnitřní mezipaměti, které může způsobovat zastaralá data uložená v mezipaměti, který se má vrátit. 

Pokud je potřeba změnit definici filtru, zvažte vytvoření nového filtru a jejím přidáním na **Lokátor streamování** adresu URL nebo publikování nového **Lokátor streamování** , která odkazuje na filtr přímo.

## <a name="next-steps"></a>Další postup

Následující články popisují, jak programově vytvářet filtry.  

- [Vytváření filtrů pomocí rozhraní REST API](filters-dynamic-manifest-rest-howto.md)
- [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Vytváření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

