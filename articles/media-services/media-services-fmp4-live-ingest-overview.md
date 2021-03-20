---
title: Azure Media Services fragmentované specifikace programu MP4 Live ingestování | Microsoft Docs
description: Tato specifikace popisuje protokol a formát fragmentace živého streamování založeného na MP4 pro Azure Media Services. Tento dokument také popisuje osvědčené postupy pro vytváření vysoce redundantních a robustních mechanismů živého přijímání.
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
ms.openlocfilehash: 7323ae611431e1d91fd1a8471914be388fcc4712
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019507"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services fragmentované specifikace ingestování MP4 v reálném čase 

Tato specifikace popisuje protokol a formát fragmentace živého streamování založeného na MP4 pro Azure Media Services. Media Services poskytuje službu živého streamování, kterou můžou zákazníci využít ke streamování živých událostí a vysílání obsahu v reálném čase s využitím Azure jako cloudové platformy. Tento dokument také popisuje osvědčené postupy pro vytváření vysoce redundantních a robustních mechanismů živého přijímání.

## <a name="1-conformance-notation"></a>1. zápis shody
Klíčová slova "" nesmí "" vyžadovat "", "", "nesmí" "," "by neměl obsahovat" "by neměl 2119 být" "doporučeno" "," "doporučuje" "

## <a name="2-service-diagram"></a>2. diagram služby
Následující diagram znázorňuje architekturu služby živého streamování na nejvyšší úrovni v Media Services:

1. Live Encoder nabízí živé kanály na kanály, které se vytvoří a zřídí prostřednictvím sady Azure Media Services SDK.
1. Kanály, programy a koncové body streamování v Media Services zpracovávají veškeré funkce živého streamování, včetně ingestování, formátování, DVR, zabezpečení, škálovatelnosti a redundance.
1. Volitelně se můžou zákazníci rozhodnout nasadit vrstvu Content Delivery Network Azure mezi koncovým bodem streamování a koncovými body klienta.
1. Datový proud koncových bodů klienta z koncového bodu streamování pomocí protokolů adaptivního streamování HTTP. Mezi příklady patří Microsoft Smooth Streaming, dynamické adaptivní streamování přes HTTP (POMLČKa nebo MPEG-POMLČKa) a Apple HTTP Live Streaming (HLS).

