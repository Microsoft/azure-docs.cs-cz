---
title: Specifikace správy živého přenosu služby Azure Media Services | Dokumenty společnosti Microsoft
description: Tato specifikace popisuje protokol a formát pro fragmentované živé streamování založené na MP4 pro Azure Media Services. Tento dokument také popisuje osvědčené postupy pro vytváření vysoce redundantních a robustních mechanismů pro správu živého provozu.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 507afad294e8233ea4de4130795f29925870fcdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888049"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Specifikace správy živého vysílání služby Azure Media Services 

Tato specifikace popisuje protokol a formát pro fragmentované živé streamování založené na MP4 pro Azure Media Services. Media Services poskytuje službu živého streamování, kterou můžou zákazníci použít k streamování živých událostí a vysílání obsahu v reálném čase pomocí Azure jako cloudové platformy. Tento dokument také popisuje osvědčené postupy pro vytváření vysoce redundantních a robustních mechanismů pro správu živého provozu.

## <a name="1-conformance-notation"></a>1. Zápis shody
Klíčová slova "MUSÍ", "NESMÍ", "POVINNÉ", "MUSÍ", "NESMÍ", "BY", "BY NEMĚL", "DOPORUČENO", "MAY" a "VOLITELNÉ" v tomto dokumentu musí být vykládány tak, jak jsou popsány v Dokumentu RFC 2119.

## <a name="2-service-diagram"></a>2. Schéma služeb
Následující diagram znázorňuje architekturu vysoké úrovně služby živého streamování ve službě Media Services:

1. Živý kodér odesílá živé kanály do kanálů, které jsou vytvořené a zřízené prostřednictvím sady Azure Media Services SDK.
1. Kanály, programy a koncové body datových proudů ve službě Media Services zpracovávají všechny funkce živého streamování, včetně ingestování, formátování, cloudového DVR, zabezpečení, škálovatelnosti a redundance.
1. Volitelně zákazníci mohou nasadit vrstvu sítě doručování obsahu Azure mezi koncovým bodem streamování a koncovými body klienta.
1. Koncové body klienta streamují z koncového bodu streamování pomocí protokolů http adaptivního streamování. Mezi příklady patří Microsoft Smooth Streaming, Dynamic Adaptive Streaming over HTTP (DASH, mpeg-DASH) a Apple HTTP Live Streaming (HLS).

