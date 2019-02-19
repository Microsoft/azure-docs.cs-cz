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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405706"
---
# <a name="define-account-filters-and-asset-filters"></a>Definujte účet filtry a filtry asset  

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. 

Filtry jsou pravidla na straně serveru, které umožňují zákazníkům provádět například následující akce: 

- Přehrávání pouze část videa (namísto přehrání videa celý). Příklad:
  - Snižte manifest zobrazíte dílčích klipů živé události ("dílčích klipů filtrování"), nebo
  - Ořízněte úvodní video ("ořezávání video").
- Dodávejte pouze zadaný interpretace nebo zadané jazykové stopy, které podporuje zařízení, která se používá k přehrávání obsahu ("verze filtrování"). 
- Upravte prezentace okno (DVR) aby bylo možné poskytovat omezenou délku okna DVR v přehrávači ("okno s úpravou prezentace").

Media Services nabízí [dynamických manifestů](filters-dynamic-manifest-overview.md) na základě předdefinovaných filtrů. Jakmile definujete filtry, klienty je použít v adrese URL streamování. Filtry můžete uplatnit adaptivní přenosové rychlosti streamování protokolů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming.

V následující tabulce jsou uvedeny příklady adresy URL s filtry:

|Protocol (Protokol)|Příklad:|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Definování filtrů

Existují dva typy filtrů prostředku: 