![průběh ingestu][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream formát – ISO 14496-12 fragmentovaný MP4
Formát přenosu pro živé streamování, který je popsaný v tomto dokumentu, je založený na [ISO-14496-12]. Podrobné vysvětlení fragmentovaných formátů a rozšíření MP4 pro soubory videa na vyžádání a ingestování živých streamování najdete v tématu [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).

### <a name="live-ingest-format-definitions"></a>Definice formátu živého příjmu
Následující seznam popisuje speciální definice formátu, které se vztahují na živé ingestování do Azure Media Services:

1. Pole **ftyp**, **živý manifest serveru** a **Moov** musí být odesílány spolu s každým požadavkem (http post). Tato pole musí být odeslána na začátku datového proudu a kdykoli se kodér musí znovu připojit, aby pokračoval v zpracování datového proudu. Další informace najdete v oddílu 6 v [1].
1. Oddíl 3.3.2 v [1] definuje volitelné pole s názvem **StreamManifestBox** pro živou příjem dat. Vzhledem k logice směrování Azure Load Balancer je použití tohoto pole zastaralé. Pole by nemělo být přítomno při ingestování do Media Services. Pokud je toto pole k dispozici, Media Services ho v tichém režimu ignoruje.
1. **TrackFragmentExtendedHeaderBox** pole definované v 3.2.3.2 v [1] musí být k dispozici pro každý fragment.
1. K vygenerování segmentů médií, které mají stejné adresy URL v několika datových centrech, by se měla použít verze 2 **TrackFragmentExtendedHeaderBox** box. Pole index fragmentu je nutné pro převzetí služeb při selhání mezi datovými dataproudy pomocí indexu, jako je například Apple HLS a MPEG-SPOJOVNÍK založený na indexu. Pokud chcete povolit převzetí služeb při selhání napříč datovými centry, musí být fragmentový index synchronizovaný napříč několika kodéry a pro každý následný fragment média se zvýší o 1, a to i v případě, že dojde k restartování nebo selhání.
1. Oddíl 3.3.6 v [1] definuje pole s názvem **MovieFragmentRandomAccessBox** (**mfra**), které se dá odeslat na konci živého příjmu za účelem označení konce datového proudu (EOS) kanálu. Vzhledem k logice ingestování Media Services, použití EOS je zastaralé a **mfra** box pro živou příjem dat by se neměl odesílat. V případě odeslání Media Services v tichém režimu ignoruje. Pro obnovení stavu bodu ingest doporučujeme použít [resetování kanálu](/rest/api/media/operations/channel#reset_channels). Pro ukončení prezentace a streamu doporučujeme také použít [program stop](/rest/api/media/operations/program#stop_programs) .
1. Doba trvání fragmentu MP4 by měla být konstantní, aby se snížila velikost manifestů klienta. Konstantní doba trvání fragmentu MP4 také vylepšuje heuristické stahování klientů pomocí značek opakování. Doba trvání může kolísat pro kompenzaci sazeb snímků, které nejsou celé číslo.
1. Doba trvání fragmentu MP4 by měla být přibližně 2 až 6 sekund.
1. Časová razítka a indexy pro fragmenty MP4 (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` a `fragment_index` ) by měly dorazit ve vzestupném pořadí. I když Media Services je odolný vůči duplicitním fragmentům, má omezená schopnost změnit pořadí fragmentů podle časové osy médií.

## <a name="4-protocol-format--http"></a>4. formát protokolu – HTTP
ISO fragmentované živé ingestování na bázi MP4 pro Media Services používá standardní dlouhodobou žádost HTTP POST k přenosu kódovaných mediálních dat, která jsou zabalená do služby ve formátu fragmentů MP4. Každý HTTP POST pošle kompletní fragment Bitstream MP4 ("Stream") od začátku do polí záhlaví (**ftyp**, **Live Server manifest** a **Moov** box) a pokračuje se sekvencí fragmentů (pole **Moof** a **mdat** ). Syntaxi URL požadavku HTTP POST najdete v části 9,2 v [1]. Příklad adresy URL příspěvku: 

`http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)`

### <a name="requirements"></a>Požadavky
Zde jsou uvedené podrobné požadavky:

1. Kodér by měl zahájit všesměrové vysílání odesláním požadavku HTTP POST s prázdným textem "tělo" (s nulovou délkou obsahu) pomocí stejné adresy URL pro příjem dat. Díky tomu může kodér rychle zjistit, zda je koncový bod živého příjmu platný, a pokud jsou vyžadovány jiné požadavky. Na protokol HTTP nemůže server odeslat zpět odpověď HTTP, dokud nebude přijata celá žádost, včetně těla zprávy POST. S ohledem na dlouhodobou podobu živé události, bez tohoto kroku, kodér nemusí být schopný detekovat žádnou chybu, dokud nedokončí odesílání všech dat.
1. Kodér musí zpracovat všechny chyby nebo výzvy k ověření z těchto důvodů: (1). Pokud (1) uspěje s odpovědí 200, pokračujte.
1. Kodér musí spustit novou žádost HTTP POST s fragmentovaným datovým proudem MP4. Datová část musí začínat poli záhlaví následovanými fragmenty. Všimněte si, že pole **ftyp**, **živý manifest serveru** a **Moov** (v tomto pořadí) musí být odesílány spolu s každým požadavkem, i když se kodér musí znovu připojit, protože předchozí požadavek byl ukončen před koncem datového proudu. 
1. Kodér musí pro nahrávání použít kódování blokového přenosu, protože není možné předpovědět celou délku obsahu živé události.
1. Pokud po odeslání posledního fragmentu dojde k překročení této události, kodér musí řádně ukončit sekvenci zpráv kódování s blokovým přenosem (většina zásobníků klienta protokolu HTTP ho automaticky zpracuje). Kodér musí počkat, až služba vrátí konečný kód odezvy, a pak připojení ukončí. 
1. Kodér nesmí používat `Events()` podstatné jméno, jak je popsáno v 9,2 v [1] pro živou příjem dat do Media Services.
1. Pokud se požadavek HTTP POST ukončí nebo vyprší s chybou TCP před koncem datového proudu, kodér musí vystavit novou žádost POST pomocí nového připojení a postupovat podle předchozích požadavků. Kromě toho kodér musí znovu odeslat předchozí dva fragmenty MP4 pro každou stopu v datovém proudu a pokračovat bez zavedení nekontinuity na časové ose média. Opakované odeslání posledních dvou fragmentů MP4 pro každou stopu zajistí, že nedojde ke ztrátě dat. Jinými slovy, pokud datový proud obsahuje zvuk i video stop a aktuální požadavek POST se nezdaří, kodér se musí znovu připojit a znovu pošle poslední dva fragmenty zvukové stopy, které byly dříve úspěšně odeslány, a poslední dva fragmenty pro stopu videa, které byly dříve úspěšně odeslány, aby se zajistilo, že nedojde ke ztrátě dat. Kodér musí udržovat "dopředné" fragmenty média, které se znovu odesílají při opětovném připojení.

## <a name="5-timescale"></a>5. Časová osa
[[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) popisuje použití časové osy pro **SmoothStreamingMedia** (oddíl 2.2.2.1), **StreamElement** (oddíl 2.2.2.3), **StreamFragmentElement** (oddíl 2.2.2.6) a **LiveSMIL** (oddíl 2.2.7.3.1). Pokud hodnota časové osy není k dispozici, použije se výchozí hodnota 10 000 000 (10 MHz). I když specifikace formátu Smooth Streaming neblokuje použití jiných hodnot časové osy, většina implementací kodéru používá tuto výchozí hodnotu (10 MHz) pro generování Smooth Streaming ingestování dat. Vzhledem k funkci [dynamického balení médií Azure](./previous/media-services-dynamic-packaging-overview.md) doporučujeme pro streamování videa a 44,1 kHz nebo pro zvukové streamy použít časovou osu 90 – khz nebo 48,1 kHz. Pokud se pro různé datové proudy používají odlišné hodnoty časového měřítka, je nutné odeslat časovou osu na úrovni datového proudu. Další informace najdete v tématu [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).     

## <a name="6-definition-of-stream"></a>6. definice "Stream"
Stream je základní Jednotková operace v reálném ingestování pro vytváření živých prezentací, zpracování převzetí služeb při selhání streamování a scénářů redundance. Stream je definovaný jako jeden jedinečný, fragmentovaný Bitstream MP4, který může obsahovat jednu stopu nebo několik stop. Celá živá prezentace může obsahovat jeden nebo více datových proudů v závislosti na konfiguraci živých kodérů. Následující příklady ilustrují různé možnosti použití datových proudů k vytvoření úplné živé prezentace.

**Příklad:** 

Zákazník chce vytvořit prezentaci živého streamování, která obsahuje následující zvukové a video přenosové rychlosti:

Video – 3000 KB/s, 1500 KB/s, 750 kb/s

Zvuk – 128 kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>Možnost 1: všechny stopy v jednom datovém proudu
V této možnosti jeden kodér vygeneruje všechny stopy zvuku a videa a pak je zabalí do jedné fragmentované Bitstream MP4. Fragmentovaná Bitstream MP4 se pak pošle pomocí jednoho připojení HTTP POST. V tomto příkladu je pro tuto živou prezentaci pouze jeden datový proud.

![Streamy – jedna stopa][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Možnost 2: každou stopu v samostatném datovém proudu
V této možnosti kodér vloží jednu stopu do každého fragmentu MP4 bitstream a pak všechny datové proudy prostřednictvím samostatných připojení HTTP. To se dá udělat s jedním kodérem nebo s více kodéry. Živá příjem dat uvidí tuto živou prezentaci, která se skládá ze čtyř datových proudů.

![Datové proudy – oddělené stopy][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Možnost 3: rozbalí zvukovou stopu s nejnižší přenosovou rychlostí videa do jednoho streamu.
V této možnosti se zákazník rozhodne rozdělit zvukovou stopu pomocí videa s nejnižší přenosovou rychlostí v jedné fragmentaci MP4 bitstream a ponechte ostatní dvě stopy jako samostatné streamy. 

![Streamy – zvukové a video stopy][image4]

### <a name="summary"></a>Souhrn
Nejedná se o vyčerpávající seznam všech možných možností ingestování v tomto příkladu. Vzhledem k tomu, že živé ingestování podporuje jakékoliv seskupení skladeb do datových proudů. Zákazníci a technici v kodéru si můžou zvolit vlastní implementace založené na složitosti, kapacitě kodéru a požadavcích na redundanci a převzetí služeb při selhání. Ve většině případů je ale k dispozici pouze jedna zvuková stopa pro celou živou prezentaci. Proto je důležité zajistit healthiness datového proudu ingestování, který obsahuje zvukovou stopu. Toto posouzení často vede k tomu, že se zvukové stopy ukládají do vlastního streamu (jak je uvedeno v možnosti 2), nebo je nanavazuje na video s nejnižší přenosovou rychlostí (jako v možnosti 3). Pro zajištění lepší redundance a odolnosti proti chybám odesílají stejnou zvukovou stopu ve dvou různých datových proudech (možnost 2 s redundantními zvukovými stopami) nebo naváže zvukovou stopu s nejméně dvěma videosoubory s minimální přenosovou rychlostí (možnost 3 se zvukovým úložištěm alespoň dvou streamů videa Media Services).

## <a name="7-service-failover"></a>7. převzetí služeb při selhání
Vzhledem k povaze živého streamování je řádná podpora převzetí služeb při selhání důležitá pro zajištění dostupnosti služby. Media Services je navržený tak, aby zpracovával různé typy selhání, včetně chyb sítě, chyb serveru a problémů s úložištěm. Při použití ve spojení se správnou logikou převzetí služeb při selhání ze strany Live Encoder můžou zákazníci dosáhnout vysoce spolehlivé služby živého streamování z cloudu.

V této části probereme scénáře převzetí služeb při selhání. V takovém případě dojde k selhání někde v rámci služby a projeví se jako chyba sítě. Tady jsou některá doporučení pro implementaci kodéru pro zpracování převzetí služeb při selhání služby:

1. Pro vytvoření připojení TCP použijte časový limit 10 sekund. Pokud pokus o navázání připojení trvá déle než 10 sekund, přerušte operaci a zkuste to znovu. 
1. Použijte krátký časový limit pro odeslání bloků zpráv požadavku HTTP. Pokud je cílová doba trvání fragmentu MP4 N sekund, použijte časový limit odeslání mezi N a 2 N sekundami. Pokud je například doba trvání fragmentu MP4 6 sekund, použijte časový limit 6 až 12 sekund. Pokud dojde k vypršení časového limitu, obnovte připojení, otevřete nové připojení a pokračujte v práci s datovým proudem na novém připojení. 
1. Udržujte vyrovnávací paměť, která má poslední dva fragmenty pro každou stopu, která byla úspěšně a zcela odeslána do služby.  Pokud je požadavek HTTP POST pro datový proud ukončený nebo uplynulý před koncem datového proudu, otevřete nové připojení a zahajte další požadavek HTTP POST, znovu odešlete hlavičky streamu, znovu odešlete poslední dva fragmenty pro každou stopu a obnovíte datový proud, aniž byste museli zavádět nekontinuitu na časové ose médií. To snižuje riziko ztráty dat.
1. Doporučujeme, aby kodér neomezil počet opakovaných pokusů o navázání připojení nebo pokračování streamování po výskytu chyby TCP.
1. Po chybě TCP:
  
    a. Aktuální připojení musí být ukončeno a pro novou žádost HTTP POST je nutné vytvořit nové připojení.

    b. Nová adresa URL příspěvku HTTP musí být stejná jako počáteční adresa URL příspěvku.
  
    c. Nový příspěvek HTTP musí zahrnovat hlavičky streamu (**ftyp**, **Live Server manifest** a **Moov** box), které jsou stejné jako HLAVIČKY streamu v počátečním příspěvku.
  
    d. Poslední dva fragmenty odeslané pro každou stopu musí být znovu odeslány a streamování musí pokračovat bez zavedení výpadku na časové ose média. Časové razítko fragmentu MP4 se musí v případě požadavků HTTP POST průběžně zvyšovat.
1. Kodér by měl ukončit požadavek HTTP POST, pokud se data neodesílají rychlostí odpovídajícím době trvání fragmentu MP4.  Požadavek HTTP POST, který neodesílá data, může zabránit Media Services v rychlém odpojení od kodéru v případě aktualizace služby. Z tohoto důvodu by se měly sledovat zprávy HTTP POST pro zhuštěné (signální signály), které se ukončí ihned po odeslání zhuštěného fragmentu.

## <a name="8-encoder-failover"></a>8. kodér převzetí služeb při selhání
Převzetí služeb při selhání kodéru je druhým typem scénáře převzetí služeb při selhání, které je potřeba vyřešit pro komplexní doručování živého streamování. V tomto scénáři se chybový stav vyskytuje na straně kodéru. 

![převzetí služeb při selhání kodéru][image5]

V případě, že dojde k převzetí služeb při selhání kodéru, platí následující očekávání:

1. Aby bylo možné pokračovat v streamování, je třeba vytvořit novou instanci kodéru, jak je znázorněno v diagramu (Stream pro 3000k video s čárkovanou čárou).
1. Nový kodér musí pro požadavky HTTP POST používat stejnou adresu URL jako neúspěšná instance.
1. Požadavek POST nového kodéru musí zahrnovat stejná fragmentovaná pole záhlaví MP4 jako neúspěšná instance.
1. Nový kodér musí být správně synchronizovaný se všemi ostatními běžícími kodéry pro stejnou živou prezentaci pro vygenerování synchronizovaných ukázek zvuku a videa s zarovnanými hranicemi fragmentů.
1. Nový datový proud musí být sémanticky ekvivalentní předchozímu datovému proudu a zaměnitelné na úrovních hlavičky a fragmentu.
1. Nový kodér by se měl pokusit minimalizovat ztrátu dat. `fragment_absolute_time` `fragment_index` Fragmenty média a by se měly zvýšit od bodu, ve kterém se kodér naposledy zastavil. `fragment_absolute_time`A `fragment_index` měly by se narůstat průběžně, ale je přípustné, aby v případě potřeby zavedla nekontinuitu. Media Services ignoruje fragmenty, které již byly přijaty a zpracovány, takže je lepší se na straně opětovného odesílání fragmentů, než je zavedení nekontinuity na časové ose médií, poznamenat. 

## <a name="9-encoder-redundancy"></a>9. redundance kodéru
U některých důležitých živých událostí, které vyžadují ještě vyšší dostupnost a kvalitu zkušeností, doporučujeme, abyste k zajištění bezproblémového převzetí služeb při selhání bez ztráty dat použili redundantní kodéry aktivní-aktivní.

![redundance kodéru][image6]

Jak je znázorněno v tomto diagramu, dvě skupiny kodérů do živé služby přidávají dvě kopie každého streamu současně. Tato instalace je podporovaná, protože Media Services může vyfiltrovat duplicitní fragmenty na základě ID streamu a časového razítka fragmentu. Výsledný živý datový proud a archiv je jediná kopie všech streamů, které jsou nejlepší možností agregace ze dvou zdrojů. Například v hypotetickém extrémním případě, pokud je jeden kodér (nemusí být stejný) spuštěný v kterémkoli daném časovém okamžiku pro každý datový proud, výsledný živý datový proud z této služby je nepřetržitý bez ztráty dat. 

Požadavky pro tento scénář jsou skoro stejné jako požadavky v případu "kodér převzetí služeb při selhání" s výjimkou, že druhá sada kodérů běží ve stejnou dobu jako primární kodéry.

## <a name="10-service-redundancy"></a>10. redundance služby
V případě vysoce redundantní globální distribuce je někdy nutné mít zálohování mezi oblastmi, aby bylo možné manipulovat s místními katastrofami. Díky rozšiřování topologie "redundance kodéru" si zákazníci můžou vymezit redundantní nasazení služby v jiné oblasti, která je propojená s druhou sadou kodérů. Zákazníci mohou také spolupracovat s poskytovatelem Content Delivery Network k nasazení globálních Traffic Manager před oběma nasazeními služby za účelem bezproblémového směrování klientského provozu. Požadavky na kodéry jsou stejné jako v případě "" redundance "kodéru. Jedinou výjimkou je, že druhá sada kodérů musí ukazovat na jiný koncový bod pro živý příjem dat. Následující diagram znázorňuje tuto instalaci:

![redundance služby][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. speciální typy formátů přijímání
Tato část popisuje speciální typy živých ingest, které jsou navržené tak, aby zpracovávala konkrétní scénáře.

### <a name="sparse-track"></a>Zhuštěná stopa
Při doručování živého streamování prezentace s bohatými klientskými prostředími je často potřeba přenášet události synchronizovaných časem nebo signály v pásmu s daty z hlavního média. Příkladem je dynamické vložení živé reklamy. Tento typ signalizace události se liší od běžného streamování zvuku a videa z důvodu jeho zhuštěného charakteru. Jinými slovy, signální data většinou neprobíhá nepřetržitě a interval může být obtížné odhadnout. Koncept zhuštěného stop byl navržen pro ingestování a vysílání dat signálů v pásmu.

Následující postup je doporučenou implementací pro ingestování zhuštěného záznamu:

1. Vytvořte samostatný fragment Bitstream MP4, který obsahuje jenom zhuštěné stopy bez stop zvuk/video.
1. V **poli manifest živého serveru** , jak je definováno v oddílu 6 v [1], použijte parametr *parentTrackName* k určení názvu nadřazené stopy. Další informace najdete v části 4.2.1.2.1.2 v [1].
1. V **poli manifest živého serveru** musí být **manifestOutput** nastavené na **true**.
1. Pro zhuštěnou povahu události signalizace doporučujeme následující:
   
    a. Na začátku živé události kodér pošle pole počátečních hlaviček do služby, což umožňuje službě zaregistrovat zhuštěnou stopu v manifestu klienta.
   
    b. Kodér by měl ukončit požadavek HTTP POST, když se data neodesílají. Dlouho běžící příspěvek HTTP, který neodesílá data, může zabránit Media Services v rychlém odpojení od kodéru v případě aktualizace služby nebo restartování serveru. V těchto případech je multimediální server dočasně blokovaný v operaci Receive na soketu.
   
    c. V době, kdy nejsou data signalizace k dispozici, by měl kodér zavřít požadavek HTTP POST. I když je požadavek POST aktivní, kodér by měl odesílat data.

    d. Při odesílání zhuštěných fragmentů může kodér nastavit explicitní hlavičku Content-Length, pokud je k dispozici.

    e. Při odesílání zhuštěných fragmentů s novým připojením by měl kodér začít odesílat z polí záhlaví a za ním následuje nová fragmenty. Jedná se o případy, kdy dojde k převzetí služeb při selhání v-mezi a nové řídké připojení je navázáno na nový server, který předtím neviděl zhuštěnou stopu.

    f. Fragment zhuštěného sledování bude k dispozici klientovi, pokud je pro klienta k dispozici odpovídající nadřazený fragment sledování, který má stejnou nebo větší hodnotu časového razítka. Pokud má například zhuštěný fragment časové razítko t = 1000, je očekáváno, že po zobrazení "videa klienta" (za předpokladu, že název nadřazené stopy je "video") časové razítko fragmentu 1000 nebo mimo něj může stáhnout zhuštěný fragment t = 1000. Všimněte si, že skutečný signál lze použít pro jinou pozici v časové ose prezentace pro svůj vyhrazený účel. V tomto příkladu je možné, že zhuštěná fragment t = 1000 má datovou část XML, která je určena pro vložení reklamy na pozici, která je několik sekund později.

    například Datová část fragmentů zhuštěného sledování může být v různých formátech (například XML, text nebo binární), v závislosti na scénáři.

### <a name="redundant-audio-track"></a>Redundantní zvuková stopa
V typickém scénáři adaptivního streamování protokolu HTTP (například Smooth Streaming nebo POMLČKy) je často v celé prezentaci jenom jedna zvuková stopa. Na rozdíl od stop videa, která má u klienta několik úrovní kvality, z důvodu chybových stavů může být zvuková stopa jediným bodem selhání, pokud je porušeno přijímání datového proudu, který obsahuje zvukovou stopu. 

Chcete-li tento problém vyřešit, Media Services podporuje živé přijímání redundantních zvukových stop. Výsledkem je, že stejnou zvukovou stopu lze odeslat několikrát v různých datových proudech. I když služba zaregistruje zvukové stopy pouze jednou v manifestu klienta, může použít redundantní zvukové stopy jako zálohy pro načítání zvukových fragmentů v případě, že má primární zvuková stopa problémy. Aby bylo možné ingestovat redundantní zvukové stopy, musí kodér:

1. Vytvořte stejnou zvukovou stopu v několika fragmentech MP4 bitstreams. Redundantní zvukové stopy musí být sémanticky ekvivalentní, se stejnými časovými razítky fragmentů a být zaměnitelné na úrovních hlavičky a fragmentu.
1. Ujistěte se, že záznam "audio" v manifestu živého serveru (oddíl 6 v [1]) je stejný pro všechny redundantní zvukové stopy.

Pro redundantní zvukové stopy se doporučuje následující implementace:

1. Poslat každou jedinečnou zvukovou stopu v datovém proudu sám o sobě. Také odešlete redundantní datový proud pro každý z těchto datových proudů zvukové stopy, kde se druhý datový proud liší od prvního pouze identifikátoru v adrese URL příspěvku HTTP: {Protocol}://{Server Address} cesta/{Publishing bodu}/Streams ({identifikátor}).
1. Použijte samostatné datové proudy k odeslání dvou nejnižší přenosové rychlosti videa. Každý z těchto datových proudů by měl obsahovat také kopii každé jedinečné zvukové stopy. Například pokud je podporováno více jazyků, tyto datové proudy by měly obsahovat zvukové stopy pro jednotlivé jazyky.
1. Použijte samostatné instance serveru (kodér) ke kódování a odeslání redundantních datových proudů uvedených v (1) a (2). 

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