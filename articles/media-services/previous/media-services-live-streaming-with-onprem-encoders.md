---
title: Datový proud za provozu s místními kodéry, které vytvářejí proudy s více přenosovými rychlostmi - Azure | Microsoft Docs
description: 'Toto téma popisuje, jak nastavit kanál, který přijímá živý datový proud s více přenosovými rychlostmi z místní kodér. Datový proud mohou být zajišťovány pak pro klientské aplikace přehrávání přes jeden nebo více koncových bodů streamování, pomocí jedné z následujících protokolů adaptivní streamování: HLS, technologie Smooth Streaming, čárka.'
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/12/2017
ms.author: cenkd;juliako
ms.openlocfilehash: b01e4498e63ab989b0b78246636cba50ed4f82d6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Živé streamování s místními kodéry, které vytvářejí proudy s více přenosovými rychlostmi

> [!NOTE]
> Od 12 může 2018 za provozu kanály se už podporu datový proud RTP/MPEG-2 transport ingestování. Proveďte prosím migraci z RTP/MPEG-2 RTMP nebo fragmentovaných MP4 (technologie Smooth Streaming) ingestování protokoly.

## <a name="overview"></a>Přehled
Ve službě Azure Media Services *kanál* představuje cestu pro zpracování datových proudů za provozu obsahu. Kanál, který obdrží živé vstupní datové proudy v jednom ze dvou způsobů:

* Místní zasílá za provozu kodér s více přenosovými rychlostmi RTMP nebo technologie Smooth Streaming (fragmentovaný MP4) datového proudu do kanálu, který není povoleno provádět kódování v reálném čase pomocí služby Media Services. Ingestované datové proudy prochází kanály bez dalšího zpracování. Tato metoda je volána *průchozí*. Za provozu encoder můžete také odeslat datový proud s jednou přenosovou rychlostí do kanálu, který není povolen pro kódování v reálném čase, ale který nedoporučujeme. Služba Media Services doručí datový proud zákazníkům, kteří ji.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě Streamovat.


* Místní kodér za provozu odešle datový proud s jednou přenosovou rychlostí do kanálu, který je povoleno provádět kódování v reálném čase pomocí služby Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Kanál potom provede kódování v reálném čase příchozího datového proudu jednou přenosovou rychlostí do datového proudu více přenosovými rychlostmi (adaptivní) videa. Služba Media Services doručí datový proud zákazníkům, kteří ji.

Od verze Media Services 2.10, když vytvoříte kanál, můžete určit, jak se mají kanál pro příjem vstupního datového proudu. Můžete také určete, jestli má kanál provést kódování v reálném čase z datového proudu. Máte dvě možnosti:

* **Předávání**: Pokud máte v plánu používat místní kodér za provozu, který má více přenosovými rychlostmi datový proud (průchozí datový proud) jako výstup tuto hodnotu zadat. V takovém případě příchozího datového proudu prochází k výstupu bez jakékoli kódování. Toto je chování kanál před vydáním 2.10. Tento článek obsahuje údaje o práci s kanály tohoto typu.
* **Live Encoding**: Zvolte tuto hodnotu, pokud máte v úmyslu použít Media Services ke kódování živého datového proudu jednou přenosovou rychlostí na datový proud s více přenosovými rychlostmi. Opouštění živého kódování kanálu v **systémem** stavu způsobuje fakturace poplatků. Doporučujeme, abyste po dokončení velmi hodinové náklady na událost live-streaming okamžitě zastavit spuštěný kanálů. Služba Media Services doručí datový proud zákazníkům, kteří ji.

> [!NOTE]
> Tento článek popisuje atributy kanály, které nejsou povolené kódování v za provozu. Informace o práci s kanály, které mají povolené kódování v reálném najdete v tématu [živé streamování využívající Azure Media Services k vytvoření datových proudů více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).
>
>Informace o doporučených místní kodéry najdete v tématu [doporučená místní kodéry](media-services-recommended-encoders.md).

Následující diagram představuje pracovním postupu živého streamování, který používá místní kodér za provozu RTMP s více přenosovými rychlostmi nebo fragmentovaný MP4 (technologie Smooth Streaming) datové proudy jako výstup.