* [Účet filtry](https://docs.microsoft.com/rest/api/media/accountfilters) (globální) – lze použít u všech prostředků v účtu Azure Media Services, mají životnost účtu.
* [Filtry Asset](https://docs.microsoft.com/rest/api/media/assetfilters) (místní) - může používat jedině pro určitý prostředek, pomocí kterého byl tento filtr k při vytvoření, mají životnost assetu. 

[Účet filtr](https://docs.microsoft.com/rest/api/media/accountfilters) a [Asset filtr](https://docs.microsoft.com/rest/api/media/assetfilters) typy mají stejné vlastnosti pro definování/popis filtru. Kromě případů, kdy se vytváří **Asset filtr**, je třeba zadat název assetu, se kterým se má přidružit filtru.

V závislosti na scénáři rozhodněte, jaký typ filtru je vhodnější (Asset filtru nebo účet). Filtrů účtů jsou vhodné pro profily zařízení (interpretace filtrování), kde se používal Asset filtry k trim konkrétní prostředek.

Následující vlastnosti použít k popisu filtry. 

|Název|Popis|
|---|---|
|firstQuality|První přenosové rychlosti kvality filtru.|
|presentationTimeRange|Prezentace časový rozsah. Tato vlastnost se používá k filtrování manifestu počáteční nebo koncové body, délka okna prezentace a živé počáteční pozice. <br/>Další informace najdete v tématu [PresentationTimeRange](#PresentationTimeRange).|
|stop|Podmínky výběr stopy. Další informace najdete v tématu [stopy](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Pomocí této vlastnosti se **Asset filtry**. Nedoporučuje se nastavit vlastnost s **filtrů účtů**.

|Název|Popis|
|---|---|
|**endTimestamp**|Hranice absolutní koncový čas. Vztahuje se na Video na vyžádání (VoD). Pro živou prezentaci je tiše ignorováno a použít jakmile skončí prezentace a datový proud změní videa na vyžádání.<br/><br/>Hodnota představuje absolutní koncového bodu datového proudu. Získá zaokrouhlí na nejbližší další GOP Start.<br/><br/>Použití StartTimestamp a EndTimestamp k oříznutí seznamu testů (manifest). Například StartTimestamp = 40000000 a EndTimestamp = 100000000 vygeneruje seznam testů, který obsahuje média mezi StartTimestamp a EndTimestamp. Pokud fragment přechází na hranici, bude celý fragment součástí manifestu.<br/><br/>Další informace naleznete **forceEndTimestamp** definice, který následuje.|
|**forceEndTimestamp**|Platí pro živá filtry.<br/><br/>**forceEndTimestamp** je logická hodnota, která určuje, zda je či není **endTimestamp** byl nastaven na platnou hodnotu. <br/><br/>Pokud je hodnota **true**, **endTimestamp** je třeba zadat hodnotu. Pokud není zadán, je vrácena chybnou žádost.<br/><br/>Pokud například chcete definovat filtr, který začíná na 5 minut na vstupním videu a má platnost až do konce datového proudu, nastavíte **forceEndTimestamp** na hodnotu false a vynechat, nechte nastavení **endTimestamp**.|
|**liveBackoffDuration**|Platí pouze pro živé. Vlastnost se používá k definování přehrávání živé pozice. Pomocí tohoto pravidla, můžete zpoždění přehrávání živé pozice a vytvořit vyrovnávací paměti na straně serveru pro hráče. LiveBackoffDuration je relativní vzhledem k živé pozice. Maximální omezení rychlosti za provozu doba trvání je 300 sekund.|
|**presentationWindowDuration**|Platí pro živá. Použití **presentationWindowDuration** použít posuvné okno k zobrazení seznamu stop. Například nastavte presentationWindowDuration = 1200000000 použít dvouminutového posuvného okna. Média během 2 minut za provozu edge se zahrne seznam stop. Pokud fragment přechází na hranici, bude celý fragment součástí seznamu stop. Doba trvání okna minimální prezentace je 60 sekund.|
|**startTimestamp**|Vztahuje se na videa na vyžádání a živé streamy. Hodnota reprezentuje absolutní počáteční bod datového proudu. Získá hodnotu zaokrouhlí na nejbližší další GOP Start.<br/><br/>Použití **startTimestamp** a **endTimestamp** k oříznutí seznamu testů (manifest). Například startTimestamp = 40000000 a endTimestamp = 100000000 vygeneruje seznam testů, který obsahuje média mezi StartTimestamp a EndTimestamp. Pokud fragment přechází na hranici, bude celý fragment součástí manifestu.|
|**timescale**|Vztahuje se na videa na vyžádání a živé streamy. Časové měřítko používat časová razítka a doby trvání výše uvedené. Výchozí časové osy je 10000000. Dá se alternativní časový rámec. Výchozí hodnota je 10000000 HNS (stovek nanosekund).|

### <a name="tracks"></a>stop

Můžete zadat seznam podmínek vlastností sledování filtru (FilterTrackPropertyConditions) podle, na kterém sleduje váš datový proud (Live nebo Video na vyžádání) by měly být zahrnuty do dynamicky generovaný manifest. Filtry jsou kombinovat pomocí logické **a** a **nebo** operace.

Podmínky pro vlastnost sledování filtru popisují typy stop, hodnoty (popsané v následující tabulce) a operací (rovná, NotEqual). 

|Název|Popis|
|---|---|
|**Bitrate**|Použijte přenosové rychlosti jeden směr určený pro filtrování.<br/><br/>Doporučená hodnota je rozsah přenosových rychlostí v bitech za sekundu. Například "0-2427000".<br/><br/>Poznámka: když konkrétní s přenosovou rychlostí s hodnotou, jako třeba částku 250 000 jedné (bity za sekundu), můžete použít tento přístup se nedoporučuje, protože přesné přenosových rychlostí může kolísat z jednoho prostředku do jiného.|
|**FourCC**|Použijte hodnotu FourCC jeden směr určený pro filtrování.<br/><br/>Hodnota je první prvek kodeky formát, jak je uvedeno v [RFC 6381](https://tools.ietf.org/html/rfc6381). V současné době jsou podporovány následující kodeky: <br/>Video: "Avc1", "hev1", "hvc1"<br/>Pro zvuk: "Mp4a", "ES-3"<br/><br/>Chcete-li zjistit hodnoty FourCC sleduje v prostředku, [získat a zkontrolujte soubor manifestu](#get-and-examine-manifest-files).|
|**Jazyk**|Použijte jazyk jeden směr určený pro filtrování.<br/><br/>Hodnota je značka jazyka, které chcete zahrnout jako uvedené v RFC 5646. Například "en".|
|**Název**|Použijte název jeden směr určený pro filtrování.|
|**Typ**|Použijte jeden směr určený pro filtrování.<br/><br/>Jsou povoleny následující hodnoty: "video", "zvuku" nebo "text".|

## <a name="example"></a>Příklad:

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

## <a name="next-steps"></a>Další postup

Následující články popisují, jak programově vytvářet filtry.  

- [Vytváření filtrů pomocí rozhraní REST API](filters-dynamic-manifest-rest-howto.md)
- [Vytváření filtrů pomocí .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Vytváření filtrů pomocí rozhraní příkazového řádku](filters-dynamic-manifest-cli-howto.md)

