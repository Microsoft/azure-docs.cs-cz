---
title: Azure Media Services fragmentovaných MP4 live ingestování specifikace | Microsoft Docs
description: Tato specifikace popisuje protokol a formát pro fragmentovaných na základě MP4 živé streamování přijímání pro Azure Media Services. Azure Media Services můžete do datového proudu živé události a všesměrovým vysíláním obsah v reálném čase pomocí služby Azure jako cloudová platforma. Tento dokument taky popisuje osvědčené postupy pro vytváření vysoce redundantní a robustní za provozu ingestování mechanismy.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 367066b0073775ae10fd5e9d084c5bc78ebf6677
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services fragmentovaných MP4 live ingestování specifikace
Tato specifikace popisuje protokol a formát pro fragmentovaných na základě MP4 živé streamování přijímání pro Azure Media Services. Služba Media Services poskytuje služba živého streamování, které zákazníci mohou používat stream živé události a všesměrového vysílání obsah v reálném čase pomocí služby Azure jako cloudová platforma. Tento dokument taky popisuje osvědčené postupy pro vytváření vysoce redundantní a robustní za provozu ingestování mechanismy.

## <a name="1-conformance-notation"></a>1. Zápis shoda
Klíč slova ",""Nesmí," "VYŽADOVÁNA," "SHALL", "se nesmí," "SHOULD", "By neměl," "Doporučené" "PRAVDĚPODOBNĚ," a "Volitelné" v tomto dokumentu se budou interpretovat popsané v dokumentu RFC 2119.

## <a name="2-service-diagram"></a>2. Diagram služby
Následující diagram ukazuje základní architektuře služby živého streamování ve službě Media Services:

1. Za provozu kodér nabízených oznámení za provozu kanály pro kanály, které jsou vytvořeny a zřízené prostřednictvím Azure Media Services SDK.
2. Kanály, programů a koncových bodů streamování ve službě Media Services zpracování všech funkcí živého streamování, včetně ingestování, formátování, DVR, zabezpečení, škálovatelnosti a redundance v cloudu.
3. Zákazníci mohou volitelně k nasazení služby Azure Content Delivery Network vrstva mezi koncový bod streamování a koncové body klientů.
4. Datový proud koncové body klienta z koncového bodu streamování pomocí protokolů HTTP adaptivní streamování. Mezi příklady patří Microsoft technologie Smooth Streaming, dynamické adaptivní datové proudy přes protokol HTTP (DASH nebo MPEG-DASH) a Apple HTTP Live Streaming (HLS).

