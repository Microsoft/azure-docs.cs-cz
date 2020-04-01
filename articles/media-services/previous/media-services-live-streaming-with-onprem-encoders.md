---
title: Streamujte živě s místními kodéry, které vytvářejí datové proudy s více přenosovými rychlostmi – Azure | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak nastavit kanál, který přijímá živý datový proud s více přenosovým poměrem z místního kodéru.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c0d19d68d016a47762fb5d2646ea6ccf74d3ef75
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476551"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Práce s kanály, které přijímají vícepřenosový živý přenos z místních kodérů

> [!NOTE]
> května 2018 již živé kanály nebudou podporovat protokol ingestestování datového proudu rtp/MPEG-2. Migrujte z protokolů s ingestováním RTP/MPEG-2 do RTMP nebo fragmentovaných protokolů s ingestováním MP4 (Smooth Streaming).

## <a name="overview"></a>Přehled
Ve službě Azure Media Services *kanál* představuje kanál pro zpracování obsahu živého streamování. Kanál přijímá živé vstupní proudy jedním ze dvou způsobů:

* Místní živý kodér odesílá vícebitový datový proud RTMP nebo plynulého streamování (fragmentovaný protokol MP4) do kanálu, který není povolen pro živé kódování pomocí služby Media Services. Pozůstalé datové proudy procházejí kanály bez dalšího zpracování. Tato metoda se nazývá *předávací*. Živý kodér může také odeslat datový proud s jedním přenosovým tokem do kanálu, který není povolen pro živé kódování, ale nedoporučujeme to. Služba Media Services poskytuje datový proud zákazníkům, kteří o něj požádají.

  > [!NOTE]
  > Použití metody průchozí je nejekonomičtější způsob, jak provést živé vysílání.


* Místní živý kodér odešle datový proud s jedním datovým proudem do kanálu, který je povolen k provádění živého kódování se službou Media Services v jednom z následujících formátů: RTMP nebo Plynulé streamování (fragmentované MP4). Kanál pak provádí živé kódování příchozího datového proudu s jedním přenosovým tokem do datového proudu videa s více přenosovou rychlostí (adaptivní). Služba Media Services poskytuje datový proud zákazníkům, kteří o něj požádají.

Počínaje vydáním služby Media Services 2.10 můžete při vytváření kanálu určit, jak má kanál přijímat vstupní datový proud. Můžete také určit, zda má kanál provádět živé kódování datového proudu. Máte dvě možnosti:

* **Předat**: Zadejte tuto hodnotu, pokud plánujete použít místní živý kodér, který má datový proud s více přenosovými rychlostmi (předávací datový proud) jako výstup. V tomto případě příchozí datový proud prochází do výstupu bez kódování. Toto je chování kanálu před vydáním 2.10. Tento článek obsahuje podrobnosti o práci s kanály tohoto typu.
* **Živé kódování**: Tuto hodnotu zvolte, pokud chcete pomocí mediálních služeb kódovat živý datový proud s jedním přenosovým tokem do datového proudu s více přenosovou rychlostí. Za ponechání kanálu živého kódování ve **spuštěném** stavu se účtují poplatky za fakturaci. Doporučujeme, abyste po dokončení živého streamování okamžitě zastavili spuštěné kanály, abyste se vyhnuli dalším hodinovým poplatkům. Služba Media Services poskytuje datový proud zákazníkům, kteří o něj požádají.

> [!NOTE]
> Tento článek popisuje atributy kanálů, které nejsou povoleny k provádění živého kódování. Informace o práci s kanály, které jsou povolené k provádění živého kódování, najdete v tématu [Živé streamování pomocí Služby Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).
>
>Informace o doporučených místních kodéry naleznete [v tématu Doporučeno v místním kodérech](media-services-recommended-encoders.md).

Následující diagram představuje pracovní postup živého streamování, který používá místní živý kodér k tomu, aby jako výstup měl datové proudy RTMP s více přenosovými rychlostmi nebo fragmentace mp4 (plynulý datový proud).

