---
title: Stream živé pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi – Azure | Dokumentace Microsoftu
description: 'Toto téma popisuje, jak vytvořit kanál, který přijímá živý datový proud s více přenosovými rychlostmi z místní kodér. Datový proud se pak dá doručit do klientské aplikace přehrávání prostřednictvím jednoho nebo více koncových bodů streamování, používat jednu z následujících protokolů pro adaptivní streamování: HLS, Smooth Streaming, DASH.'
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
ms.date: 02/10/2019
ms.author: cenkd;juliako
ms.openlocfilehash: 67d86ca7ed79f431bf762d4a3679e18a7b4bc373
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990212"
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi

> [!NOTE]
> Od 12. května 2018, živé kanály budou nadále podporu přenosového streamu RTP/MPEG-2 protokol ingestování. Migrujte prosím z RTP/MPEG-2 na RTMP nebo MP4 s fragmentací (Smooth Streaming) ingestovací protokoly.

## <a name="overview"></a>Přehled
Ve službě Azure Media Services *kanál* představuje kanál pro zpracování obsahu živého streamování. Kanál obdrží živé vstupní datové proudy v jednom ze dvou způsobů:

* Místní odešle kodér služby live Encoding s více přenosovými rychlostmi RTMP nebo technologie Smooth Streaming (fragmentovaný MP4) datového proudu do kanálu, který není povolené provádět živé kódování pomocí Media Services. Ingestované datové proudy prochází kanály bez dalšího zpracování. Tato metoda je volána *předávací*. Live encoder můžete také odeslat datový proud s jednou přenosovou rychlostí do kanálu, který není povolen pro kódování v reálném čase, ale nedoporučujeme, který. Služba Media Services doručí datový proud zákazníkům, kteří si ji vyžádat.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě Streamovat.


* Místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí do kanálu, který má povolené provádět živé kódování pomocí Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Kanál potom provede kódování v reálném čase příchozího datového proudu s jednou přenosovou rychlostí na datový proud s více přenosovými rychlostmi (adaptivní) videa. Služba Media Services doručí datový proud zákazníkům, kteří si ji vyžádat.

Od verze Media Services 2.10, když vytvoříte kanál, můžete určit, jak chcete, aby váš kanál pro příjem vstupního datového proudu. Můžete také určit, zda chcete kanál provádět živé kódování datového proudu. Máte dvě možnosti:

* **Předávání**: Tuto hodnotu zadejte, pokud budete chtít použít místní kodér služby live Encoding, která má datový proud s více přenosovými rychlostmi (průchozí datový proud) jako výstup. V takovém případě příchozího datového proudu se předá prostřednictvím výstup bez jakékoli kódování. Toto je chování kanál před 2.10 vydáním. Tento článek obsahuje podrobné informace o práci s kanály tohoto typu.
* **Živé kódování**: Tuto hodnotu zvolte, pokud máte v plánu používat Media Services ke kódování živého datového proudu s jednou přenosovou rychlostí na datový proud s více přenosovými rychlostmi. Opuštění živého kódování kanálu v **systémem** stavu s sebou nese náklady účtování poplatků. Doporučujeme, abyste po dokončení se vyhnout poplatkům za velmi hodinové události živého streamování okamžitě zastavit spuštěné kanálů. Služba Media Services doručí datový proud zákazníkům, kteří si ji vyžádat.

> [!NOTE]
> Tento článek popisuje atributy kanály, které nejsou povolené provádět živé kódování. Informace o práci s kanály, které mají povolené kódování v reálném najdete v tématu [živého streamování využívajícího službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).
>
>Informace o doporučené místní kodéry najdete v tématu [doporučené místní kodéry](media-services-recommended-encoders.md).

Následující diagram představuje pracovní postup živého streamování, který používá místní kodér služby live Encoding RTMP s více přenosovými rychlostmi nebo fragmentovaný MP4 (technologie Smooth Streaming) datové proudy jako výstup.

![Živý pracovní postup][live-overview]

## <a id="scenario"></a>Běžný scénář živého streamování
Následující kroky popisují úlohy, které jsou součástí procesu vytváření běžných aplikací živého streamování.

