---
title: Typy živých událostí Azure Media Services | Dokumenty společnosti Microsoft
description: Ve službě Azure Media Services lze živou událost nastavit buď na *předávací* nebo *živé kódování*. Tento článek ukazuje podrobnou tabulku, která porovnává typy živých událostí.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244647"
---
# <a name="live-event-types-comparison"></a>Porovnání typů živých událostí

Ve službě Azure Media Services live [událost](https://docs.microsoft.com/rest/api/media/liveevents) může být nastavena buď *předávací* (místní živý kodér odešle datový proud s více datovým tokem) nebo *živé kódování* (místní živý kodér odešle jeden datový proud datového toku). 

Tento článek porovnává funkce typů živých událostí.

## <a name="types-comparison"></a>Porovnání typů 

Následující tabulka porovnává funkce typů živých událostí. Typy jsou nastaveny během vytváření pomocí [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** - Místní živý kodér odešle datový proud více datových toků. Ingestované datové proudy procházejí živou událostí bez dalšího zpracování. Označuje se také jako předávací živá událost.
* **LiveEventEncodingType.Standard** - Místní živý kodér odešle jeden datový tok datového toku do živé události a media services vytvoří více datových proudů datového toku. Pokud je zdroj příspěvku rozlišení 720p nebo vyšší, předvolba **Default720p** zakóduje sadu párů rozlišení 6/přenosová rychlost (podrobnosti následují dále v článku).
* **LiveEventEncodingType.Premium1080p** - místní živý kodér odešle jeden datový proud datového toku do živé události a media services vytvoří více datových proudů datového toku. Přednastavení Default1080p určuje výstupní sadu párů rozlišení/přenosové rychlosti (podrobnosti následují dále v článku). 

| Funkce | Předávací živá událost | Standardní nebo Premium1080p Živá událost |
| --- | --- | --- |
| Vstup s jedním přenosovým tokem je v cloudu zakódován do více přenosových rychlostí |Ne |Ano |
| Maximální rozlišení videa pro příspěvek krmiva |4K (4096x2160 při 60 snímcích/s) |1080p (1920x1088 při 30 snímcích/s)|
| Doporučené maximální vrstvy v příspěvkovém posuvu|Až 12|Jeden zvuk|
| Maximální hladiny ve výstupu| Stejné jako vstup|Až 6 (viz Přednastavení systému níže)|
| Maximální celková šířka pásma příspěvku|60 Mb/s|Není dostupné.|
| Maximální přenosový tok pro jednu vrstvu v příspěvku |20 Mb/s|20 Mb/s|
| Podpora vícejazykových zvukových stop|Ano|Ne|
| Podporované vstupní video kodeky |H.264/AVC a H.265/HEVC|H.264/AVC|
| Podporované výstupní video kodeky|Stejné jako vstup|H.264/AVC|
| Podporovaná bitová hloubka, vstup a výstup videa|Až 10bitový včetně HDR 10/HLG|8bitový|
| Podporované vstupní zvukové kodeky|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Podporované výstupní zvukové kodeky|Stejné jako vstup|AAC-LC|
| Maximální rozlišení videa výstupního videa|Stejné jako vstup|Standardní - 720p, Premium1080p - 1080p|
| Maximální kmitočet snímků vstupního videa|60 snímků za sekundu|Standardní nebo Premium1080p - 30 snímků za sekundu|
| Vstupní protokoly|RTMP, fragmentovaný-MP4 (Plynulé streamování)|RTMP, fragmentovaný-MP4 (Plynulé streamování)|
| Price|Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na záložku "Živé video"|Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na záložku "Živé video"|
| Maximální doba běhu| 24 hodin x 365 dní, živé lineární | 24 hodin x 365 dní, živé lineární (náhled)|
| Schopnost projít vloženými daty titulků CEA 608/708|Ano|Ano|
| Možnost zapnout živý přepis|Ano|Ano|
| Podpora pro vkládání břidlic|Ne|Ne|
| Podpora pro signalizaci reklam y prostřednictvím rozhraní API| Ne|Ne|
| Podpora signalizace reklamy prostřednictvím in-pásmových zpráv SCTE-35|Ano|Ano|
| Schopnost zotavit se ze krátkých stánků v příspěvku krmiva|Ano|Částečné|
| Podpora nejednotných vstupních žvatláků|Ano|Ne – vstup musí mít pevnou dobu trvání GOP|
| Podpora pro vstup s proměnnou snímkovou frekvencí|Ano|Ne – vstup musí být pevný kmitočet snímků. Drobné variace jsou tolerovány, například při vysokých pohybových scénách. Zdroj příspěvku však nemůže přetažení kmitočet snímků (například na 15 snímků za sekundu).|
| Automatické vypnutí živé události při ztrátě vstupního informačního kanálu|Ne|Po 12 hodinách, pokud není spuštěn žádný LiveOutput|

## <a name="system-presets"></a>Přednastavení systému

Rozlišení a přenosové rychlosti obsažené ve výstupu z živého kodéru jsou určeny [předsetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Pokud používáte **standardní** živý kodér (LiveEventEncodingType.Standard), pak přednastavení *Default720p* určuje sadu dvojic rozlišení 6/přenosových rychlostí popsaných níže. V opačném případě, pokud používáte **premium1080p** živé kodér (LiveEventEncodingType.Premium1080p), pak *Default1080p* přednastavení určuje výstupní sadu dvojice rozlišení/přenosové rychlosti.

> [!NOTE]
> Přednastavení Default1080p nelze použít na živou událost, pokud byla nastavena pro standardní živé kódování - zobrazí se chyba. Pokud se pokusíte použít přednastavení Default720p na živý kodér Premium1080p, zobrazí se také chyba.

### <a name="output-video-streams-for-default720p"></a>Výstupní video streamy pro výchozí 720p

Pokud je příspěvek zdroje 720p nebo vyšší rozlišení, **Default720p** přednastavení bude kódovat krmivo do následujících 6 vrstev. V následující tabulce je bitrate v kbps, MaxFPS představuje maximální povolenou kmitočet snímků (v snímcích za sekundu), profil představuje použitý profil H.264.

| Datový tok | impulzu | Vlastnost Height | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoká |
| 2200 |960 |540 |30 |Vysoká |
| 1350 |704 |396 |30 |Vysoká |
| 850 |512 |288 |30 |Vysoká |
| 550 |384 |216 |30 |Vysoká |
| 200 |340 |192 |30 |Vysoká |

> [!NOTE]
> Pokud potřebujete přizpůsobit přednastavení živého kódování, otevřete lístek podpory prostřednictvím portálu Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, že existuje pouze jedna vrstva s rozlišením 720p a maximálně 6 vrstev celkem. Také určit, že požadujete přednastavení pro standard živé kodér.
> Specifické hodnoty přenosových rychlostí a rozlišení mohou být časem upraveny

### <a name="output-video-streams-for-default1080p"></a>Výstupní video streamy pro Výchozí1080p

Pokud je příspěvek zdroje 1080p rozlišení, **Default1080p** přednastavení bude kódovat krmivo do následujících 6 vrstev.

| Datový tok | impulzu | Vlastnost Height | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Vysoká |
| 3000 |1280 |720 |30 |Vysoká |
| 1600 |960 |540 |30 |Vysoká |
| 800 |640 |360 |30 |Vysoká |
| 400 |480 |270 |30 |Vysoká |
| 200 |320 |180 |30 |Vysoká |

> [!NOTE]
> Pokud potřebujete přizpůsobit přednastavení živého kódování, otevřete lístek podpory prostřednictvím portálu Azure Portal. Měli byste uvést tabulku požadovaných rozlišení a přenosových rychlostí. Ověřte, zda je pouze jedna vrstva v 1080p a maximálně 6 vrstev. Také určit, že požadujete přednastavení pro Premium1080p živé kodér.
> Konkrétní hodnoty přenosových rychlostí a rozlišení mohou být časem upraveny.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Výstupní zvukový datový proud pro výchozí 720p a Default1080p

Pro předvolby *Default720p* i *Default1080p* je zvuk kódován do stereo AAC-LC při 128 kbps. Vzorkovací frekvence se řídí vzorkovací frekvencí zvukové stopy v příspěvkovém kanálu.

## <a name="implicit-properties-of-the-live-encoder"></a>Implicitní vlastnosti živého kodéru

Předchozí část popisuje vlastnosti živého kodéru, které lze explicitně ovládat prostřednictvím přednastavení – například počet hladin, rozlišení a přenosové rychlosti. Tato část objasňuje implicitní vlastnosti.

### <a name="group-of-pictures-gop-duration"></a>Trvání skupiny obrázků (GOP)

Živý kodér sleduje strukturu [gop](https://en.wikipedia.org/wiki/Group_of_pictures) příspěvku krmiva - což znamená, že výstupní vrstvy budou mít stejnou dobu trvání GOP. Proto se doporučuje nakonfigurovat místní kodér k vytvoření příspěvku krmiva, který má pevnou dobu trvání GOP (obvykle 2 sekundy). Tím zajistíte, že odchozí streamy HLS a MPEG DASH ze služby mají také pevné doby trvání GOP. Většina zařízení pravděpodobně toleruje malé odchylky v trvání gop.

### <a name="frame-rate"></a>Kmitočet

Živý kodér také sleduje dobu trvání jednotlivých snímků videa v příspěvkovém kanálu - což znamená, že výstupní vrstvy budou mít snímky se stejnou dobou trvání. Proto se doporučuje nakonfigurovat místní kodér tak, aby vytvářel příspěvek, který má pevnou snímkovou frekvenci (maximálně 30 snímků za sekundu). Tím zajistíte, že odchozí proudy HLS a MPEG DASH ze služby mají také pevné doby trvání snímkových frekvencí. Malé rozdíly v snímkových frekvencích mohou být tolerovány většinou zařízení, ale neexistuje žádná záruka, že živý kodér vytvoří výstup, který bude hrát správně. Místní živý kodér by neměl upouštět rámečky (např. v podmínkách nízkého stavu baterie) nebo jakýmkoli způsobem měnit snímkovou frekvenci.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Rozlišení příspěvkových posuvů a výstupních vrstev

Živý kodér je nakonfigurován tak, aby se zabránilo převedení příspěvkového kanálu. V důsledku toho maximální rozlišení výstupních vrstev nepřesáhne rozlišení příspěvkového posuvu.

Pokud například odešlete příspěvek v 720p na live událost nakonfigurovanou pro živé kódování Default1080p, výstup bude mít pouze 5 vrstev, počínaje 720p při 3Mbps, což se sníží na 1080p při 200 kbps. Nebo pokud odešlete příspěvek krmiva na 360p do živé události nakonfigurované pro standardní živé kódování, výstup bude obsahovat 3 vrstvy (v rozlišení 288p, 216p a 192p). V případě degenerované, pokud odešlete příspěvek krmivo, řekněme 160x90 pixelů standardní živé kodér, výstup bude obsahovat jednu vrstvu na 160x90 rozlišení při stejném točivém toku jako příspěvku krmiva.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bitová rychlost příspěvku posuvu a výstupních vrstev

Živý kodér je nakonfigurován tak, aby dodržoval nastavení přenosové rychlosti v přednastavení, bez ohledu na datový tok příspěvku. V důsledku toho může přenosová rychlost výstupních vrstev překročit toto žpon. Pokud například odešlete příspěvek v rozlišení 720p při 1 Mb/s, výstupní vrstvy zůstanou stejné jako v [tabulce](live-event-types-comparison.md#output-video-streams-for-default720p) výše.

## <a name="next-steps"></a>Další kroky

[Přehled živého streamování](live-streaming-overview.md)