![Živý pracovní postup][live-overview]

## <a id="scenario"></a>Běžný scénář live-streaming
Následující kroky popisují úlohy týkající se vytváření běžné aplikace live-streaming.

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte místní kodér za provozu, který má RTMP s více přenosovými rychlostmi nebo fragmentovaný MP4 (technologie Smooth Streaming) datového proudu jako výstup. Další informace najdete v článku [Podpora RTMP ve službě Azure Media Services a kodéry pro kódování v reálném čase](http://go.microsoft.com/fwlink/?LinkId=532824).

    Můžete také provést tento krok po vytvoření kanálu.
2. Vytvoření a spuštění kanálu.

3. Načtěte URL ingestování kanálu.

    Za provozu kodér adresu URL ingestování používá k odesílání datového proudu do kanálu.
4. Získat adresu URL náhledu kanálu.

    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program.

    Pokud používáte portál Azure, vytváření program vytvoří také asset.

    Při použití sady .NET SDK nebo REST, budete muset vytvořit prostředek služby a nastavení, aby používal tento prostředek, při vytváření program.
6. Publikujte asset, který je přidružený k programu.   

    >[!NOTE]
    >Při vytvoření účtu Azure Media Services **výchozí** koncový bod streamování je přidána k vašemu účtu v **Zastaveno** stavu. Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**.

7. Až budete připraveni začít Streamovat a archivovat, spusťte program.

8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního datového proudu.

9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

10. Odstraňte program (a volitelně můžete odstranit i asset).     

## <a id="channel"></a>Popis kanálu a jeho souvisejících součástí
### <a id="channel_input"></a>Vstup kanálu (ingestování) konfigurace
#### <a id="ingest_protocols"></a>Ingestování datových proudů
Služba Media Services podporuje příjem provozu kanály pomocí více přenosovými rychlostmi fragmentovaný soubor MP4 a RTMP s více přenosovými rychlostmi jako protokoly datových proudů. Když ingestování RTMP streamování protokol je vybraná, dva ingestování (vstupu) koncové body jsou vytvořené pro kanál:

* **Primární adresa URL**: Určuje plně kvalifikovanou adresu URL v kanálu primární RTMP ingestování koncový bod.
* **Sekundární adresa URL** (volitelné): Určuje plně kvalifikovanou adresu URL v kanálu sekundární RTMP ingestování koncový bod.

Pokud chcete zlepšit odolnost a odolnost proti chybám vaší ingestování datového proudu (stejně jako kodéru převzetí služeb při selhání a odolnost proti chybám), použijte adresu sekundární hlavně pro následující scénáře:

- Jeden kodér dvojitou předání na primární i sekundární adresy URL:

    Hlavním účelem tohoto scénáře je poskytnout další odolnost vůči kolísání sítě a tlumení. Některé RTMP kodéry nezpracovávají sítě odpojí dobře. Při odpojení síťového se stane, kodér může zastavit kódování a není odeslat data ve vyrovnávací paměti případě o obnovení. To způsobí, že nespojitosti a ztrátě dat. Odpojení sítě může dojít z důvodu chybné sítě nebo Údržba na straně Azure. Primární a sekundární adresy URL snižují problémy se sítí a poskytují řízené procesu upgradu. Pokaždé, když odpojení síťového naplánované se stane Media Services spravuje primární a sekundární odpojí a poskytuje zpožděné odpojit mezi nimi. Kodéry pak mít čas zachovat odesílání dat a znovu připojit znovu. Pořadí odpojí může být náhodné, ale bude vždy zpoždění mezi primární a sekundární nebo sekundární nebo primární adresy URL. V tomto scénáři modulu encoder je stále jediný bod selhání.

- Více kodéry s každou kodér vkládání do vyhrazené bodu:

    V tomto scénáři poskytuje i kodér a ingestuje redundance. V tomto scénáři encoder1 nabízených oznámení na adresu URL, primární a encoder2 nabízených oznámení na sekundární adresu URL. Když kodér selže, můžete zachovat jiné kodér odeslání dat. Je možné udržovat redundanci dat, protože Media Services neodpojí primární a sekundární adresy URL ve stejnou dobu. Tento scénář předpokládá, že kodéry se synchronizovat čas a zadejte přesně stejná data.  

- Více kodéry dvojitou předání na primární i sekundární adresy URL:

    V tomto scénáři obou kodéry nabízel data pro primární i sekundární adresy URL. To poskytuje nejlepší spolehlivost a odolnost proti chybám, protože jako redundanci dat. V tomto scénáři může tolerovat selhání obou kodér a odpojí, i když jedna kodér přestane fungovat. Se předpokládá, že kodéry se synchronizovat čas a zadejte přesně stejná data.  

Informace o RTMP kodéry najdete v tématu [podpora RTMP ve službě Azure Media Services a kodéry Live](http://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Ingestovaných adres URL (koncových bodů)
Kanál, který obsahuje vstupní koncový bod (ingestovanou adresu URL) určit v za provozu kodér tak můžete posouvat kodéru datových proudů do vašeho kanálů.   

Adresy URL ingestování získáte, když vytvoříte kanál. Abyste mohli získat tyto adresy URL, nemá kanál v **systémem** stavu. Až budete připraveni začít předání dat do kanálu, kanál musí být v **systémem** stavu. Po kanál spuštění příjem dat, můžete zobrazit náhled datového proudu prostřednictvím URL náhledu.

Máte možnost z příjem fragmentovaných MP4 (technologie Smooth Streaming) živý datový proud připojení přes protokol SSL. K ingestování přes protokol SSL, nezapomeňte aktualizovat adresu URL ingestování protokolem HTTPS. V současné době nelze ingestování RTMP přes protokol SSL.

#### <a id="keyframe_interval"></a>Interval klíčový snímek
Pokud používáte místní kodér za provozu k vygenerování více přenosovými rychlostmi datového proudu, @keyframe, které určuje interval Určuje dobu trvání skupinu obrázků (GOP) používá tento externí kodér. Po kanál obdrží tento příchozí datový proud, abyste mohli zajistit živého datového proudu pro klientské aplikace přehrávání v některém z následujících formátů: technologie Smooth Streaming, dynamické adaptivní datové proudy přes protokol HTTP (pomlčka) a HTTP Live Streaming (HLS). Když provádíte živé vysílání datového proudu, HLS je vždy zabalené dynamicky. Ve výchozím nastavení služba Media Services automaticky vypočítá segment balení poměr HLS (fragmenty jednotlivých segmentů), který je na základě intervalu @keyframe, které určuje přijaté z kodéru za provozu.

Následující tabulka uvádí výpočtu doby trvání segmentu:

| Interval klíčový snímek | HLS segment balení poměr (FragmentsPerSegment) | Příklad: |
| --- | --- | --- |
| Menší než nebo rovna 3 sekund |3:1 |Pokud KeyFrameInterval (nebo GOP) 2 sekundy, je výchozí HLS segment balení poměr 3: 1. Tím se vytvoří segment HLS 6 sekundu. |
| 3 až 5 sekund |2:1 |Pokud KeyFrameInterval (nebo GOP) je 4 a víc sekund, je výchozí HLS segment balení poměr 2: 1. Tím se vytvoří segment HLS 8 sekundu. |
| Větší než 5 sekund |1:1 |Pokud KeyFrameInterval (nebo GOP) 6 sekund, je výchozí HLS segment balení poměr 1: 1. Tím se vytvoří segment HLS 6 sekundu. |

Poměr fragmenty za segment můžete změnit konfiguraci kanálu výstup a nastavení FragmentsPerSegment na ChannelOutputHls.

Hodnota intervalu @keyframe, které určuje můžete také změnit nastavením vlastnosti KeyFrameInterval na ChanneInput. Pokud nastavíte explicitně KeyFrameInterval, segmentovat HLS poměr balení, vypočítané FragmentsPerSegment prostřednictvím pravidel popsaných dříve.  

Pokud nastavíte explicitně KeyFrameInterval a FragmentsPerSegment, používá služba Media Services hodnoty, které nastavíte.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které mají povoleno publikování videa tohoto kanálu. Povolené IP adresu lze zadat jako jednu z těchto možností:

* Jednu IP adresu (například 10.0.0.1)
* Rozsah adres IP, který používá IP adresy a masky podsítě CIDR (například 10.0.0.1/22)
* Rozsah adres IP, která používá IP adresu a masku podsítě s tečkou (například 10.0.0.1(255.255.252.0))

Pokud nejsou zadány žádné IP adresy a neexistuje žádná definice pravidla, je povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

### <a name="channel-preview"></a>Náhled kanálu
#### <a name="preview-urls"></a>Adresy URL Preview
Kanály zadejte koncový bod preview (URL náhledu), který používáte pro zobrazení náhledu a ověřit před další zpracování a doručení datového proudu.

Když vytvoříte kanál, můžete získat adresu URL náhledu. Abyste mohli získat adresu URL, nemá kanál v **systémem** stavu. Po kanál spuštění příjem dat, můžete zobrazit náhled datového proudu.

V současné době mohou být zajišťovány preview datového proudu pouze v fragmentovaných MP4 formátu (technologie Smooth Streaming), bez ohledu na zadaný typ vstupu. Můžete použít [monitorování stavu technologie Smooth Streaming](http://smf.cloudapp.net/healthmonitor) player k testování datový proud smooth. Můžete také použít přehrávač, který je hostován na portálu Azure k zobrazení datového proudu.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které se mohou připojit ke koncovému bodu náhledu. Pokud nejsou zadány žádné IP adresy, je povoleno jakékoli IP adresy. Povolené IP adresu lze zadat jako jednu z těchto možností:

* Jednu IP adresu (například 10.0.0.1)
* Rozsah adres IP, který používá IP adresy a masky podsítě CIDR (například 10.0.0.1/22)
* Rozsah adres IP, která používá IP adresu a masku podsítě s tečkou (například 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Výstup kanál
Informace o kanálu výstup najdete v tématu [@keyframe, které určuje interval](#keyframe_interval) části.

### <a name="channel-managed-programs"></a>Spravované kanál programy
Kanál je přidružený k programům, které můžete použít k řízení publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálů a programů je podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka archivačního okna také určuje maximální počet časových klientů můžete prohledávat směrem zpět v čase od aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružena k assetu, který ukládá obsah datového proudu. Prostředek je namapovaná na kontejner objektů blob bloku v účtu úložiště Azure a soubory v prostředku jsou uloženy jako objekty BLOB v tomto kontejneru. Chcete program publikovat, takže vaši zákazníci mohou zobrazit datového proudu, je nutné vytvořit lokátor OnDemand pro přidružený asset. Tento Lokátor vám pomůže vytvořit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. Můžete publikovat a archivovat různé části události podle potřeby. Představte si například, že je vaše firemní požadavky chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval šest hodin události, ale tento program není publikována. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte nový program pro každou jednotlivou událost. Až budete připraveni začít Streamovat a archivovat, spusťte program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte program a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud program používá je. Program musí být odstraněny jako první.

I po zastavení a odstranění programu můžou uživatelé Streamovat archivovaný obsah jako video na vyžádání, dokud neodstraníte asset. Pokud chcete archivovaný obsah zachovat, ale není k dispozici pro streamování, odstraňte Lokátor streamování.

## <a id="states"></a>Stavy kanál a fakturace
Možné hodnoty pro aktuální stav kanál:

* **Zastavit**: Toto je počáteční stav kanálu po jeho vytvoření. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povolené.
* **Spouštění**: spuštění kanálu. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál vrátí **Zastaveno** stavu.
* **Spuštění**: kanál může zpracovat datových proudů za provozu.
* **Zastavení**: kanál se zastaví. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Odstranění**: kanál se odstraňuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka uvádí přiřazení stavů kanálu k režimu fakturace.

| Stav kanálu | Indikátory v uživatelském rozhraní portálu | Fakturováno? |
| --- | --- | --- | --- |
| **Spouštění** |**Spouštění** |Ne (přechodný stav) |
| **Spuštění** |**Připraveno** (žádné spuštěné programy)<p><p>nebo<p>**Streamování** (alespoň jeden spuštěným programem) |Ano |
| **Zastavení** |**Zastavení** |Ne (přechodný stav) |
| **Byla zastavena** |**Byla zastavena** |Ne |

## <a id="cc_and_ads"></a>Uzavřené přidávání titulků a ad vložení
Následující tabulka ukazuje podporované standardy pro uzavřené přidávání titulků a ad vložení.

| Standard | Poznámky |
| --- | --- |
| CEA 708 a EIA 608 (708/608) |Titulky jsou CEA 708 a EIA 608 standardy pro Spojené státy americké a Kanadu.<p><p>V současné době přidávání titulků je podporována pouze v případě, že se provádí v kódovaného vstupního datového proudu. Budete muset použít kodér médií za provozu, která můžete vložit 608 nebo 708 titulky v kódovaného datový proud, který je odeslán Media Services. Služba Media Services doručí obsah s titulky vložené do vašeho prohlížeče. |
| TTML uvnitř .ismt (technologie Smooth Streaming textové stopy) |Dynamické balení Media Services umožňuje vašim klientům ke streamování obsahu v některém z následujících formátů: DASH, HLS nebo technologie Smooth Streaming. Ale pokud jste ingestování fragmentovaný soubor MP4 (technologie Smooth Streaming) s titulky uvnitř .ismt (technologie Smooth Streaming textové stopy), abyste mohli zajistit datový proud jenom pro klienty technologie Smooth Streaming. |
| SCTE-35 |SCTE 35 je digitální signalizační systém, který se používá k převinutí vložení inzerování. Podřízené příjemci pomocí signál splice – reklamu do datového proudu pro přiděleném čase. SCTE 35, musí se poslat jako zhuštěné sledování v vstupního datového proudu.<p><p>V současné době pouze podporované vstupního datového proudu formátu této má u sebe ad signály fragmentován MP4 (technologie Smooth Streaming). Jediný podporovaný výstupní formát je také technologie Smooth Streaming. |

## <a id="considerations"></a>Důležité informace
Pokud používáte místní kodér za provozu na odesílat datový proud s více přenosovými rychlostmi do kanálu, platí následující omezení:

* Ujistěte se, že máte dostatek volného připojení k Internetu k odesílání dat do ingestování bodů.
* Ingestovanou adresu URL, pomocí sekundární vyžaduje dodatečnou šířku pásma.
* Příchozí datový proud více přenosovými rychlostmi může mít maximálně 10 úrovně kvalitu videa (vrstvy) a nesmí být delší než 5 zvukových stop.
* Nejvyšší průměrné přenosovou rychlostí v žádném z úrovně kvalitu videa by měla být nižší než 10 MB/s.
* Agregace průměrná přenosových rychlostí pro všechny videa a zvuku datové proudy musí mít míň než 25 MB/s.
* Nelze změnit vstupní protokol když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Můžete ingestování s jednou přenosovou rychlostí v kanálu. Ale protože kanál nezpracovává datového proudu, klientské aplikace se také zobrazí datový proud s jednou přenosovou rychlostí. (Nedoporučujeme tuto možnost.)

Zde jsou další důležité informace týkající se práce s kanály a související součásti:

* Pokaždé, když překonfigurujete kodér za provozu, volání **resetovat** metoda na kanálu. Před resetujete kanál, budete muset, zastavte program. Po resetování kanálu restartujte program.

  > [!NOTE]
  > Při dalším spuštění programu, musíte ji přidružit nového prostředku a vytvořit nový. 
  
* Kanál se dá zastavit jenom v případě, že se **systémem** byly zastaveny stavu a všechny programy na kanálu.
* Ve výchozím nastavení můžete přidat pouze pět kanály pro váš účet Media Services. Další informace najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).
* Fakturuje se jenom v případě, že je kanál v **systémem** stavu. Další informace najdete v tématu [kanálu stavy a fakturace](media-services-live-streaming-with-onprem-encoders.md#states) části.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Váš názor
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doporučené místní kodéry](media-services-recommended-encoders.md)

[Azure Media Services fragmentovaných MP4 život ingestování specifikace](media-services-fmp4-live-ingest-overview.md)

[Přehled služby Azure Media Services a běžné scénáře](media-services-overview.md)

[Koncepty služby Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
