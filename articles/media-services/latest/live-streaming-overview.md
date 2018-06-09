---
title: Přehled živé streamování využívající Azure Media Services | Microsoft Docs
description: Toto téma poskytuje přehled živé streamování využívající Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: b8c9375d8ad915200cbc8b2e1a62979fd1b7d179
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238263"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování s Azure Media Services v3

Při doručování živě streamovaných událostí pomocí služby Azure Media Services se běžně podílejí následující komponenty:

* Kamera používaná k vysílání události.
* Za provozu kodér videa, který převádí signály z kamery (nebo jiné zařízení, jako jsou přenosné počítače) pro datové proudy, které se odesílají do Media Services živé streamování služby. Signály může také zahrnovat inzerování SCTE 35 a Ad upozornění. 
* Služba Media Services živého streamování umožňuje ingestování, zobrazte náhled, balíčku, zaznamenejte, šifrování a všesměrového vysílání obsah k vašim zákazníkům nebo CDN pro další distribuci.

Tento článek poskytuje podrobnější přehled a zahrnuje diagramy hlavní součásti účastnící se živé streamování pomocí služby Media Services.

## <a name="overview-of-main-components"></a>Přehled hlavních součástí

Ve službě Media Services [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) je zodpovědná za zpracování obsahu živého streamování. LiveEvent poskytuje vstupní koncový bod (ingestovanou adresu URL), pak poskytnete místní kodér za provozu. LiveEvent přijímá živé vstupní datové proudy ze za provozu kodér ve formátu RTMP nebo technologie Smooth Streaming a zpřístupní se k vysílání datového proudu prostřednictvím jednoho nebo více [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints). A [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) vám umožňuje řídit publikování, nahrávání a nastavení okna formátu DVR, živého datového proudu. LiveEvent také poskytuje koncový bod preview (URL náhledu), který používáte pro zobrazení náhledu a ověřit před další zpracování a doručení datového proudu. 

Služba Media Services poskytuje **dynamické balení**, které umožňuje zobrazit náhled a všesměrovým vysíláním vašeho obsahu v MPEG DASH, HLS, technologie Smooth Streaming formátů streamování bez nutnosti ručně znovu zabalte do těchto formátů streamování. Můžete přehrát zpět se HLS, DASH nebo Smooth přehrávače kompatibilní. Můžete také použít [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) k testování datového proudu.

Služba Media Services umožňuje doručovat obsah dynamicky šifrovat (**dynamického šifrování**) s Advanced Encryption Standard (AES-128) nebo všechny tři systémů hlavní digitální rights management (DRM): Microsoft PlayReady Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.

V případě potřeby můžete také použít **dynamické filtrování**, které můžete použít k řízení počet sleduje, formátů, přenosových rychlostí, které jsou odeslaná do hráči. Služba Media Services podporuje také vkládání reklam.


## <a name="liveevent-types"></a>Typy LiveEvent

A [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) může mít jednu ze dvou typů: kódování v reálném čase a předávací. 

### <a name="live-encoding-with-media-services"></a>Live kódování pomocí služby Media Services

![Live kódování](./media/live-streaming/live-encoding.png)

Místní kodér za provozu odešle datový proud s jednou přenosovou rychlostí do LiveEvent, který má povolené kódování v reálném pomocí služby Media Services v jednom z těchto protokolů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). LiveEvent potom provede kódování v reálném čase příchozí zprávy jednou přenosovou rychlostí datový proud na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

Při vytváření tohoto typu LiveEvent, zadejte **základní** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Průchod

![Průchozí](./media/live-streaming/pass-through.png)

Průchozí je optimalizovaná pro živé datové proudy dlouho běžící nebo 24 x 7 lineární kódování v reálném čase pomocí místní kodér za provozu. Místní kodéru odešle více přenosovými rychlostmi **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) k LiveEvent, který je nakonfigurován pro **průchozí** doručení. **Průchozí** doručování nastává, když ingestované datové proudy prochází **LiveEvent**s bez dalšího zpracování. 

