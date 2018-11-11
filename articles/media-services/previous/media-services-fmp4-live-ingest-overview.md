---
title: Specifikace ingestování fragmentovaného MP4 za Azure Media Services | Dokumentace Microsoftu
description: Tato specifikace popisuje protokol a formáty fragmentovaného MP4 podle živého streamování ingestování pro Azure Media Services. Azure Media Services můžete používat streamování události v reálném čase a vysílat obsah v reálném čase s využitím Azure jako cloudovou platformu. Tento dokument popisuje také osvědčené postupy pro sestavování vysoce redundantní a robustní živé ingestování mechanismy.
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
ms.openlocfilehash: c6ff386913ed66cf4f74cb577bb8ca58e6932ada
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228874"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Specifikace ingestování fragmentovaného MP4 za Azure Media Services
Tato specifikace popisuje protokol a formáty fragmentovaného MP4 podle živého streamování ingestování pro Azure Media Services. Služba Media Services poskytuje služby živého streamování, které zákazníci mohou používat streamování události v reálném čase a vysílat obsah v reálném čase s využitím Azure jako cloudovou platformu. Tento dokument popisuje také osvědčené postupy pro sestavování vysoce redundantní a robustní živé ingestování mechanismy.

## <a name="1-conformance-notation"></a>1. Zápis shody
Klíč slova ",""Nesmí," "REQUIRED" "SHALL", "se nesmí," "SHOULD", "By neměl," "Doporučené" "Může" a "Volitelné" v tomto dokumentu mají být interpretován tak, jak jsou popsané v dokumentu RFC 2119.

## <a name="2-service-diagram"></a>2. Diagram služby
Následující diagram znázorňuje základní architektura služeb živého streamování služby Media Services:

1. Live encoder nabízených oznámení živých kanálů na kanály, které jsou vytvořeny a zřídit přes Azure Media Services SDK.
1. Kanály, programy a koncové body streamování ve službě Media Services zpracování všech funkcí živého streamování, včetně ingestování, formátování, DVR, zabezpečení, škálovatelnosti a redundance v cloudu.
1. Zákazníci mohou volitelně k nasazení Azure Content Delivery Network vrstvu mezi koncový bod streamování a koncové body klienta.
1. Datový proud, koncové body klienta z koncového bodu streamování pomocí protokolu HTTP adaptivní streamování protokolů. Mezi příklady patří, Microsoft Smooth Streaming, dynamické adaptivní streamování přes HTTP (DASH nebo MPEG-DASH) a Apple HTTP Live Streaming (HLS).

