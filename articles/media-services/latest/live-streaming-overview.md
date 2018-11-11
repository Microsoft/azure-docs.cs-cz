---
title: Přehled živého streamování využívajícího službu Azure Media Services | Dokumentace Microsoftu
description: Toto téma poskytuje přehled živého streamování využívajícího službu Azure Media Services v3.
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
ms.date: 11/08/2018
ms.author: juliako
ms.openlocfilehash: a4569505cb9a42f6682391a8b06725dea5e539dc
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344967"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování pomocí služby Azure Media Services v3

Při doručování živě streamovaných událostí pomocí Azure Media Services se běžně podílejí následující komponenty:

* Kamera používaná k vysílání události.
* Živé video encoder, který převádí signály z kamery (nebo jiné zařízení, jako jsou přenosné počítače) pro datové proudy, které se odesílají do služby živého streamování. Signály mohou zahrnovat také inzerování SCTE 35 a Ad pomůcky. 
* Služba Media Services Live Streaming vám umožňuje ingestovat, ve verzi preview, balení, záznamu, šifrování a všesměrového vysílání obsahu vašim zákazníkům nebo do sítě CDN pro další distribuci.

Tento článek poskytuje podrobný přehled a zahrnuje diagramy hlavní součásti účastnící se živé streamování pomocí služby Media Services.

## <a name="overview-of-main-components"></a>Přehled hlavních komponent

Ve službě Media Services zodpovídají za zpracování obsahu živého streamování události [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents). Poskytuje vstupní koncový bod Livestream (adresa URL ingestu), pak poskytnete místní kodér služby live Encoding. Livestream přijímat živé vstupní datové proudy z live encoder ve formátu RTMP nebo technologie Smooth Streaming a zpřístupňuje je prostřednictvím jednoho nebo více datových proudů [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints). A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) vám umožňuje řídit publikování, nahrávání a nastavení okna DVR, živého datového proudu. Livestream také poskytuje koncový bod ve verzi preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením. 

Služba Media Services poskytuje **dynamické balení**, která umožňuje ve verzi preview a váš obsah MPEG DASH, HLS, technologie Smooth Streaming pro vysílání datových proudů formátů, aniž byste museli ručně znovu zabalit do těchto formátů streamování. Můžete přehrávat pomocí libovolné HLS, DASH nebo Smooth kompatibilní přehrávače. Můžete také použít [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) k testování datového proudu.

Služba Media Services umožňuje doručovat obsah zašifrovaný dynamicky (**dynamického šifrování**) s Advanced Encryption Standard (AES-128) nebo některou z systémy pro správu (DRM) tři hlavní digitálních práv: Microsoft PlayReady Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.

V případě potřeby můžete také použít **dynamické filtrování**, které je možné řídit počet stop, formátů, přenosových rychlostí, který společností zašleme hráči. Služba Media Services také podporuje vkládání reklam.

### <a name="new-live-encoding-improvements"></a>Nová vylepšení migrace za provozu kódování

Následující nová vylepšení byly dokončeny v nejnovější verzi.