![Živý pracovní postup][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Běžný scénář živého vysílání
Následující kroky popisují úlohy spojené s vytvářením běžných aplikací živého streamování.

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte místní živý kodér, který má jako výstup datový proud RTMP nebo fragmentovaného mp4 (Plynulé streamování). Další informace najdete v článku [Podpora RTMP ve službě Azure Media Services a kodéry pro kódování v reálném čase](https://go.microsoft.com/fwlink/?LinkId=532824).

    Tento krok můžete provést také po vytvoření kanálu.
2. Vytvoření a spuštění kanálu.

3. Načíst adresu URL ingestování kanálu.

    Živý kodér používá ingestující adresu URL k odeslání datového proudu do kanálu.
4. Načtěte adresu URL náhledu kanálu.

    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program.

    Při použití portálu Azure, vytvoření programu také vytvoří prostředek.

    Při použití sady .NET SDK nebo REST je třeba vytvořit datový zdroj a určit, zda chcete tento datový zdroj použít při vytváření programu.
6. Publikujte datový zdroj, který je přidružen k programu.   

    >[!NOTE]
    >Při vytvoření účtu Azure Media Services se do vašeho účtu ve stavu **Zastaveno** přidá výchozí koncový bod **streamování.** Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**.

7. Až budete připraveni začít streamovat a archivovat, spusťte program.

8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního proudu.

9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

10. Odstraňte program (a volitelně odstraňte datový zdroj).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Popis kanálu a jeho souvisejících součástí
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Konfigurace vstupu kanálu (ingestování)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Ingestování protokolu streamování
Služba Media Services podporuje ingestování živých informačních kanálů pomocí fragmentovaných mp4 s více přenosovými rychlostmi a vícebitového protokolu RTMP jako protokolů streamování. Když je vybrán protokol streamování instika RTMP, vytvoří se pro kanál dva koncové body ingestování (vstup):

* **Primární adresa URL**: Určuje plně kvalifikovanou adresu URL primárního koncového bodu ingestování RTMP kanálu.
* **Sekundární adresa URL** (volitelné): Určuje plně kvalifikovanou adresu URL sekundárního koncového bodu ingestování RTMP kanálu.

Sekundární adresu URL použijte, pokud chcete zlepšit odolnost a odolnost proti chybám datového proudu ingestování (stejně jako převzetí služeb při selhání kodéru a odolnost proti chybám), zejména pro následující scénáře:

- Dvojité zatlačení jednoho kodéru na primární i sekundární adresy URL:

    Hlavním účelem tohoto scénáře je poskytnout větší odolnost proti kolísání sítě a nervozita. Některé rtmp kodéry nezvládnou síťové odpojení dobře. Dojde-li k odpojení sítě, může kodér zastavit kódování a potom neodesílat data ve vyrovnávací paměti, když dojde k opětovnému připojení. To způsobí nespojitosti a ztrátu dat. K odpojení sítě může dojít z důvodu chybné sítě nebo údržby na straně Azure. Primární/sekundární adresy URL snižují problémy se sítí a poskytují řízený proces upgradu. Pokaždé, když dojde k plánovanému odpojení sítě, spravuje služba Media Services primární a sekundární odpojení a poskytuje zpožděné odpojení mezi těmito dvěma. Kodéry pak mají čas, aby odesílání dat a znovu připojit. Pořadí odpojení může být náhodné, ale vždy bude existovat zpoždění mezi primárními / sekundárními nebo sekundárními / primárními adresami URL. V tomto scénáři kodér je stále jediný bod selhání.

- Více kodéry, přičemž každý kodér tlačí na vyhrazený bod:

    Tento scénář poskytuje kodér a ingestuce redundance. V tomto scénáři kodér1 odešle na primární adresu URL a kodér2 odešle na sekundární adresu URL. Když kodér selže, druhý kodér může pokračovat v odesílání dat. Redundanci dat lze zachovat, protože služba Media Services současně neodpojuje primární a sekundární adresy URL. Tento scénář předpokládá, že kodéry jsou časově synchronizovány a poskytují přesně stejná data.  

- Více kodéry double-tlačí na primární i sekundární adresy URL:

    V tomto scénáři oba kodéry nabízená data na primární a sekundární adresy URL. To poskytuje nejlepší spolehlivost a odolnost proti chybám, stejně jako redundanci dat. Tento scénář může tolerovat selhání kodéru i odpojení, i když jeden kodér přestane fungovat. Předpokládá, že kodéry jsou časově synchronizovány a poskytují přesně stejná data.  

Informace o živých kodérech RTMP najdete v [tématu Podpora RTMP služby Azure Media Services a Živé kodéry](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Ingestování adres URL (koncové body)
Kanál poskytuje vstupní koncový bod (ingestovat URL), který zadáte v živém kodéru, takže kodér může tlačit datové proudy do vašich kanálů.   

Při vytváření kanálu můžete získat ingestující adresy URL. Chcete-li získat tyto adresy URL, kanál nemusí být ve **stavu Spuštěno.** Až budete připraveni začít s odesíláním dat do kanálu, kanál musí být ve **stavu Spuštěno.** Po zahájení příjem dat kanálu můžete zobrazit náhled datového proudu prostřednictvím adresy URL náhledu.

Máte možnost ingestování fragmentovaného živého datového proudu MP4 (Smooth Streaming) přes připojení TLS. Chcete-li ingestovat přes TLS, nezapomeňte aktualizovat ingestující adresu URL na HTTPS. V současné době nelze ingestovat RTMP přes TLS.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Interval klíčových snímků
Pokud používáte místní živý kodér ke generování datového proudu s více přenosovými rychlostmi, interval klíčových snímků určuje dobu trvání skupiny obrázků (GOP), jak ji používá tento externí kodér. Poté, co kanál obdrží tento příchozí datový proud, můžete dodávat živý přenos do aplikací pro přehrávání klientů v některém z následujících formátů: Plynulé streamování, dynamické adaptivní streamování přes HTTP (DASH) a živé streamování HTTP (HLS). Když provádíte živé vysílání, HLS je vždy dynamicky zabaleno. Ve výchozím nastavení media services automaticky vypočítá poměr balení segmentu HLS (fragmenty na segment) na základě intervalu klíčových snímků, který je přijat z živého kodéru.

V následující tabulce je uvedeno, jak se počítá doba trvání segmentu:

| Interval klíčových snímků | Poměr balení segmentu HLS (FragmentsPerSegment) | Příklad |
| --- | --- | --- |
| Méně než nebo rovno 3 sekundy |3:1 |Pokud keyframeinterval (nebo GOP) je 2 sekundy, výchozí segment HLS obalový poměr je 3 až 1. Tím se vytvoří 6sekundový segment HLS. |
| 3 až 5 sekund |2:1 |Pokud keyframeinterval (nebo GOP) je 4 sekundy, výchozí segment HLS obalový poměr je 2 až 1. Tím se vytvoří 8sekundový segment HLS. |
| Větší než 5 sekund |1:1 |Pokud keyframeinterval (nebo GOP) je 6 sekund, výchozí poměr balení segmentu HLS je 1 až 1. Tím se vytvoří 6sekundový segment HLS. |

Poměr fragmentů za segment můžete změnit konfigurací výstupu kanálu a nastavením FragmentsPerSegment na ChannelOutputHls.

Hodnotu intervalu klíčových snímků můžete také změnit nastavením vlastnosti KeyFrameInterval na channelinput. Pokud explicitně nastavíte KeyFrameInterval, poměr balení segmentu HLS FragmentsPerSegment se vypočítá pomocí pravidel popsaných výše.  

Pokud explicitně nastavíte keyframeinterval a fragmentyPerSegment, media services používá hodnoty, které jste nastavili.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které mohou publikovat video na tomto kanálu. Povolenou adresu IP lze zadat jako jednu z následujících adres:

* Jedna IP adresa (například 10.0.0.1)
* Oblast IP adres, která používá adresu IP a masku podsítě CIDR (například 10.0.0.1/22)
* Oblast IP, která používá adresu IP a tečkovanou masku desítkové podsítě (například 10.0.0.1(255.255.252.0))

Pokud nejsou zadány žádné adresy IP a neexistuje žádná definice pravidla, není povolena žádná adresa IP. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

### <a name="channel-preview"></a>Náhled kanálu
#### <a name="preview-urls"></a>Náhled adres URL
Kanály poskytují koncový bod náhledu (adresu URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL náhledu. Chcete-li získat adresu URL, kanál nemusí být ve **stavu Spuštěno.** Po spuštění kanálu můžete zobrazit náhled datového proudu.

V současné době může být stream náhledu dodáván pouze ve fragmentovaném formátu MP4 (Smooth Streaming), bez ohledu na zadaný typ vstupu. K testování plynulého datového proudu můžete použít přehrávač [Sledování stavu vyhlazení datových](https://playready.directtaps.net/smoothstreaming/) proudů. K zobrazení streamu můžete taky použít přehrávač, který je hostovaný na webu Azure Portal.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které se mohou připojit ke koncovému bodu náhledu. Pokud nejsou zadány žádné adresy IP, je povolena libovolná adresa IP. Povolenou adresu IP lze zadat jako jednu z následujících adres:

* Jedna IP adresa (například 10.0.0.1)
* Oblast IP adres, která používá adresu IP a masku podsítě CIDR (například 10.0.0.1/22)
* Oblast IP, která používá adresu IP a tečkovanou masku desítkové podsítě (například 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Výstup kanálu
Informace o výstupu kanálu naleznete v části [Interval klíčových snímků.](#keyframe_interval)

### <a name="channel-managed-programs"></a>Programy spravované kanálem
Kanál je přidružen k programům, které můžete použít k řízení publikování a ukládání segmentů v živém přenosu. Kanály spravují programy. Vztah kanálu a programu je podobný tradičním médiím, kde kanál má konstantní proud obsahu a program je vymezen na určitou časovou událost v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka archivačního okna také určuje maximální počet času, po který mohou klienti hledat zpět v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružen k datovému zdroji, který ukládá streamovaný obsah. Prostředek je mapován na kontejner blob bloku v účtu úložiště Azure a soubory v datovém zdroji jsou uloženy jako objekty BLOB v tomto kontejneru. Chcete-li publikovat program tak, aby zákazníci mohli zobrazit datový proud, musíte vytvořit lokátor OnDemand pro přidružený datový zdroj. Tento lokátor můžete použít k vytvoření adresy URL streamování, kterou můžete poskytnout svým klientům.

Kanál podporuje až tři souběžně spuštěné programy, takže můžete vytvořit více archivů stejného příchozího datového proudu. Podle potřeby můžete publikovat a archivovat různé části události. Představte si například, že vaším obchodním požadavkem je archivovat 6 hodin programu, ale vysílat pouze posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program je nastaven na archivaci šesti hodin události, ale program není publikován. Druhý program je nastaven na archivaci po dobu 10 minut a tento program je publikován.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte nový program pro každou událost. Až budete připraveni začít streamovat a archivovat, spusťte program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

Chcete-li odstranit archivovaný obsah, zastavte a odstraňte program a odstraňte přidružený datový zdroj. Datový zdroj nelze odstranit, pokud jej program používá. Program musí být nejprve odstraněn.

I po zastavení a odstranění programu mohou uživatelé streamovat archivovaný obsah jako video na vyžádání, dokud datový zdroj neodstraníte. Pokud chcete archivovaný obsah zachovat, ale nemáte ho k dispozici pro streamování, odstraňte lokátor streamování.

## <a name="channel-states-and-billing"></a><a id="states"></a>Stavy kanálů a fakturace
Možné hodnoty pro aktuální stav kanálu zahrnují:

* **Zastaveno**: Toto je počáteční stav kanálu po jeho vytvoření. V tomto stavu lze aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spuštění**: Kanál se spouští. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu **Zastaveno.**
* **Spuštění**: Kanál může zpracovávat živé přenosy.
* **Zastavení**: Kanál je zastaven. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování.
* **Odstraňování**: Kanál se odstraňuje. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování.

Následující tabulka ukazuje, jak se stavy kanálů mapují do fakturačního režimu.

| Stav kanálu | Indikátory portálového uj., | Účtovány? |
| --- | --- | --- |
| **Spuštění** |**Spuštění** |Ne (přechodný stav) |
| **Spuštěno** |**Připraveno** (bez spuštěné programy)<p><p>– nebo –<p>**Streamování** (alespoň jeden spuštěný program) |Ano |
| **Zastavování** |**Zastavování** |Ne (přechodný stav) |
| **Zastaveno** |**Zastaveno** |Ne |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Skryté titulky a vkládání reklam
V následující tabulce jsou uvedeny podporované standardy pro skryté titulky a vkládání reklam.

| Standard | Poznámky |
| --- | --- |
| CEA-708 a EIA-608 (708/608) |CEA-708 a EIA-608 jsou standardy s uzavřenými titulky pro Spojené státy a Kanadu.<p><p>V současné době titulky je podporována pouze v případě, že provádí v kódované vstupní datový proud. Musíte použít kodér živých médií, který může vložit titulky 608 nebo 708 do kódovaného datového proudu odeslaného do služby Media Services. Mediální služby doručují obsah s vloženými titulky divákům. |
| TTML uvnitř .ismt (Plynulé streamování textových stop) |Dynamické balení mediálních služeb umožňuje klientům streamovat obsah v libovolném z následujících formátů: DASH, HLS nebo Smooth Streaming. Pokud však ingestujete fragmentované MP4 (Plynulé streamování) s titulky uvnitř .ismt (Stopy textu plynulé streamování), můžete datový proud doručit pouze klientům plynulého streamování. |
| SCTE-35 |SCTE-35 je digitální signalizační systém, který se používá k cue reklamní vložení. Downstream přijímače používají signál splice reklamy do proudu pro přidělený čas. SCTE-35 musí být odeslán jako řídká stopa ve vstupním proudu.<p><p>V současné době je jediným podporovaným formátem vstupního datového proudu, který přenáší signály reklam, fragmentovaný formát MP4 (Plynulé streamování). Jediným podporovaným výstupním formátem je také plynulé streamování. |

## <a name="considerations"></a><a id="considerations"></a>Požadavky
Pokud k odeslání datového proudu s více přenosovými rychlostmi do kanálu používáte místní živý kodér, platí následující omezení:

* Ujistěte se, že máte dostatek volného připojení k Internetu k odesílání dat do ingestujících bodů.
* Použití sekundární ingestující adresy URL vyžaduje další šířku pásma.
* Příchozí datový proud s více přenosovou rychlostí může mít maximálně 10 úrovní kvality videa (vrstev) a maximálně 5 zvukových stop.
* Nejvyšší průměrný přenosový tok pro libovolnou úroveň kvality videa by měl být nižší než 10 Mb/s.
* Agregát průměrných přenosových rychlostí pro všechny video a audio streamy by měl být nižší než 25 Mb/s.
* Vstupní protokol nelze změnit, pokud je spuštěn kanál nebo přidružené programy. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* V kanálu můžete ingestovat jeden přenosový tok. Ale protože kanál nezpracovává datový proud, klientské aplikace také obdrží jeden datový proud datového toku. (Tuto možnost nedoporučujeme.)

Zde jsou další aspekty týkající se práce s kanály a souvisejícími součástmi:

* Pokaždé, když překonfigurujete živý kodér, zavolejte metodu **Reset** v kanálu. Před resetováním kanálu je třeba program zastavit. Po obnovení kanálu restartujte program.

  > [!NOTE]
  > Po restartování programu je třeba jej přidružit k novému datovému zdroji a vytvořit nový lokátor. 
  
* Kanál lze zastavit pouze v případě, že je ve stavu **Spuštěno** a všechny programy v kanálu byly zastaveny.
* Ve výchozím nastavení můžete do účtu Mediálních služeb přidat pouze pět kanálů. Další informace naleznete v [tématu Kvóty a omezení](media-services-quotas-and-limitations.md).
* Bude se vám účtovat pouze v případě, že je kanál ve stavu **Spuštěno.** Další informace najdete v části [Stavy kanálu a fakturace.](media-services-live-streaming-with-onprem-encoders.md#states)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Váš názor
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doporučujese na místních kodéry](media-services-recommended-encoders.md)

[Azure Media Services fragmentované MP4 žije ingestestovat specifikace](../media-services-fmp4-live-ingest-overview.md)

[Přehled a běžné scénáře Azure Media Services](media-services-overview.md)

[Koncepty mediálních služeb](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