![požití toku][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream formát - ISO 14496-12 roztříštěné MP4
Formát drátu pro živé vysílání ingestů popsaných v tomto dokumentu je založen na [ISO-14496-12]. Podrobné vysvětlení fragmentovaného formátu MP4 a rozšíření pro soubory videa na vyžádání i pro živé streamování naleznete v tématu [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definice formátu živého ingestování
Následující seznam popisuje definice speciálního formátu, které se vztahují na živé ingestování do služby Azure Media Services:

1. **Ftyp**, **Live Server Manifest Box**a **moov** boxy musí být zaslány s každým požadavkem (HTTP POST). Tato pole musí být odeslána na začátku datového proudu a pokaždé, když kodér musí znovu připojit k obnovení datového proudu ingestest. Další informace naleznete v oddíle 6 v [1].
1. Oddíl 3.3.2 v [1] definuje volitelné pole s názvem **StreamManifestBox** pro živé ingestestování. Vzhledem k logice směrování nástrojů pro vyrovnávání zatížení Azure pomocí tohoto pole se zastaralá. Pole by nemělo být přítomno při ingestování do mediálních služeb. Pokud je toto pole k dispozici, media services tiše ignoruje.
1. **TrackFragmentExtendedHeaderBox** box definované v 3.2.3.2 v [1] musí být přítomen pro každý fragment.
1. Verze 2 pole **TrackFragmentExtendedHeaderBox** by měla být použita ke generování segmentů médií, které mají stejné adresy URL ve více datových centrech. Pole indexu fragmentu je povinné pro převzetí služeb při selhání mezi datovými centry formátů streamování založených na indexu, jako je Apple HLS a Index-based MPEG-DASH. Chcete-li povolit převzetí služeb při selhání mezi datovými centry, musí být index fragmentu synchronizován mezi více kodéry a zvýšen o 1 pro každý následující fragment média, a to i napříč restartováním nebo selháním kodéru.
1. Oddíl 3.3.6 v [1] definuje pole s názvem **MovieFragmentRandomAccessBox** (**mfra**), které může být odesláno na konci živého požití k označení konce datového proudu (EOS) do kanálu. Vzhledem k ingestlogiky Media Services, pomocí EOS je zastaralé a **mfra** box pro živé požití by neměly být odeslány. Pokud je odeslána, media Services tiše ignoruje. Chcete-li obnovit stav bodu ingestování, doporučujeme použít [obnovení kanálu](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Doporučujeme také, abyste k ukončení prezentace a datového proudu pomocí [programu Stop](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) ukončili prezentaci.
1. Doba trvání fragmentu MP4 by měla být konstantní, chcete-li zmenšit velikost manifestů klienta. Konstantní doba trvání fragmentu MP4 také zlepšuje heuristiku stahování klientů pomocí opakovaných značek. Doba trvání může kolísat kompenzovat non-celé číslo snímkové frekvence.
1. Doba trvání fragmentu MP4 by měla být přibližně 2 až 6 sekund.
1. MP4 fragment časová razítka a indexy `fragment_index`**(TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` a ) by měly dorazit v rostoucím pořadí. Přestože je služba Media Services odolná vůči duplicitním fragmentům, má omezenou schopnost uspořádat fragmenty podle časové osy média.

## <a name="4-protocol-format--http"></a>4. Formát protokolu – HTTP
Iso fragmentovaný příjem živého vysílání pro media services založený na protokolu MP4 používá standardní dlouhotrvající požadavek HTTP POST k přenosu kódovaných mediálních dat, která jsou zabalena v fragmentovaném formátu MP4 do služby. Každý HTTP POST odešle kompletní fragmentovaný MP4 bitstream ("stream"), počínaje začátkem s hlavičkovými boxy (**ftyp**, **Live Server Manifest Box**a **moov** boxy) a pokračuje sekvencí fragmentů (**mola** a **mdat** boxů). Syntaxe adresy URL pro požadavek HTTP POST naleznete v části 9.2 v [1]. Příkladem adresy URL POST je: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Požadavky
Zde jsou podrobné požadavky:

1. Kodér by měl spustit vysílání odesláním požadavku HTTP POST s prázdným "tělem" (nulová délka obsahu) pomocí stejné adresy URL ingestování. To může pomoci kodér rychle zjistit, zda je platný koncový bod živého ingestování a zda jsou vyžadovány nějaké ověřování nebo jiné podmínky. Podle protokolu HTTP nemůže server odeslat zpět odpověď HTTP, dokud nebude přijat celý požadavek, včetně těla POST. Vzhledem k dlouhotrvající povaze živé události bez tohoto kroku nemusí být kodér schopen rozpoznat žádnou chybu, dokud nedokončí odesílání všech dat.
1. Kodér musí zpracovat všechny chyby nebo problémy ověřování z důvodu (1). Pokud (1) uspěje s odpovědí 200, pokračujte.
1. Kodér musí spustit nový požadavek HTTP POST s fragmentovaným datovým proudem MP4. Datová část musí začínat s hlavičkami polí, následovaný fragmenty. Všimněte si, že **ftyp**, **Live Server Manifest Box**a **moov** boxy (v tomto pořadí) musí být odeslány s každou žádost, i v případě, že kodér musí znovu připojit, protože předchozí požadavek byl ukončen před koncem datového proudu. 
1. Kodéru musí používat blokový přenos kódování pro nahrávání, protože je nemožné předpovědět celou délku obsahu živé události.
1. Když událost je u konce, po odeslání poslední fragment, kodér musí řádně ukončit blokový přenos kódování zprávy sekvence (většina http klientské zásobníky zpracovat automaticky). Kodér musí čekat na službu vrátit kód konečné odpovědi a potom ukončit připojení. 
1. Kodér nesmí používat `Events()` nosné označení popsané v části 9.2 v [1] pro živé požití do mediálních služeb.
1. Pokud požadavek HTTP POST ukončí nebo časový limit s chybou TCP před koncem datového proudu, kodér musí vydat nový požadavek POST pomocí nového připojení a postupujte podle předchozích požadavků. Kromě toho musí kodér znovu odeslat předchozí dva fragmenty MP4 pro každou stopu v datovém proudu a pokračovat bez zavedení diskontinuity v časové ose média. Opětovné odeslání posledních dvou fragmentů MP4 pro každou stopu zajišťuje, že nedojde ke ztrátě dat. Jinými slovy, pokud datový proud obsahuje zvuk ovou stopu i stopu videa a aktuální požadavek POST se nezdaří, musí se kodér znovu připojit a znovu odeslat poslední dva fragmenty zvukové stopy, které byly dříve úspěšně odeslány, a poslední dva fragmenty pro video dříve úspěšně odeslány, aby se zajistilo, že nedojde ke ztrátě dat. Kodér musí udržovat "vpřed" vyrovnávací paměť fragmentů média, které se znovu odešle při opětovném připojení.

## <a name="5-timescale"></a>5. Časová osa
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) popisuje použití časové osy pro **SmoothStreamingMedia** (oddíl 2.2.2.1), **StreamElement** (oddíl 2.2.2.3), **StreamFragmentElement** (oddíl 2.2.2.6) a **LiveSMIL** (oddíl 2.2.7.3.1). Pokud hodnota časové osy není k dispozici, výchozí hodnota je 10 000 000 (10 MHz). Přestože specifikace formátu Plynulé streamování neblokuje použití jiných hodnot časové osy, většina implementací kodéru používá tuto výchozí hodnotu (10 MHz) ke generování dat ingestování plynulého streamování. Vzhledem k funkci [Dynamické balení médií Azure](media-services-dynamic-packaging-overview.md) doporučujeme použít časovou osu 90 KHz pro datové proudy videa a 44,1 KHz nebo 48,1 KHz pro zvukové datové proudy. Pokud se pro různé datové proudy používají různé hodnoty časové osy, musí být odeslána časová osa na úrovni datového proudu. Další informace naleznete v tématu [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definice "potoka"
Datový proud je základní jednotka provozu při živém ingestování pro vytváření živých prezentací, zpracování přepouštění služeb při selhání streamování a scénáře redundance. Datový proud je definován jako jeden jedinečný, fragmentovaný datový proud MP4, který může obsahovat jednu stopu nebo více stop. Úplná živá prezentace může obsahovat jeden nebo více datových proudů v závislosti na konfiguraci živých kodérů. Následující příklady ilustrují různé možnosti použití datových proudů k vytvoření úplné živé prezentace.

**Příklad:** 

Zákazník chce vytvořit prezentaci živého přenosu, která obsahuje následující přenosové rychlosti zvuku a videa:

Video – 3000 kbps, 1500 kbps, 750 kbps

Zvuk – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Možnost 1: Všechny stopy v jednom proudu
V této možnosti jeden kodér generuje všechny zvukové a video stopy a pak je sváže do jednoho fragmentovaného datového proudu MP4. Fragmentovaný datový proud MP4 je pak odeslán prostřednictvím jediného připojení HTTP POST. V tomto příkladu je pouze jeden datový proud pro tuto živou prezentaci.

![Streamy-jedna stopa][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Možnost 2: Každá stopa v samostatném datovém proudu
V této možnosti kodér vloží jednu stopu do každého fragmentu mp4 bitstream a potom zaúčtuje všechny datové proudy přes samostatné připojení HTTP. To lze provést pomocí jednoho kodéru nebo s více kodéry. Živé požití vidí tuto živou prezentaci jako složenou ze čtyř proudů.

![Stopy oddělující datové proudy][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Možnost 3: Složka zvukové stopy s nejnižší přenosovou rychlostí video stopy do jednoho datového proudu
V této možnosti se zákazník rozhodne sdružovat zvukovou stopu s nejnižší přenosovou rychlostí videostopu do jednoho fragmentu datového proudu MP4 a ponechat další dvě stopy videa jako samostatné datové proudy. 

![Streamy-audio a video stopy][image4]

### <a name="summary"></a>Souhrn
Toto není vyčerpávající seznam všech možných možností požití v tomto příkladu. Ve skutečnosti je jakékoli seskupení stop do streamů podporováno živým požitím. Zákazníci a dodavatelé kodéru si mohou vybrat vlastní implementace na základě technické složitosti, kapacity kodéru a aspekty redundance a převzetí služeb při selhání. Ve většině případů však existuje pouze jedna zvuková stopa pro celou živou prezentaci. Proto je důležité zajistit zdraví ingestování datového proudu, který obsahuje zvukovou stopu. Tato úvaha často vede k uvedení zvukové stopy do vlastního datového proudu (jako v možnosti 2) nebo sdružování s nejnižší přenosovou rychlostí video stopy (jako v možnosti 3). Také pro lepší redundanci a odolnost proti chybám, odesílání stejné zvukové stopy ve dvou různých proudech (možnost 2 s redundantními zvukovými stopami) nebo sdružování zvukové stopy s nejméně dvěma videostopami s nejnižší přenosovou rychlostí (možnost 3 se zvukem svázaným alespoň ve dvou video streamů) je vysoce doporučeno pro živé příjem do mediálních služeb.

## <a name="7-service-failover"></a>7. Převzetí služeb při selhání
Vzhledem k povaze živého streamování je pro zajištění dostupnosti služby důležitá dobrá podpora převzetí služeb při selhání. Služba Media Services je určena pro zpracování různých typů selhání, včetně chyb v síti, chyb serveru a problémů s úložištěm. Při použití ve spojení s vlastní logikou převzetí služeb při selhání ze strany živého kodéru mohou zákazníci dosáhnout vysoce spolehlivé služby živého streamování z cloudu.

V této části probereme scénáře převzetí služeb převzetí služeb převzetí služeb. V tomto případě selhání se stane někde v rámci služby a projeví se jako chyba sítě. Tady jsou některá doporučení pro implementaci kodéru pro zpracování převzetí služeb převzetí služeb převzetí služeb:

1. Pro navázání připojení TCP použijte časový limit 10 sekund. Pokud pokus o navázání připojení trvá déle než 10 sekund, přerušte operaci a akci opakujte. 
1. Pro odeslání bloků zpráv požadavku HTTP použijte krátký časový čas. Pokud je cílová doba trvání fragmentu MP4 N sekund, použijte časový rozsah odeslání mezi N a 2 N sekundami; Pokud je například doba trvání fragmentu MP4 6 sekund, použijte časový čas 6 až 12 sekund. Pokud dojde k časovému limitu, obnovte připojení, otevřete nové připojení a pokračujte v příjemi datového proudu v novém připojení. 
1. Udržovat postupné vyrovnávací paměti, která má poslední dva fragmenty pro každou stopu, které byly úspěšně a zcela odeslány do služby.  Pokud je požadavek HTTP POST pro datový proud ukončen nebo vypršel časový limit před koncem datového proudu, otevřete nové připojení a zahajte další požadavek HTTP POST, znovu odešlete záhlaví datového proudu, znovu odešlete poslední dva fragmenty pro každou stopu a pokračujte v datovém proudu bez zavedení v časové ose médií. Tím se snižuje pravděpodobnost ztráty dat.
1. Doporučujeme, aby kodér neomezuje počet opakovaných pokusů o navázání připojení nebo obnovení streamování po výskytu chyby TCP.
1. Po chybě TCP:
  
    a. Aktuální připojení musí být ukončeno a musí být vytvořeno nové připojení pro nový požadavek HTTP POST.

    b. Nová adresa URL HTTP POST musí být stejná jako počáteční adresa URL POST.
  
    c. Nový HTTP POST musí obsahovat hlavičky datového proudu **(ftyp**, **Live Server Manifest Box**a **moov** box), které jsou shodné s hlavičkami datového proudu v počátečním POST.
  
    d. Poslední dva fragmenty odeslané pro každou stopu musí být znovu odeslány a streamování musí pokračovat bez zavedení diskontinuity v časové ose média. Časová razítka fragmentu MP4 se musí průběžně zvyšovat, a to i v rámci požadavků HTTP POST.
1. Kodér by měl ukončit požadavek HTTP POST, pokud data nejsou odesílána rychlostí úměrnou době trvání fragmentu MP4.  Požadavek HTTP POST, který neodesílá data, může zabránit mediálním službám v rychlém odpojení od kodéru v případě aktualizace služby. Z tohoto důvodu http post pro řídké (ad signál) stopy by měly být krátkodobé, ukončení, jakmile je odeslán řídké fragment.

## <a name="8-encoder-failover"></a>8. Převzetí služeb při selhání kodéru
Převzetí služeb při selhání kodéru je druhý typ scénáře převzetí služeb při selhání, který je třeba řešit pro doručení živého streamování od konce. V tomto scénáři dojde k chybové podmínky na straně kodéru. 

![převzetí služeb při selhání kodéru][image5]

Následující očekávání platí od koncového bodu živého požití, když dojde k převzetí služeb při selhání kodéru:

1. Nová instance kodéru by měla být vytvořena, aby pokračovala ve streamování, jak je znázorněno v diagramu (Stream pro video 3000 kb s přerušovanou čárou).
1. Nový kodér musí použít stejnou adresu URL pro požadavky HTTP POST jako instance se nezdařilo.
1. Požadavek POST nového kodéru musí obsahovat stejná fragmentovaná pole záhlaví MP4 jako instance, která selhala.
1. Nový kodér musí být správně synchronizován se všemi ostatními spuštěnými kodéry pro stejnou živou prezentaci, aby se generovaly synchronizované vzorky zvuku a videa se zarovnanými hranicemi fragmentu.
1. Nový datový proud musí být sémanticky ekvivalentní s předchozí datový proud a zaměnitelné na hlavičky a fragment úrovně.
1. Nový kodér by se měl pokusit minimalizovat ztrátu dat. `fragment_absolute_time` Fragmenty `fragment_index` média a média by se měly zvýšit od místa, kde se kodér naposledy zastavil. `fragment_absolute_time` A `fragment_index` by se měla neustále zvyšovat, ale je přípustné zavést diskontinuitu, pokud je to nutné. Služba Media Services ignoruje fragmenty, které již obdržela a zpracovala, takže je lepší chybovat na straně opětovného odesílání fragmentů než zavádět nespojitosti v časové ose médií. 

## <a name="9-encoder-redundancy"></a>9. Redundance kodéru
U některých kritických živých událostí, které vyžadují ještě vyšší dostupnost a kvalitu zkušeností, doporučujeme používat aktivní aktivní redundantní kodéry k dosažení bezproblémového převzetí služeb při selhání bez ztráty dat.

![redundance kodéru][image6]

Jak je znázorněno v tomto diagramu, dvě skupiny kodéry push dvě kopie každého datového proudu současně do živé služby. Toto nastavení je podporováno, protože služba Media Services může odfiltrovat duplicitní fragmenty na základě ID datového proudu a časového razítka fragmentu. Výsledný živý datový proud a archiv je jedna kopie všech datových proudů, která je nejlepší možnou agregací z těchto dvou zdrojů. Například v hypotetickém extrémním případě, pokud existuje jeden kodér (nemusí být stejný) spuštěný v daném okamžiku pro každý datový proud, výsledný živý proud ze služby je nepřetržitý bez ztráty dat. 

Požadavky pro tento scénář jsou téměř stejné jako požadavky v případě převzetí služeb při selhání "Kodér", s výjimkou, že druhá sada kodéry jsou spuštěny současně s primární kodéry.

## <a name="10-service-redundancy"></a>10. Redundance služeb
Pro vysoce redundantní globální distribuci někdy musíte mít zálohu mezi oblastmi pro zpracování regionálních havárií. Rozšíření množení "Redundance kodéru" si zákazníci mohou vybrat, zda mají redundantní nasazení služby v jiné oblasti, která je propojena s druhou sadou kodéry. Zákazníci mohou také spolupracovat s poskytovatelem sítě pro doručování obsahu a nasadit globálního manažera provozu před dvěma nasazeními služeb za účelem bezproblémového směrování provozu klientů. Požadavky na kodéry jsou stejné jako případ redundance kodéru. Jedinou výjimkou je, že druhá sada kodéry musí být poukázal na jiný koncový bod živého ingestestování. Následující diagram znázorňuje toto nastavení:

![redundance služeb][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Speciální typy formátů požití
Tato část popisuje speciální typy formátů živého ingestování, které jsou navrženy tak, aby zpracovávat konkrétní scénáře.

### <a name="sparse-track"></a>Řídká stopa
Při předvádění živé prezentace s bohatým klientem je často nutné přenášet časově synchronizované události nebo signály v pásmu s hlavními mediálními daty. Příkladem je dynamické vkládání živých reklam. Tento typ signalizace událostí se liší od běžného streamování zvuku a videa kvůli jeho řídké povaze. Jinými slovy, signalizační data se obvykle neděje nepřetržitě a interval může být těžké předvídat. Koncept řídké skladby byl navržen tak, aby schvaloval a vysílal in-band signalizační data.

Následující kroky jsou doporučené implementace pro požití řídké stopy:

1. Vytvořte samostatný fragmentovaný datový proud MP4, který obsahuje pouze řídké stopy, bez zvukových a obrazových stop.
1. V **poli manifestu živého serveru,** jak je definováno v části 6 v [1], použijte parametr *parentTrackName* k určení názvu nadřazené stopy. Další informace viz bod 4.2.1.2.1.2 v [1].
1. V **poli manifestu živého serveru**musí být **manifestOutput** nastaven na **hodnotu true**.
1. Vzhledem k řídké povaze signalizační události doporučujeme následující:
   
    a. Na začátku živé události kodér odešle počáteční záhlaví pole do služby, která umožňuje službě zaregistrovat řídké stopy v manifestu klienta.
   
    b. Kodér by měl ukončit požadavek HTTP POST při neodeslání dat. Dlouhotrvající protokol HTTP POST, který neodesílá data, může zabránit mediálním službám v rychlém odpojení od kodéru v případě aktualizace služby nebo restartování serveru. V těchto případech je server médií dočasně blokován v operaci příjmu na soketu.
   
    c. Během doby, kdy nejsou k dispozici signalizační data, by měl kodér zavřít požadavek HTTP POST. Když je požadavek POST aktivní, kodér by měl odesílat data.

    d. Při odesílání řídké fragmenty kodér můžete nastavit explicitní obsah délka záhlaví, pokud je k dispozici.

    e. Při odesílání řídké fragmenty s novým připojením kodér by měl začít odesílat z polí záhlaví, následovaný nové fragmenty. Toto je pro případy, ve kterých dojde k převzetí služeb při selhání mezi a nové řídké připojení je navázáno na nový server, který dosud neviděl řídké stopy.

    f. Řídké track fragment bude k dispozici klientovi při odpovídající nadřazené stopy fragment, který má stejnou nebo větší hodnotu časového razítka je k dispozici klientovi. Například pokud řídké fragment má časové razítko t = 1000, očekává se, že poté, co klient vidí "video" (za předpokladu, že název nadřazené stopy je "video") fragment časové razítko 1000 nebo dále, můžete stáhnout řídké fragment t = 1000. Všimněte si, že skutečný signál by mohl být použit pro jinou pozici v časové ose prezentace pro jeho určený účel. V tomto příkladu je možné, že řídké fragmentt= 1000 má datovou část XML, která je pro vložení reklamy na pozici, která je o několik sekund později.

    g. Datová část řídké stopy fragmenty mohou být v různých formátech (například XML, text nebo binární), v závislosti na scénáři.

### <a name="redundant-audio-track"></a>Redundantní zvuková stopa
V typickém scénáři adaptivního streamování HTTP (například Plynulé streamování nebo DASH) je často v celé prezentaci pouze jedna zvuková stopa. Na rozdíl od stop videa, které mají více úrovní kvality pro klienta vybrat v chybových podmínkách, zvuková stopa může být jeden bod selhání, pokud je přerušeno příjem datového proudu, který obsahuje zvukovou stopu. 

Chcete-li tento problém vyřešit, Media Services podporuje živé požití redundantních zvukových stop. Myšlenka spoje na tom, že stejnou zvukovou stopu lze odeslat vícekrát v různých streamech. Přestože služba zaregistruje zvukovou stopu pouze jednou v manifestu klienta, může použít redundantní zvukové stopy jako zálohy pro načítání zvukových fragmentů, pokud má primární zvuková stopa problémy. Chcete-li spotřebovat redundantní zvukové stopy, musí kodér:

1. Vytvořte stejnou zvukovou stopu ve více fragmentech datových proudů MP4. Redundantní zvukové stopy musí být sémanticky ekvivalentní, se stejnýmfragment emotama a být zaměnitelné na úrovni záhlaví a fragmentu.
1. Ujistěte se, že položka "audio" v manifestu živého serveru (oddíl 6 v [1]) je stejná pro všechny redundantní zvukové stopy.

Pro redundantní zvukové stopy se doporučuje následující implementace:

1. Odešlete každou jedinečnou zvukovou stopu v datovém proudu samostatně. Také odešlete redundantní datový proud pro každý z těchto datových proudů zvukových stop, kde se druhý datový proud liší od prvního pouze identifikátorem v adrese URL HTTP POST: {protocol}://{server address}/{publishing point path}/Streams({identifier}).
1. K odeslání dvou nejnižších datových proudů videa použijte samostatné datové proudy. Každý z těchto datových proudů by měl také obsahovat kopii každé jedinečné zvukové stopy. Pokud je například podporováno více jazyků, měly by tyto datové proudy obsahovat zvukové stopy pro každý jazyk.
1. Pomocí samostatných instancí serveru (kodéru) můžete kódovat a odesílat redundantní datové proudy uvedené v (1) a (2). 

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