![příjem toku][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Proud formátu – ISO 14496 12 fragmentovaného MP4
Přenosový formát pro živé streamování ingestování popsané v tomto dokumentu je podle [ISO-14496-12]. Podrobné vysvětlení fragmentovaného MP4 formátu a rozšíření i pro soubory videa na vyžádání a živé ingestování datových proudů, naleznete v tématu [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Živé ingestování definice formátů
Následující seznam popisuje zvláštní formát definice, které platí pro živé ingestování do služby Azure Media Services:

1. **Ftyp**, **Live pole serveru Manifest**, a **moov** polí se musí odeslat spolu s každou žádostí (HTTP POST). Tato pole se musí odeslat na začátku datového proudu a ingestovat pokaždé, když musíte znovu připojit ke kodéru obnovit datový proud. Další informace najdete v části 6 v [1].
1. Oddíl 3.3.2 v [1] definuje do volitelné pole, která volá **StreamManifestBox** pro živé ingestování. Z důvodu logiku směrování pro vyrovnávání zatížení Azure pomocí tohoto pole je zastaralé. Do pole by neměl být k dispozici, když se ingestuje do služby Media Services. Pokud toto políčko je k dispozici, Media Services je tiše ignoruje.
1. **TrackFragmentExtendedHeaderBox** pole definované v 3.2.3.2 v [1] musí být k dispozici pro každý fragment.
1. Verze 2 **TrackFragmentExtendedHeaderBox** pole by měla sloužit ke generování segmenty médií, které mají stejné adresy URL v několika datových centrech. Fragment index pole je povinné pro převzetí služeb při selhání mezi datovým centrem na základě indexu datových proudů formátů, například Apple HLS a MPEG-DASH podle indexu. Povolit převzetí služeb při selhání mezi datovým centrem, index fragmentu musí synchronizovat napříč více kodérů a zvýší o 1 pro každý fragment po sobě jdoucích media i přes kodér restartování nebo selhání.
1. Oddíl 3.3.6 v [1] definuje pole s názvem **MovieFragmentRandomAccessBox** (**mfra**), který může odeslat na konci živé ingestování udávajících end-datového proudu (SESTAVENÁ) do kanálu. Z důvodu logiku ingestování Media Services, je zastaralé pomocí SESTAVENÁ a **mfra** pole pro živé ingestování by neměla být odeslána. V případě odeslaný, Media Services tiše ignoruje ji. Chcete-li obnovit stav bodu ingestování, doporučujeme použít [kanál resetovat](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Doporučujeme také, že používáte [zastavení programu](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) prezentace a datového proudu.
1. Doba trvání fragment MP4 by měla být konstantní, aby se zmenšila velikost manifesty klientů. Konstantní MP4 fragment trvání také zlepšuje heuristiky stažení klienta prostřednictvím opakujte značky. Doba trvání může kolísat jako kompenzaci za snímkových jiných než celých čísel.
1. Doba trvání fragment MP4 by měla být mezi přibližně 2 až 6 sekundami.
1. MP4 fragment časová razítka a indexy (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` a `fragment_index`) by MĚL dorazí ve vzestupném pořadí. Sice odolnost duplicitních fragmentů Media Services, má omezenou schopnost změnit pořadí fragmenty podle na časové ose média.

## <a name="4-protocol-format--http"></a>4. Formát protokolu – HTTP
ISO fragmentovaný soubor MP4 podle živé ingestování pro Media Services používá standardní požadavku HTTP POST dlouho běžící přenášet data kódovaného média, která je zabalený ve formátu fragmentovaného MP4 do služby. Každý HTTP POST odešle kompletní fragmentovaný proud MP4 ("datový proud"), od začátku s polí hlavičky (**ftyp**, **Live pole serveru Manifest**, a **moov** polí ) a pokračujte v sekvenci fragmenty (**moof** a **mdat** polí). Syntaxe adresy URL pro odeslání požadavku HTTP POST najdete v části 9.2 v [1]. Je například adresa URL příspěvku: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Požadavky
Tady je podrobné požadavky:

1. Kodér začínat vysílání odesláním požadavku HTTP POST s prázdný "základní text" (nulové délky obsahu) pomocí stejné adresy URL ingestování. To může pomoct kodér rychle zjistit, jestli je platný koncový bod živé ingestování, a pokud neexistují žádné ověřování nebo jiné podmínky. Na protokolu HTTP server nelze odeslat zpět odpověď HTTP dokud přijata žádost o včetně textu POST. Vzhledem k povaze dlouhotrvající živou událost, bez tohoto kroku kodér nemusí být schopna zjistit všechny chyby, dokud nebude dokončeno odesílání všechna data.
1. Kodér musí umět zpracovat žádné chyby nebo výzev ověřování z důvodu (1). Pokud (1) úspěšná odpověď 200, pokračovat.
1. Kodér musí začínat fragmentovaný proud MP4 nový požadavek HTTP POST. Datová část musí začínat znakem pole záhlaví, za nímž následuje fragmenty. Všimněte si, že **ftyp**, **Live pole serveru Manifest**, a **moov** pole (v tomto pořadí) se musí odeslat spolu s každou žádostí, i v případě, že kodér musíte znovu připojit, protože předchozí Požadavek byl ukončen před koncem datového proudu. 
1. Kodér musí používat kódování pro odesílání, protože není možné předpovědět celou délku obsahu živou událost přenosu bloků.
1. Po události, po odeslání poslední fragment, musí být kodér řádně ukončen blokového kódovací sekvenci zpráv (většina zásobníky klienta HTTP ji zpracovat automaticky). Kodér musí čekat na návratový kód poslední odpovědi služby a pak ukončit připojení. 
1. Kodér musí použít `Events()` podstatné jméno, jak je popsáno v 9.2 v [1] pro živé ingestování do Media Services.
1. Pokud požadavek HTTP POST skončí nebo časového limitu TCP chybou před koncem datového proudu, musíte kodér předal nový požadavek POST s použitím nového připojení a postupujte podle výše uvedených požadavků. Kromě toho musí kodér znovu odeslat předchozí dva fragmenty MP4 pro každý sledování v datovém proudu a pokračovat bez vnášení diskontinuitu na časové ose média. Nové odeslání poslední dva fragmenty MP4 pro každý sledování zajišťuje, že nedochází ke ztrátě dat. Jinými slovy Pokud datový proud obsahuje audio a video sledování a aktuální požadavek POST selže, kodér musíte znovu připojit a znovu odeslat poslední dva fragmenty zvukové stopy, které byly úspěšně jste odeslali dřív, a poslední dva fragmenty pro video sledování, které byly dřív úspěšně odeslána, ujistěte se, že nedochází ke ztrátě dat. Kodér, musíte mít "forward" rezervu fragmenty média, které se odešle při opětovném připojení.

## <a name="5-timescale"></a>5. Timescale
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) popisuje využití časový rámec pro **SmoothStreamingMedia** (část 2.2.2.1) a **StreamElement** (část 2.2.2.3) a **StreamFragmentElement** () Části 2.2.2.6), a **LiveSMIL** (části 2.2.7.3.1). Pokud hodnota měřítka není k dispozici, je výchozí hodnota používaná pro 10 000 000 (10 MHz). I když specifikace formátu technologie Smooth Streaming nebrání použití jiné časové hodnoty, většina implementací kodér použít tuto výchozí hodnotu (10 MHz) ke generování, technologie Smooth Streaming ingestovat data. Z důvodu [Azure Media dynamické balení](media-services-dynamic-packaging-overview.md) funkcí, doporučujeme použít 90 KHz časovou osu pro datové proudy videa a 44,1 KHz nebo 48.1 KHz pro zvukové datové proudy. Pokud různé časové osy hodnoty se používají pro různé datové proudy, se musí odeslat na časové ose úrovni datového proudu. Další informace najdete v tématu [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definice "datového proudu"
Stream je základní jednotkou operací v živé ingestování pro vytváření živé prezentace zpracování datových proudů převzetí služeb při selhání a redundance scénáře. Stream je definován jako jeden jedinečný, fragmentované MP4 proud, který může obsahovat jediné nebo více stopy. Úplné živou prezentaci může obsahovat jeden nebo více datových proudů v závislosti na konfiguraci kodéry. Následující příklady znázorňují různé možnosti použití datových proudů sestavit úplný živou prezentaci.

**Příklad:** 

Zákazník si chce vytvořit živou prezentaci datových proudů, která zahrnuje následující přenosových rychlostí audio/video:

Video – 3000 kB/s, 1 500 kb/s, 750 kb/s

Audio – 128 kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>Možnost 1: Všechny stopy do jednoho datového proudu
Při použití této možnosti jedné kodér vygeneruje všechny stopy audio/video a potom jim obsahuje ureitou do jednoho fragmentovaný proud MP4. Fragmentovaný proud MP4 se pak odešlou přes samostatné připojení HTTP POST. V tomto příkladu je pouze jeden datový proud pro tato živá prezentace.

![Sledování datových proudů: 1][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Možnost 2: Každý sledovat v samostatných datového proudu
Při použití této možnosti kodér do jednotlivých proud fragment MP4 Vloží jednu stopu a publikuje všechny datové proudy přes samostatné připojení prostřednictvím protokolu HTTP. To můžete udělat s kodér jeden nebo více kodérů. Živé ingestování vidí tato živá prezentace, jak se skládá ze čtyř datových proudů.

![Datové proudy samostatné stopy][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Možnost 3: Vytvoření balíčku zvukové stopy s nejnižší sledovat videa s přenosovou rychlostí do jednoho datového proudu
Při použití této možnosti zákazník vybere sadu zvukové stopy s sledovat videa nejnižší s přenosovou rychlostí v proud jeden fragment MP4 a ponechte další dvě videa sleduje jako samostatné datové proudy. 

![Sleduje datové proudy zvuku a videa][image4]

### <a name="summary"></a>Souhrn
Nejedná se o vyčerpávající seznam všech možných ingestování možností pro účely tohoto příkladu. Jako skutečnosti živé ingestování podporuje všechny seskupení sleduje do datových proudů. Zákazníci a dodavatelé encoder můžete zvolit vlastní implementace na základě engineering složitost, kodér kapacitu a redundanci a důležité informace o převzetí služeb při selhání. Ale ve většině případů je pouze jedna zvuková stopa pro celé živé prezentace. Proto je důležité pro zaručení healthiness ingestování datového proudu, který obsahuje zvukové stopy. Posouzení často za následek vložení zvukové stopy vlastní datový proud (viz možnost 2) nebo sdružování s sledovat videa nejnižší s přenosovou rychlostí (viz možnost 3). Navíc lépe redundanci a odolnost proti chybám, odesílání stejné zvukové stopy ve dvou různých datových proudů (možnost 2 s redundantní zvukové stopy) nebo sdružování zvukové stopy aspoň dvě videa sleduje nejnižší s přenosovou rychlostí (možnost 3 se zvukem dodávat alespoň dvou datové proudy videa) důrazně doporučujeme pro živé ingestování do Media Services.

## <a name="7-service-failover"></a>7. Převzetí služeb při selhání
Vzhledem k povaze živého streamování je důležité pro zajištění dostupnosti služby podpory dobré převzetí služeb při selhání. Služba Media Services je navržen pro zpracování různých typů chyb, včetně chyby sítě, server chyb a problémů s úložištěm. Při použití ve spojení s logikou správné převzetí služeb při selhání ze strany kodér služby live Encoding, zákazníci můžou dosáhnout vysoce spolehlivé služby živého streamování z cloudu.

V této části se podíváme na scénáře převzetí služeb při selhání služby. V tomto případě této chybě dojde někde v rámci služby, a projeví se to jako chybu v síti. Zde je několik doporučení pro implementaci kodéru pro zpracování převzetí služeb při selhání:

1. Pomocí 10 sekundách časový limit pro navázání připojení TCP. Pokud pokus o navázání připojení trvá déle než 10 sekund, přerušení operace a zkuste to znovu. 
1. Použijte krátký časový limit pro odesílání požadavku protokolu HTTP bloků zpráv. Pokud doba trvání cílové MP4 fragment je po dobu N sekund, použijte časový limit odeslání N až 2 N sekund. Například pokud doba trvání MP4 fragment je 6 sekund, použijte vypršení časového limitu 6 až 12 sekund. Pokud dojde k vypršení časového limitu, obnovení připojení, otevřít nové připojení a obnovit datový proud přijímat nová připojení. 
1. Zachovat posledních vyrovnávací paměti, který má poslední dva fragmenty pro každý stopu, které byly úspěšně a zcela odeslány do služby.  Pokud požadavku HTTP POST pro datový proud se ukončily nebo které vyprší časový limit před konce datového proudu, otevřít nové připojení a začít jiná žádost HTTP POST, znovu odeslat datový proud hlavičky, znovu odeslat poslední dva fragmenty pro každý sledovat a obnovit datový proud bez vnášení d iscontinuity na časové ose média. To snižuje riziko ztráty.
1. Doporučujeme vám, že kodér neomezuje počet opakovaných pokusů připojení nebo obnovení streamování, když dojde k chybě TCP.
1. Po chybě TCP:
  
    a. Aktuální připojení musí být uzavřeny, a je třeba vytvořit nové připojení pro nový požadavek HTTP POST.

    b. Nové HTTP POST adresa URL musí být stejný jako počáteční adresu URL příspěvku.
  
    c. Nový příspěvek HTTP musí obsahovat datový proud hlavičky (**ftyp**, **Live pole serveru Manifest**, a **moov** polí), které jsou stejné jako datový proud hlavičky v počáteční příspěvku.
  
    d. Musí být Zopakuje poslední dva fragmenty odeslány pro každý sledování a streamování musí pokračovat bez vnášení diskontinuitu na časové ose média. Časová razítka fragment MP4, musíte zvýšit nepřetržitě, i přes požadavky HTTP POST.
1. Kodér by měla ukončit požadavku HTTP POST, pokud se neodesílají data s rychlostí odpovídající s dobou trvání fragment MP4.  Požadavek HTTP POST, který neodesílá dat zabránit rychle se odpojuje od kodér v případě aktualizace služby Media Services. Z tohoto důvodu HTTP POST pro zhuštěné (reklamním signálu) sleduje by MĚL mít krátkodobé trvání, ukončuje ihned poté, co přijde zhuštěné fragment.

## <a name="8-encoder-failover"></a>8. Kodér převzetí služeb při selhání
Kodér převzetí služeb při selhání je druhý typ scénáře převzetí služeb při selhání, který musí být určena pro začátku do konce živého streamování. V tomto scénáři dojde k chybě na straně kodér. 

![Kodér převzetí služeb při selhání][image5]

Kodér převzetí služeb při selhání se stane z koncového bodu živé ingestování nastavte následujícím požadavkům:

1. Nová instance kodér měl by být vytvořen pokračovat datových proudů, jak je znázorněno na obrázku (Stream pro videa s přerušované čáry k 3000).
1. Nový kodér musí používat stejnou adresu URL pro požadavky HTTP POST jako chybné instance.
1. Nový kodér požadavek POST musí obsahovat stejné pole fragmentovaného MP4 záhlaví jako chybné instance.
1. Nový kodér, musí se všechny ostatní spuštěné kodérů pro stejný živou prezentaci ke generování synchronizovaných ukázky audio/video s hranicemi zarovnané fragment správně synchronizovat.
1. Nový datový proud musí být sémanticky ekvivalentní s předchozí datový proud a na úrovni záhlaví a fragment zaměnitelné.
1. Nový kodér snažte se ztráty dat co nejmenší. `fragment_absolute_time` a `fragment_index` médií fragmentů by MĚL zvýšit z bodu, kde poslední zastavení kodér. `fragment_absolute_time` a `fragment_index` by MĚL zvýšit průběžné způsobem, ale se dosáhlo zavést diskontinuitu, v případě potřeby. Media Services bude ignorovat fragmenty, které má již přijme a zpracuje, tak, aby byl lépe pečlivého opakovaným odesláním ji opravte fragmenty než uvedení nespojitosti na časové ose média. 

## <a name="9-encoder-redundancy"></a>9. Kodér úrovně redundance
U některých důležitých živých událostí, vyžadují ještě vyšší dostupnost a kvalitní prostředí, doporučujeme použít redundantní kodéry aktivní aktivní abyste dosáhli bezproblémového převzetí služeb při selhání bez ztráty dat.

![Kodér úrovně redundance][image6]

Jak je znázorněno v tomto diagramu, dvě skupiny kodérů dvě kopie jednotlivých datových proudech současně push do živého provozu. Tato instalace je podporována, protože Media Services můžete filtrovat podle časového razítka ID a fragment datového proudu duplicitních fragmentů. Výsledný živého datového proudu a archivovat je jedinou kopii všech datových proudů, která je nejlepší možné agregace ze dvou zdrojů. Například v hypotetické extreme případu, pokud je jeden kodér (to nemusí být stejný jako ten) spuštěná v libovolném časovém okamžiku v čase pro každý datový proud, výsledný živý datový proud ze služby je souvislý bez ztráty dat. 

Požadavky pro tento scénář jsou téměř stejné jako požadavky v tomto případě "Kodér převzetí", s tím rozdílem, druhá sada kodérů používáte ve stejnou dobu jako primární kodérů.

## <a name="10-service-redundancy"></a>10. Redundance služby
Pro vysoce redundantní globální distribuci někdy musí mít mezi různými oblastmi zálohování pro zpracování regionální havárií. Pokud rozvineme "Kodér redundance" topologie, zákazníci zvolit, aby nasazení redundantní služby v jiné oblasti, která je propojená s druhou sadu kodérů. Zákazníci můžou také spolupracovat s poskytovatelem Content Delivery Network k nasazení na globální Traffic managera před nasazením dvou služeb bez problémů směrovat přenosy klienta. Požadavky na u kodérů jsou stejné jako případ "Kodér redundance". Jedinou výjimkou je, že druhá sada kodérů je potřeba se odkazovala na jinou živé ingestování koncového bodu. Následující diagram znázorňuje tento instalační program:

![Redundance služby][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Speciální typy ingestování formátů
Tato část popisuje zvláštní typy živé ingestování formáty, které jsou určeny ke zpracování určitých scénářů.

### <a name="sparse-track"></a>Zhuštěný sledování
Při doručování živého streamování prezentace s plnohodnotné klientské prostředí, často je potřeba přenést událostí synchronizovat čas nebo signály integrovaná s hlavním multimediální data. Příkladem je dynamické živého vkládání reklam. Tento typ signalizace události se liší od pravidelných zvuk/video streamování z důvodu jeho zhuštěné povahy. Jinými slovy signalizační data obvykle neproběhne ani průběžně a interval může být obtížné odhadnout. Koncept zhuštěné sledování je navržená pro příjem a všesměrového vysílání signalizační data integrovaná.

Následující kroky jsou doporučenou implementaci pro příjem zhuštěné track:

1. Vytvoření samostatné fragmentovaný proud MP4, obsahující pouze zhuštěné sleduje bez audio/video stopy.
1. V **Live pole serveru Manifest** jak jsou definovány v části 6 v [1], použijte *parentTrackName* parametr k určení názvu nadřazené sledování. Další informace najdete v části 4.2.1.2.1.2 v [1].
1. V **Live pole serveru Manifest**, **manifestOutput** musí být nastaveno na **true**.
1. Vzhledem k zhuštěné povaze signalizace události, doporučujeme následující:
   
    a. Na začátku živou událost, odešle kodér počáteční záhlaví pole ke službě, která umožňuje služba určená k zaregistrování zhuštěné jeden směr určený v manifestu klienta.
   
    b. Kodér by měla ukončit požadavku HTTP POST, když se data neodesílají. Operace HTTP POST dlouho běžící, ne odesílání dat zabránit rychle se odpojuje od kodér v případě restartování update nebo serveru služby Media Services. V těchto případech je média server dočasně blokována v rámci operace příjem na soketu.
   
    c. Během doby, kdy signalizační dat není k dispozici zavřete kodér SHOULD HTTP POST požadavek. Požadavek POST je aktivní, by MĚL kodér odesílat data.

    d. Při odesílání zhuštěné fragmentů, můžete kodér explicitní hlavička content-length, nastavit, pokud je k dispozici.

    e. Při odesílání zhuštěné fragmenty s novým připojením, by MĚL kodér zahájit odesílání z pole záhlaví, za nímž následuje nové fragmenty. Toto je pro případy, ve kterých se stane, převzetí služeb při selhání mezi a nové zhuštěné připojení bylo zavedeno na nový server, který nebylo nikdy zhuštěné sledování před.

    f. Fragment zhuštěné sledování jsou k dispozici do klienta po odpovídající nadřazené sledování část, která se má hodnotu větší nebo rovna časové razítko je k dispozici pro klienta. Například pokud je zhuštěný fragment časové razítko t = 1 000, očekává se, která po klienta se zobrazí "video" (za předpokladu, že je název nadřazené sledování "video") fragment časové razítko 1000 nebo novější, může stáhnout sadu zhuštěných fragment t = 1 000. Všimněte si, že skutečné signálu může být pro jinou pozici na časové ose prezentaci pro určený účel. V tomto příkladu je možné, který zhuštěné fragment t = 1 000 má datovou část XML, který je pro vkládání ad v pozici, která je pár sekund později.

    g. Datová část fragmenty zhuštěné sledování může být v různých formátech (například XML, textový nebo binární), v závislosti na scénáři.

### <a name="redundant-audio-track"></a>Redundantní zvukové stopy
V rámci typického HTTP adaptivní streamování scénáře (například technologie Smooth Streaming nebo POMLČKY) často existuje pouze jedna zvuková stopa v celé prezentace. Na rozdíl od sleduje video, které mají více úrovní kvality pro klienta lze vybírat chybových podmínek, zvukové stopy může být jediný bod selhání, pokud ingestování datového proudu, který obsahuje zvukové stopy bylo přerušeno. 

Chcete-li tento problém vyřešit, služba Media Services podporuje živé ingestování redundantní zvukové stopy. Cílem je, že stejné zvukové stopy lze odeslat více než jednou v různých datových proudů. I když službu pouze zaregistruje zvukové stopy jednou v manifestu klienta, může využívat redundantní zvukové stopy jako zálohy pro načtení zvukové fragmenty, pokud primární zvukové stopy dochází k problémům. Ingestování redundantní zvukové stopy kodér potřebuje:

1. Vytvoření stejné zvukové stopy ve více fragmentu MP4 bitstreams. Redundantní zvukové stopy musí být sémanticky rovnocenné pomocí stejného fragmentu časová razítka a je zaměnitelné na úrovni záhlaví a fragment.
1. Ujistěte se, že "zvuku" položka v za provozu Server manifestu (oddíl 6 v [1]) je stejný pro všechny zvukové stopy redundantní.

Pro redundantní zvukové stopy se doporučuje následující implementaci:

1. Posílat každý jedinečný zvukové stopy v datovém proudu samostatně. Také odesílat datový proud redundantní pro každou z těchto datových proudů zvuková stopa, kde se liší od první druhého datového proudu pouze podle identifikátoru v adrese URL POST protokolu HTTP: {protokol} :// {adresa serveru} / {publikování path}/Streams({identifier}) bodu.
1. Pomocí samostatné datové proudy odesílat dva nejnižší videa přenosových rychlostí. Každá z těchto datových proudů by MĚL také obsahovat kopii každého jedinečný zvuková stopa. Například když podporuje více jazyků, by MĚL obsahovat tyto datové proudy zvukové stopy pro jednotlivé jazyky.
1. Použijte samostatný server (kodér) instance má být zakódován a odeslat redundantní datové proudy, které jsou uvedené v (1) a (2). 

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
