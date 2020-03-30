---
title: Živé streamování pomocí Služby Azure Media Services k vytváření datových proudů s více přenosovými rychlostmi | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak nastavit kanál, který přijímá jeden datový proud živého přenosu z místního kodéru a poté provádí živé kódování pro adaptivní datový proud datového toku pomocí služby Media Services.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: a32624c37cd8ca7fbef9e38ca61de9369791dd25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162527"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi

> [!NOTE]
> května 2018 již živé kanály nebudou podporovat protokol ingestestování datového proudu rtp/MPEG-2. Migrujte z protokolů s ingestováním RTP/MPEG-2 do RTMP nebo fragmentovaných protokolů s ingestováním MP4 (Smooth Streaming).

## <a name="overview"></a>Přehled
Ve službě Azure Media Services (AMS) **kanál** představuje kanál pro zpracování živého streamování obsahu. **Kanál** přijímá živé vstupní proudy jedním ze dvou způsobů:

* Místní živý kodér odešle datový proud s jedním datovým proudem do kanálu, který je povolen k provádění živého kódování se službou Media Services v jednom z následujících formátů: RTMP nebo Plynulé streamování (fragmentované MP4). Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.
* Místní živý kodér odešle vícebitový **rtmp** nebo **plynulý datový proud** (fragmentovaný MP4) do kanálu, který není povolen pro živé kódování pomocí AMS. Pozůstalé datové proudy procházejí **kanálem**s bez dalšího zpracování. Tato metoda se nazývá **předávací**. Můžete použít následující živé kodéry, které vypracovává plynulé streamování s více přenosovými rychlostmi: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco a Elemental. Následující živé kodéry výstup RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek a Tricaster kodéry.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.

  > [!NOTE]
  > Použití metody průchozí je nejekonomičtější způsob, jak provést živé vysílání.
  > 
  > 

Počínaje vydáním služby Media Services 2.10 můžete při vytváření kanálu určit, jakým způsobem má kanál přijímat vstupní datový proud a zda chcete, aby kanál prováděl živé kódování datového proudu. Máte dvě možnosti:

* **Žádné** – Zadejte tuto hodnotu, pokud máte v plánu použít místní živý kodér, který bude výstup vícedatový datový proud (předávací datový proud). V tomto případě příchozí datový proud prošel do výstupu bez kódování. Toto je chování kanálu před vydáním 2.10.  Podrobnější informace o práci s kanály tohoto typu naleznete v tématu [Živé streamování s místními kodéry, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
* **Standardní** – Tuto hodnotu zvolte, pokud chcete pomocí mediálních služeb zakódovat živý datový proud s přenosovým tokem do datového proudu s více přenosovou rychlostí. Uvědomte si, že existuje dopad na fakturaci pro živé kódování a měli byste si uvědomit, že opuštění kanálu živého kódování ve stavu "Spuštěno" bude účtovat fakturační poplatky.  Doporučujeme okamžitě zastavit spuštěné kanály po dokončení živého streamování, abyste se vyhnuli dalším hodinovým poplatkům.

> [!NOTE]
> Toto téma popisuje atributy kanálů, které jsou povoleny k provádění živého kódování **(Typ standardního** kódování). Informace o práci s kanály, které nejsou povoleny k provádění živého kódování, naleznete [v tématu Živé streamování s místními kodéry, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
> 
> Přečtěte si část [Důležité informace.](media-services-manage-live-encoder-enabled-channels.md#Considerations)
> 
> 

## <a name="billing-implications"></a>Důsledky fakturace
Kanál živého kódování začne fakturace, jakmile se přenese na "Spuštěno" prostřednictvím rozhraní API.   Stav můžete taky zobrazit na webu Azure Portal nebo vhttps://aka.ms/amse)nástroji Průzkumník Avismu Media Services ( .

Následující tabulka ukazuje, jak se stavy kanálu mapují na fakturační stavy v rozhraní API a na webu Azure Portal. Stavy se mírně liší mezi rozhraním API a portálu UX. Jakmile je kanál ve stavu "Spuštěno" prostřednictvím rozhraní API nebo ve stavu "Připraveno" nebo "Streamování" na webu Azure Portal, fakturace bude aktivní.
Chcete-li kanálu zabránit v dalším účtování, musíte kanál zastavit prostřednictvím rozhraní API nebo na webu Azure Portal.
Jste zodpovědní za zastavení kanálů po dokončení kanálu živého kódování.  Pokud kanál kódování nezastavíte, bude fakturace pokračovat.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Stavy kanálů a jejich mapování do fakturačního režimu
Aktuální stav kanálu. Možné hodnoty zahrnují:

* **Zastaveno**. Toto je počáteční stav kanálu po jeho vytvoření (pokud nebylo na portálu vybráno automatické spuštění.) V tomto stavu nedojde k žádné fakturaci. V tomto stavu lze aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spuštění**. Kanál se rozjíždí. V tomto stavu nedojde k žádné fakturaci. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do zastaveného stavu.
* **Spuštěno**. Kanál je schopen zpracovávat živé přenosy. Nyní se účtuje využití. Chcete-li zabránit dalšímu účtování, musíte kanál zastavit. 
* **Zastavení**. Kanál se zastavuje. V tomto přechodném stavu nedojde k žádné fakturaci. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování.
* **Odstranění**. Kanál se odstraňuje. V tomto přechodném stavu nedojde k žádné fakturaci. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování.

Následující tabulka ukazuje, jak se stavy kanálu mapují do fakturačního režimu. 

| Stav kanálu | Indikátory portálového uj., | Je to Fakturace? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (bez spuštěné programy)<br/>– nebo –<br/>Streamování (alespoň jeden spuštěný program) |ANO |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |Ne |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatické vypnutí nepoužívaných kanálů
ledna 2016 zavedla služba Media Services aktualizaci, která automaticky zastaví kanál (s povoleným aktivním kódováním) poté, co byl dlouhou dobu spuštěn v nepoužívaném stavu. To platí pro kanály, které nemají žádné aktivní programy a které delší dobu neobdržely vstupní příspěvek.

Prahová hodnota pro nevyužité období je nominálně 12 hodin, ale může se změnit.

## <a name="live-encoding-workflow"></a>Pracovní postup živého kódování
Následující diagram představuje pracovní postup živého streamování, kde kanál přijímá jeden datový proud datového toku v jednom z následujících protokolů: RTMP nebo Plynulé streamování; potom zakóduje datový proud do datového proudu s více datovými proudy. 

![Živý pracovní postup][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Běžný scénář živého streamování
Následující část představuje obecné kroky, které jsou součástí procesu vytváření běžných aplikací pro živé streamování.

> [!NOTE]
> V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin.
>
> Existuje dopad na fakturaci pro živé kódování a měli byste si uvědomit, že ponechání kanálu živého kódování ve stavu "Spuštěno" bude účtováno hodinové poplatky za fakturaci. Doporučujeme okamžitě zastavit spuštěné kanály po dokončení živého streamování, abyste se vyhnuli dalším hodinovým poplatkům. 

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte místní živý kodér, který může výstup **jednoho** datového toku v jednom z následujících protokolů: RTMP nebo Plynulé streamování. 

    Tento krok můžete provést i po vytvoření kanálu.
2. Vytvořte a spusťte kanál. 
3. Načtěte adresu URL ingestování kanálu. 

    Adresu URL ingestování používá kodér po kódování v reálném čase k odesílání datového proudu do kanálu.
4. Načtěte adresu URL náhledu kanálu. 

    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program. 

    Při použití portálu Azure vytvoření programu také vytvoří datový zdroj. 

    Při použití sady .NET SDK nebo REST je třeba vytvořit datový zdroj a určit použití tohoto datového zdroje při vytváření programu. 
6. Publikujte datový zdroj přidružený k programu.   

    >[!NOTE]
    >Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**. 

7. Jakmile budete připraveni začít streamovat a archivovat, spusťte program.
8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního proudu.
9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.
10. Odstraňte program (a volitelně můžete odstranit i asset).   

> [!NOTE]
> Je velmi důležité nezapomenout zastavit live kódování kanálu. Uvědomte si, že existuje hodinový dopad fakturace pro živé kódování a měli byste si uvědomit, že opuštění kanálu živého kódování ve stavu "Spuštěno" bude účtovat fakturační poplatky.  Doporučujeme okamžitě zastavit spuštěné kanály po dokončení živého streamování, abyste se vyhnuli dalším hodinovým poplatkům. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Konfigurace vstupu (ingestování) kanálu
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Ingestování protokolu streamování
Pokud je **typ kodéru** nastaven na **standardní**, jsou platné možnosti:

* RtMP **RTMP** s jednou přenosovou rychlostí
* Fragmentovaný **přenosový** datový tok (plynulé streamování)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>RTMP s jednou přenosovou rychlostí
Požadavky:

* Příchozí datový proud nemůže obsahovat video s více přenosovou rychlostí
* Datový proud videa by měl mít průměrný datový tok nižší než 15 Mb/s
* Zvukový proud by měl mít průměrný datový tok nižší než 1 Mb/s
* Podporováné kodeky jsou následující:
* MPEG-4 AVC / H.264 Video
* Základní, hlavní, vysoký profil (8bitový 4:2:0)
* Vysoký 10 profilů (10bitový 4:2:0)
* Vysoký 422 profil (10bitový 4:2:2)
* MPEG-2 AAC-LC Audio
* Mono, Stereo, Surround (5.1, 7.1)
* Vzorkovací frekvence 44,1 kHz
* MPEG-2 styl ADTS balení
* Doporučené kodéry zahrnují:
* [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md)
* Živý kodér Flash Media

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmentovaný soubor MP4 s jednou přenosovou rychlostí (technologie Smooth Streaming)
Typický případ použití:

Použijte místní živé kodéry od dodavatelů, jako jsou Elemental Technologies, Ericsson, Ateme, Envivio, a odesílejte vstupní datový proud přes otevřený internet do nedalekého datového centra Azure.

Požadavky:

Stejné jako u [rtmp s jedním přenosovým tokem](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Další aspekty
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Maximální rozlišení příchozího datového proudu videa je 1920x1080 a maximálně 60 polí za sekundu, pokud je prokládaný, nebo 30 snímků za sekundu, pokud je progresivní.

### <a name="ingest-urls-endpoints"></a>Ingestování adres URL (koncové body)
Kanál poskytuje vstupní koncový bod (ingestovat URL), který zadáte v živém kodéru, takže kodér může tlačit datové proudy do kanálů.

Po vytvoření kanálu můžete získat ingestující adresy URL. Chcete-li získat tyto adresy URL, kanál nemusí být ve **stavu Spuštěno.** Když jste připraveni začít odesílání dat do kanálu, musí být ve **stavu Spuštěno.** Jakmile kanál začne příjem dat, můžete zobrazit náhled datového proudu prostřednictvím náhledu URL.

Máte možnost ingestovat fragmentovaný přenos MP4 (Plynulé streamování) přes připojení SSL. Chcete-li ingestovat přes SSL, nezapomeňte aktualizovat ingestující adresu URL na HTTPS. V současné době AMS nepodporuje SSL s vlastními doménami.  

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které mohou publikovat video na tomto kanálu. Povolené ADRESY IP lze zadat buď jako jednu IP adresu (například "10.0.0.1"), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například "10.0.0.1/22"), nebo jako rozsah IP adres pomocí IP adresy a tečkované masky podsítě (například "10.0.0.1(255.255.252.0)').

Pokud žádné IP adresy nezadáte a nedefinujete žádné pravidlo, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

## <a name="channel-preview"></a>Náhled kanálu
### <a name="preview-urls"></a>Náhled adres URL
Kanály poskytují koncový bod náhledu (adresu URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL náhledu. Chcete-li získat adresu URL, kanál nemusí být ve **stavu Spuštěno.**

Jakmile kanál začne příjem dat, můžete zobrazit náhled datového proudu.

> [!NOTE]
> V současné době lze stream náhledu doručovat pouze ve formátu Fragmentované MP4 (Plynulé streamování) bez ohledu na zadaný typ vstupu.  K zobrazení streamu můžete použít hráče hostovaného na webu Azure Portal.
> 
> 

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které se mohou připojit ke koncovému bodu náhledu. Pokud nejsou zadány žádné adresy IP, bude povolena žádná adresa IP. Povolené ADRESY IP lze zadat buď jako jednu IP adresu (například "10.0.0.1"), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například "10.0.0.1/22"), nebo jako rozsah IP adres pomocí IP adresy a tečkované masky podsítě (například "10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Nastavení živého kódování
Tato část popisuje, jak lze upravit nastavení živého kodéru v kanálu, když je **typ kódování** kanálu nastaven na **standardní**.

> [!NOTE]
> Kanál příspěvku může obsahovat pouze jednu zvukovou stopu – požití více zvukových stop není v současné době podporováno. Při živém kódování pomocí [místních živých kódování](media-services-live-streaming-with-onprem-encoders.md)můžete odeslat informační kanál příspěvků v protokolu Hladký datový proud obsahující více zvukových stop.
> 
> 

### <a name="ad-marker-source"></a>Zdroj značky reklamy
Můžete určit zdroj signálů značek reklam. Výchozí hodnota je **Api**, což znamená, že živý kodér v kanálu by měl poslouchat asynchronní **rozhraní API pro značky reklam**.

### <a name="cea-708-closed-captions"></a>CEA 708 Skryté titulky
Volitelný příznak, který říká živému kodéru, aby ignoroval všechna data titulků CEA 708 vložená do příchozího videa. Pokud je příznak nastaven na false (výchozí), kodér detekuje a znovu vloží data CEA 708 do výstupních datových proudů videa.

#### <a name="index"></a>Index
Doporučuje se odeslat v jednom programu přenosu datového proudu (SPTS). Pokud vstupní datový proud obsahuje více programů, živý kodér v kanálu analyzuje tabulku map programů (PMT) ve vstupu, identifikuje vstupy, které mají název typu datového proudu MPEG-2 AAC ADTS nebo AC-3 System-A nebo AC-3 System-B nebo MPEG-2 Private PES nebo MPEG-1 Audio nebo MPEG-2 Audio a uspořádá je v pořadí uvedeném v PMT. Index založený na nule se pak používá k vyzvednutí n-té položky v tomto uspořádání.

#### <a name="language"></a>Jazyk
Identifikátor jazyka audio stream, v souladu s ISO 639-2, například ENG. Pokud není k dispozici, výchozí hodnota je UND (undefined).

### <a name="system-preset"></a><a id="preset"></a>Přednastavení systému
Určuje přednastavení, které má použít živý kodér v rámci tohoto kanálu. V současné době je jedinou povolenou hodnotou **Default720p** (výchozí).

**Default720p** zakóduje video do následujících 6 vrstev.

#### <a name="output-video-stream"></a>Výstupní video stream

| Datový tok | impulzu | Vlastnost Height | MaxFPS | Profil | Název výstupního datového proudu |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoká |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Vysoká |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Vysoká |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Vysoká |Video_512x288_850kbps |
| 550 |384 |216 |30 |Vysoká |Video_384x216_550kbps |
| 200 |340 |192 |30 |Vysoká |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Výstupní zvukový proud

Zvuk je kódován do stereo AAC-LC při 128 kbps, vzorkovací frekvence 48 kHz.

## <a name="signaling-advertisements"></a>Signalizační reklamy
Pokud je ve vašem kanálu povoleno živé kódování, máte v kanálu součást, která zpracovává video a může s ním manipulovat. Můžete signalizovat kanálu vložit břidlice a / nebo reklamy do odchozího adaptivního datového toku. Břidlice jsou stále obrázky, které můžete použít k zakrytí vstupního živého přenosu v určitých případech (například během komerční přestávky). Reklamní signály jsou časově synchronizované signály, které vložíte do odchozího streamu, abyste přehrávači videa řekli, aby přijal zvláštní opatření - například přepnete na reklamu ve vhodnou dobu. Podívejte se na tento [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) pro přehled signalizačního mechanismu SCTE-35 používaného pro tento účel. Níže je typický scénář, který můžete implementovat ve vaší živé události.

1. Nechte diváky, aby před začátkem akce získali obrázek PRE-EVENT.
2. Nechte své diváky získat po skončení události obrázek POST-EVENT.
3. Nechte diváky získat obrázek ERROR-EVENT, pokud se během události zobrazí problém (například výpadku napájení na stadionu).
4. Odesláním obrázku AD-BREAK skryjte živý přenos událostí během komerční přestávky.

Níže jsou uvedeny vlastnosti, které můžete nastavit při signalizaci reklamy. 

### <a name="duration"></a>Doba trvání
Doba trvání komerční přestávky v sekundách. To musí být nenulová kladná hodnota, aby bylo možné zahájit komerční přestávku. Když probíhá komerční přestávka a doba trvání je nastavena na nulu s CueId odpovídající probíhající komerční přestávce, pak je tato přestávka zrušena.

### <a name="cueid"></a>CueId
Jedinečné ID pro komerční přestávku, které má být použito navazující aplikací k přijetí vhodných opatření. Musí být celé celé číslo. Tuto hodnotu můžete nastavit na libovolné náhodné kladné celé číslo nebo použít protiproudý systém ke sledování ID Cue. Ujistěte se, že normalizovat všechna ID na kladná celá čísla před odesláním prostřednictvím rozhraní API.

### <a name="show-slate"></a>Zobrazit břidlici
Nepovinný parametr. Signalizuje živému kodéru, aby během komerční přestávky přepnul na [výchozí břidlicový](media-services-manage-live-encoder-enabled-channels.md#default_slate) obraz a skryl příchozí video kanál. Zvuk je také ztlumen během břidlice. Výchozí hodnota je **false**. 

Použitý obrázek bude ten, který je zadán prostřednictvím výchozí vlastnosti Id datového zdroje břidlice v době vytvoření kanálu. Břidlice bude roztažena tak, aby odpovídala velikosti obrázku zobrazení. 

## <a name="insert-slate--images"></a>Vložení obrázků břidlice
Živý kodér v kanálu může být signalizován pro přepnutí na břidlicový obraz. To může být také signalizováno k ukončení on-děje břidlice. 

Živý kodér lze nakonfigurovat tak, aby přepínal na břidlicový obraz a v určitých situacích skryl příchozí video signál – například během přestávky na reklamu. Pokud takový břidlice není nakonfigurován, vstupní video není maskován během této záloze reklamy.

### <a name="duration"></a>Doba trvání
Doba trvání břidlice v sekundách. To musí být nenulová kladná hodnota, aby bylo možné spustit břidlici. Pokud je on-děje břidlice, a doba trvání nula je zadán, pak, že on-děje břidlice bude ukončena.

### <a name="insert-slate-on-ad-marker"></a>Vložení břidlice na značku reklamy
Pokud je nastavena hodnota true, toto nastavení nakonfiguruje živý kodér tak, aby během přestávky reklamy vkládal břidlicový obraz. Výchozí hodnotou je hodnota true. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Výchozí Id břidlicového datového zdroje

Nepovinný parametr. Určuje Id datového zdroje datového zdroje, který obsahuje bitovou kopii břidlice. Výchozí hodnota je null. 


> [!NOTE] 
> Před vytvořením kanálu by měl být obrázek břidlice s následujícími omezeními odeslán jako vyhrazený datový zdroj (žádné jiné soubory by neměly být v tomto datovém zdroji). Tento obrázek se používá pouze v případě, že živý kodér vkládá břidlici kvůli přerušení reklamy nebo byl explicitně signalizován k vložení břidlice. V současné době neexistuje možnost použít vlastní obrázek, když živý kodér vstoupí do stavu "vstupnísignál ztracen". Můžete hlasovat pro tuto funkci [zde](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* Maximálně 1920x1080 v rozlišení.
* Maximálně 3 Mbytes ve velikosti.
* Název souboru musí mít příponu *.jpg.
* Obrázek musí být odeslán do asset jako jediný AssetFile v tomto asset a tento AssetFile by měl být označen jako primární soubor. Prostředek nelze zašifrovat úložištěm.

Pokud není **zadáno výchozí Id datového zdroje břidlice** a **vložení břidlice na značku reklamy** je nastaveno na **hodnotu true**, bude k skrytí vstupního datového proudu videa použita výchozí bitová kopie Mediální služby Azure. Zvuk je také ztlumen během břidlice. 

## <a name="channels-programs"></a>Programy kanálu
Kanál je přidružený k programům, které vám umožňují řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah Kanálu a programu je velmi podobný tradičním médiím, kde kanál má konstantní proud obsahu a program je vymezen na nějakou časovou událost v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka archivačního okna také určuje maximální počet času, po který mohou klienti hledat zpět v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružen k datovému zdroji, který ukládá streamovaný obsah. Prostředek je mapován na kontejner objektů blob bloku v účtu Azure Storage a soubory v datovém zdroji jsou uloženy jako objekty BLOB v tomto kontejneru. Chcete-li publikovat program tak, aby zákazníci mohli zobrazit datový proud, musíte vytvořit lokátor OnDemand pro přidružený datový zdroj. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři souběžně spuštěné programy, takže můžete vytvořit více archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte a spusťte nový program pro každou událost, jak je popsáno v části Programování aplikací živého streamování.

Jakmile budete připraveni začít streamovat a archivovat, spusťte program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program. 

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte program a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud ho program používá. Nejdřív odstraňte program. 

I po zastavení a odstranění programu můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud neodstraníte asset.

Pokud chcete archivovaný obsah zachovat, ale nechcete ho zpřístupňovat pro streamování, odstraňte lokátor streamování.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Získání náhledu náhledu živého kanálu
Pokud je povoleno živé kódování, můžete nyní získat náhled živého přenosu, jakmile se dostane do kanálu. To může být cenným nástrojem pro kontrolu, zda váš živý přenos skutečně dosahuje kanálu. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Stavy kanálů a způsob, jakým se stavy mapují do fakturačního režimu
Aktuální stav kanálu. Možné hodnoty zahrnují:

* **Zastaveno**. Toto je počáteční stav kanálu po jeho vytvoření. V tomto stavu lze aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spuštění**. Kanál se rozjíždí. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do zastaveného stavu.
* **Spuštěno**. Kanál je schopen zpracovávat živé přenosy.
* **Zastavení**. Kanál se zastavuje. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování.
* **Odstranění**. Kanál se odstraňuje. Během tohoto stavu nejsou povoleny žádné aktualizace ani streamování.

Následující tabulka ukazuje, jak se stavy kanálu mapují do fakturačního režimu. 

| Stav kanálu | Indikátory portálového uj., | Účtovány? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (bez spuštěné programy)<br/>– nebo –<br/>Streamování (alespoň jeden spuštěný program) |Ano |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |Ne |

> [!NOTE]
> V současné době je průměr startu kanálu asi 2 minuty, ale někdy může trvat až 20 + minut. Obnovení kanálu může trvat až 5 minut.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Požadavky
* Když typ kódování Kanálu **standardu** dojde ke ztrátě vstupního zdroje/příspěvku, kompenzuje jej nahrazením zdrojového videa/zvuku chybovým štítem a tichem. Kanál bude i nadále vydávat břidlice, dokud vstup / příspěvek krmiva pokračuje. Doporučujeme, aby živý kanál nebyl ponechán v takovém stavu déle než 2 hodiny. Za tímto bodem není zaručeno chování kanálu při opětovném připojení vstupu, ani jeho chování v reakci na příkaz Reset. Budete muset kanál zastavit, odstranit a vytvořit nový.
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Pokaždé, když překonfigurujete živý kodér, zavolejte metodu **Reset** v kanálu. Před resetováním kanálu je třeba program zastavit. Po obnovení kanálu restartujte program.
* Kanál lze zastavit pouze v případě, že je ve stavu Spuštěno a všechny programy v kanálu byly zastaveny.
* Ve výchozím nastavení můžete k účtu Mediálních služeb přidat pouze 5 kanálů. Jedná se o měkkou kvótu pro všechny nové účty. Další informace naleznete v [tématu Kvóty a omezení](media-services-quotas-and-limitations.md).
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Fakturuje se vám pouze v případě, že je kanál ve **stavu Spuštěno.** Další informace naleznete v [této](media-services-manage-live-encoder-enabled-channels.md#states) části.
* V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. 
* Ujistěte se, že máte koncový bod streamování, ze kterého chcete streamovat obsah ve stavu **Spuštěno.**
* Přednastavení kódování používá pojem "maximální snímková frekvence" 30 fps. Takže pokud je vstup 60fps/59.94i, vstupní snímky jsou vynechány / de-prokládané na 30/29.97 fps. Pokud je vstup 50fps/50i, vstupní snímky jsou vynechány /de-prokládané na 25 fps. Pokud je vstup 25 fps, výstup zůstane na 25 fps.
* Nezapomeňte zastavit své kanály, když udělal. Pokud tak neučiníte, fakturace bude pokračovat.

## <a name="known-issues"></a>Známé problémy
* Doba spuštění kanálu byla vylepšena v průměru na 2 minuty, ale v době zvýšené poptávky může ještě trvat až 20 + minut.
* Břidlicové obrázky by měly odpovídat [omezením popsaným zde](media-services-manage-live-encoder-enabled-channels.md#default_slate). Pokud se pokusíte vytvořit kanál s výchozí břidlice, která je větší než 1920x1080, požadavek nakonec dojde k chybě.
* Znovu.... nezapomeňte zastavit své kanály, když jste hotovi streaming. Pokud tak neučiníte, fakturace bude pokračovat.

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [Novou žádost o podporu.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Další krok

Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doručování živě streamovaných událostí pomocí služby Azure Media Services](media-services-overview.md)

[Vytvořte kanály, které provádějí živé kódování z datového toku singe na adaptivní datový tok s portálem](media-services-portal-creating-live-encoder-enabled-channel.md)

[Vytvoření kanálů, které provádějí živé kódování z datového toku singe na adaptivní datový proud datového toku pomocí sady .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Správa kanálů pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Koncepty mediálních služeb](media-services-concepts.md)

[Specifikace fragmentace živého ingestu služby Azure Media Services](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

