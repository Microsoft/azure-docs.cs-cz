---
title: Přehrávání záznamů – Azure
description: Živé video analýzy můžete použít na IoT Edge pro průběžné nahrávání videa, kde můžete nahrávat video do cloudu po týdny nebo měsíce. Záznam můžete také omezit na klipy, které jsou zajímavé, prostřednictvím záznamu založeného na událostech. Tento článek pojednává o tom, jak se nahrávky mají přehrát.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0351f10d9fac3ad7e3b4fdd5fd549eb7c0023694
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490890"
---
# <a name="playback-of-recordings"></a>Přehrávání záznamů 

## <a name="pre-read"></a>Před čtením  

* [Přehrávání videa](video-playback-concept.md)
* [Nepřetržité nahrávání videa](continuous-video-recording-concept.md)
* [Nahrávání videa na základě událostí](event-based-video-recording-concept.md)

## <a name="background"></a>Pozadí  

Live video Analytics na IoT Edge můžete použít pro [záznam nepřetržitého nahrávání videa](continuous-video-recording-concept.md) (CVR), kde můžete nahrávat video do cloudu pro týdny nebo měsíce. Nahrávku můžete také omezit na klipy, které vás zajímají, prostřednictvím [nahrávání videa založeného na událostech](event-based-video-recording-concept.md) (EVR). 