- Nový režim s nízkou latencí. Další informace najdete v tématu [latence](#latency).
- (Zvýšení stability a další podporu zdrojového kodér) Vylepšená podpora RTMP ve službě.
- Ingestování RTMPS zabezpečené.

    Při vytváření Livestream získáte 4 ingestované adresy URL. Ingestování 4 adresy URL jsou téměř identické, mít stejný token streamování (AppId), jenom část čísla portu se liší. Dva z adres URL jsou primární a záložní pro RTMPS.   
- Podpora překódování 24 hodin. 
- Vylepšená podpora ad signalizace v RTMP prostřednictvím SCTE35.

## <a name="liveevent-types"></a>Typy Livestream

A [Livestream](https://docs.microsoft.com/rest/api/media/liveevents) může být jeden ze dvou typů: kódování v reálném čase a předávací. 

### <a name="live-encoding-with-media-services"></a>Živé kódování pomocí Media Services

![živé kódování](./media/live-streaming/live-encoding.png)

Místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí Livestream, který má povolené provádět živé kódování pomocí Media Services v jednom z těchto protokolů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Livestream potom provede kódování v reálném čase z příchozí datový proud s jednou přenosovou rychlostí na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

Při vytváření tohoto typu Livestream, zadejte **základní** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Průchod

![Předávací](./media/live-streaming/pass-through.png)

Předávací je optimalizovaný pro dlouho běžící živé streamy nebo 24 x 7 lineární kódování v reálném čase pomocí místní kodér služby live Encoding. Místní kodér odešle s více přenosovými rychlostmi **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) k Livestream, který je nakonfigurovaný pro **předávací** doručování. **Předávací** doručování nastává, když ingestované datové proudy prochází **Livestream**bez dalšího zpracování. 

Předávací LiveEvents může podporovat až k rozlišení 4K a HEVC procházet při použití s technologie Smooth Streaming ingestování protokolu. 

Při vytváření tohoto typu Livestream, zadejte **žádný** (LiveEventEncodingType.None).

> [!NOTE]
> Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
> 

## <a name="liveevent-types-comparison"></a>Porovnání typů Livestream 

Následující tabulka obsahuje porovnání funkcí těchto dvou typů Livestream.

| Funkce | Předávací Livestream | Standardní Livestream |
| --- | --- | --- |
| Vstup s jednou přenosovou rychlostí je zakódován do více přenosových rychlostí v cloudu |Ne |Ano |
| Maximální rozlišení, počet vrstev |4Kp30  |720p, 6 vrstvy, 30 snímků za sekundu |
| Vstupní protokoly |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Cena |Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Živé Video" |Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximální doba běhu |Nepřetržitě |Nepřetržitě |
| Podpora vkládaní slaty |Ne |Ano |
| Podpora ad signalizace přes rozhraní API|Ne |Ano |
| Podpora ad signalizace prostřednictvím SCTE35 inband|Ano |Ano |
| Předávací titulky CEA 608/708 |Ano |Ano |
| Schopnost zotavení z stručný přepínání v příspěvku informačního kanálu |Ano |Ne (Livestream se začne slating po sekundách 6 bez vstupních dat)|
| Podpora pro vstupní GOPs nerovnoměrné |Ano |Ne – vstup musí být nastaven GOPs 2 s |
| Podpora pro vstup míra proměnné rámce |Ano |Ne – vstup musí být stanovena snímkovou frekvenci.<br/>Malé změny jsou například tolerovat při vysoké pohybu scény. Ale kodér nelze umístit do 10 snímků za sekundu. |
| Dojde ke ztrátě Auto přístupnými z Livestream při zadání informačního kanálu |Ne |Po 12 hodinách, pokud neexistuje žádný LiveOutput spuštění |

## <a name="liveevent-states"></a>Stavy Livestream 

Aktuální stav Livestream. Možné hodnoty:

|Stav|Popis|
|---|---|
|**Zastavení**| Toto je počáteční stav Livestream po jeho vytvoření (Pokud byla vybrána autostart zadat.) Vyvolá se v tomto stavu bez fakturace. V tomto stavu je možné aktualizovat vlastnosti Livestream ale streamování není povolené.|
|**Spuštění**| Livestream se spouští. Vyvolá se v tomto stavu bez fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě Livestream se vrátí do stavu Zastaveno.|
|**Spuštění**| Livestream se dokáže zpracovávat živé streamy. To je nyní fakturaci využití. Je nutné zastavit Livestream, aby se zabránilo další fakturace.|
|**Zastavení**| Livestream se zastavuje. Vyvolá se v tomto přechodném stavu bez fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.|
|**Odstraňuje se**| Livestream se odstraňuje. Vyvolá se v tomto přechodném stavu bez fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.|

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) vám umožňuje řídit publikování, nahrávání a nastavení okna DVR, živého datového proudu. Je podobná tradičním médiím, kde (Livestream) kanál obsahuje nepřetržitý datový proud obsahu a program (LiveOutput) je vymezen na určité načasované události v této Livestream relace Livestream a LiveOutput.
Můžete určit počet hodin, které chcete uchovávat zaznamenaný obsah LiveOutput nastavením **ArchiveWindowLength** vlastnost. **ArchiveWindowLength** je formátu ISO 8601 časový interval platnosti délka okna archivu (digitální Video záznam nebo formátu DVR). Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. 

