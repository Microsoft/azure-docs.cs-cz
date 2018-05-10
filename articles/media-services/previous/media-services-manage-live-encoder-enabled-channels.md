---
title: Živé streamování využívající Azure Media Services k vytvářejí proudy s více přenosovými rychlostmi | Microsoft Docs
description: 'Toto téma popisuje, jak nastavit kanál, který přijímá živý datový proud s jednou přenosovou rychlostí z místní kodér a potom provede kódování v reálném čase do datového proudu s adaptivní přenosovou rychlostí pomocí služby Media Services. Datový proud můžete pak bude doručen do klientské aplikace přehrávání přes jeden nebo více koncové body streamování, pomocí jedné z následujících protokolů adaptivní streamování: HLS, Smooth Stream MPEG DASH.'
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: juliako;anilmur
ms.openlocfilehash: c4d5533c443d27afa56471ce048efc5a375f6780
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi

> [!NOTE]
> Od 12 může 2018 za provozu kanály se už podporu datový proud RTP/MPEG-2 transport ingestování. Proveďte prosím migraci z RTP/MPEG-2 RTMP nebo fragmentovaných MP4 (technologie Smooth Streaming) ingestování protokoly.

## <a name="overview"></a>Přehled
V Azure Media Services (AMS) **kanál** představuje cestu pro zpracování obsahu živého streamování. A **kanál** přijímat živé vstupní datové proudy v jednom ze dvou způsobů:

* Místní kodér za provozu odešle datový proud s jednou přenosovou rychlostí do kanálu, který je povoleno provádět kódování v reálném čase pomocí služby Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný soubor MP4). Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.
* Místní kodér za provozu odešle více přenosovými rychlostmi **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) do kanálu, který není povoleno provádět kódování v reálném čase pomocí služby AMS. Ingestované datové proudy prochází **kanál**s bez dalšího zpracování. Tato metoda je volána **průchozí**. Můžete použít následující kodéry, které výstupu technologie Smooth Streaming více přenosovými rychlostmi: MediaExcel, Ateme, představte si komunikace, Envivio, Cisco a Elemental. Následující kodéry výstupu RTMP: Adobe Flash média Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek a čase kodérů.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.
  
  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě Streamovat.
  > 
  > 

Od verze Media Services 2.10, když vytvoříte kanál, můžete zadat způsobem, který chcete použít pro kanál pro příjem vstupního datového proudu a zda chcete pro kanál provést kódování v reálném čase z datového proudu. Máte dvě možnosti:

* **Žádný** – zadejte tuto hodnotu, pokud budete chtít použít kodér místní za provozu, který bude výstupního datového proudu více přenosovými rychlostmi (průchozí datový proud). V takovém případě příchozího datového proudu předána do výstupu bez jakékoli kódování. Toto je chování kanál před vydáním verze 2.10.  Podrobné informace o práci s kanály tohoto typu, najdete v části [živé streamování s místními kodéry, které vytvářejí proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
* **Standardní** – vyberte tuto hodnotu, pokud máte v úmyslu použít Media Services ke kódování živého datového proudu jednou přenosovou rychlostí do datového proudu více přenosovými rychlostmi. Mějte na paměti, že je fakturace dopad pro kódování v reálném čase a by měl pamatovat, že ponechat živého kanálu kódování ve stavu "Spuštění" bude platit poplatky fakturace.  Doporučuje se po dokončení náklady na velmi hodinové živé streamování události okamžitě zastavit spuštěný kanálů.

> [!NOTE]
> Toto téma popisuje atributy kanály, které mají povolené kódování v reálném (**standardní** kódování typu). Informace o práci s kanály, které nejsou povolené kódování v reálném najdete v tématu [živé streamování s místními kodéry, které vytvářejí proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
> 
> Projděte si [aspekty](media-services-manage-live-encoder-enabled-channels.md#Considerations) části.
> 
> 

## <a name="billing-implications"></a>Dopady na fakturaci
Živého kanálu kódování začne fakturace, jakmile je přechodů mezi stavy "Spuštění" prostřednictvím rozhraní API.   Můžete také zobrazit stav na portálu Azure nebo v nástroji Azure Media Services Explorer (http://aka.ms/amse).

Následující tabulka ukazuje, jak kanál stavy mapovány fakturace stavů v portálu rozhraní API a Azure. Upozorňujeme, že jsou mírně liší rozhraní API a portál UX stavy Jakmile kanál, který je ve stavu "Spuštěná" prostřednictvím rozhraní API nebo ve stavu "Připraveno" nebo "Streaming" na webu Azure portal fakturace bude aktivní.
Chcete-li ukončit kanál z jste další fakturace, budete muset zastavte kanál prostřednictvím rozhraní API nebo na portálu Azure.
Jste zodpovědní za zastavení kanálů, když jste hotovi s živého kanálu kódování.  Nepodařilo se zastavit kódování kanál způsobí trvalá fakturace.

### <a id="states"></a>Stavy kanál a jak jsou mapovány na fakturační režim
Aktuální stav kanálu. Možné hodnoty:

* **Zastavit**. Toto je počáteční stav kanálu po jeho vytvoření (Pokud je automatické spuštění byla vybrána v portálu.) V tomto stavu dojde k žádné fakturace. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spouštění**. Kanál se spouští. V tomto stavu dojde k žádné fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu Zastaveno.
* **Spuštění**. Kanál dokáže zpracovávat živé streamy. Nyní ji je fakturace využití. Je třeba ukončit kanál, aby se zabránilo další fakturace. 
* **Zastavení**. Kanál se zastavuje. V tomto dočasném stavu dojde k žádné fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Odstranění**. Kanál se odstraňuje. V tomto dočasném stavu dojde k žádné fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka uvádí přiřazení stavů kanálu k režimu fakturace. 

| Stav kanálu | Indikátory v uživatelském rozhraní portálu | Je fakturace? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (žádný běžící program)<br/>nebo<br/>Streamování (nejméně jeden běžící program) |ANO |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |Ne |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatické vypnutí nepoužívané kanálů
Počínaje 25 leden 2016 Media Services nasazuje aktualizace, která automaticky zastaví kanál (s kódováním v reálném čase povolit) po po dlouhou dobu spuštění v nepoužívané stavu. To platí pro kanály, které mají žádné aktivní programy a které neobdrželi vstupní příspěvku kanálu pro delší dobu.

Prahová hodnota pro nepoužívané období je jmenovitě 12 hodin, ale mohou podléhat změnám.

## <a name="live-encoding-workflow"></a>Živý pracovní postup kódování
Následující diagram představuje živé streamování pracovního postupu kde kanál, který přijímá datový proud s jednou přenosovou rychlostí v jednom z těchto protokolů: RTMP nebo technologie Smooth Streaming; kóduje pak datový proud na datový proud s více přenosovými rychlostmi. 

![Živý pracovní postup][live-overview]

## <a id="scenario"></a>Běžný scénář živého streamování
Následující část představuje obecné kroky, které jsou součástí procesu vytváření běžných aplikací pro živé streamování.

> [!NOTE]
> V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. Obraťte se na amslived@microsoft.com Pokud potřebujete kanál běžel delší. Mějte na paměti, že je fakturace dopad pro kódování v reálném čase a měli pamatovat ponechat živého kanálu kódování ve stavu "Spuštění" je zpoplatněná hodinové poplatky.  Doporučuje se po dokončení náklady na velmi hodinové živé streamování události okamžitě zastavit spuštěný kanálů. 
> 
> 

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte místní kodér za provozu, který můžete výstup **jeden** datový proud přenosovou rychlostí v jednom z těchto protokolů: RTMP nebo technologie Smooth Streaming. 
   
    Tento krok můžete provést i po vytvoření kanálu.
2. Vytvořte a spusťte kanál. 
3. Načtěte adresu URL ingestování kanálu. 
   
    Adresu URL ingestování používá kodér po kódování v reálném čase k odesílání datového proudu do kanálu.
4. Načtěte adresu URL náhledu kanálu. 
   
    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program. 
   
    Při použití portálu Azure, vytváření program vytvoří také asset. 
   
    Při použití sady .NET SDK nebo REST je potřeba vytvořit prostředek služby a nastavení, aby používal tento prostředek, při vytváření Program. 
6. Publikujte asset přidružený k programu.   
   
    >[!NOTE]
    >Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**. 
    
7. Jakmile budete připraveni začít streamovat a archivovat, spusťte program.
8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního datového proudu.
9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.
10. Odstraňte program (a volitelně můžete odstranit i asset).   

> [!NOTE]
> Je velmi důležité, abyste nezapomněli ukončit kanál živé kódování. Mějte na paměti, že je každou hodinu fakturace vlivu pro kódování v reálném čase a by měl pamatovat, že ponechat živého kanálu kódování ve stavu "Spuštění" bude platit poplatky fakturace.  Doporučuje se po dokončení náklady na velmi hodinové živé streamování události okamžitě zastavit spuštěný kanálů. 
> 
> 

## <a id="channel"></a>Vstup kanál (ingestování) konfigurace
### <a id="Ingest_Protocols"></a>Ingestování datových proudů
Pokud **kodér typ** je nastaven na **standardní**, platné možnosti jsou:

* Jednou přenosovou rychlostí **RTMP**
* Jednou přenosovou rychlostí **fragmentovaný soubor MP4** (technologie Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>S jednou přenosovou rychlostí RTMP
Aspekty:

* Příchozí datový proud nesmí obsahovat více přenosovými rychlostmi video
* Datový proud videa by měl mít průměrná přenosovou rychlostí nižší než 15 MB/s
* Zvukový datový proud musí mít průměrná přenosovou rychlostí nižší než 1 MB/s
* Podporované kodeky jsou následující:
* MPEG-4 AVC / H.264 Video
* Směrný plán, hlavní, vysoce profilu (8bitové 4:2:0)
* Vysoká 10 profilu (10 bitů 4:2:0)
* Vysoká 422 profilu (10 bitů 4:2:2)
* MPEG-2 AAC-LC zvuk
* Mono, stereofonním systémem, příkazu obklopit (5.1, 7.1)
* 44,1 kHz vzorkovací frekvenci
* Balení ADTS styl MPEG-2
* Doporučené kodéry patří:
* Kodér Telestream Wirecast
* Flash Media Encoder za provozu

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmentovaný soubor MP4 s jednou přenosovou rychlostí (technologie Smooth Streaming)
Typický případ použití:

Použít místní kodéry od dodavatelů elementární technologie, Ericsson, Ateme, Envivio odesílat vstupního datového proudu prostřednictvím Internetu otevřete k blízkým Azure datového centra.

Aspekty:

Stejné jako u [s jednou přenosovou rychlostí RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Další důležité informace
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Maximální rozlišení pro příchozí datový proud videa je 1920 × 1080 a maximálně 60 pole za sekundu Pokud prokládaných nebo 30 snímků za sekundu, pokud progresivní.

### <a name="ingest-urls-endpoints"></a>Ingestovaných adres URL (koncových bodů)
Kanál, který obsahuje vstupní koncový bod (ingestovanou adresu URL) určit v za provozu kodér tak můžete posouvat kodéru datových proudů do vašeho kanálů.

Po vytvoření kanálu můžete získat ingestování adresy URL. K získání těchto adres URL, nemá kanál v **systémem** stavu. Pokud jste připravení začít s předání dat do kanálu, musí být **systémem** stavu. Jakmile se kanál spustí příjem dat, můžete zobrazit náhled datového proudu prostřednictvím URL náhledu.

Máte možnost z příjem fragmentovaný soubor MP4 (technologie Smooth Streaming) živý datový proud připojení přes protokol SSL. K ingestování přes protokol SSL, nezapomeňte aktualizovat adresu URL ingestování protokolem HTTPS. Všimněte si, že v současné době AMS nepodporuje SSL s vlastní domény.  

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které mají povoleno publikování videa tohoto kanálu. Povolené IP adresy můžete zadat buď jako jednu IP adresu (např. '10.0.0.1'), rozsah IP adres pomocí IP adresy a masky podsítě s technologií CIDR (např. 10.0.0.1/22), nebo jako rozsah IP adres pomocí IP adresy a masky podsítě s tečkou (např. '10.0.0.1(255.255.252.0)').

Pokud žádné IP adresy nezadáte a nedefinujete žádné pravidlo, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

## <a name="channel-preview"></a>Náhled kanálu
### <a name="preview-urls"></a>Adresy URL Preview
Kanály zadejte koncový bod preview (URL náhledu), který používáte pro zobrazení náhledu a ověřit před další zpracování a doručení datového proudu.

Když vytvoříte kanál, můžete získat adresu URL náhledu. Získat adresu URL, nemá kanál v **systémem** stavu.

Jakmile se kanál spustí příjem dat, můžete zobrazit náhled datového proudu.

> [!NOTE]
> Aktuálně datový proud preview mohou být dodány pouze v fragmentovaný soubor MP4 formátu (technologie Smooth Streaming) bez ohledu na zadaný typ vstupu. Můžete použít [ http://smf.cloudapp.net/healthmonitor ](http://smf.cloudapp.net/healthmonitor) player k testování datový proud Smooth. Přehrávač hostované na portálu Azure můžete použít také k zobrazení datového proudu.
> 
> 

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které se mohou připojit ke koncovému bodu náhledu. Pokud nejsou zadány žádné IP adresy, bude možné jakékoli IP adresy. Povolené IP adresy můžete zadat buď jako jednu IP adresu (např. 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (např. 10.0.0.1/22), nebo jako rozsah IP adres pomocí IP adresy a masky podsítě s tečkou (např. 10.0.0.1(255.255.252.0)).

## <a name="live-encoding-settings"></a>Nastavení kódování za provozu
Tato část popisuje, jak pro kodér za provozu v rámci kanálu lze změnit nastavení, když **kódování typu** kanálu je nastaven na **standardní**.

> [!NOTE]
> Váš informační kanál příspěvku může obsahovat pouze jediné zvukové stopy – příjem z více zvukových stop není aktuálně podporována. Při provádění kódování v reálném čase s [místní live kóduje](media-services-live-streaming-with-onprem-encoders.md), můžete odeslat příspěvek informačního kanálu v protokol Smooth Streaming obsahující více zvukových stop.
> 
> 

### <a name="ad-marker-source"></a>Zdroj značky služby AD
Můžete určit zdroj signálů reklamních značek. Výchozí hodnota je **rozhraní Api**, což naznačuje, že kodér za provozu v rámci kanálu naslouchat požadavkům k asynchronní **rozhraní API reklamní značky**.

### <a name="cea-708-closed-captions"></a>CEA 708 uzavřený titulky
Volitelné příznak, který informuje za provozu kodér ignorovat všechna data titulky CEA 708 vložený příchozí video. Když příznak nastaven na hodnotu false (výchozí), kodér rozpozná a CEA 708 data znovu vložit do výstupní datové proudy videa.

#### <a name="index"></a>Index
Doporučujeme odeslat v jednom programu přenosový stream (SPTS). Pokud vstupní datový proud obsahuje více programy, kodér za provozu v rámci kanálu analyzuje Program Mapa tabulky (platba) ve vstupu, identifikuje vstupních hodnot, které má název typu stream MPEG-2 AAC ADTS nebo AC-3 A systému nebo AC 3 systému-B nebo privátní PES MPEG-2 nebo zvuk MPEG-1 nebo ve zvukovém souboru MPEG-2 a uspořádá je v pořadí zadaném v platbě Index založený na nule pak umožňuje vyzvedne, až n tou položku tohoto uspořádání.

#### <a name="language"></a>Jazyk
Identifikátor jazyka zvuk datového proudu, který odpovídá s bitovou kopií ISO 639-2, jako je například ENG. Pokud není přítomný, výchozí hodnota je a (není definovaná).

### <a id="preset"></a>Přednastavení systému
Určuje přednastavení, které chcete použít kodérem za provozu v rámci tohoto kanálu. V současné době pouze povolená hodnota je **Default720p** (výchozí).

Všimněte si, že pokud budete potřebovat vlastní přednastavení, měli byste požádat amslived@microsoft.com.

**Default720p** bude zakódovat video do následující vrstvy 7.

#### <a name="output-video-stream"></a>Výstupní datový proud videa
| Přenosovou rychlostí | Šířka | Výška | MaxFPS | Profil | Název výstupního datového proudu |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoký |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hlavní |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hlavní |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hlavní |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hlavní |Video_384x216_550kbps |
| 350 |340 |192 |30 |Směrný plán |Video_340x192_350kbps |
| 200 |340 |192 |30 |Směrný plán |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Výstupní datový proud zvuk
Zvuk je zakódován do stereofonním systémem AAC-LC na 64 kb/s, vzorkovací frekvenci 44,1 kHz.

## <a name="signaling-advertisements"></a>Signalizace oznámení o inzerovaném programu
Pokud kanál Live Encoding povolené, máte součást v vašeho kanálu, který je video zpracování a s ním pracovat. Můžete signál pro kanál vložit slaty nebo oznámení o inzerovaném programu odchozí datového proudu s adaptivní přenosovou rychlostí. Slaty jsou stále bitové kopie, které můžete použít tak, aby pokrývalo až vstupní za provozu informačního kanálu v některých případech (například při zalomení komerční). Inzerování signály, jsou čas synchronizován signály, které můžete vložit do datového proudu odchozí říct přehrávání videa pro zvláštní akce – například přepnout na oznámení o inzerovaném programu v příslušnou dobu. Toto [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) přehled SCTE 35 signalizační používáno pro tento účel. Níže je typický scénář, který může implementovat v živé události.

1. Máte vašeho prohlížeče získat bitovou kopii před událostí před zahájením události.
2. Máte vašeho prohlížeče získat bitovou kopii po události po skončení události.
3. Máte vašeho prohlížeče získat bitovou kopii událost chyby, pokud došlo k potížím při událostí (například power Chyba v stadium).
4. Odešlete bitovou kopii AD-BREAK skrýt živé události kanálu během zalomení komerční.

Níže jsou vlastnosti, které můžete nastavit při signalizace oznámení o inzerovaném programu. 

### <a name="duration"></a>Doba trvání
Doba v sekundách pro komerční rozdělení. Toto musí být kladná hodnota nulová zahájíte komerční rozdělení. Pokud probíhá zalomení komerční a doba trvání je nastaven na hodnotu nula s CueId odpovídající komerční rozdělení průběžné, pak tohoto rozdělení je zrušená.

### <a name="cueid"></a>CueId
Jedinečné ID pro komerční rozdělení, který bude používat aplikace pro příjem dat provést příslušné akce. Musí být kladné celé číslo. Můžete nastavit tuto hodnotu libovolné náhodné kladné celé číslo nebo použít ke sledování Cue ID nadřazeného systému. Zkontrolujte, zda jste k normalizaci všechna ID na kladná celá čísla před odesláním prostřednictvím rozhraní API.

### <a name="show-slate"></a>Zobrazit projektem
Volitelné. Signály za provozu kodér přepnout do [výchozí projektem](media-services-manage-live-encoder-enabled-channels.md#default_slate) bitovou kopii při zalomení komerční a skrýt příchozí video informačního kanálu. Zvuk je také ztlumen během projektem. Výchozí hodnota je **false**. 

Obrázek použitý bude zadanému prostřednictvím asset výchozí projektem Vlastnost Id v době vytvoření kanálu. Přizpůsobit velikost obrázku zobrazení bude dojít k roztažení projektem. 

## <a name="insert-slate--images"></a>Vložení obrázků projektem
Kodér za provozu v rámci kanálu můžete signál přepnout do bitové kopie projektem. Můžete ho také signál k ukončení průběžné projektem. 

Kodér za provozu lze konfigurovat přepnout do bitové kopie projektem a skrýt příchozí video signál v určitých situacích – například během pozastavení služby ad. Pokud takový projektem není nakonfigurováno, není během tohoto rozdělení ad maskovat vstupní video.

### <a name="duration"></a>Doba trvání
Doba projektem v sekundách. Toto musí být kladná hodnota nulová ke spuštění projektem. Pokud dojde průběžné projektem a je zadaná doba trvání nula, bude ukončen této průběžné projektem.

### <a name="insert-slate-on-ad-marker"></a>Vložit slate nebo reklamní značku
Při konfiguraci nastaven na hodnotu true, toto nastavení za provozu kodér vložení bitovou kopii projektem během pozastavení služby ad. Výchozí hodnota je true. 

### <a id="default_slate"></a>Id prostředku výchozí projektem

Volitelné. Určuje Id prostředku služby Asset média, který obsahuje bitovou kopii projektem. Výchozí hodnota je null. 


>[!NOTE] 
>Před vytvořením kanálu, musí být nahrán projektem obrázku s těmito omezeními jako vyhrazené asset (žádné další soubory musí být v této asset). Tuto bitovou kopii se používá jenom v případě, že kodér za provozu je vkládání projektem z důvodu přerušení služby ad nebo byla explicitně signál k vložení projektem. Za provozu encoder můžete také přejít do režimu projektem během určité chybové stavy – například pokud dojde ke ztrátě vstupní signál. Momentálně není žádná možnost používat vlastní image, když za provozu kodér přejde takové ke ztrátě vstupní signál stavu. Pro tuto funkci můžete hlasovat [zde](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Maximálně 1920 × 1080 v řešení.
* Maximálně 3 MB velikost.
* Název souboru musí mít příponu *.jpg.
* Obrázek musí být odeslán do prostředku jako pouze AssetFile v tom, že prostředek a tento AssetFile by měl být označen jako primární soubor. Asset nemůže být šifrování úložiště.

Pokud **výchozí z břidlice Asset Id** není zadán, a **vložit projektem na ad značky** je nastaven na **true**, výchozí Azure Media Services image se používají ke skrytí vstupní datový proud videa. Zvuk je také ztlumen během projektem. 

## <a name="channels-programs"></a>Kanál pro programy
Kanál je přidružený k programům, které vám umožňují řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálů a programů se velmi podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka archivačního okna také určuje maximální časový úsek, který můžou klienti prohledávat od aktuální živé pozice směrem zpět v čase. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružena k Assetu, který ukládá obsah datového proudu. Prostředek je namapovaná na kontejner objektů blob bloku v účtu úložiště Azure a soubory v prostředku jsou uloženy jako objekty BLOB v tomto kontejneru. Chcete program publikovat, takže vaši zákazníci mohou zobrazit datový proud je nutné vytvořit lokátor OnDemand pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte a spusťte nový program pro každou jednotlivou událost, jak je popsáno v části programování aplikací živé vysílání datového proudu.

Jakmile budete připraveni začít streamovat a archivovat, spusťte program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program. 

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte program a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud ho program používá. Nejdřív odstraňte program. 

I po zastavení a odstranění programu můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud neodstraníte asset.

Pokud chcete archivovaný obsah zachovat, ale nechcete ho zpřístupňovat pro streamování, odstraňte lokátor streamování.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Získávání miniatur náhled živého kanálu
Pokud je povolena funkce Live Encoding, teď se můžete náhled živého kanálu jako nedosáhne kanál. To může být cenným nástrojem ke kontrole, jestli váš informační kanál za provozu ve skutečnosti dosahuje kanál. 

## <a id="states"></a>Stavy kanál a jak se stavy mapovány do fakturace režimu
Aktuální stav kanálu. Možné hodnoty:

* **Zastavit**. Toto je počáteční stav kanálu po jeho vytvoření. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spouštění**. Kanál se spouští. V tomto stavu nejsou povoleny žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu Zastaveno.
* **Spuštění**. Kanál dokáže zpracovávat živé streamy.
* **Zastavení**. Kanál se zastavuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Odstranění**. Kanál se odstraňuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka uvádí přiřazení stavů kanálu k režimu fakturace. 

| Stav kanálu | Indikátory v uživatelském rozhraní portálu | Fakturováno? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (žádný běžící program)<br/>nebo<br/>Streamování (nejméně jeden běžící program) |Ano |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |Ne |

> [!NOTE]
> V současné době průměr spuštění kanálu je asi 2 minuty, ale v některých případech může trvat až 20 +. Resetování kanálu může trvat až 5 minut.
> 
> 

## <a id="Considerations"></a>Důležité informace
* Když kanál **standardní** typ kódování dojde ke ztrátě vstupní zdroj/příspěvku informačního kanálu, ho kompenzuje ho tak, že nahradíte zdroj video nebo zvuk s projektem chyba a nečinnosti. Kanál bude pokračovat pro vydávání projektem, dokud vstup/příspěvku kanálu obnoví. Doporučujeme vám, že nebyla ve stavu, po dobu delší než 2 hodiny zbývajících živého kanálu. Za tímto bodem, není zaručena chování kanál na opětovné připojení vstupní, ani je její chování v reakci na příkaz obnovení. Je nutné zastavit kanál, odstraňte jej a vytvořte novou.
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Pokaždé, když překonfigurujete kodér za provozu, volání **resetovat** metoda na kanálu. Před resetujete kanál, budete muset, zastavte program. Po resetování kanálu restartujte program.
* Kanál se dá zastavit jenom v případě, že je ve stavu spuštění a zastavení všech programů na kanál.
* Ve výchozím nastavení můžete přidat pouze 5 kanály pro váš účet Media Services. Toto je doporučené kvóty na všechny nové účty. Další informace najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Se účtují pouze pokud je kanál v **systémem** stavu. Další informace najdete v části [to](media-services-manage-live-encoder-enabled-channels.md#states) části.
* V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. Pokud potřebujete, aby kanál běžel delší dobu, kontaktujte nás na adrese amslived@microsoft.com.
* Zajistěte, aby tak, aby měl koncový bod streamování, ze kterého chcete Streamovat obsah v **systémem** stavu.
* Předvolby kódování používá pojem "maximální kmitočet" 30 snímků za sekundu. Pokud vstup je 60fps / 59.97i, Vstupní rámce jsou vyřazeny nebo deaktivuje-interlaced na 30/29,97 fps. Pokud vstup je 50fps/50i, Vstupní rámce jsou vyřazeny nebo deaktivuje-interlaced až 25 fps. Pokud vstupní 25 snímků za sekundu, zůstane výstup 25 snímků za sekundu.
* Nezapomeňte si zastavení YOUR kanály po dokončení. Pokud ne, bude pokračovat fakturace.

## <a name="known-issues"></a>Známé problémy
* Doba spuštění kanálu bylo vylepšeno v průměru 2 minuty, ale v některých případech zvýšené poptávky může stále trvat až 20 +.
* Projektem obrázků musí být v souladu omezeními popsanými [zde](media-services-manage-live-encoder-enabled-channels.md#default_slate). Pokud se pokusíte vytvořit kanál s výchozí kontejner, který je větší než 1920 × 1080, požadavek bude běh chybu.
* Ještě jednou... Nezapomeňte kanály YOUR STOP po dokončení streamování. Pokud ne, bude pokračovat fakturace.

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doručování živě streamovaných událostí pomocí služby Azure Media Services](media-services-overview.md)

[Vytvoření kanálů, které proveďte kódování v reálném čase s přenosovou rychlostí v jednom datového proudu s adaptivní přenosovou rychlostí pomocí portálu](media-services-portal-creating-live-encoder-enabled-channel.md)

[Vytvoření kanálů, které proveďte kódování v reálném čase s přenosovou rychlostí v jednom datového proudu s adaptivní přenosovou rychlostí pomocí .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Správa kanálů pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[Koncepty služby Media Services](media-services-concepts.md)

[Azure Media Services fragmentovaných MP4 Live Ingestování specifikace](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