Průchozí LiveEvents může podporovat až 4 kB řešení a HEVC předávat při použití s technologie Smooth Streaming ingestování. 

Při vytváření tohoto typu LiveEvent, zadejte **žádné** (LiveEventEncodingType.None).

> [!NOTE]
> Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
> 

## <a name="liveevent-types-comparison"></a>Porovnání LiveEvent typy 

Následující tabulka porovnává funkce dva typy LiveEvent.

| Funkce | Průchozí LiveEvent | Základní LiveEvent |
| --- | --- | --- |
| Jednou přenosovou rychlostí vstup je zakódován do více přenosových rychlostí v cloudu |Ne |Ano |
| Maximální rozlišení, počet vrstev |4Kp30  |720p, 6 vrstev, 30 snímků za sekundu |
| Vstupní protokoly |RTMP funkce Smooth Streaming |RTMP funkce Smooth Streaming |
| Cena |Najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Live Video" |Najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximální doba běhu |Nepřetržitě |Nepřetržitě |
| Podpora pro vkládání slaty |Ne |Ano |
| Podpora ad signalizace prostřednictvím rozhraní API|Ne |Ano |
| Podpora ad signalizace prostřednictvím SCTE35 inband|Ano |Ano |
| Průchozí CEA 608/708 titulky |Ano |Ano |
| Možnost obnovení z stručný míst v příspěvku informačního kanálu |Ano |Ne (LiveEvent zahájíte slating po 6 + sekundách bez vstupní data)|
| Podpora pro vstupní GOPs neuniformní |Ano |Ne – vstup musí být vyřešeny 2 sekundu GOPs |
| Podpora pro vstup míra proměnné rámce |Ano |Ne – vstup je potřeba opravit obnovovací frekvence.<br/>Malé změny jsou například tolerovat během scény vysoké pohybu. Ale kodér nelze vyřadit do 10 snímků za sekundu. |
| Auto přístupnými z LiveEvent při vstupu kanálu dojde ke ztrátě |Ne |Po 12 hodinách, pokud neexistuje žádné LiveOutput systémem |

## <a name="liveevent-states"></a>LiveEvent stavy 

Aktuální stav LiveEvent. Možné hodnoty:

|Stav|Popis|
|---|---|
|**Byla zastavena**| Toto je počáteční stav LiveEvent po jeho vytvoření (Pokud byla vybrána automatické spuštění zadaný.) V tomto stavu dojde k žádné fakturace. V tomto stavu můžete aktualizovat vlastnosti LiveEvent, ale datový proud není povolen.|
|**Spouštění**| Bude spuštěn LiveEvent. V tomto stavu dojde k žádné fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, vrátí LiveEvent zastaveném stavu.|
|**Spuštění**| LiveEvent je schopen zpracování datových proudů za provozu. Nyní ji je fakturace využití. Je potřeba zastavit LiveEvent, aby se zabránilo další fakturace.|
|**Zastavení**| Probíhá zastavování LiveEvent. V tomto dočasném stavu dojde k žádné fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.|
|**Odstranění**| LiveEvent se odstraňuje. V tomto dočasném stavu dojde k žádné fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.|

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) vám umožňuje řídit publikování, nahrávání a nastavení okna formátu DVR, živého datového proudu. Je podobná tradičním médiím, kde kanál (LiveEvent) obsahuje nepřetržitý datový proud obsahu a program (LiveOutput) je vymezen na určité načasované události v této LiveEvent LiveEvent a LiveOutput relace.
Můžete určit počet hodin, které chcete uchovávat zaznamenaný obsah pro LiveOutput nastavením **ArchiveWindowLength** vlastnost. **ArchiveWindowLength** je časový interval trvání délka archivačního okna (digitální záznam videa nebo formátu DVR) ISO 8601. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. 

