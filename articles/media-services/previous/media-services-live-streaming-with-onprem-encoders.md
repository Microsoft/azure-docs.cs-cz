---
title: Živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi – Azure | Microsoft Docs
description: Toto téma popisuje, jak nastavit kanál, který přijímá živý datový proud s více přenosovými rychlostmi z místního kodéru.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 316372f091833519f0479d07355d2845c82743b6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014838"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Práce s kanály, které přijímají živý datový proud s více přenosovými rychlostmi z místních kodérů

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Od 12. května 2018 už živé kanály nebudou podporovat protokol ingestování přenosového streamu RTP/MPEG-2. Migrujte prosím z protokolu RTP/MPEG-2 na RTMP nebo fragmentujte protokoly ingesta MP4 (Smooth Streaming).

## <a name="overview"></a>Přehled
V Azure Media Services *kanál* představuje kanál pro zpracování obsahu živého streamování. Kanál přijímá živé vstupní proudy jedním ze dvou způsobů:

* On-premises Live Encoder odesílá datový proud s více přenosovými rychlostmi nebo Smooth Streaming (fragmentovaný MP4) do kanálu, který není povolen k provádění kódování v reálném čase pomocí Media Services. Ingestované datové proudy procházejí kanálem bez dalšího zpracování. Tato metoda se nazývá *předávací*. Živý kodér může také odeslat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale nedoporučujeme to. Media Services dodává datový proud zákazníkům, kteří si je vyžádají.

  > [!NOTE]
  > Použití předávací metody je nejúčinnější způsob, jak provádět živé streamování.


* On-premises Live Encoder odesílá datový proud s jednou přenosovou rychlostí do kanálu, který má povolené kódování v reálném čase, pomocí Media Services v jednom z následujících formátů: RTMP nebo Smooth Streaming (fragmentovaný MP4). Kanál pak provede živé kódování příchozího datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi (adaptivní). Media Services dodává datový proud zákazníkům, kteří si je vyžádají.

Počínaje verzí Media Services 2,10 můžete při vytváření kanálu určit, jak chcete, aby kanál přijímal vstupní datový proud. Můžete také určit, jestli chcete, aby kanál prováděl živé kódování vašeho streamu. Máte dvě možnosti:

* **Předat** po: tuto hodnotu zadejte, pokud plánujete použít místní kodér Live, který má datový proud s více přenosovými rychlostmi (předávací proud) jako výstup. V tomto případě příchozí datový proud projde do výstupu bez kódování. Toto je chování kanálu před vydáním verze 2,10. Tento článek obsahuje podrobné informace o práci s kanály tohoto typu.
* **Live Encoding**: tuto hodnotu vyberte, pokud plánujete používat Media Services ke kódování živého datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi. Když necháte živý kanál kódování ve **spuštěném** stavu, účtují se poplatky za účtování. Doporučujeme, abyste okamžitě zastavili spuštěné kanály po dokončení události živého streamování, abyste se vyhnuli dodatečným hodinovým poplatkům. Media Services dodává datový proud zákazníkům, kteří si je vyžádají.

> [!NOTE]
> Tento článek pojednává o atributech kanálů, u kterých není povoleno provádět kódování v reálném čase. Informace o práci s kanály, které mají povolené kódování v reálném čase, najdete v tématu [živé streamování pomocí Azure Media Services k vytváření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).
>
>Informace o doporučených místních kodérech najdete v tématu [Doporučené místní kodéry](media-services-recommended-encoders.md).

Následující diagram představuje pracovní postup živého streamování, který používá místní živý kodér ke streamování s více přenosovými rychlostmi nebo fragmenty souborů MP4 (Smooth Streaming) jako výstup.

