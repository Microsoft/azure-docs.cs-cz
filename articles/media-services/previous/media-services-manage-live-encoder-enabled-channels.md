---
title: Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi | Dokumentace Microsoftu
description: 'Toto téma popisuje, jak vytvořit kanál, který přijímá datový proud s jednou přenosovou rychlostí za provozu z místní kodér a potom provede kódování v reálném čase na datový proud s adaptivní přenosovou rychlostí pomocí služby Media Services. Datový proud může pak bude doručen do klientské aplikace přehrávání prostřednictvím jednoho nebo více koncové body streamování, pomocí jedné z následujících protokolů pro adaptivní streamování: HLS, technologie Smooth Stream MPEG DASH.'
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
ms.date: 11/29/2018
ms.author: juliako;anilmur
ms.openlocfilehash: e7159a8e3acf45105a11cc4574f9474457bed3ea
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682652"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi

> [!NOTE]
> Od 12. května 2018, živé kanály budou nadále podporu přenosového streamu RTP/MPEG-2 protokol ingestování. Migrujte prosím z RTP/MPEG-2 na RTMP nebo MP4 s fragmentací (Smooth Streaming) ingestovací protokoly.

## <a name="overview"></a>Přehled
V Azure Media Services (AMS) **kanál** představuje kanál pro zpracování obsahu živého streamování. A **kanál** přijímat živé vstupní datové proudy v jednom ze dvou způsobů:

* Místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí do kanálu, který má povolené provádět živé kódování pomocí Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný soubor MP4). Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.
* Místní kodér služby live Encoding odešle s více přenosovými rychlostmi **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) na kanál, který není povolené provádět živé kódování pomocí AMS. Ingestované datové proudy prochází **kanál**bez dalšího zpracování. Tato metoda je volána **předávací**. Můžete použít následující kodéry, které výstupu technologie Smooth Streaming s více přenosovými rychlostmi: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco a Elemental. Následující kodéry výstupu RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek a Tricaster kodérů.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.
  
  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě Streamovat.
  > 
  > 

Od verze Media Services 2.10, když vytvoříte kanál, můžete určit, jakým způsobem chcete, aby pro váš kanál pro příjem vstupního datového proudu a určuje, jestli chcete pro kanál provádět živé kódování datového proudu. Máte dvě možnosti:

* **Žádný** – zadat tuto hodnotu, pokud budete chtít použít místní kodér služby live Encoding které bude výstupního datového proudu s více přenosovými rychlostmi (průchozí datový proud). V takovém případě příchozího datového proudu předává do výstup bez jakékoli kódování. Toto je chování kanál před 2.10 verzí.  Podrobné informace o práci s kanály tohoto typu, najdete v článku [živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
* **Standardní** – tuto hodnotu zvolte, pokud máte v plánu používat Media Services ke kódování živého datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi. Mějte na paměti, že fakturace dopad kódování v reálném čase a měli pamatovat opuštění živého kódování kanálu ve stavu "Spuštěno" budou účtovat poplatky.  Doporučuje se po dokončení se vyhnout poplatkům za velmi hodinové živě streamované události okamžitě zastavit spuštěné kanálů.

> [!NOTE]
> Toto téma popisuje atributy kanálů, které můžou provádět živé kódování (**standardní** typ kódování). Informace o práci s kanály, které nejsou povolené provádět živé kódování najdete v tématu [živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
> 
> Přečtěte si [aspekty](media-services-manage-live-encoder-enabled-channels.md#Considerations) oddílu.
> 
> 

## <a name="billing-implications"></a>Vliv na fakturaci
Živé kódování kanálu začne fakturace, jakmile je přechodů mezi stavy na "Spuštěno" prostřednictvím rozhraní API.   Můžete taky zobrazit stav na webu Azure Portal nebo v nástroji Azure Media Services Explorer (http://aka.ms/amse).

Následující tabulka uvádí přiřazení stavů kanálu stavům fakturace na portálu Azure a rozhraní API. Stavy se mírně liší mezi rozhraním API a portál uživatelské prostředí Jakmile je kanál ve stavu "Spuštěna" prostřednictvím rozhraní API nebo ve stavu "Připraveno" nebo "Streaming" na webu Azure Portal bude aktivováno fakturace.
Pokud chcete zastavit kanál z můžete dále fakturace, je nutné zastavit kanál prostřednictvím rozhraní API nebo na webu Azure Portal.
Zodpovídáte za ukončení kanálů, jakmile budete hotovi s živého kódování kanálu.  Nepodařilo se zastavit kódování kanálu způsobí trvalé fakturace.

### <a id="states"></a>Stavy kanálu a jak jsou mapovány k režimu fakturace
Aktuální stav kanálu. Možné hodnoty:

* **Zastavit**. Toto je počáteční stav kanálu po jeho vytvoření (Pokud autostart byla vybrána na portálu.) Vyvolá se v tomto stavu bez fakturace. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spouští se**. Kanál se spouští. Vyvolá se v tomto stavu bez fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu Zastaveno.
* **Spuštění**. Kanál dokáže zpracovávat živé streamy. To je nyní fakturaci využití. Je nutné zastavit kanál, aby se zabránilo další fakturace. 
* **Zastavuje se**. Kanál se zastavuje. Vyvolá se v tomto přechodném stavu bez fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Odstraňuje se**. Kanál se odstraňuje. Vyvolá se v tomto přechodném stavu bez fakturace. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka uvádí přiřazení stavů kanálu k režimu fakturace. 

| Stav kanálu | Indikátory v uživatelském rozhraní portálu | Je to fakturace? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (žádný běžící program)<br/>nebo<br/>Streamování (nejméně jeden běžící program) |ANO |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |Ne |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatické vypnutí kanálů nepoužívané
Od 25. ledna 2016, Media Services nasazeny v aktualizaci, která se automaticky zastaví kanál (s funkcí live encoding, povolené) za ho běhu nevyužitých stavu dlouhou dobu. To platí pro kanály, které mají žádné aktivní programy a které neobdrželi vstupní příspěvek informační kanál pro delší dobu.

Prahová hodnota pro nepoužívané období je formálně 12 hodin, ale může se změnit.

## <a name="live-encoding-workflow"></a>Živé kódování pracovního postupu
Následující diagram představuje živého streamování pracovního postupu kde kanál přijímá datový proud s jednou přenosovou rychlostí v jednom z těchto protokolů: RTMP nebo technologie Smooth Streaming; potom kóduje datový proud na datový proud s více přenosovými rychlostmi. 

![Živý pracovní postup][live-overview]

## <a id="scenario"></a>Běžný scénář živého streamování
Následující část představuje obecné kroky, které jsou součástí procesu vytváření běžných aplikací pro živé streamování.

> [!NOTE]
> V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. Pokud potřebujete, aby kanál běžel delší dobu, kontaktujte nás na adrese amslived@microsoft.com. Fakturační dopad kódování v reálném čase a jste měli mít na paměti, že byste museli opustit živého kódování kanálu ve stavu "Spuštěno" budou účtovat poplatky hodinové fakturace.  Doporučuje se po dokončení se vyhnout poplatkům za velmi hodinové živě streamované události okamžitě zastavit spuštěné kanálů. 
> 
> 

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte místní kodér služby live Encoding, který umí produkovat **jeden** datový proud s přenosovou rychlostí v jednom z těchto protokolů: RTMP nebo technologie Smooth Streaming. 
   
    Tento krok můžete provést i po vytvoření kanálu.
2. Vytvořte a spusťte kanál. 
3. Načtěte adresu URL ingestování kanálu. 
   
    Adresu URL ingestování používá kodér po kódování v reálném čase k odesílání datového proudu do kanálu.
4. Načtěte adresu URL náhledu kanálu. 
   
    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program. 
   
    Při použití na webu Azure portal, vytvoření programu vytvoří také asset. 
   
    Při použití sady .NET SDK nebo REST je potřeba vytvořit prostředek a nastavení, aby používal tento prostředek po vytvoření programu. 
6. Publikujte asset přidružený k programu.   
   
    >[!NOTE]
    >Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**. 
    
7. Jakmile budete připraveni začít streamovat a archivovat, spusťte program.
8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního datového proudu.
9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.
10. Odstraňte program (a volitelně můžete odstranit i asset).   

> [!NOTE]
> Je velmi důležité, abyste nezapomněli zastavit Live Encoding kanálu. Mějte na paměti, že je na hodinový fakturace dopad kódování v reálném čase a měli pamatovat opuštění živého kódování kanálu ve stavu "Spuštěno" budou účtovat poplatky.  Doporučuje se po dokončení se vyhnout poplatkům za velmi hodinové živě streamované události okamžitě zastavit spuštěné kanálů. 
> 
> 

## <a id="channel"></a>Kanál se uživatelovo zadání (ingestování) konfigurace
### <a id="Ingest_Protocols"></a>Ingestování datových proudů
Pokud **typ kodéru** je nastavena na **standardní**, platné možnosti jsou:

* S jednou přenosovou rychlostí **RTMP**
* S jednou přenosovou rychlostí **fragmentovaný soubor MP4** (technologie Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>S jednou přenosovou rychlostí RTMP
Požadavky:

* Příchozí datový proud nemůže obsahovat videa s více přenosovými rychlostmi
* Datový proud videa by měl mít průměrné s přenosovou rychlostí nižší než 15 MB/s
* Zvukový datový proud musí mít průměrné s přenosovou rychlostí nižší než 1 MB/s
* Podporované kodeky jsou následující:
* AVC MPEG-4 / videa H.264
* Směrný plán, hlavní, vysoce profilu (8 bitů 4:2:0)
* Vysoká 10 profilu (10-bit 4:2:0)
* Vysoká do profilu 422 (10-bit 4:2:2)
* Zvuk MPEG-2 AAC-LC
* Mono, Stereo obklopit (5.1, 7.1)
* 44,1 kHz vzorkovací frekvence
* Balení ADTS styl MPEG-2
* Doporučené kodérů patří:
* Telestream Wirecast
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmentovaný soubor MP4 s jednou přenosovou rychlostí (technologie Smooth Streaming)
Typický případ použití:

Použití místních kodérů z dodavatelé, jako je technologie Elemental, Ericsson, Ateme, Envivio odeslat vstupního datového proudu přes otevřený internet k blízkým Azure datového centra.

Požadavky:

Stejné jako u [s jednou přenosovou rychlostí RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Další důležité informace
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Maximální rozlišení pro příchozí datový proud videa je 1920 × 1080 a nanejvýš 60 pole za sekundu Pokud prokládanou nebo 30 snímků za sekundu, pokud progresivní.

### <a name="ingest-urls-endpoints"></a>Ingestované adresy URL (koncových bodů)
Kanál obsahuje vstupní koncový bod (adresa URL ingestu) určit v live encoder, tak můžete nabízet kodér datových proudů do kanálů.

Po vytvoření kanálu můžete získat adresy URL ingestování. Pokud chcete získat tyto adresy URL, nemá kanál v **systémem** stavu. Když jste připravení začít zapisovat data do kanálu, musí být v **systémem** stavu. Jakmile se kanál spustí ingestovat data, můžete zobrazit náhled datového proudu prostřednictvím adresy URL ve verzi preview.

Máte možnost ingestovat fragmentovaný soubor MP4 živý stream (technologie Smooth Streaming) připojení přes protokol SSL. K ingestování přes protokol SSL, nezapomeňte aktualizovat adresu URL ingestování na protokol HTTPS. V současné době nepodporuje AMS SSL s použitím vlastních domén.  

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které je povoleno publikování videa do tohoto kanálu. Povolené IP adresy můžete zadat jako jednu IP adresu (například '10.0.0.1'), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například ' 10.0.0.1/22') nebo IP adresy v rozsahu (například pomocí IP adresy a masky podsítě s tečkou , "10.0.0.1(255.255.252.0)').

Pokud žádné IP adresy nezadáte a nedefinujete žádné pravidlo, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

## <a name="channel-preview"></a>Náhled kanálu
### <a name="preview-urls"></a>Adresy URL náhledu
Kanály nabízejí koncový bod ve verzi preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Když vytvoříte kanál, můžete získat adresu URL ve verzi preview. Získat adresu URL, nemá kanál ve **systémem** stavu.

Jakmile se kanál spustí ingestovat data, můžete zobrazit náhled datového proudu.

> [!NOTE]
> Pouze aktuálně doručit stream náhledu v fragmentovaný soubor MP4 formátu (technologie Smooth Streaming) bez ohledu na zadaný typ vstupu.  Chcete-li zobrazit datový proud, můžete použít přehrávač hostované na webu Azure Portal.
> 
> 

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které jsou povolené pro připojení ke koncovému bodu ve verzi preview. Pokud nejsou zadány žádné IP adresy, povolí se všechny IP adresy. Povolené IP adresy můžete zadat jako jednu IP adresu (například '10.0.0.1'), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například ' 10.0.0.1/22') nebo IP adresy v rozsahu (například pomocí IP adresy a masky podsítě s tečkou , "10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Živé kódování.
Tato část popisuje, jak nastavení pro live encoder v rámci kanálu je možné upravit, když **typ kódování** kanálu je nastavena na **standardní**.

> [!NOTE]
> Váš příspěvek kanál může obsahovat jenom jeden zvukové stopy – ingestovat z více zvukové stopy v tuto chvíli nepodporuje. Při provádění živé kódování pomocí [místní živé kóduje](media-services-live-streaming-with-onprem-encoders.md), můžete poslat příspěvek kanálu v protokolu technologie Smooth Streaming s více zvukové stopy.
> 
> 

### <a name="ad-marker-source"></a>Zdroj značky služby AD
Můžete určit zdroj signálů reklamních značek. Výchozí hodnota je **Api**, což znamená, kodér služby live Encoding v rámci kanálu naslouchat požadavkům na asynchronní **rozhraní API reklamní značky**.

### <a name="cea-708-closed-captions"></a>Skryté titulky CEA 708 skryté titulky
Volitelný příznak, který informuje live encoder ignorovat veškerá data titulky CEA 708 součástí příchozích video. Když je příznak nastaven na hodnotu false (výchozí), kodér rozpozná a znovu skryté titulky CEA 708 data vložit do výstupní datové proudy videa.

#### <a name="index"></a>Index
Doporučuje se má poslat v jediném programu přenosový stream (SPTS). Pokud vstupní stream obsahuje více programů, live encoder v rámci kanálu analyzuje Program mapování tabulky (platba) ve vstupu, identifikuje vstupy, které mají název typu stream MPEG-2 AAC ADTS nebo AC-3 A systému nebo AC 3 System-B nebo privátní PES MPEG-2 nebo MPEG-1 Zvuk nebo zvukový MPEG-2 a uspořádává je v pořadí zadaném v platbě Index o základu 0 se pak použije ke sbírání n tou položku do tohoto režimu.

#### <a name="language"></a>Jazyk
Identifikátor jazyka zvukový datový proud, který odpovídá ISO 639-2, jako je například ENG. Pokud není k dispozici, výchozí hodnota je a (nedefinované).

### <a id="preset"></a>Systémová předvolba
Určuje nastavení používané kodér služby live Encoding v rámci tohoto kanálu. V současné době pouze povolená hodnota je **Default720p** (výchozí).

Všimněte si, že pokud potřebujete vlastní předvolby, měli byste požádat amslived@microsoft.com.

**Default720p** bude kódování videa do následujících 6 vrstev.

#### <a name="output-video-stream"></a>Výstupní Stream videa
| S přenosovou rychlostí | Šířka | Výška | MaxFPS | Profil | Název výstupní Stream |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoký |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Vysoký |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Vysoký |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Vysoký |Video_512x288_850kbps |
| 550 |384 |216 |30 |Vysoký |Video_384x216_550kbps |
| 200 |340 |192 |30 |Vysoký |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Výstup zvukový Stream

Zvuk je zakódován do stereo AAC-LC na 128 kb/s, vzorkovací frekvenci 48 kHz.

## <a name="signaling-advertisements"></a>Signalizace oznámení o inzerovaných programech
Pokud váš kanál Live Encoding povolené, máte komponenta v kanálu, který je zpracování videa a můžete pracovat s. Mohou signalizovat pro kanál k vložení do odchozího streamu s adaptivní přenosovou rychlostí slaty nebo reklamy. Slaty jsou nepohyblivé obrázky, které můžete použít k zakrytí vstupního živého kanálu v určitých situacích (třeba během komerční přestávky). Inzerování signálů, jsou čas synchronizovaný signály, které můžete vložit do streamu odchozí říct přehrávač videa se zvláštní akce – například pokud chcete přepnout na oznámení o inzerovaném programu v příslušnou dobu. Najdete v tomto [blogu](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) přehled SCTE 35 signalizační mechanismu použitém k tomuto účelu. Níže je typický scénář, který je možné implementovat v živé události.

1. Máte vaši uživatelé získat image před událostí před spuštěním události.
2. Máte vaši uživatelé získat image po události, až událost skončí.
3. Máte vaši uživatelé získat obrázek událost chyby, pokud dojde k potížím během události (například výpadku napájení v stadionem).
4. Odešlete image AD-BREAK skrýt živou událost kanálu během komerční přestávky.

Toto jsou vlastnosti, které můžete nastavit, pokud signalizace oznámení o inzerovaných programech. 

### <a name="duration"></a>Doba trvání
Doba v sekundách pro komerční přestávky. Toto musí být nenulové kladné hodnoty zahájíte komerční přestávky. Když probíhá komerční přestávky a doba trvání je nastavit na nulu CueId shodu probíhající komerční přestávky, pak se zruší tuto přerušení.

### <a name="cueid"></a>CueId
Jedinečné ID pro komerční přestávky, použije aplikace pro příjem dat provést příslušné akce. Musí být kladné celé číslo. Můžete tuto hodnotu nastavit na libovolný náhodné celé kladné číslo nebo použít ke sledování startovací ID nadřazeného systému. Zkontrolujte všechna ID pro kladná celá čísla normalizace před odesláním prostřednictvím rozhraní API.

### <a name="show-slate"></a>Břidlicová show
Volitelné. Signály kodér služby live Encoding pro přepnutí do [výchozí břidlicová](media-services-manage-live-encoder-enabled-channels.md#default_slate) image během komerční přestávky a skrýt příchozích video informačního kanálu. Zvuk během břidlicová také ztlumený. Výchozí hodnota je **false**. 

Obrázek použitý bude uvedeno prostřednictvím assetu slatu výchozí vlastnost Id v době vytváření kanálů. Břidlicová bude roztažen tak, aby odpovídal zadané velikosti zobrazení obrázku. 

## <a name="insert-slate--images"></a>Vložit břidlicová imagí
Live encoder v rámci kanálu můžete signalizován přejděte do obrázku slatu. Můžete ho také signál k ukončení břidlicová probíhající. 

Kodér služby live Encoding je možné nakonfigurovat si přepnout na obrázek slatu a skrýt příchozích video signál v určitých situacích – například při přerušení ad. Pokud takové břidlicová není nakonfigurovaná, není během této ad přerušení maskovaná vstupního videa.

### <a name="duration"></a>Doba trvání
Doba břidlicová během několika sekund. Ten musí mít nenulové kladné hodnoty, aby bylo možné spustit břidlicová. Pokud dojde probíhající břidlicová a nulovou dobou trvání je zadán, bude tento probíhající břidlicová ukončen.

### <a name="insert-slate-on-ad-marker"></a>Vložit slate nebo reklamní značku
Při konfiguraci nastavenou na hodnotu true, toto nastavení kodér služby live Encoding vložit obrázek slatu při přerušení ad. Výchozí hodnota je true. 

### <a id="default_slate"></a>Id Assetu slatu výchozí

Volitelné. Určuje Id prostředku služby Asset média, který obsahuje obrázek slatu. Výchozí hodnota je null. 


> [!NOTE] 
> Před vytvořením kanálu, obrázek slatu s následujícími omezeními musí být nahrán jako vyhrazený prostředek (žádné další soubory by měly být v tomto prostředku). Tato image se používá pouze v případě, kodér služby live Encoding je vložení břidlicová z důvodu přerušení ad nebo byla explicitně signalizována k vložení břidlicová. Není aktuálně neumožňuje použití vlastní image, když kodér služby live Encoding přejde do takové stavu ke ztrátě vstupního signálu. Můžete hlasovat pro tuto funkci [tady](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Maximálně 1920 × 1080 v řešení.
* Maximálně 3 MB k dispozici velikosti.
* Název souboru musí mít příponu *.jpg.
* Na obrázku musí být odeslán do Assetu jako pouze AssetFile, prostředky a toto AssetFile musí být označený jako primární soubor. Prostředek nemůže být šifrování úložiště.

Pokud **výchozí břidlicová Id Assetu** není zadán, a **vložit slate nebo reklamní značku** je nastavena na **true**, výchozí image Azure Media Services se používají ke skrytí vstupního videa datový proud. Zvuk během břidlicová také ztlumený. 

## <a name="channels-programs"></a>Programy kanálu
Kanál je přidružený k programům, které vám umožňují řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálů a programů se velmi podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka okna archivu také určuje, že maximální počet časových klientů můžete posunout zpátky v čase od aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružena k Assetu, který ukládá obsah datového proudu. Prostředek je namapovaná na kontejner objektů blob bloku v účtu Azure Storage a soubory v prostředku se ukládají jako objekty BLOB v tomto kontejneru. Program publikovat, takže vaši zákazníci mohou zobrazit datový proud je nutné vytvořit lokátor OnDemand pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte a spusťte nový program pro každou jednotlivou událost, jak je popsáno v části programování aplikací živého streamování.

Jakmile budete připraveni začít streamovat a archivovat, spusťte program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program. 

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte program a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud ho program používá. Nejdřív odstraňte program. 

I po zastavení a odstranění programu můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud neodstraníte asset.

Pokud chcete archivovaný obsah zachovat, ale nechcete ho zpřístupňovat pro streamování, odstraňte lokátor streamování.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Získávání náhled živého kanálu
Pokud Live Encoding je povolená, můžete teď získáte náhled živého kanálu dosáhne kanálu. To může být cenným nástrojem ke kontrole, jestli živého kanálu je ve skutečnosti dosažení kanálu. 

## <a id="states"></a>Stavy kanálu a jak stavů k režimu fakturace
Aktuální stav kanálu. Možné hodnoty:

* **Zastavit**. Toto je počáteční stav kanálu po jeho vytvoření. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spouští se**. Kanál se spouští. V tomto stavu nejsou povoleny žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu Zastaveno.
* **Spuštění**. Kanál dokáže zpracovávat živé streamy.
* **Zastavuje se**. Kanál se zastavuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Odstraňuje se**. Kanál se odstraňuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

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
* Pokud kanál **standardní** typ kódování dojde ke ztrátě kanál vstupní zdroj/příspěvek, ho vyrovnává se tak, že nahradíte zdroj videa nebo zvuku s břidlicová chyba a nečinnosti. Kanál bude generovat břidlicová, dokud vstup/příspěvek kanálu obnoví. Doporučujeme vám, že nebude ve stavu, po dobu delší než 2 hodiny vlevo živého kanálu. Za tímto bodem, není zaručeno chování kanál na vstupní opětovné připojení, ani jeho chování v reakci na příkaz Obnovit je. Budete muset zastavit kanál, odstraňte ho a vytvořte novou.
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Pokaždé, když je znovu nakonfigurovat live encoder, zavolejte **resetování** metodu na kanál. Před restartováním kanál, budete muset Zastavit program. Po resetování kanálu restartujte program.
* Kanál můžete zastavit pouze, pokud je ve spuštěném stavu a byly zastaveny všechny programy na kanálu.
* Ve výchozím nastavení je 5 kanály přidat pouze do účtu Media Services. Toto je doporučené kvóty na všechny nové účty. Další informace najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Nebudete dostávat faktury, pokud je váš kanál ve **systémem** stavu. Další informace najdete v [to](media-services-manage-live-encoder-enabled-channels.md#states) oddílu.
* V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. Pokud potřebujete, aby kanál běžel delší dobu, kontaktujte nás na adrese amslived@microsoft.com.
* Ujistěte se, že jste koncový bod streamování, ze kterého chcete Streamovat obsah v **systémem** stavu.
* Použitá předvolba kódování používá pojem "max kmitočet" 30 snímků za sekundu. Ano, pokud je vstup 60 snímků za sekundu / 59.94i, vstupní snímků jsou vyřazeny nebo rušení-interlaced na 30/29,97 snímků za sekundu. Pokud je vstup 50 snímků za sekundu/50i, vstupní snímků jsou vyřadit/rušení-interlaced do 25 snímků za sekundu. Pokud je vstup 25 snímků za sekundu, výstup zůstává na 25 snímků za sekundu.
* Nezapomeňte si svoje kanály STOP po dokončení. Pokud ne, budou dál fakturace.

## <a name="known-issues"></a>Známé problémy
* Doba spuštění kanálu je vylepšená průměrem 2 minuty, ale čas od času zvýšené poptávky může stále trvat až 20 +.
* Slatu bitové kopie odpovídat omezení popsaná [tady](media-services-manage-live-encoder-enabled-channels.md#default_slate). Pokud se pokusíte k vytvoření kanálu s výchozí kontejner, který je větší než 1920 × 1080, skončí chybou nakonec požadavku.
* Ještě jednou... Nezapomeňte kanály YOUR STOP po dokončení datových proudů. Pokud ne, budou dál fakturace.

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doručování živě streamovaných událostí pomocí Azure Media Services](media-services-overview.md)

[Vytvořte kanály, které živé kódování v jednom bitrate do datového proudu s adaptivní přenosovou rychlostí pomocí portálu](media-services-portal-creating-live-encoder-enabled-channel.md)

[Vytvořte kanály, které proveďte kódování v reálném čase ze jednom přenosové rychlosti streamování s adaptivní přenosovou rychlostí pomocí sady .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Správa kanálů pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[Koncepty služby Media Services](media-services-concepts.md)

[Specifikace Ingestování fragmentovaného MP4 za služby Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