**ArchiveWindowLength** také určuje maximální počet časových klientů můžete posunout zpátky v čase od aktuální živé pozice. LiveOutputs můžou běžet po určenou dobu, obsah, který se do délky okna nevejde se bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý LiveOutput je přidružený [Asset](https://docs.microsoft.com/rest/api/media/assets)a zaznamenává data do kontejneru v úložišti Azure, které jsou připojené k účtu Media Services. Pokud chcete publikovat LiveOutput, musíte vytvořit [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Livestream podporuje až tři současně spuštěné LiveOutputs, takže můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Například je vaše firemní požadavky vysílání 24 x 7 živé lineární kanál, ale chcete vytvořit "záznamy" programů v průběhu dne nabídnout zákazníkům jako obsah na vyžádání pro zobrazení můžete projít.  V tomto scénáři je nejprve vytvořit primární LiveOutput s krátkou archivačního okna 1 hodina pro zákazníky Nalaďte si jako primární živého datového proudu. By vytvořit StreamingLocator pro tento LiveOutput a publikovat do aplikace nebo webu jako kanál "Live".  Kdy je spuštěná informačního kanálu, můžete programově vytvořit druhý souběžných LiveOutput na začátku zobrazit (nebo 5 minut, než již v rané fázi poskytují některé popisovače mají být odebrány později.) Tento druhý LiveOutput lze zastavit 5 minut po skončení programu nebo události a pak můžete vytvořit nové StreamingLocator publikovat tento program jako prostředek na vyžádání v katalogu vaší aplikace.  Můžete tento postup opakujte více než jednou pro ostatní meze programů nebo upozorňuje, že chcete sdílet jako na vyžádání, okamžitě, všechny při "Live" informační kanál z první LiveOutput nadále vysílání lineární kanál.  Kromě toho můžete využít výhod podpory dynamického filtru oříznout head a konec archivu z LiveOutput zavedena pro "překrytí safety" mezi aplikacemi a zajištění přesnější začátek a konec obsahu. Archivovaný obsah můžete také odeslat do katalogu [transformace](https://docs.microsoft.com/rest/api/media/transforms) pro kódování nebo přesné oříznutím rámce do více formátů výstupu má být použit jako syndikace k jiným službám.

## <a name="streamingendpoint"></a>StreamingEndpoint

Jakmile se datový proud přenáší do události LiveEvent, můžete zahájit událost streamování vytvořením prostředku, výstupu LiveOutput a lokátoru streamování StreamingLocator. To bude archivovat datového proudu a zpřístupní ji se divákům prostřednictvím [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Po vytvoření účtu Media Services je ke svému účtu v zastaveném stavu přidá výchozí koncový bod streamování. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete Streamovat obsah musí být ve spuštěném stavu.

## <a name="latency"></a>Latence

Tato část popisuje výsledky, které se zobrazí při použití nastavení s nízkou latencí a různé přehrávače. Výsledky lišit v závislosti na latenci sítě CDN.

Pokud chcete využít novou funkci LowLatency, můžete nastavit **StreamOptionsFlag** k **LowLatency** na Livestream. Po vytvoření a spuštění je datový proud, můžete [Azure Media Player](http://ampdemo.azureedge.net/) (AMP) ukázka stránku a nastavit možnosti přehrávání použít "S nízkou latencí heuristiky profil".

### <a name="pass-through-liveevents"></a>Předávací LiveEvents

||2S GOP nízká latence povoleno|1s GOP nízká latence povoleno|
|---|---|---|
|POMLČKA v knihovně AMP|10s|8S|
|HLS v přehrávači nativní aplikace pro iOS|14s|10s|
|HLS. JS v přehrávači Mixer|30 s|16s|

## <a name="billing"></a>Fakturace

Livestream začne fakturace jako jeho stav změní na "Spuštěno". Pokud chcete zastavit Livestream z fakturace, je nutné zastavit Livestream.

> [!NOTE]
> Když **LiveEventEncodingType** na vaše [Livestream](https://docs.microsoft.com/rest/api/media/liveevents) je nastavena pro Basic, Media Services se automaticky přístupnými jakékoli Livestream, který je stále ve stavu "Spuštěno" po ztratí vstupní kanál a existuje 12 hodin žádné LiveOutputs spuštěná. Ale můžete pořád účtovat čas, kdy Livestream byl ve stavu "Spuštěno".
>

Následující tabulka ukazuje, jak Livestream stavů k režimu fakturace.

| Livestream stavu | Je to fakturace? |
| --- | --- |
| Spouštění |Ne (přechodný stav) |
| Spuštěno |ANO |
| Zastavování |Ne (přechodný stav) |
| Zastaveno |Ne |

## <a name="next-steps"></a>Další postup

[Živé streamování kurz](stream-live-tutorial-with-api.md)
