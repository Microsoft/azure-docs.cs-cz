---
title: Azure Media Services typy Livestream | Microsoft Docs
description: V Azure Media Services může být živá událost nastavena buď na *předávací* , nebo na *živé kódování*. V tomto článku se zobrazuje podrobná tabulka, která porovnává typy událostí typu Live.
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
ms.openlocfilehash: c79d45cfac22f41f05071b619c444e7b7ab7956a
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397300"
---
# <a name="live-event-types-comparison"></a>Porovnání typů živých událostí

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V Azure Media Services může být  [živá událost](/rest/api/media/liveevents) nastavena buď na *předávací* (místní živý kodér posílá datový proud s více přenosovými rychlostmi), nebo za běhu za *provozu* (místní kodér Live Encoder odesílá datový proud s jednou přenosovou rychlostí). 

V tomto článku jsou porovnávány funkce typů živých událostí.

## <a name="types-comparison"></a>Porovnání typů 

Následující tabulka porovnává funkce typů živých událostí. Typy jsou nastaveny během vytváření pomocí [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** – místní kodér Live posílá datový proud s více přenosovými rychlostmi. Příjmové streamy procházejí prostřednictvím živé události bez dalšího zpracování. Také se označuje jako předávací živá událost.
* **LiveEventEncodingType. Standard** – místní Live Encoder odesílá datový proud s jednou přenosovou rychlostí do živé události a Media Services vytvoří více datových proudů s přenosovou rychlostí. Pokud je informační kanál příspěvku v rámci řešení 720p nebo vyšší, **Default720p** předvolba zakóduje sadu 6 párů rozlišení/přenosové rychlosti (podrobnosti následují dále v článku).
* **LiveEventEncodingType. Premium1080p** – místní Live Encoder odesílá datový proud s jednou přenosovou rychlostí do živé události a Media Services vytvoří více datových proudů s přenosovou rychlostí. Předvolba Default1080p určuje výstupní sadu párů rozlišení/přenosové rychlosti (podrobnosti následují dále v článku). 

| Příznak | Předávací živá událost | Událost Standard nebo Premium1080p Live |
| --- | --- | --- |
| Vstup s jednou přenosovou rychlostí je kódovaný do více přenosových rychlostí v cloudu. |No |Yes |
| Maximální rozlišení videa pro kanál příspěvků |4K (4096x2160 v 60 snímcích za sekundu) |1080p (1920x1088 v 30 snímcích/s)|
| Doporučené maximum vrstev v kanálu příspěvků|Až 12|Jeden zvuk|
| Maximální počet vrstev ve výstupu| Stejné jako vstup|Až 6 (viz Předvolby systému níže)|
| Maximální agregovaná šířka pásma kanálu příspěvků|60 MB/s|–|
| Maximální přenosová rychlost pro jednu vrstvu v příspěvku |20 MB/s|20 MB/s|
| Podpora pro zvukové stopy ve více jazycích|Yes|No|
| Podporované vstupní kodeky videa |H. 264/AVC a H. 265/HEVC|H. 264/AVC|
| Podporované video kodeky pro výstup|Stejné jako vstup|H. 264/AVC|
| Podporovaná bitová hloubka videa, vstup a výstup|Až 10 bitů včetně HDR 10/HLG|8 bitů|
| Podporované vstupní zvukové kodeky|AAC-LC, HE-AAC V1, IT-AAC v2|AAC-LC, HE-AAC V1, IT-AAC v2|
| Podporované výstupní zvukové kodeky|Stejné jako vstup|AAC-LC|
| Maximální video rozlišení výstupního videa|Stejné jako vstup|Standard-720p, Premium1080p – 1080p|
| Maximální Snímková frekvence vstupního videa|60 snímků za sekundu|Standard nebo Premium1080p-30 snímků za sekundu|
| Vstupní protokoly|RTMP, fragmentované – MP4 (Smooth Streaming)|RTMP, fragmentované – MP4 (Smooth Streaming)|
| Cena|Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu živé video.|Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu živé video.|
| Maximální doba běhu| 24 hodin × 365 dní, živý lineární | 24 hodin × 365 dní, živý lineární (Preview)|
| Možnost předávání dat titulků Embedded CEA 608/708|Yes|Yes|
| Možnost zapnout živý přepis|Yes|Yes|
| Podpora pro vložení SLAT|No|No|
| Podpora pro signalizaci reklamy prostřednictvím rozhraní API| No|No|
| Podpora pro signalizaci reklamy prostřednictvím zpráv SCTE-35 in-band|Yes|Yes|
| Možnost obnovení z krátkých kabin v informačním kanálu příspěvků|Yes|Částečné|
| Podpora pro neuniformní vstupní GOPs|Yes|Ne – vstup musí mít pevnou skupinu GOP dobu trvání.|
| Podpora pro vstup variabilní frekvence snímků|Yes|Ne – vstupní hodnota musí být pevná snímková frekvence. Vedlejší variace jsou tolerovány, například při vysokém pohybu scény. Kanál příspěvků ale nemůže vyřadit kmitočet snímků (například na 15 snímků za sekundu).|
| Automaticky shutoff událost živé události při ztrátě vstupního kanálu|No|Pokud není LiveOutput spuštěný, po 12 hodinách.|

## <a name="system-presets"></a>Předvolby systému

Rozlišení a přenosové rychlosti obsažené ve výstupu z kodéru Live Encoder jsou určeny podle [přednastaveného](/rest/api/media/liveevents/create#liveeventencoding). Pokud používáte **standardní** Live Encoder (LiveEventEncodingType. Standard), pak předvolba *Default720p* určuje sadu párů hodnot 6 a přenosových rychlostí popsaných níže. V opačném případě, pokud používáte **Premium1080p** Live Encoder (LiveEventEncodingType. Premium1080p), pak předvolba *Default1080p* určuje výstupní sadu párů rozlišení/přenosové rychlosti.

> [!NOTE]
> Předvolbu Default1080p nemůžete použít pro živou událost, pokud byla nastavená pro standardní kódování v reálném čase – zobrazí se chyba. Pokud se pokusíte použít předvolbu Default720p pro kodér Premium1080p Live, zobrazí se také chyba.

### <a name="output-video-streams-for-default720p"></a>Výstupní video proudy pro Default720p

Pokud je informační kanál příspěvku z řešení 720p nebo vyšší, **Default720p** předvolba zakóduje kanál do následujících 6 vrstev. V následující tabulce je přenosová rychlost v KB/s, MaxFPS představuje maximální povolenou frekvenci snímků (v rámcích za sekundu), profil představuje použitý profil H. 264.

| Rychlostí | Width (Šířka) | Height (Výška) | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoké |
| 2200 |960 |540 |30 |Vysoké |
| 1350 |704 |396 |30 |Vysoké |
| 850 |512 |288 |30 |Vysoké |
| 550 |384 |216 |30 |Vysoké |
| 200 |340 |192 |30 |Vysoké |

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování, otevřete prosím lístek podpory prostřednictvím webu Azure Portal. Měli byste zadat požadovanou tabulku pro rozlišení videa a přenosové rychlosti. Přizpůsobení přenosové rychlosti zvukového kódování se nepodporuje. Ověřte, že existuje pouze jedna vrstva s rozlišením 720p a maximálně 6 vrstev celkem. Také zadejte, že požadujete přednastavení.

### <a name="output-video-streams-for-default1080p"></a>Výstupní video proudy pro Default1080p

Pokud je informační kanál příspěvku z rozlišení 1080p, předvolba **Default1080p** zakóduje kanál do následujících 6 vrstev.

| Rychlostí | Width (Šířka) | Height (Výška) | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Vysoké |
| 3000 |1280 |720 |30 |Vysoké |
| 1600 |960 |540 |30 |Vysoké |
| 800 |640 |360 |30 |Vysoké |
| 400 |480 |270 |30 |Vysoké |
| 200 |320 |180 |30 |Vysoké |

> [!NOTE]
> Pokud potřebujete přizpůsobit živé kódování, otevřete prosím lístek podpory prostřednictvím webu Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, zda je v 1080p pouze jedna vrstva, a maximálně 6 vrstev. Také určete, že požadujete přednastavení pro Premium1080p Live Encoder. Konkrétní hodnoty přenosových rychlostí a rozlišení se můžou v průběhu času upravovat.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Výstupní zvukový stream pro Default720p a Default1080p

Pro *Default720p* i *Default1080p* přednastavení je zvuk kódovaný na stereofonní AAC-LC při 128 kb/s. Vzorkovací frekvence se řídí zvukovou stopou v informačním kanálu příspěvků.

## <a name="implicit-properties-of-the-live-encoder"></a>Implicitní vlastnosti kodéru Live Encoder

Předchozí část popisuje vlastnosti živého kodéru, který se dá řídit explicitně, prostřednictvím přednastaveného, jako je počet vrstev, rozlišení a přenosové rychlosti. Tato část vysvětluje implicitní vlastnosti.

### <a name="group-of-pictures-gop-duration"></a>Doba trvání skupiny obrázků (skupinu GOP)

Live Encoder se řídí strukturou [skupinu GOP](https://en.wikipedia.org/wiki/Group_of_pictures) kanálu příspěvků – to znamená, že výstupní vrstvy budou mít stejnou skupinu GOP dobu trvání. Proto se doporučuje nakonfigurovat místní kodér na vytvoření informačního kanálu, který má pevnou skupinu GOP dobu trvání (obvykle 2 sekundy). Tím se zajistí, že odchozí datové proudy HLS a MPEG z této služby mají také pevnou dobu trvání skupinu GOP. Malé odchylky v skupinu GOP trvání je pravděpodobně možné tolerovat většinou zařízení.

### <a name="frame-rate"></a>Snímková frekvence

Živý kodér také sleduje dobu trvání jednotlivých snímků videa v informačním kanálu příspěvků – to znamená, že výstupní vrstvy budou mít snímky se stejnou dobou trvání. Proto se doporučuje nakonfigurovat místní kodér na vytvoření informačního kanálu, který má fixní kmitočet snímků (maximálně 30 snímků za sekundu). Tím se zajistí, že odchozí datové proudy HLS a MPEG z této služby mají také trvání pevných kmitočtů snímků. Malá variace v kmitočtech snímků můžou být tolerováná většinou zařízení, ale není nijak zaručeno, že živý kodér vytvoří výstup, který se bude přehrávat správně. Váš místní živý kodér by neměl odstraňovat snímky (např. při nízkém stavu baterie) nebo jakýmkoli způsobem proměnliví kmitočet snímků.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Rozlišení kanálu a výstupních vrstev příspěvku

Live Encoder je nakonfigurován tak, aby se předešlo převádění informačního kanálu příspěvků. Výsledkem je, že maximální rozlišení výstupních vrstev nebude přesáhnout na kanál příspěvků.

Pokud například odešlete informační kanál příspěvků v 720p do živé události nakonfigurované pro Default1080p Live Encoding, bude mít výstup pouze 5 vrstev počínaje hodnotou 720p v 3Mbps, a to až 1080p při 200 kbps. Nebo pokud odešlete informační kanál příspěvků na 360p do živé události nakonfigurované pro standardní kódování v reálném čase, bude výstup obsahovat 3 vrstvy (s rozlišeními 288p, 216p a 192p). Pokud v případě, že odešlete informační kanál příspěvku, například 160x90 pixelů do standardního kodéru Live Encoder, bude výstup obsahovat jednu vrstvu na rozlišení 160x90 na stejné přenosové rychlosti jako u kanálu příspěvků.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Přenosová rychlost kanálu a výstupních vrstev příspěvků

Kodér Live Encoder je nakonfigurován tak, aby dodržoval nastavení přenosové rychlosti v předvolbě bez ohledu na rychlost kanálu příspěvků. V důsledku toho může rychlost přenosů výstupních vrstev přesáhnout kanál příspěvků. Pokud například odešlete příspěvek v informačním kanálu s rozlišením 720p na 1 MB/s, výstupní vrstvy zůstanou stejné jako v [tabulce](live-event-types-comparison.md#output-video-streams-for-default720p) výše.

## <a name="next-steps"></a>Další kroky

[Přehled živého streamování](live-streaming-overview.md)