![ingestování toku][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitový proud formátu – ISO 14496-12 fragmentovaný soubor MP4
Přenosový formát pro živé streamování ingestování popsané v tomto dokumentu je založena na [ISO-14496-12]. Podrobné vysvětlení fragmentovaných formátu MP4 a rozšíření jak pro soubory na vyžádání video-on-demand a přijímat živé streamování najdete v tématu [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Za provozu ingestování definice formátu
Následující seznam popisuje speciální formát, který definice, které platí pro live ingestování do služby Azure Media Services:

1. **Ftyp**, **Live pole Manifest serveru**, a **moov** polí, musí se poslat spolu s každou žádostí (HTTP POST). V těchto polích, musí se poslat na začátku datového proudu a ingestování kdykoli kodér musíte znovu připojit ke obnovit datového proudu. Další informace najdete v části 6 v [1].
2. Oddíl 3.3.2 v [1] definuje volitelné pole názvem **StreamManifestBox** pro za provozu ingestování. Z důvodu logice směrování pro vyrovnávání zatížení Azure pomocí toto pole je zastaralé. Pole není by měla být k dispozici při příjem ve službě Media Services. Pokud toto pole je přítomen, Media Services je bezobslužně ignoruje.
3. **TrackFragmentExtendedHeaderBox** musí být k dispozici pro každý fragment pole definovaná v 3.2.3.2 v [1].
4. Verze 2 **TrackFragmentExtendedHeaderBox** pole se má použít k vygenerování médiu segmentech, které mají stejné adresy URL v několika datových centrech. Pole indexu fragment je REQUIRED mezi datacenter převzetí služeb při selhání na základě indexu streamování formátů, jako je například Apple HLS a na základě indexu MPEG-DASH. Pokud chcete povolit převzetí služeb při selhání mezi datacenter, fragment index musí synchronizovat napříč více kodéry a zvýší o 1 pro každý fragment následných média, dokonce i v jiných kodér restartování nebo selhání.
5. Oddíl 3.3.6 v [1] definuje pole s názvem **MovieFragmentRandomAccessBox** (**mfra**), mohou být odeslány na konci za provozu přijímání znamenat end z stream (SESTAVENÁ) do kanálu. Z důvodu logice ingestování Media Services, je pomocí SESTAVENÁ zastaralé a **mfra** pole pro přijímání za provozu by neměla být odeslána. Pokud se odeslat, Media Services ji bezobslužně ignoruje. Pokud chcete resetovat stav bodu ingestování, doporučujeme použít [resetování kanálu](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Také doporučujeme použít [zastavit Program](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) na konec prezentace a datový proud.
6. Doba trvání fragment MP4 by měla být konstantní ke snížení velikosti manifesty klientů. Konstantní MP4 fragment trvání taky zlepšuje heuristiky stažení klienta prostřednictvím opakujte značky. Doba trvání může kolísá kompenzovat kmitočet celé číslo.
7. Doba trvání MP4 fragment, musí být mezi přibližně 2 až 6 sekund.
8. MP4 fragmentovat časová razítka a indexy (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` a `fragment_index`) by MĚL doručení ve vzestupném pořadí. Sice odolné vůči duplicitní fragmenty Media Services, má omezenou schopnost změnit pořadí fragmenty podle časovou osu média.

## <a name="4-protocol-format--http"></a>4. Formát protokolu – HTTP
ISO fragmentovaný soubor MP4 na základě za provozu ingestování pro Media Services využívá standardní požadavku HTTP POST dlouho běžící k přenosu kódovaného média data, která je součástí fragmentovaných formátu MP4 ke službě. Každý HTTP POST odešle úplná fragmentovaný proud MP4 ("stream"), od začátku s polí hlavičky (**ftyp**, **Live pole Manifest serveru**, a **moov** polí ) a pokračujte v sekvenci fragmenty (**moof** a **mdat** polí). Syntaxe adresy URL pro požadavek HTTP POST naleznete v části in 9.2 [1]. Příklad adresy URL POST je: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Požadavky
Zde jsou uvedeny podrobné požadavky:

1. Kodér měli začít vysílání pomocí stejné adresy URL přijímání odeslání požadavku HTTP POST se prázdný "textem" (nulové délky obsahu). To může pomoct kodér rychle zjistit, jestli koncový bod přijímání za provozu je platný, a pokud neexistují žádné ověřování nebo jinými podmínkami vyžaduje. Na protokolu HTTP server nelze odeslat zpět odpovědi HTTP dokud není přijata žádost o včetně textu POST. Vzhledem k povaze dlouho běžící živé události, bez tohoto kroku kodér nemusí být schopna zjistit všechny chyby, dokud dokončením odeslání všechna data.
2. Kodér musí zpracovat žádné chyby nebo výzev ověřování z důvodu (1). Pokud (1) úspěšné odpovědi 200, pokračovat.
3. Kodér musí začínat fragmentovaný proud MP4 nový požadavek HTTP POST. Do polí hlavičky, za nímž následuje fragmenty musí začínat datové části. Všimněte si, že **ftyp**, **Live pole Manifest serveru**, a **moov** polí (v tomto pořadí), musí se poslat s každou žádostí, i v případě, že kodér musíte znovu připojit, protože předchozí Požadavek byl ukončen před konec datového proudu. 
4. Kodér musí používat kódování pro odesílání, protože není možné předpovědět celou délku obsahu živé události bloku přenosu.
5. Pokud události myši, po odeslání poslední fragment kodér musí končit řádně blokové kódování sekvenci zpráv (většina zásobníky klienta HTTP zpracování ji automaticky) přenosu. Kodér musí čekat na službu, kterou chcete návratový kód konečné odpovědi a pak ukončit připojení. 
6. Kodér nesmí použít `Events()` podstatné jméno, jak je popsáno v 9.2 in [1] pro přijímání za provozu ve službě Media Services.
7. Pokud požadavek HTTP POST ukončí nebo časového limitu s chybou TCP před konec datového proudu, musí kodér předal nový požadavek POST s použitím nového připojení a postupujte podle výše uvedených požadavků. Kromě toho musí kodér znovu odeslal předchozí dva MP4 fragmenty pro každý sledování v datovém proudu a pokračovat bez zavedení nespojitost v časové ose média. Odešlete posledních dvou MP4 fragmenty pro každý sledování zajišťuje, že existuje nedošlo ke ztrátě dat. Jinými slovy pokud obsahuje datového proudu audio a video sledovat a aktuální žádost POST selže, kodér musíte znovu připojit a opakujte odeslání poslední dva fragmenty pro zvuk sledování, které byly dříve úspěšně odeslán, a posledních dvou fragmenty pro přehrávání videa sledování, které byly dříve úspěšně odeslány, zajistěte, aby nedošlo ke ztrátě dat. Kodér musí zachovat "forward" vyrovnávací paměti fragmenty média, které opětovně odešle při opětovném připojení.

## <a name="5-timescale"></a>5. Časová osa
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) popisuje použití časový rámec pro **SmoothStreamingMedia** (část 2.2.2.1) a **StreamElement** (část 2.2.2.3) a **StreamFragmentElement** () Část 2.2.2.6), a **LiveSMIL** (část 2.2.7.3.1). Pokud časový rámec hodnota neexistuje, je výchozí hodnota používaná pro 10 000 000 (10 MHz). I když specifikaci formátu technologie Smooth Streaming nebrání použití jiných hodnot časový rámec, většina implementace kodér používat toto výchozí nastavení hodnotu (10 MHz) ke generování, technologie Smooth Streaming ingestovat data. Z důvodu [Azure Media dynamické balení](media-services-dynamic-packaging-overview.md) funkce, doporučujeme použít za 90 KHz časový rámec pro datové proudy videa a 44,1 KHz nebo 48.1 KHz pro zvukové datové proudy. Pokud jiný časový rámec hodnoty se používají pro různé datové proudy, musí se poslat časový rámec úrovni datového proudu. Další informace najdete v tématu [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definice "stream"
Datový proud je základní jednotkou operace v za provozu přijímání pro sestavování za provozu prezentací, zpracování datových proudů převzetí služeb při selhání a redundance scénáře. Datový proud je definován jako jeden jedinečný, fragmentovaných MP4 proud, který může obsahovat jediné stopy nebo několik stop. Prezentace úplné online může obsahovat jeden nebo více datových proudů, v závislosti na konfiguraci kodéry za provozu. Následující příklady znázorňují různé možnosti používání datových proudů do tvoří úplné živé prezentaci.

**Příklad:** 

Chce zákazník vytvořit živé streamování prezentace, které zahrnuje následující přenosových rychlostí zvuku a videa:

Video – 3000 kB/s, 1 500 kb/s, 750 kb/s

Zvuk – 128 kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>Možnost 1: Všechny sleduje v jeden datový proud
V tuto možnost jeden kodér generuje všechny sleduje zvuku a videa a pak je do jedné fragmentovaný proud MP4 obsahuje ureitou. Fragmentovaný proud MP4 se pak odešlou prostřednictvím jednoho připojení HTTP POST. V tomto příkladu je pouze jeden datový proud pro prezentaci online.

![Sledování datové proudy: 1][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Možnost 2: Každý sledování v samostatných datového proudu
Tuto možnost kodér umístí jeden sledování do každé proud fragment MP4 a odešle všechny datové proudy přes samostatné připojení prostřednictvím protokolu HTTP. To lze provést pomocí kodéru jeden nebo více kodéry. Za provozu přijímání uvidí prezentaci online jako se skládá z čtyři datových proudů.

![Sleduje samostatné datové proudy][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Možnost 3: Sady zvukové stopy s nejnižší sledovat videa přenosovou rychlostí do jednoho datového proudu
Tuto možnost Zákazník zvolí svázat zvukové stopy s sledovat videa nejnižší přenosovou rychlostí do jednoho fragment MP4 proud a nechte ostatní dva video sleduje jako samostatné datové proudy. 

![Sleduje datové proudy zvuk a video][image4]

### <a name="summary"></a>Souhrn
Nejedná se o vyčerpávající seznam všech možných přijímání možností v tomto příkladu. Jako matter z skutečnosti za provozu přijímání podporuje všechny seskupení sleduje do datových proudů. Zákazníci a dodavatelé encoder můžete zvolit vlastní implementací na základě engineering složitost, kodér kapacitu a redundanci a důležité informace o převzetí služeb při selhání. Ale ve většině případů není pouze jeden zvuk sledovat v celé prezentaci za provozu. Ano je důležité zajistit healthiness ingestování datového proudu obsahující zvuk sledovat. Tento aspekt často vede k uvedení zvukové stopy v vlastní datový proud (jako možnost 2) nebo sdružování s sledovat videa nejnižší přenosovou rychlostí (jako možnost 3). Navíc lepší redundanci a odolnost proti chybám, odesílání stejné zvuk sledovat ve dvou různých datových proudů (možnost 2 s redundantní zvukových stop) nebo sdružování zvukové stopy s alespoň dvě z video sleduje nejnižší přenosovou rychlostí (možnost 3 s zvuk dodávat alespoň dvou datové proudy videa) důrazně doporučujeme pro za provozu ingestování ve službě Media Services.

## <a name="7-service-failover"></a>7. Převzetí služeb při selhání služby
Zadaný povaha živé vysílání datového proudu je důležité pro zajištění dostupnosti služby podpory funkční převzetí služeb při selhání. Služba Media Services je navržen pro zpracování různých typů chyb, včetně chyby sítě, server chyb a problémů s úložištěm. Při použití ve spojení s logikou správné převzetí služeb při selhání ze strany kodér za provozu, můžete dosáhnout zákazníkům vysoce spolehlivá služba živého streamování z cloudu.

V této části probereme scénáře převzetí služeb při selhání služby. V takovém případě selhání probíhá někde v rámci služby, a projeví se to jako chybu v síti. Tady jsou některá doporučení pro implementaci kodéru pro zpracování převzetí služeb při selhání služby:

1. Použijte 10 sekundu vypršení časového limitu pro vytvoření připojení protokolu TCP. Pokud pokus o připojení trvá déle než 10 sekund, zrušení operace a akci opakujte. 
2. Použijte krátký časový limit pro odesílání zpráv bloky požadavek HTTP. Pokud platnost cíl MP4 fragment po dobu N sekund, použijte časový limit odeslání N až 2 po dobu N sekund; Například pokud doba fragment MP4 je 6 sekund, použijte vypršení časového limitu 6 až 12 sekund. Pokud dojde k vypršení časového limitu, obnovení připojení, otevřít nové připojení a obnovit datový proud ingestování na nové připojení. 
3. Udržujte postupného vyrovnávací paměť, která má posledních dvou fragmenty pro každý sledování, které byly úspěšně a úplně odeslány do služby.  Pokud požadavku HTTP POST pro datový proud je ukončeno, nebo časového limitu před konec datového proudu, otevřít nové připojení a zahájit další požadavek HTTP POST, znovu odeslat hlavičky datového proudu, znovu odeslat poslední dva fragmenty pro každý sledovat a obnovit datový proud bez Úvod d iscontinuity v časové ose média. Tím se snižuje riziko ztráty dat.
4. Doporučujeme vám, že kodér neomezuje počet opakovaných pokusů připojení nebo obnovení streamování potom, co dojde k chybě protokolu TCP.
5. Po chybě TCP:
  
    a. Aktuální připojení musí být uzavřeny a je třeba vytvořit nové připojení, a to pro nový požadavek HTTP POST.

    b. Nové HTTP POST adresa URL musí být stejný jako počáteční adresa URL POST.
  
    c. Nové POST protokolu HTTP musí obsahovat záhlaví datového proudu (**ftyp**, **Live pole Manifest serveru**, a **moov** polí), jsou stejné jako datový proud hlavičky v počáteční POST.
  
    d. Poslední dva fragmenty odeslané pro každý sledovat nutné odeslat znovu, a vysílání datového proudu musí pokračovat bez zavedení nespojitost v časové ose média. Časová razítka fragment MP4 musíte zvýšit nepřetržitě, i přes požadavky HTTP POST.
6. Kodér musí ukončit požadavku HTTP POST, jestliže data nejsou odesílána rychlostí úměrná trvání fragment MP4.  Požadavek HTTP POST, který neodesílá data zabránit rychle z kodéru odpojení v případě aktualizaci služby Media Services. Z tohoto důvodu HTTP POST pro zhuštěné (signál ad) sleduje by měla být krátkodobou, ukončení hned, jak se odesílají zhuštěných fragment.

## <a name="8-encoder-failover"></a>8. Kodér převzetí služeb při selhání
Kodér převzetí služeb při selhání je druhý typ scénáře převzetí služeb při selhání, který je potřeba vzít v úvahu pro živé streamování doručení začátku do konce. V tomto scénáři k chybovému stavu dojde na straně kodér. 

![Kodér převzetí služeb při selhání][image5]

Když kodér převzetí služeb při selhání se stane, platí následující očekávání z koncového bodu za provozu přijímání:

1. Chcete-li pokračovat, streamování, by MĚL vytvořit novou instanci kodér, jak je znázorněno na obrázku (datový proud pro 3000 tisíc video s přerušovanou čáru).
2. Nové kodér musí používat stejnou adresu URL pro požadavky HTTP POST jako nezdařených instancí.
3. Požadavek POST na nový kodér musí obsahovat stejnou polí fragmentovaných MP4 záhlaví jako nezdařených instancí.
4. Nové kodér musí správně synchronizovat s všechny ostatní spuštěné kodéry pro stejné za provozu prezentace ke generování synchronizoval ukázky zvuku a videa s hranicemi zarovnaný fragment.
5. Nový datový proud musí být sémanticky ekvivalentní s předchozí datového proudu a na úrovni záhlaví a fragment zaměnitelné.
6. Nové kodéru se pokuste ztráty dat co nejmenší. `fragment_absolute_time` a `fragment_index` média měli zvýšit fragmenty z bodu, kde poslední zastavení kodér. `fragment_absolute_time` a `fragment_index` měli zvýšit průběžně, ale je přípustné zavádět nespojitost, v případě potřeby. Media Services ignoruje fragmenty, které má již přijme a zpracuje, proto je lepší pečlivého odešlete fragmenty než se zavést nespojitosti v časové ose média. 

## <a name="9-encoder-redundancy"></a>9. Kodér redundance
V případě důležitých živých událostí, vyžádání i vyšší dostupnost a kvalitní uživatelský dojem, doporučujeme dosáhli bezproblémového převzetí služeb při selhání bez ztráty dat pomocí redundantní kodéry v režimu aktivní aktivní.

![Kodér redundance][image6]

Jak je ukázáno v tomto diagramu, dvě skupiny kodéry dvě kopie každé datového proudu současně push ve službě za provozu. Tato instalace je podporována, protože Media Services můžete filtrovat podle ID a fragment datového proudu časové razítko duplicitní fragmenty. Výsledný živý datový proud a archivu je jedna kopie všechny datové proudy, které je nejlepší možný agregace ze dvou zdrojů. Například v hypotetický extreme případě, pokud je jeden kodér (nemusí to být stejný jako ten) spuštěná v libovolném bodě v čase pro každý datový proud, výsledná živý datový proud ze služby je souvislý bez ztráty dat. 

Požadavky pro tento scénář jsou téměř stejné jako v případě "Kodér převzetí", s výjimkou spuštěným druhá sada kodéry ve stejnou dobu jako primární kodéry požadavky.

## <a name="10-service-redundancy"></a>10. Služba redundance
Pro vysoce redundantní globální distribuci někdy musí mít mezi oblastmi zálohování pro zpracování místní havárie. Rozšíření na topologii "Kodér redundance", zákazníci můžou rozhodnout pro nasazení redundantní služby v jiné oblasti, která je propojená s druhá sada kodéry. Zákazníci také můžete pracovat s poskytovatelem Content Delivery Network nasazení globální správce provozu před nasazení dvou služby bezproblémově směrovat přenosy klienta. Požadavky kodéry jsou stejné jako "Kodér redundance" případu. Jedinou výjimkou je, že druhá sada kodéry musí být odkazoval na jiný za provozu ingestování koncový bod. Následující diagram znázorňuje tento instalační program:

![Služba redundance][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Speciální typy přijímání formáty
Tato část popisuje speciální typy provozu přijímání formátů, které jsou určeny ke zpracování konkrétní scénáře.

### <a name="sparse-track"></a>Zhuštěný sledování
Při předvádění živé streamování prezentace plně funkčního klienta prostředí, často je nutné přenášet synchronizovat čas události nebo signály integrovaná s daty hlavní média. Příkladem je vkládání reklam dynamické za provozu. Tento typ signalizace událostí se liší od regulární zvuku a videa streamování z důvodu jeho zhuštěných povahy. Jinými slovy signalizační data obvykle neodehrává nepřetržitě a interval může být snadno předpovědět. Koncept zhuštěných sledovat byla navržená tak, aby ingestování a všesměrového vysílání v Vzdálená signalizační data.

Následující kroky jsou doporučené implementace pro příjem zhuštěných sledovat:

1. Vytvořte samostatný fragmentovaný proud MP4, obsahující pouze zhuštěných sleduje bez sleduje zvuku a videa.
2. V **Live pole Manifest serveru** definuje v části 6 [1], použijte *parentTrackName* parametr zadat název nadřazené sledovat. Další informace najdete v části 4.2.1.2.1.2 v [1].
3. V **Live pole Manifest serveru**, **manifestOutput** musí být nastavena na **true**.
4. Dané zhuštěných podstatě signalizační událostí, doporučujeme následující:
   
    a. Na začátku živé události, odešle kodér polí hlavičky počáteční službu, která umožňuje služby zhuštěné sledovat zaregistrovat v manifestu klienta.
   
    b. Kodér musí ukončit požadavku HTTP POST, když se data neodesílají. Rychle se odpojuje od kodér v případě restartu služby update nebo serveru služby Media Services můžete zabránit HTTP POST dlouho běžící, který není odesílat data. V těchto případech je server mediálních dočasně blokovaných v operace příjmu na soketu.
   
    c. V době, kdy signalizační data nejsou k dispozici kodér SHOULD zavřete POST protokolu HTTP žádosti. Při požadavku POST je aktivní, měli kodér odesílat data.

    d. Při odesílání zhuštěných fragmenty, modulu encoder můžete nastavit explicitní hlavičku content-length, pokud je k dispozici.

    e. Při odesílání zhuštěných fragmenty se nové připojení, začněte kodér odesílání do polí hlavičky, za nímž následuje nové fragmenty. Je to pro případy, ve kterých se stane, převzetí služeb při selhání mezi a nové zhuštěných připojení bylo zavedeno na nový server, který nebylo nikdy zhuštěných sledovat před.

    f. Zhuštěný sledovat fragment k dispozici pro klienta po odpovídající fragment sledovat nadřazené, který má hodnotu větší nebo rovno časové razítko je k dispozici pro klienta. Například pokud zhuštěných fragment obsahuje časové razítko t = 1 000, očekává se, které po klienta vidí "video" (za předpokladu, že název nadřazeného sledovat je "video") fragmentovat časové razítko 1000 nebo i mimo může stahovat zhuštěných fragment t = 1 000. Všimněte si, že skutečný signál by mohly být použity pro jinou pozici v časové ose prezentace jeho určený účel. V tomto příkladu je možné, zhuštěných fragment t = 1 000 má datovou část XML, který je pro vkládání ad do pozice, který je několik sekund později.

    g. Datová část zhuštěných sledovat fragmenty může být v různých formátech (například XML, text nebo binární), v závislosti na scénáři.

### <a name="redundant-audio-track"></a>Redundantní zvuk sledování
Typický HTTP adaptivní streamování scénář (například technologie Smooth Streaming nebo pomlčkou) často má jenom jeden zvukové stopy v celé prezentace. Na rozdíl od sleduje video, které mají více úrovní kvality pro klienta výběr ve chybové stavy, zvuk sledovat může být jediný bod selhání Pokud možnému datový proud, který obsahuje audio track je poškozená. 

Chcete-li tento problém vyřešit, služba Media Services podporuje za provozu přijímání redundantní zvukových stop. Cílem je, že stejné zvuk sledovat lze odeslat vícekrát v různých datových proudů. I když služba pouze zaregistruje zvuk sledovat jednou v manifestu klienta, může použít redundantní zvukových stop jako záložní pro načítání zvuku fragmenty, pokud má primární zvuk sledování problémů. Pro ingestování redundantní zvukových stop, kodéru, musí:

1. Vytvořte stejné zvuk sledovat více fragment MP4 bitstreams. Redundantní zvukových stop musí být sémanticky ekvivalentní, časová razítka stejné fragment a být na úrovni záhlaví a fragment zaměnitelné.
2. Zkontrolujte, zda položka "zvuk" v za provozu Server manifestu (část 6 v [1]) stejný pro všechny redundantní zvukových stop.

Následující implementace se doporučuje pro redundantní zvukových stop:

1. Odeslat každý jedinečný zvukové stopy v datovém proudu samostatně. Navíc odesílat redundantní datový proud pro každou z těchto datových proudů zvuk sledovat, kde druhý datový proud se liší od prvního pouze podle identifikátoru v adrese URL protokolu HTTP POST: {protokolu} :// {serveru address} / {publikování path}/Streams({identifier}) bodu.
2. Použijte samostatné datové proudy k odeslání dvě nejnižší video přenosových rychlostí. Každý z těchto datových proudů by obsahovat také kopii každý jedinečný zvuk sledovat. Například když podporuje více jazyky, těchto datových proudů by MĚLO obsahovat zvuk sleduje pro jednotlivé jazyky.
3. Použijte samostatný server (kodér) instance pro kódování a odesílat redundantní uveden v datové proudy (1) a (2). 

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