Váš účet Media Service je propojený s účtem Azure Storage a při nahrávání videa do cloudu se obsah zapisuje do [assetu služby Media Service](terminology.md#asset). Media Services umožňuje [streamovat tento obsah](terminology.md#streaming), buď po dokončení nahrávání, nebo když nahrávání probíhá. Media Services poskytuje potřebné možnosti pro doručování datových proudů prostřednictvím protokolů HLS nebo MPEG-SPOJOVNÍK do zařízení pro přehrávání (klientů). Další podrobnosti najdete v článku o [přehrávání videa](video-playback-concept.md) . Pomocí rozhraní API služby Media Service můžete vygenerovat požadavky na adresy URL pro streamování, které klienti použijí k přehrání videa & zvuku. Pokud chcete vytvořit adresu URL streamování pro určitý prostředek, přečtěte si téma [Vytvoření lokátoru streamování a adres URL sestavení](../latest/create-streaming-locator-build-url.md). Po vytvoření adresy URL streamování pro Asset můžete a uložte přidružení adresy URL ke zdroji videa (to je fotoaparát) ve vašem systému správy obsahu.

Například při streamování přes HLS by adresa URL streamování vypadala jako `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Pro MPEG-POMLČKa by vypadala jako `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Tím se vrátí soubor manifestu, který mimo jiné popisuje celkovou dobu trvání dodávaného datového proudu, a to, zda představuje předem zaznamenaný obsah nebo "aktivní" kanál.

### <a name="live-vs-vod"></a>Živý vs. VoD  

Protokoly streamování, jako je HLS a MPEG-POMLČKa, byly vytvořeny pro zpracování scénářů, jako jsou streamování živých videí, a také streamování na vyžádání/předem zaznamenaný obsah, jako jsou televizní pořady a filmy. Pro živá videa jsou HLS a klienti MPEG-SPOJOVNÍKy navrženi tak, aby se spouštěla z nejaktuálnějšího času a vyššího. S filmy ale můžou Návštěvníci začít od začátku a v případě, že se k nim rozhodne, přejít. Manifesty HLS a MPEG-SPOJOVNÍK mají příznaky, které označují klientům, zda video představují živý datový proud nebo je předem zaznamenaného obsahu.
Tento koncept se vztahuje také na HLS a MPEG-SPOJOVNÍKy z prostředků, které obsahují video zaznamenané pomocí živé analýzy videí na IoT Edge.

## <a name="browsing-and-selective-playback-of-recordings"></a>Procházení a selektivní přehrávání nahrávek  

Vezměte v úvahu scénář, ve kterém jste v IoT Edge používali Live video Analytics pro nahrávání videa jenom z 8:00 do 10AM ve dnech, kdy se škola otevřela v celém školním roce. Nebo možná budete nahrávat video jenom z 7AM a 19:00 na státní svátky. V jednom z těchto dvou scénářů při pokusu o procházení a zobrazení záznamu videa budete potřebovat:

* Způsob, jak určit, jaká data a hodiny mají být v záznamu.
* Způsob výběru části (například 9:00 do 11AM v novém roce dne) záznamu na přehrávání.

Media Services poskytuje rozhraní API pro dotazy (availableMedia) pro řešení prvního problému a filtry časového rozsahu (Čas_spuštění, čas_ukončení), které se budou pobývat druhým.

## <a name="query-api"></a>Rozhraní API pro dotazy 

Při použití CVR nemůže zařízení s přehráváním (klienty) požádat o manifest, který pokrývá přehrávání celého záznamu.  Nahrávání na více let by vytvořilo soubor manifestu, který je příliš velký pro přehrávání, a byl nepraktický k analýze použitelných částí na straně klienta.  Klient potřebuje zjistit, jaké rozsahy dat mají data v záznamu, aby mohl provádět platné požadavky bez nutnosti hodně uhodnout práce. Tady je místo, kde se nové rozhraní API pro dotazy připraví – klienti teď můžou pomocí tohoto rozhraní API na straně serveru vyhledat obsah.

Kde hodnota přesnosti může být jedna z následujících hodnot: Year, month, Day nebo Full (viz níže). 

|Přesnost|year|month|day|kompletní|
|---|---|---|---|---|
|Dotaz|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Odpověď|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Úplná odpověď na věrnost. Pokud vůbec žádné mezery neexistují, začátek by byl Čas_spuštění a konec by byl čas_ukončení.|
|Omezení|&#x2022;Čas_spuštění <= čas_ukončení<br/>&#x2022;obou by měly být ve formátu YYYY, jinak vrátí chybu.<br/>&#x2022;hodnoty může být libovolný počet roků od sebe.<br/>Hodnoty &#x2022;jsou včetně.|&#x2022;Čas_spuštění <= čas_ukončení<br/>&#x2022;obou by měly být ve formátu YYYY-MM, jinak vrátí chybu.<br/>Hodnoty &#x2022;můžou být navzájem od nanejvýš 12 měsíců.<br/>Hodnoty &#x2022;jsou včetně.|&#x2022;Čas_spuštění <= čas_ukončení<br/>&#x2022;obou by měly být ve formátu RRRR-MM-DD, jinak návratová chyba.<br/>Hodnoty &#x2022;můžou být navzájem až 31 dnů od sebe.<br/>Hodnoty jsou včetně.|&#x2022;Čas_spuštění < čas ukončení<br/>Hodnoty &#x2022;mohou být nanejvýš 25 hodin od.<br/>Hodnoty &#x2022;jsou včetně.|

#### <a name="additional-request-format-considerations"></a>Další požadavky formátu požadavků

* Všechny časy jsou v UTC.
* Parametr řetězce dotazu Precision je povinný.  
* Parametry řetězce dotazu Čas_spuštění a čas_ukončení jsou požadovány pro hodnoty month, Day a Full Precision.  
* Parametry řetězce dotazu Čas_spuštění a čas_ukončení jsou volitelné pro hodnotu přesnosti roku (žádná, ani jedna nebo obě jsou podporovány).  

    * Pokud je hodnota startTime vynechána, předpokládá se, že se jedná o první rok v záznamu.
    * Pokud je parametr čas_ukončení vynechán, předpokládá se, že se jedná o poslední rok v záznamu.
    * Například pokud vaše nahrávka začala v 2011 a pokračovala až do 2020, pak:

        * /availableMedia? Precision = year je stejný jako/availableMedia? Precision = Year&Čas_spuštění = 2011&čas_ukončení = 2020
        * /availableMedia? Precision = Year&Čas_spuštění = 2015 je stejná jako/availableMedia? Precision = Year&Čas_spuštění = 2015&čas_ukončení = 2020.
        * /availableMedia? Precision = Year&čas_ukončení = 2018 je stejná jako/availableMedia? Precision = Year&Čas_spuštění = 2011&čas_ukončení = 2018

Pokud pro časové rozsahy nejsou k dispozici žádná data médií, vrátí se prázdný seznam.

Pokud prostředek neobsahuje záznam z mediálního grafu, bude vrácena odpověď HTTP 400 s chybovou zprávou s vysvětlením, že tato funkce je k dispozici pouze pro obsah zaznamenaný prostřednictvím mediálního grafu.

Pokud je doba zadaná v parametrech větší než povolená maximálním časovým rozsahem pro daný typ dotazu, bude vrácena HTTP 400 s chybovou zprávou vysvětlující maximální povolený časový rozsah pro požadovaný typ dotazu.

Za předpokladu, že časový rozsah je platný pro zadané parametry, nebudou vráceny žádné chyby pro dotazy, které nejsou mimo časový interval v záznamu.  To znamená, že pokud záznam začal 7 hodin před tím, ale zákazník žádá o dostupné médium od {UtcNow – 24 hodin} až UtcNow, stačí vrátit data {UtcNow – 7} hodin.

### <a name="response-format"></a>Formát odpovědi  

Volání availableMedia vrací sadu časových hodnot.

Odpověď bude tělo JSON, kde timeRanges hodnoty jsou pole dat standardu ISO 8601 UTC pro rok (ve formátu YYYY), měsíc (ve formátu RRRR-MM) nebo den (RRRR-MM-DD) v závislosti na požadované přesnosti.  Úplný timeRanges bude obsahovat počáteční i koncovou hodnotu formátovanou jako datum a čas UTC standardu ISO 8601 (ve formátu RRRR-MM-DDThh: mm: ss. sssZ).

Pro dotaz availableMedia se rozhraní API odeberou z časové osy videa. Jakékoli výpadky v časové ose se zobrazí v odpovědi jako mezery.

#### <a name="response-example-for-availablemedia"></a>Příklad odpovědi pro availableMedia

##### <a name="example-1-live-recording-with-no-gaps"></a>Příklad 1: živé nahrávání bez mezer

Tady je odpověď ukazující všechna dostupná média pro 21. prosince 2019, kde bylo dokončeno 100% nahrávky. Odpověď má pouze jeden pár Start/end.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Příklad 2: záznam za provozu s mezerou 2 sekund

Z nějakého důvodu je možné, že fotoaparát nedokázal na jeden den odeslat platné video o 2 sekundový interval. Tady je odpověď ukazující všechna dostupná média pro 21. prosince 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Příklad 3: záznam za provozu s 8 hodinovou mezerou

Předpokládejme, že fotoaparát nebo místní zařízení ztratily napájení během dne po dobu 8 hodin, od 4 do 12:00 UTC a neexistoval zdroj napájení pro zálohování. Tady je odpověď ukazující všechna dostupná média za tento den.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Příklad 4: záznam za provozu, ve kterém se při letním svátkech nezaznamenává žádné video

Dejme tomu, že jste video nahráli jenom v případě, že je škola v relaci, a záznam se zastavil od 17. června do 6. září. Dotaz na dostupné měsíce by vypadal takto:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Pokud se pak zobrazí výzva k zadání dnů v červnu, měli byste vidět:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Příklad 5: záznam za provozu, ve kterém se při přenosu za víkendy nebo svátky nezaznamenávají žádné video

Předpokládejme, že jste video nahráli jenom během pracovní doby. Dotaz na dny k dispozici by vypadal jako

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Filtry časového rozsahu

Jak je uvedeno výše, tyto filtry vám pomůžou vybrat části záznamu (například z 9:00 do 11AM v novém roce dne) pro přehrávání. Při streamování přes HLS by adresa URL streamování vypadala jako `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Chcete-li vybrat část záznamu, přidejte Čas_spuštění a parametr čas_ukončení, například: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . Proto filtry časového rozsahu jsou modifikátory adresy URL používané k popisu části časové osy nahrávky, která je obsažena v manifestu streamování:

* `starttime` je razítko DateTime ISO 8601, které popisuje požadovaný počáteční čas časové osy videa ve vráceném manifestu.
* `endtime` je razítko DateTime ISO 8601, které popisuje požadovaný koncový čas časové osy videa vrácené v manifestu.

Maximální délka (v čase) takového manifestu nemůže být delší než 24 hodin.

Tady jsou omezení pro filtry.

|startTime| endTime |Výsledek|
|---|---|---|
|Vyskytuje |Vyskytuje |Vrátí poslední část videa v Assetu, až do maximální délky 4 hodiny.<br/><br/>Pokud Asset v poslední době nezapsal (žádná nová data videa nepřichází), vrátí se manifest na vyžádání (VoD). V opačném případě je vrácen živý manifest.|
|Aktuální|Vyskytuje|    Vrátí manifest s jakýmkoli videem, který je novější než Čas_spuštění, pokud by takový manifest byl kratší než 24 hodin.<br/>Pokud by délka manifestu překročila 24 hodin, pak vrátí chybu.<br/>Pokud Asset v poslední době nezapsal (žádná nová data videa nepřichází), vrátí se manifest na vyžádání (VoD). V opačném případě je vrácen živý manifest.
|Vyskytuje|Aktuální |Chyba – Pokud je přítomen Čas_spuštění, pak je koncový čas povinný.|
|Aktuální|Aktuální|Vrátí VoD manifest s jakýmkoli videem, který je k dispozici mezi startTime a čas_ukončení.<br/>Rozsah (Čas_spuštění, čas_ukončení) nemůže být delší než 24 hodin.|

### <a name="response-examples"></a>Příklady odpovědí  

#### <a name="example-1-live-recording-with-no-gaps"></a>Příklad 1: živé nahrávání bez mezer

Tady je odpověď ukazující všechna dostupná média pro 21. prosince 2019, kde bylo dokončeno 100% nahrávky. Odpověď má pouze jeden pár Start/end.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Pokud je záznam souvislý, můžete požadovat manifesty HLS nebo POMLČKy pro libovolnou část času v rámci tohoto páru Start/end – Pokud je rozsah 24 hodin nebo méně. Příklad pro požadavek na 4 hodinu HLS manifest pro výše by byl:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Příklad 2: záznam za provozu s mezerou 2 sekund

Z nějakého důvodu je možné, že fotoaparát nedokázal na jeden den odeslat platné video o 2 sekundový interval. Tady je odpověď ukazující dostupné médium pro 21. prosince 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

U záznamu, jako je výše, byste mohli vyžádat manifesty HLS a POMLČKy s libovolnými páry startTime/čas_ukončení, pokud byl rozsah nižší než 24 hodin. Pokud se tyto hodnoty překládají mezi mezerou ve 04:01:08AM UTC, pak vrácený manifest zaznamená diskontinuitu na časové ose médií podle příslušných specifikací pro tyto protokoly.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Příklad 3: záznam živého záznamu s mezerou na 8 hodin

Předpokládejme, že fotoaparát nebo místní zařízení ztratily napájení během dne po dobu 8 hodin, od 4 do 12:00 UTC a neexistoval zdroj napájení pro zálohování. Tady je odpověď ukazující všechna dostupná média za tento den.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Pomocí takového záznamu:

* Pokud si vyžádáte manifest, ve kterém byly filtry rozsahu startTime/čas_ukončení uvnitř časové osy "dostupné", konkrétně od půlnoci do 4AM nebo od poledne k půlnoci, služba vrátí manifest, který pokrývá čas od času spuštění až po čas ukončení, například:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Pokud si vyžádáte manifest, ve kterém byly hodnoty Čas_spuštění a čas_ukončení uvnitř závorce (například z 8:00 až 10AM UTC), služba se chová stejným způsobem, jako by výsledkem filtru Assetu byl prázdný výsledek.

    [Toto je požadavek, který získá prázdnou odpověď.] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Pokud vyžádáte manifest, ve kterém je pouze jeden z Čas_spuštění nebo čas ukončení uvnitř otvoru, vrácený manifest bude obsahovat pouze část tohoto časového rozpětí. Přichycení hodnoty startTime nebo čas_ukončení k nejbližší platné hranici. Například pokud jste požádali o 3-HR datový proud z 10AM do 13:00, odpověď by měla obsahovat 1 – hod média v hodnotě od 12:00 do 13:00.

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    Vrácený manifest by začínal na 2019-12-21T12:00:00.000 Z, i když se požadavek vyžádal o začátek 10AM. Při sestavování systému správy videí pomocí plug-in přehrávače by měla být provedena péče pro prohlížeč. Nezohledňující prohlížeč se zaznamená, proč se časová osa přehrávání od poledne a 10AM, jak je požadováno.

## <a name="recording-and-playback-latencies"></a>Latence nahrávání a přehrávání

Při použití živé analýzy videí na IoT Edge k nahrávání do Assetu zadáte vlastnost segmentLength, která oznámí modulu, aby agreguje minimální dobu trvání videa (v sekundách) předtím, než se nahraje do cloudu. Pokud je například segmentLength nastavené na 300, pak modul nashromáždí za 5 minut množství videí před nahráním 1 5 minut "bloku", přejde do režimu akumulace na dalších 5 minut a odešle se znovu. Zvýšení segmentLength má výhodu snížit náklady transakce Azure Storage, protože počet čtení a zápisů nebude častější než jednou za segmentLength sekund.

V důsledku toho se streamování videa z Media Services bude zpozdit alespoň o tolik času. 

Jiný faktor, který určuje latenci přehrávání (prodleva mezi okamžikem, kdy se událost koná před fotoaparátem), je [skupinu GOP](https://en.wikipedia.org/wiki/Group_of_pictures) trvání skupiny obrázků. Vzhledem [k omezení zpoždění živých streamů pomocí 3 jednoduchých techniků](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) vysvětluje dobu delší, než je doba skupinu GOP trvání, i latence. Je běžné, že máte fotoaparáty na IP adresy, které se používají ve scénářích dohledu a zabezpečení nakonfigurovaných tak, aby používaly GOPs déle než 30 sekund. To má velký vliv na celkovou latenci.

## <a name="next-steps"></a>Další kroky

[Kurz nepřetržitého nahrávání videa](continuous-video-recording-tutorial.md)