1. Připojte k počítači videokameru. Spustit a nakonfigurovat místní kodér služby live Encoding, která má s více přenosovými rychlostmi RTMP nebo MP4 s fragmentací (Smooth Streaming) datového proudu jako výstup. Další informace najdete v článku [Podpora RTMP ve službě Azure Media Services a kodéry pro kódování v reálném čase](https://go.microsoft.com/fwlink/?LinkId=532824).

    Můžete také provést tento krok po vytvoření kanálu.
2. Vytvoření a spuštění kanálu.

3. Adresa URL ingestu načtení kanálu.

    Live encoder adresu URL ingestování používá k odesílání datového proudu do kanálu.
4. Načtěte adresu URL náhledu kanálu.

    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program.

    Při použití na webu Azure portal vytvořit program vytvoří také asset.

    Při použití sady .NET SDK nebo REST, budete muset vytvořit asset a nastavení, aby používal tento prostředek po vytvoření programu.
6. Publikujte asset, který je spojen s programem.   

    >[!NOTE]
    >Po vytvoření účtu Azure Media Services **výchozí** koncový bod streamování se přidá k němu **Zastaveno** stavu. Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**.

7. Jakmile budete připraveni začít Streamovat a archivovat, spusťte program.

8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního datového proudu.

9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

10. Odstraňte program (a volitelně můžete odstranit i asset).     

## <a id="channel"></a>Popis kanálu a jeho souvisejících součástí.
### <a id="channel_input"></a>Vstupní kanál (ingestování) konfigurace
#### <a id="ingest_protocols"></a>Ingestování datových proudů
Služba Media Services podporuje ingestování živé kanály s využitím jako protokoly datových proudů s více přenosovými rychlostmi fragmentovaný soubor MP4 a RTMP s více přenosovými rychlostmi. Při zpracování příjmu RTMP streamovací protokol je vybrána, dva ingestování (vstupu) koncových bodů jsou vytvořeny pro kanál:

* **Primární adresa URL**: Určuje plně kvalifikovanou adresu URL kanálu primární RTMP ingestování koncového bodu.
* **Sekundární adresa URL** (volitelné): Určuje plně kvalifikovanou adresu URL kanálu sekundární RTMP ingestování koncového bodu.

Pokud chcete zvýšit odolnost a odolnost proti chybám vaše ingestování datového proudu (stejně jako kodéru převzetí služeb při selhání a odolnost proti chybám), použijte sekundární adresa URL speciálně pro následující scénáře:

- Jeden kodér double nahráním do primárních a sekundárních adresy URL:

    Hlavním účelem tento scénář je poskytnout další odolnost vůči kolísání v síti a tlumení. Některé RTMP kodérů nezpracovávají dobře odpojeními sítě. Pokud dojde k odpojení sítě kodéru může zastavit kódování a není odešlete data ve vyrovnávací paměti při volání metody reconnect se stane. To způsobí, že nespojitosti a ztráty. Odpojeními sítě může dojít z důvodu chybné sítě nebo Údržba na straně Azure. Adresy URL primárního a sekundárního snížit problémy se sítí a poskytnout řízený proces upgradu. Pokaždé, když odpojit naplánované sítě se stane, Media Services spravuje primární a sekundární odpojí a poskytuje opožděných mezi těmito dvěma odpojit. Kodérů pak dostatek času k posílat data a znovu se připojit znovu. Pořadí odpojí může být náhodné, ale bude vždy zpoždění mezi primární a sekundární nebo sekundární nebo primární adresy URL. V tomto scénáři je jediný bod selhání stále kodér.

- Více kodérů pomocí jednotlivých kodéru doručením (push) do vyhrazené bodu:

    V tomto scénáři poskytuje obou kodér a ingestuje redundance. V tomto scénáři encoder1 nabízených oznámení odeslané na adresu primárního a encoder2 nabízených oznámení odeslané na adresu sekundární. Při selhání pro kodér zachovat jiných kodér odesílat data. Redundanci dat lze udržovat, protože Media Services neodpojí primární a sekundární adresy URL ve stejnou dobu. Tento scénář předpokládá, že kodérů čas synchronizovaný a poskytují naprosto stejná data.  

- Více kodérů double nahráním do primárních a sekundárních adresy URL:

    V tomto scénáři obou kodérů vložení dat do primární i sekundární adresy URL. To poskytuje nejlepší spolehlivost a odolnost proti chybám, protože jako redundanci dat. V tomto scénáři může tolerovat možnost, jak zakódovaným selháním a odpojí, i v případě, že jeden kodér přestane fungovat. To předpokládá, že kodérů jsou čas synchronizovaný a poskytují naprosto stejná data.  

Informace o RTMP kodérů najdete v tématu [podpora RTMP ve službě aplikace Azure Media Services a kodéry](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Ingestované adresy URL (koncových bodů)
Kanál obsahuje vstupní koncový bod (adresa URL ingestu) určit v live encoder, tak můžete nabízet kodér datových proudů do kanálů.   

Adresy URL ingestování můžete získat, když vytvoříte kanál. Abyste mohli získat tyto adresy URL, nemá kanál ve **systémem** stavu. Až budete připravení začít zapisovat data do kanálu, kanál musí být v **systémem** stavu. Po spuštění kanál ingestovat data můžete zobrazit náhled datového proudu prostřednictvím adresy URL ve verzi preview.

Máte možnost ingestovat fragmentovaného MP4 živý stream (technologie Smooth Streaming) připojení přes protokol SSL. K ingestování přes protokol SSL, nezapomeňte aktualizovat adresu URL ingestování na protokol HTTPS. V současné době nelze ingestování RTMP přes protokol SSL.

#### <a id="keyframe_interval"></a>Interval klíčových snímků
Při použití místní kodér služby live Encoding ke generování datový proud s více přenosovými rychlostmi, interval klíčových snímků určuje dobu, po skupiny obrázky (GOP), protože používá tuto externí kodér. Až kanál obdrží tento příchozí datový proud, můžete doručovat živého datového proudu do klientské aplikace pro přehrávání v některém z následujících formátů: Technologie Smooth Streaming, dynamické adaptivní streamování přes HTTP (DASH) a HTTP Live Streaming (HLS). Když provádíte živého streamování, HLS je vždy zabalený dynamicky. Ve výchozím nastavení Media Services automaticky vypočítá segmentu balení poměr HLS (fragmentů podle segmentu), který je na základě intervalu klíčový snímek, který se poslal kodér služby live Encoding.

Následující tabulka ukazuje, jak se počítá segmentu doba trvání:

| Interval klíčových snímků | Poměr balení segmentu HLS (FragmentsPerSegment) | Příklad: |
| --- | --- | --- |
| Maximálně tři sekundy |3:1 |Pokud KeyFrameInterval (nebo GOP) je dlouhý 2 sekundy, poměr balení segmentu HLS výchozí je 3: 1. Tím se vytvoří segment HLS 6sekundu. |
| 3 až 5 sekund |2:1 |Pokud KeyFrameInterval (nebo GOP) 4 sekundy, poměr výchozí HLS segmentu balení je 2: 1. Tím se vytvoří 8 druhé HLS segmentu. |
| Větší než 5 sekund |1:1 |Pokud je KeyFrameInterval (nebo GOP) 6 sekund, poměr výchozí HLS segmentu balení je 1: 1. Tím se vytvoří segment HLS 6sekundu. |

Poměr fragmenty na segment můžete změnit konfiguraci výstupu v kanálu a nastavení FragmentsPerSegment ChannelOutputHls.

Hodnota intervalu klíčových snímků můžete také změnit tak, že nastavíte vlastnost KeyFrameInterval na ChannelInput. Pokud nastavíte explicitně KeyFrameInterval, segmentovat HLS poměr balení, vypočítané FragmentsPerSegment prostřednictvím pravidel popsaných dříve.  

Pokud nastavíte explicitně KeyFrameInterval a FragmentsPerSegment, Media Services využívá hodnoty, které jste nastavili.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které je povoleno publikování videa do tohoto kanálu. Povolené IP adresy se dá nastavit jako jeden z následujících akcí:

* Jedna IP adresa (např. 10.0.0.1)
* Rozsah adres IP, která používá IP adresu a masku podsítě CIDR (třeba 10.0.0.1/22)
* Rozsah adres IP, která používá IP adresy a masky podsítě (například 10.0.0.1(255.255.252.0))

Pokud žádné IP adresy nezadáte a neexistuje žádná definice pravidla, je povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

### <a name="channel-preview"></a>Náhled kanálu
#### <a name="preview-urls"></a>Adresy URL náhledu
Kanály nabízejí koncový bod ve verzi preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Když vytvoříte kanál, můžete získat adresu URL ve verzi preview. Abyste mohli získat adresu URL kanálu nemá v **systémem** stavu. Po spuštění kanál ingestovat data můžete zobrazit náhled datového proudu.

V současné době se dodávají stream ve verzi preview jenom v fragmentovaného MP4 formátu (technologie Smooth Streaming), bez ohledu na zadaný typ vstupu. Můžete použít [technologie Smooth Streaming monitorování stavu](http://playready.directtaps.net/smoothstreaming/) přehrávače pro datový proud smooth testování. Můžete také použít přehrávač, který je hostovaný na webu Azure Portal k zobrazení vašeho datového proudu.

#### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které jsou povolené pro připojení ke koncovému bodu ve verzi preview. Pokud nejsou zadány žádné IP adresy, jakékoli IP adresy je povolené. Povolené IP adresy se dá nastavit jako jeden z následujících akcí:

* Jedna IP adresa (např. 10.0.0.1)
* Rozsah adres IP, která používá IP adresu a masku podsítě CIDR (třeba 10.0.0.1/22)
* Rozsah adres IP, která používá IP adresy a masky podsítě (například 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Výstupní kanál
Informace o kanálu výstupu najdete v tématu [interval klíčových snímků](#keyframe_interval) oddílu.

### <a name="channel-managed-programs"></a>Kanál spravovaných aplikací
Kanál je přidružený k programům, které vám umožní řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálů a programů je podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka okna archivu také určuje, že maximální počet časových klientů můžete posunout zpátky v čase od aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružena k assetu, který ukládá obsah datového proudu. Prostředek je namapovaná na kontejner objektů blob bloku v účtu úložiště Azure a soubory v prostředku se ukládají jako objekty BLOB v tomto kontejneru. Program publikovat, takže vaši zákazníci mohou zobrazit datový proud, vytvořte Lokátor OnDemand pro přidružený asset. Tento Lokátor můžete vytvořit adresu URL streamování, kterou následně poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. Můžete publikovat a archivovat různé části události podle potřeby. Představte si například, že je vaše firemní požadavky chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikován. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte nový program pro každou jednotlivou událost. Jakmile budete připraveni začít Streamovat a archivovat, spusťte program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte program a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud ho program používá. Nejdřív odstraňte program.

I po zastavení a odstranění programu můžou uživatelé Streamovat archivovaný obsah jako video na vyžádání, dokud neodstraníte asset. Pokud chcete archivovaný obsah zachovat, ale není k dispozici pro streamování, odstraňte Lokátor streamování.

## <a id="states"></a>Stavy kanálu a fakturaci
Možné hodnoty pro aktuální stav kanálu:

* **Zastavit**: Toto je počáteční stav kanálu po jeho vytvoření. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povolené.
* **Spouští se**: Kanál se spouští. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí **Zastaveno** stavu.
* **Spuštění**: Kanál může zpracovávat živé streamy.
* **Zastavuje se**: Kanál se zastavuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Odstraňuje se**: Kanál se odstraňuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka uvádí přiřazení stavů kanálu k režimu fakturace.

| Stav kanálu | Indikátory v uživatelském rozhraní portálu | Fakturováno? |
| --- | --- | --- | --- |
| **Spuštění** |**Spuštění** |Ne (přechodný stav) |
| **Spuštění** |**Připraveno** (žádný běžící program)<p><p>nebo<p>**Streamování** (nejméně jeden běžící program) |Ano |
| **Zastavení** |**Zastavení** |Ne (přechodný stav) |
| **Zastavení** |**Zastavení** |Ne |

## <a id="cc_and_ads"></a>Uzavřené titulků a ad vložení
Následující tabulka ukazuje podporované standardy pro vložení uzavřené titulků a ad.

| Standard | Poznámky |
| --- | --- |
| Skryté titulky CEA-708 a EIA 608 (708/608) |Skryté titulky CEA-708 a EIA 608 jsou titulků standardy pro Spojené státy a Kanadu.<p><p>V současné době titulků je podporována pouze v případě, že se přenášejí v kódovaného vstupního datového proudu. Budete muset použít kodér médií za provozu, který může vložit 608 nebo 708 popisky v kódovaného datový proud, který je odeslán do Media Services. Media Services přenášejí obsah s vložené popisky pro vaši uživatelé. |
| TTML uvnitř .ismt (technologie Smooth Streaming textové stopy) |Dynamické balení Media Services umožňuje vašim klientům ke streamování obsahu v některém z následujících formátů: DASH, HLS nebo technologie Smooth Streaming. Nicméně, pokud jste ingestování fragmentovaného MP4 (technologie Smooth Streaming) s titulky uvnitř .ismt (technologie Smooth Streaming textové stopy), můžete datový proud doručovat jenom pro klienty technologie Smooth Streaming. |
| SCTE-35 |SCTE 35 je digitální signalizační systém, který se používá k upozornění na vložení reklamy. Příjem dat příjemci pomocí signál splice reklamy do datového proudu pro přiděleném čase. SCTE 35 musí být odeslána jako zhuštěné sledovat v vstupního datového proudu.<p><p>V současné době pouze podporované vstupního datového proudu naformátovat tento mělo fragmentaci ad signály MP4 (technologie Smooth Streaming). Jediný podporovaný výstupního formát je také technologie Smooth Streaming. |

## <a id="considerations"></a>Důležité informace
Pokud používáte místní kodér služby live Encoding k odesílání datového proudu s více přenosovými rychlostmi do kanálu, platí následující omezení:

* Ujistěte se, že máte dostatek volného připojení k Internetu k odesílání dat do bodů ingestování.
* Adresa URL ingestace sekundárního pomocí vyžaduje větší šířku pásma.
* Příchozí datový proud s více přenosovými rychlostmi můžete mít maximálně 10 úrovněmi kvalita videa (vrstvy) a maximálně 5 zvukové stopy.
* Nejvyšší průměrný přenosové rychlosti pro některé z úrovní kvalita videa musí mít míň než 10 MB/s.
* Agregace průměrné přenosových rychlostí u všech videí a zvukových streamů musí mít míň než 25 MB/s.
* Nelze změnit vstupní protokol, pokud kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* V kanálu vám umožní ingestovat s jednou přenosovou rychlostí. Ale protože kanál nezpracovává datového proudu, klientské aplikace dostanou také datový proud s jednou přenosovou rychlostí. (Nedoporučujeme tuto možnost.)

Tady jsou další důležité informace týkající se práce s kanály a související součásti:

* Pokaždé, když je znovu nakonfigurovat live encoder, zavolejte **resetování** metodu na kanál. Před restartováním kanál, budete muset Zastavit program. Po resetování kanálu restartujte program.

  > [!NOTE]
  > Po opětovném spuštění programu, musíte ho přidružit k nového assetu a vytvořit nový. 
  
* Kanál lze zastavit pouze, když je v **systémem** byly zastaveny stavu a všechny programy na kanálu.
* Ve výchozím nastavení můžete přidat pouze pět kanály pro váš účet Media Services. Další informace najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).
* Bude se vám účtovat pouze v případě, že je váš kanál ve **systémem** stavu. Další informace najdete v tématu [kanálů stavy a fakturace](media-services-live-streaming-with-onprem-encoders.md#states) oddílu.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Váš názor
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doporučené místní kodéry](media-services-recommended-encoders.md)

[Azure Media Services fragmentovaného MP4 život příjem specifikace](media-services-fmp4-live-ingest-overview.md)

[Přehled služby Azure Media Services a běžné scénáře](media-services-overview.md)

[Koncepty služby Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