![Živý pracovní postup][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Běžný scénář živého streamování
Následující kroky popisují úlohy, které se podílejí na vytváření běžných aplikací pro živé streamování.

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte místní kodér Live Encoder, který má RTMP s více přenosovými rychlostmi, nebo fragmentový datový proud MP4 (Smooth Streaming) jako výstup. Další informace najdete v článku [Podpora RTMP ve službě Azure Media Services a kodéry pro kódování v reálném čase](https://go.microsoft.com/fwlink/?LinkId=532824).

    Tento krok můžete provést i po vytvoření kanálu.
2. Vytvoření a spuštění kanálu.

3. Načtěte adresu URL pro ingestování kanálu.

    Live Encoder používá k odeslání datového proudu do kanálu adresu URL pro příjem.
4. Načte adresu URL náhledu kanálu.

    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program.

    Při použití Azure Portal vytvoří program také Asset.

    Pokud používáte sadu .NET SDK nebo REST, je nutné vytvořit Asset a při vytváření programu určit, že se má tento Asset použít.
6. Publikujte Asset, který je přidružený k programu.   

    >[!NOTE]
    >Po vytvoření účtu Azure Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno** . Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**.

7. Spusťte program, až budete připraveni začít streamovat a archivovat.

8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního proudu.

9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

10. Odstraňte program (a volitelně odstraňte Asset).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Popis kanálu a jeho souvisejících komponent
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Konfigurace vstupu kanálů (ingestování)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Protokol instreamování
Media Services podporuje ingestování živých kanálů pomocí přenosu dat s více přenosovými rychlostmi a RTMP s více přenosovými rychlostmi jako protokoly streamování. Pokud je vybrán protokol RTMP invysílání datového proudu, vytvoří se pro kanál dva koncové body ingestování (vstupní):

* **Primární adresa URL**: Určuje plně KVALIFIKOVANOU adresu URL primárního koncového bodu RTMP pro kanál.
* **Sekundární adresa URL** (volitelné): Určuje plně KVALIFIKOVANOU adresu URL sekundárního koncového bodu RTMP ingesta kanálu.

Sekundární adresu URL použijte v případě, že chcete zlepšit odolnost a odolnost proti chybám pro váš datový proud ingestu (a také převzetí služeb při selhání a odolnost proti chybám), zejména v následujících situacích:

- Jeden kodér dvakrát nanáší na primární i sekundární adresu URL:

    Hlavním účelem tohoto scénáře je zajistit větší odolnost proti výkyvům sítě a kolísání. Některé kodéry RTMP nezpracovávají správně síťové odpojení. Když dojde k odpojení od sítě, může kodér zastavit kódování a pak při opětovném připojení Neodesílat data uložená do vyrovnávací paměti. To způsobuje výpadky a ztráty dat. K odpojení sítě může dojít z důvodu špatné sítě nebo údržby na straně Azure. Primární a sekundární adresy URL snižují problémy se sítí a poskytují řízený proces upgradu. Pokaždé, když dojde k zaplánovanému odpojení sítě, Media Services spravuje primární a sekundární odpojení a poskytuje zpožděné odpojení mezi nimi. Kodéry pak budou mít čas na uchovávání dat a znovu se znovu připojí. Pořadí odpojení může být náhodné, ale mezi primárními a sekundárními nebo sekundárními a primárními adresami URL bude vždycky zpoždění. V tomto scénáři je kodér stále jediným bodem selhání.

- Více kodérů, přičemž každý kodér přenáší do vyhrazeného bodu:

    Tento scénář poskytuje jak kodér, tak i ingestuje redundanci. V tomto scénáři encoder1 přenáší na primární adresu URL a encoder2 nabízená oznámení na sekundární adresu URL. V případě, že kodér selhává, může ostatní kodér uchovávat data. Redundanci dat je možné zachovat, protože Media Services neprovádí odpojení primárních a sekundárních adres URL ve stejnou dobu. V tomto scénáři se předpokládá, že kodéry jsou synchronizované s časem a poskytují přesně stejná data.  

- Více kodérů dvakrát přenáší do primární i sekundární adresy URL:

    V tomto scénáři obě kodéru zadávají data do primární i sekundární adresy URL. To zajišťuje nejlepší spolehlivost a odolnost proti chybám a také redundanci dat. Tento scénář může tolerovat chyby a odpojení kodéru, i když jeden kodér přestane fungovat. Předpokládá, že kodéry jsou v čase synchronizovány a poskytují přesně stejná data.  

Informace o živých kodérech RTMP najdete v tématu [Azure Media Services podpoře RTMP a živých kodérů](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Adresy URL ingestování (koncové body)
Kanál poskytuje vstupní koncový bod (adresa URL pro příjem), který zadáte v živém kodéru, takže kodér může do kanálů vložit streamy.   

Při vytváření kanálu můžete získat adresy URL pro příjem. Aby se tyto adresy URL dostaly, kanál se nemusí nacházet ve stavu **spuštěno** . Až budete připraveni začít s vložením dat do kanálu, kanál musí být ve stavu **spuštěno** . Až kanál začne ingestovat data, můžete zobrazit náhled datového proudu prostřednictvím adresy URL náhledu.

Máte možnost ingestovat fragmentované živé streamy MP4 (Smooth Streaming) přes připojení TLS. Pokud chcete ingestovat přes TLS, nezapomeňte aktualizovat adresu URL ingestování na HTTPS. V současné době nemůžete ingestovat RTMP přes TLS.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Interval klíčového snímku
Pokud k vygenerování datového proudu s více přenosovými rychlostmi používáte místní živý kodér, interval klíčového snímku určuje dobu trvání skupiny obrázků (skupinu GOP), jak ji používá daný externí kodér. Až kanál přijme tento příchozí datový proud, můžete živý datový proud doručovat do aplikací pro přehrávání klienta v libovolném z následujících formátů: Smooth Streaming, dynamické adaptivní streamování přes HTTP (POMLČKa) a HTTP Live Streaming (HLS). Když provádíte živé streamování, HLS se vždy dynamicky zabalí. Ve výchozím nastavení Media Services automaticky vypočítá poměr HLS segmentu (fragmenty na segment) na základě intervalu klíčových snímků, který je obdržen z kodéru Live.

Následující tabulka ukazuje, jakým způsobem je vypočítána doba trvání segmentu:

| Interval klíčového snímku | Poměr obalů segmentů HLS (FragmentsPerSegment) | Příklad |
| --- | --- | --- |
| Menší než nebo rovno 3 sekundy |3:1 |Pokud je KeyFrameInterval (nebo skupinu GOP) 2 sekundy, výchozí Poměrový podíl segmentu HLS je 3 – 1. Tím se vytvoří segment HLS o 6 sekund. |
| 3 až 5 sekund |2:1 |Pokud je KeyFrameInterval (nebo skupinu GOP) 4 sekundy, výchozí Poměrový podíl segmentu HLS je 2 – 1. Tím se vytvoří HLS segment o 8 sekund. |
| Více než 5 sekund |1:1 |Pokud je KeyFrameInterval (nebo skupinu GOP) 6 sekund, je výchozí Poměrový podíl segmentu HLS 1 – 1. Tím se vytvoří segment HLS o 6 sekund. |

Poměr fragmentů jednotlivých segmentů můžete změnit konfigurací výstupu kanálu a nastavením FragmentsPerSegment na ChannelOutputHls.

Hodnotu intervalu klíčového snímku můžete také změnit nastavením vlastnosti KeyFrameInterval v ChannelInput. Pokud jste explicitně nastavili KeyFrameInterval, vypočítá se poměrný podíl HLS segmentu FragmentsPerSegment prostřednictvím dříve popsaných pravidel.  

Pokud jste explicitně nastavili KeyFrameInterval i FragmentsPerSegment, používá Media Services hodnoty, které jste nastavili.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které můžou publikovat video na tento kanál. Povolená IP adresa může být zadaná jako jedna z následujících:

* Jedna IP adresa (například 10.0.0.1)
* Rozsah IP adres, který používá IP adresu a masku podsítě CIDR (například 10.0.0.1/22)
* Rozsah IP adres, který používá IP adresu a masku podsítě s tečkami (například 10.0.0.1 (255.255.252.0)).

Pokud nejsou zadané žádné IP adresy a není k dispozici žádná definice pravidla, není povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

### <a name="channel-preview"></a>Náhled kanálu
#### <a name="preview-urls"></a>Náhledové adresy URL
Kanály poskytují koncový bod verze Preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Adresu URL náhledu můžete získat při vytváření kanálu. Pokud chcete získat adresu URL, kanál nemusí být ve stavu **spuštěno** . Až kanál začne přijímat data, můžete zobrazit náhled streamu.

V současné době se Stream Preview dá doručit jenom ve formátu fragmentů MP4 (Smooth Streaming), bez ohledu na zadaný vstupní typ. K otestování hladkého streamu můžete použít přehrávač [monitorování stavu Smooth Streaming](https://playready.directtaps.net/smoothstreaming/) . K zobrazení datového proudu můžete použít také přehrávač, který je hostovaný v Azure Portal.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které mají povolené připojení ke koncovému bodu Preview. Nejsou-li zadány žádné IP adresy, je povolena jakákoli IP adresa. Povolená IP adresa může být zadaná jako jedna z následujících:

* Jedna IP adresa (například 10.0.0.1)
* Rozsah IP adres, který používá IP adresu a masku podsítě CIDR (například 10.0.0.1/22)
* Rozsah IP adres, který používá IP adresu a masku podsítě s tečkami (například 10.0.0.1 (255.255.252.0)).

### <a name="channel-output"></a>Výstup kanálu
Informace o výstupu kanálu naleznete v části [interval klíčového snímku](#keyframe_interval) .

### <a name="channel-managed-programs"></a>Programy spravované kanálem
Kanál je přidružen k programům, které lze použít k řízení publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálu a programu je podobný tradičnímu médiu, kde kanál obsahuje konstantní datový proud s obsahem a program je vymezen na nějaké časované události v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka okna archivu také určuje maximální počet časových limitů, které mohou klienti vyhledávat v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružen k assetu, který ukládá obsah streamu. Asset je namapovaný na kontejner objektů blob bloku v účtu úložiště Azure a soubory v prostředku se ukládají jako objekty BLOB v tomto kontejneru. Chcete-li publikovat program, aby mohli vaši zákazníci zobrazit datový proud, je nutné pro přidružený prostředek vytvořit Lokátor OnDemand. Pomocí tohoto lokátoru můžete vytvořit adresu URL streamování, kterou můžete poskytnout svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže můžete vytvořit několik archivů stejného příchozího datového proudu. Podle potřeby můžete publikovat a archivovat různé části události. Představte si například, že váš požadavek vaší firmy je archivovat 6 hodin programu, ale vysílat pouze posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program je nastavený tak, aby archivoval šest hodin události, ale program není publikovaný. Druhý program je nastaven na archivaci po dobu 10 minut a tento program je publikován.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte nový program pro každou událost. Spusťte program, až budete připraveni začít streamovat a archivovat. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

Chcete-li odstranit archivovaný obsah, zastavte a odstraňte program a potom odstraňte přidružený Asset. Prostředek se nedá odstranit, pokud ho program používá. Nejdříve je nutné odstranit program.

I po zastavení a odstranění programu můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, dokud Asset neodstraníte. Pokud chcete zachovat archivovaný obsah, ale nemáte ho k dispozici pro streamování, odstraňte Lokátor streamování.

## <a name="channel-states-and-billing"></a><a id="states"></a>Stavy kanálu a fakturace
K dispozici jsou možné hodnoty pro aktuální stav kanálu:

* **Zastaveno**: Jedná se o počáteční stav kanálu po jeho vytvoření. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Začíná**: kanál se spouští. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu **Zastaveno** .
* **Spuštěno**: kanál dokáže zpracovávat živé streamy.
* **Zastavování**: kanál je ukončován. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Odstraňování**: kanál se odstraňuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka ukazuje, jak se stavy kanálu mapují na režim fakturace.

| Stav kanálu | Indikátory uživatelského rozhraní portálu | Účtuje? |
| --- | --- | --- |
| **Spouštění** |**Spouštění** |Ne (přechodný stav) |
| **Spuštěno** |**Připraveno** (žádné spuštěné programy)<p><p>nebo<p>**Streamování** (aspoň jeden spuštěný program) |Yes |
| **Zastavování** |**Zastavování** |Ne (přechodný stav) |
| **Zastaveno** |**Zastaveno** |No |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Skryté titulky a vkládání reklam
Následující tabulka ukazuje podporované standardy pro uzavřené titulky a vkládání reklam.

| Standard | Poznámky |
| --- | --- |
| CEA-708 a EIA-608 (708/608) |CEA-708 a EIA-608 jsou standardy titulků pro USA a Kanadu.<p><p>V současné době se titulky podporují jenom v případě, že se přenesou do kódovaného vstupního streamu. Potřebujete použít živý kodér médií, který může vkládat popisky 608 nebo 708 do kódovaného datového proudu, který je odeslán do Media Services. Media Services doručuje obsah pomocí vložených titulků vašim uživatelům. |
| TTML uvnitř. ismt (textové stopy Smooth Streaming) |Media Services dynamické balení umožňuje klientům streamovat obsah v libovolném z následujících formátů: POMLČKy, HLS nebo Smooth Streaming. Pokud však ingestují fragmenty MP4 (Smooth Streaming) s titulky uvnitř. ismt (Smooth Streaming textové stopy), můžete datový proud doručovat pouze do Smooth Streaming klientů. |
| SCTE-35 |SCTE-35 je systém digitálního signálu, který se používá k oznámení vkládání inzerce. Přijímač pro příjem dat používají signál k spojení inzerce do datového proudu za přidělený čas. SCTE-35 musí být odesláno jako zhuštěné stopa ve vstupním datovém proudu.<p><p>V současné době je jediným podporovaným formátem vstupního datového proudu, který přenáší signály AD, fragmentem MP4 (Smooth Streaming). Jediným podporovaným formátem výstupu je také Smooth Streaming. |

## <a name="considerations"></a><a id="considerations"></a>Požadavky
Pokud k odeslání datového proudu s více přenosovými rychlostmi do kanálu používáte místní živý kodér, platí následující omezení:

* Ujistěte se, že máte dostatečné bezplatné připojení k Internetu, aby bylo možné odesílat data do bodů příjmu.
* Použití sekundární adresy URL pro přijímání vyžaduje větší šířku pásma.
* Příchozí datový proud s více přenosovými rychlostmi může mít maximálně 10 úrovní kvality videa (vrstvy) a maximálně 5 zvukových stop.
* Nejvyšší Průměrná přenosová rychlost pro libovolnou úroveň kvality videa by měla být nižší než 10 MB/s.
* Agregace průměrných přenosů pro všechna videa a zvukové streamy by měla být nižší než 25 MB/s.
* Vstupní protokol nemůžete změnit, pokud je spuštěný kanál nebo jeho přidružené programy. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* V kanálu můžete přijmout jednu přenosovou rychlost. Vzhledem k tomu, že kanál nezpracovává datový proud, budou klientské aplikace také přijímat datový proud s jednou přenosovou rychlostí. (Tuto možnost nedoporučujeme.)

Tady jsou další otázky týkající se práce s kanály a souvisejícími součástmi:

* Pokaždé, když znovu nakonfigurujete živý kodér, zavolejte na kanálu metodu **resetování** . Před resetováním kanálu musíte program zastavit. Po resetování kanálu restartujte program.

  > [!NOTE]
  > Po restartování programu je třeba ho přidružit k novému assetu a vytvořit nový lokátor. 
  
* Kanál lze zastavit pouze v případě, že je ve stavu **spuštěno** a všechny programy v kanálu byly zastaveny.
* Ve výchozím nastavení můžete k účtu Media Services přidat jenom pět kanálů. Další informace najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).
* Fakturuje se vám jenom v případě, že je váš kanál ve stavu **spuštěno** . Další informace najdete v části [stavy kanálu a fakturace](media-services-live-streaming-with-onprem-encoders.md#states) .

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="suggestions-and-feedback"></a>Návrhy a zpětná vazba

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doporučené na místních kodérech](media-services-recommended-encoders.md)

[Azure Media Services fragmentované specifikace ingestování MP4](../media-services-fmp4-live-ingest-overview.md)

[Přehled a běžné scénáře Azure Media Services](media-services-overview.md)

[Media Services koncepty](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