**ArchiveWindowLength** také určuje maximální počet časových klientů můžete hledat zpět v čase od aktuální živé pozice. LiveOutputs můžou běžet po určenou dobu a obsah, který se do délky okna nevejde je vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý LiveOutput je přidružen [Asset](https://docs.microsoft.com/rest/api/media/assets)a zaznamenává data do kontejneru v úložišti Azure, které jsou připojené k účtu Media Services. Chcete-li publikovat LiveOutput, musíte vytvořit [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

LiveEvent podporuje až tři současně spuštěné LiveOutputs, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Například vaše obchodní požadavek zaměřuje na vysílání 24 x 7 lineární kanál za provozu, ale chcete vytvořit "záznam" programy během dne nabízet zákazníkům jako obsahu na vyžádání pro opravný zobrazení.  V tomto scénáři je nejprve vytvořit primární LiveOutput, s krátkou archivačního okna 1 hodina nebo méně pro zákazníky nalaďte jako primární živý datový proud. By vytvořte StreamingLocator pro tento LiveOutput a publikování aplikace nebo web jako "Živé" informačního kanálu.  Jako informačního kanálu běží, můžete vytvořit druhý souběžných LiveOutput prostřednictvím kódu programu na začátku zobrazit (nebo 5 minut dříve zajistit některé popisovače později trim.) Tento druhý LiveOutput lze zastavit 5 minut po skončení program nebo událostí, a potom můžete vytvořit nové StreamingLocator publikovat tento program jako prostředek na vyžádání v katalogu vaší aplikace.  Tento postup můžete opakovat vícekrát pro další hranice program nebo upozorňuje, že chcete sdílet jako na vyžádání okamžitě, všechny při "Živé" kanálu z první LiveOutput nadále vysílání lineární informačního kanálu.  Kromě toho můžete využít výhod podpory dynamického filtru oříznout head a tail archivu z LiveOutput zavedena pro "překrývají bezpečnost" mezi programy a dosáhnout přesnější počáteční a koncové obsahu. Archivovaný obsah může být také odeslány do [transformace](https://docs.microsoft.com/rest/api/media/transforms) pro kódování nebo přesné subclipping rámce do několika formátů výstup má být použit jako syndikace k jiným službám.

## <a name="streamingendpoint"></a>StreamingEndpoint

Jakmile máte datový proud plyne do LiveEvent, můžete začít tak, že vytvoříte Asset, LiveOutput a StreamingLocator událost streamování. To bude archivu datového proudu a zpřístupní ji divákům prostřednictvím [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Při vytvoření účtu Media Services je výchozí koncový bod streamování přidána k vašemu účtu v zastaveném stavu. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamické šifrování, koncový bod streamování, ze kterého chcete Streamovat obsah musí být ve spuštěném stavu.

## <a name="billing"></a>Fakturace

LiveEvent začne fakturace také její stav přechází do "Spuštěný". Chcete-li zastavit LiveEvent z fakturace, budete muset zastavit LiveEvent.

> [!NOTE]
> Když **LiveEventEncodingType** na vaše [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) je nastaven na Basic Media Services automaticky přístupnými žádné LiveEvent, který je stále ve stavu "Spuštění" 12 hodin po vstupní kanál dojde ke ztrátě a neexistují žádné LiveOutputs je spuštěna. Ale bude stále se fakturuje čas, kdy LiveEvent byl ve stavu "Spuštění".
>

Následující tabulka ukazuje, jak LiveEvent stavy mapovány fakturace režimu.

| LiveEvent stavu | Je fakturace? |
| --- | --- |
| Spouštění |Ne (přechodný stav) |
| Spuštěno |ANO |
| Zastavování |Ne (přechodný stav) |
| Zastaveno |Ne |

## <a name="next-steps"></a>Další postup

[Živé streamování kurzu](stream-live-tutorial-with-api.md)
