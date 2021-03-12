---
title: Živé streamování pomocí Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi | Microsoft Docs
description: Toto téma popisuje, jak nastavit kanál, který obdrží živý datový proud s jednou přenosovou rychlostí z místního kodéru a pak provede živé kódování pro datový proud s adaptivní přenosovou rychlostí pomocí Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: b9b4cd54375a13da95259e27da680255f785df45
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013206"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Od 12. května 2018 už živé kanály nebudou podporovat protokol ingestování přenosového streamu RTP/MPEG-2. Migrujte prosím z protokolu RTP/MPEG-2 na RTMP nebo fragmentujte protokoly ingesta MP4 (Smooth Streaming).

## <a name="overview"></a>Přehled
V Azure Media Services (AMS) **kanál** představuje kanál pro zpracování obsahu živého streamování. **Kanál** přijímá živé vstupní proudy jedním ze dvou způsobů:

* On-premises Live Encoder odesílá datový proud s jednou přenosovou rychlostí do kanálu, který má povolené kódování v reálném čase, pomocí Media Services v jednom z následujících formátů: RTMP nebo Smooth Streaming (fragmentovaný MP4). Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.
* On-premises Live Encoder pošle do kanálu **RTMP** s více přenosovými rychlostmi nebo **Smooth Streaming** (fragmentovaný MP4) do kanálu, který není povolený pro provádění živého kódování v AMS. Ingestované datové proudy procházejí **kanálem** s bez dalšího zpracování. Tato metoda se nazývá **předávací**. Můžete použít následující živé kodéry, které mají výstup s více přenosovými rychlostmi Smooth Streaming: MediaExcel, Ateme, představovat komunikaci, Envivio, Cisco a prvky. Následující kodéry pro výstup ve výstupním [datovém](media-services-configure-wirecast-live-encoder.md)toku: Wirecast, Haivision, Teradek Encoders.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.

  > [!NOTE]
  > Použití předávací metody je nejúčinnější způsob, jak provádět živé streamování.
  > 
  > 

Počínaje verzí Media Services 2,10 můžete při vytváření kanálu určit, jak chcete, aby kanál přijímal vstupní datový proud a zda chcete, aby kanál prováděl živé kódování vašeho datového proudu. Máte dvě možnosti:

* **Žádné** – tuto hodnotu zadejte, pokud plánujete použít místní kodér Live, který bude výstupem datového proudu s více přenosovými rychlostmi (předávací proud). V tomto případě příchozí datový proud předaný do výstupu bez kódování. Toto chování kanálu před vydáním verze 2,10.  Podrobnější informace o práci s kanály tohoto typu najdete v tématu [živé streamování s místními kodéry, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – tuto hodnotu vyberte, pokud chcete použít Media Services ke kódování živého datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi. Mějte na paměti, že pro živé kódování je potřeba fakturace a že byste měli mít na paměti, že když zachováte živý kanál kódování ve stavu spuštěno, budou se účtovat poplatky za účtování.  Doporučuje se, abyste ihned zastavili spuštěné kanály po dokončení události živého streamování, abyste se vyhnuli dodatečným hodinovým poplatkům.

> [!NOTE]
> Toto téma popisuje atributy kanálů, které jsou povoleny pro živé kódování (**standardní** typ kódování). Informace o práci s kanály, které nepodporují živé kódování, najdete v tématu [živé streamování s místními kodéry, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
> 
> Nezapomeňte si prohlédnout část s [informacemi](media-services-manage-live-encoder-enabled-channels.md#Considerations) o tom, co je potřeba.
> 
> 

## <a name="billing-implications"></a>Důsledky fakturace
Kanál Live Encoding začíná při přechodu do stavu "spuštěno" prostřednictvím rozhraní API.   Stav můžete zobrazit také v Azure Portal nebo v nástroji Azure Media Services Explorer ( https://aka.ms/amse) .

Následující tabulka ukazuje, jak se stavy kanálu mapují na fakturační stavy v rozhraní API a Azure Portal. Stavy se mírně liší mezi rozhraním API a uživatelského prostředí portálu. Jakmile je kanál ve stavu spuštěno prostřednictvím rozhraní API nebo ve stavu "připraveno" nebo "Streaming" ve Azure Portal, bude faktura aktivní.
Chcete-li zastavit vyúčtování kanálu, je nutné zastavit kanál přes rozhraní API nebo v Azure Portal.
Zodpovídáte za zastavování kanálů, když jste hotovi s kanálem kódování Live Encoding.  Při zastavení kanálu pro kódování dojde k pokračování fakturace.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Stavy kanálu a jejich mapování na režim fakturace
Aktuální stav kanálu. Mezi možné hodnoty patří:

* **Zastaveno**. Toto je počáteční stav kanálu po jeho vytvoření (Pokud jste na portálu nevybrali Automatické spuštění). V tomto stavu nedochází k žádnému fakturaci. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spouští** se. Kanál se spouští. V tomto stavu nedochází k žádnému fakturaci. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu Zastaveno.
* **Spuštěno**. Kanál dokáže zpracovávat živé streamy. Nyní je využití fakturace. Aby se zabránilo dalšímu fakturaci, je nutné kanál zastavit. 
* **Zastavuje** se. Kanál se zastavuje. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Probíhá odstraňování**. Kanál se odstraňuje. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka ukazuje, jak se stavy kanálu mapují na režim fakturace. 

| Stav kanálu | Indikátory uživatelského rozhraní portálu | Účtuje se fakturace? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (žádné spuštěné programy)<br/>nebo<br/>Streamování (aspoň jeden spuštěný program) |ANO |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |No |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatické vypnutí pro nepoužívané kanály
Od 25. ledna 2016 Media Services zavedli aktualizaci, která automaticky zastaví kanál (s povoleným živým kódováním) poté, co byl po dlouhou dobu spuštěný v nepoužívaném stavu. To platí pro kanály, které neobsahují žádné aktivní programy a které po delší dobu nedostaly vstupní příspěvek na dávky.

Prahová hodnota pro nevyužité období je jmenovitá 12 hodin, ale může se změnit.

## <a name="live-encoding-workflow"></a>Pracovní postup Live Encoding
Následující diagram představuje pracovní postup živého streamování, kde kanál obdrží datový proud s jednou přenosovou rychlostí v jednom z následujících protokolů: RTMP nebo Smooth Streaming; pak datový proud zakóduje do datového proudu s více přenosovými rychlostmi. 

![Živý pracovní postup][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Běžný scénář živého streamování
Následující část představuje obecné kroky, které jsou součástí procesu vytváření běžných aplikací pro živé streamování.

> [!NOTE]
> V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin.
>
> Pro živé kódování se účtuje váš dopad na fakturaci a nezapomeňte si uvědomit, že při zanechání kanálu živého kódování ve stavu spuštěno budou účtovány poplatky za hodinové účtování. Doporučuje se, abyste ihned zastavili spuštěné kanály po dokončení události živého streamování, abyste se vyhnuli dodatečným hodinovým poplatkům. 

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte on-premises Live Encoder, který může vystavovat jeden datový proud s **jednou** přenosovou rychlostí v jednom z následujících protokolů: RTMP nebo Smooth Streaming. 

    Tento krok můžete provést i po vytvoření kanálu.
2. Vytvořte a spusťte kanál. 
3. Načtěte adresu URL ingestování kanálu. 

    Adresu URL ingestování používá kodér po kódování v reálném čase k odesílání datového proudu do kanálu.
4. Načtěte adresu URL náhledu kanálu. 

    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte program. 

    Při použití Azure Portal vytvoří program také Asset. 

    Při použití sady .NET SDK nebo REST musíte vytvořit Asset a při vytváření programu určit, že se má tento Asset použít. 
6. Publikujte Asset přidružený k programu.   

    >[!NOTE]
    >Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**. 

7. Jakmile budete připraveni začít streamovat a archivovat, spusťte program.
8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního proudu.
9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program.
10. Odstraňte program (a volitelně můžete odstranit i asset).   

> [!NOTE]
> Je velmi důležité se zapomenout zastavit Live Encoding kanál. Mějte na paměti, že při živém kódování má dopad na hodinovou fakturaci a máte pozor, abyste si zanechali kanál Live Encoding ve stavu spuštěno, a účtují se za ně poplatky za účtování.  Doporučuje se, abyste ihned zastavili spuštěné kanály po dokončení události živého streamování, abyste se vyhnuli dodatečným hodinovým poplatkům. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Konfigurace vstupu kanálu (ingestování)
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Protokol instreamování
Pokud je **typ kodéru** nastavený na **standardní**, platné možnosti jsou:

* **RTMP** s jednou přenosovou rychlostí
* **Fragmentovaná** s jednou přenosovou rychlostí: MP4 (Smooth Streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>RTMP s jednou přenosovou rychlostí
Požadavky:

* Příchozí datový proud nemůže obsahovat video s více přenosovými rychlostmi.
* Datový proud videa by měl mít průměrnou rychlost nižší než 15 MB/s.
* Datový proud zvuku by měl mít průměrnou přenosovou rychlost nižší než 1 MB/s.
* Podporovány jsou následující kodeky:
* Video MPEG-4 AVC/H. 264
* Směrný plán, hlavní, vysoký profil (8 bitů 4:2:0)
* Vysoký 10 profil (10 bitů 4:2:0)
* Profil High 422 (10 bitů 4:2:2)
* Zvuk MPEG-2 AAC-LC
* Mono, stereo, Surround (5,1, 7,1)
* vzorkovací frekvence 44,1 kHz
* ADTS balení stylu MPEG-2
* Mezi Doporučené kodéry patří:
* [Wirecastový Stream](media-services-configure-wirecast-live-encoder.md)
* Live Encoder Flash Media

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmentovaný soubor MP4 s jednou přenosovou rychlostí (technologie Smooth Streaming)
Typický případ použití:

Používejte místní živé kodéry od dodavatelů, jako jsou například prvky technologie, Ericsson, Ateme, Envivio, které odesílají vstupní proud přes otevřený Internet do nejbližšího datového centra Azure.

Požadavky:

Stejné jako pro [RTMP s jednou přenosovou rychlostí](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Další důležité informace
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Maximální rozlišení příchozího streamu videa je 1080 a maximálně 60 polí za sekundu, pokud je prokládaný, nebo 30 snímků za sekundu, pokud je progresivní.

### <a name="ingest-urls-endpoints"></a>Adresy URL ingestování (koncové body)
Kanál poskytuje vstupní koncový bod (adresa URL pro příjem), který zadáte v živém kodéru, takže kodér může do kanálů vložit streamy.

Po vytvoření kanálu můžete získat adresy URL pro přijímání. Aby se tyto adresy URL dostaly, kanál se nemusí nacházet ve stavu **spuštěno** . Až budete připraveni začít s vložením dat do kanálu, musí být ve stavu **spuštěno** . Jakmile kanál začne přijímat data, můžete zobrazit náhled datového proudu prostřednictvím adresy URL náhledu.

Máte možnost ingestovat fragmenty živého datového proudu MP4 (Smooth Streaming) přes připojení TLS. Pokud chcete ingestovat přes TLS, nezapomeňte aktualizovat adresu URL ingestování na HTTPS. AMS v současné době nepodporuje TLS s vlastními doménami.  

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které můžou publikovat video na tento kanál. Povolené IP adresy je možné zadat buď jako jednu IP adresu (například 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například 10.0.0.1/22), nebo rozsah IP adres pomocí IP adresy a masky podsítě s tečkami (například 10.0.0.1 (255.255.252.0)).

Pokud žádné IP adresy nezadáte a nedefinujete žádné pravidlo, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.

## <a name="channel-preview"></a>Náhled kanálu
### <a name="preview-urls"></a>Náhledové adresy URL
Kanály poskytují koncový bod verze Preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Adresu URL náhledu můžete získat při vytváření kanálu. Aby se adresa URL získala, kanál nemusí být ve stavu **spuštěno** .

Jakmile kanál začne přijímat data, můžete zobrazit náhled streamu.

> [!NOTE]
> V současné době se datový proud ve verzi Preview dá doručit jenom ve formátu fragmentovaných MP4 (Smooth Streaming) bez ohledu na zadaný vstupní typ.  K zobrazení datového proudu můžete použít přehrávač, který je hostovaný v Azure Portal.
> 
> 

### <a name="allowed-ip-addresses"></a>Povolené IP adresy
Můžete definovat IP adresy, které mají povolené připojení ke koncovému bodu Preview. Pokud nejsou zadány žádné IP adresy, nebude povolena žádná IP adresa. Povolené IP adresy je možné zadat buď jako jednu IP adresu (například 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (například 10.0.0.1/22), nebo rozsah IP adres pomocí IP adresy a masky podsítě s tečkami (například 10.0.0.1 (255.255.252.0)).

## <a name="live-encoding-settings"></a>Nastavení živého kódování
Tato část popisuje, jak lze upravit nastavení pro živý kodér v kanálu, pokud je **typ kódování** kanálu nastaven na hodnotu **Standard**.

> [!NOTE]
> Váš informační kanál příspěvku může obsahovat jenom jednu zvukovou stopu, ale příjem několika zvukových stop se v tuto chvíli nepodporuje. Při živém kódování s [místními kódováními v reálném čase](media-services-live-streaming-with-onprem-encoders.md)můžete odeslat informační kanál příspěvků v protokolu Smooth Streaming, který obsahuje více zvukových stop.
> 
> 

### <a name="ad-marker-source"></a>Zdroj značky reklamy
Můžete zadat zdroj pro signály značek AD. Výchozí hodnota je **rozhraní API**, které indikuje, že živý kodér v kanálu by měl naslouchat **rozhraní API asynchronní značky AD**.

### <a name="cea-708-closed-captions"></a>Skryté titulky CEA 708
Volitelný příznak, který dává kodéru pokyn, aby ignoroval všechna data titulů CEA 708 vložená do příchozího videa. Pokud je příznak nastaven na hodnotu false (výchozí), kodér detekuje a znovu vloží data CEA 708 do výstupních streamů videa.

#### <a name="index"></a>Index
Doporučuje se posílat v jednom datovém proudu jednoho programu (SPTS). Pokud vstupní datový proud obsahuje více programů, Live Encoder v kanálu analyzuje tabulku map programu (SPLÁTKy) ve vstupu, identifikuje vstupy, které mají název typu datového proudu MPEG-2 AAC ADTS nebo AC-3 System-B nebo MPEG-1 audio nebo MPEG-2 a jsou uspořádány v pořadí zadaném v poli SPLÁTKa. Index založený na nule se pak použije k výběru n-tého záznamu v tomto uspořádání.

#### <a name="language"></a>Jazyk
Identifikátor jazyka zvukového datového proudu, který je v souladu s ISO 639-2, jako je například ENG. Pokud není k dispozici, výchozí hodnota je UND (Nedefinováno).

### <a name="system-preset"></a><a id="preset"></a>Předvolby systému
Určuje předvolby, které má živý kodér používat v rámci tohoto kanálu. V současné době je jediná povolená hodnota **Default720p** (výchozí).

**Default720p** bude kódovat video do následujících 6 vrstev.

#### <a name="output-video-stream"></a>Výstupní video stream

| Rychlostí | Width (Šířka) | Height (Výška) | MaxFPS | Profil | Název výstupního datového proudu |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Vysoká |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Vysoká |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Vysoká |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Vysoká |Video_512x288_850kbps |
| 550 |384 |216 |30 |Vysoká |Video_384x216_550kbps |
| 200 |340 |192 |30 |Vysoká |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Výstupní zvukový stream

Zvuk je kódovaný na stereofonní AAC-LC při 128 kbps, vzorkovací frekvenci 48 kHz.

## <a name="signaling-advertisements"></a>Signalizace inzerce
Když je váš kanál Live Encoding povolený, budete mít ve svém kanálu součást, která zpracovává video a může s ním manipulovat. Můžete signalizovat, že kanál bude vkládat SLAT nebo reklamy do odchozího datového proudu s adaptivní přenosovou rychlostí. Hodnoty SLAT jsou stále obrázky, které můžete použít k pokrytí vstupních kanálů v reálném čase (například během komerčního přerušení). Reklamní signály jsou signálem synchronizovaný čas, který vložíte do odchozího datového proudu, abyste přehrávači videa informovali, že má provést zvláštní akci – například pro přechod na reklamu v příslušném čase. Přehled mechanismu signalizace SCTE-35, který se používá pro tento účel, najdete v tomto [blogu](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) . Níže je typický scénář, který můžete implementovat ve své živé události.

1. Pořiďte si, aby si čtenáři před zahájením události získali image před začátkem události.
2. Získejte po ukončení události image po UDÁLOSTech.
3. Pokud dojde k problému v průběhu události (například selhání napájení v Stadium Stadium), zobrazí se vašim čtenářům chybová zpráva o chybě.
4. Odesláním bitové kopie AD-BREAK skryjete živý informační kanál události během komerčního přerušení.

Níže jsou uvedeny vlastnosti, které můžete nastavit při signalizaci inzerce. 

### <a name="duration"></a>Doba trvání
Doba trvání komerčního přerušení v sekundách. Aby bylo možné zahájit komerční přerušení, musí být to nenulová kladná hodnota. V případě, že probíhá komerční přerušení a doba trvání je nastavena na hodnotu nula s CueId odpovídajícím probíhajícímu komerčnímu přerušení, pak toto přerušení bylo zrušeno.

### <a name="cueid"></a>CueId
Jedinečné ID pro komerční přerušení, které má aplikace pro příjem dat použít k provedení příslušných akcí. Musí být kladné celé číslo. Tuto hodnotu můžete nastavit na jakékoli náhodné kladné celé číslo nebo pomocí nadřazeného systému sledovat ID hromádky. Před odesláním prostřednictvím rozhraní API je třeba nastavit, aby se všechna ID před odesláním přes rozhraní API normalizoval.

### <a name="show-slate"></a>Zobrazit SLAT
Nepovinný parametr. Signalizuje Live Encoder, aby se během komerčního přerušení přepnul na [výchozí obrázek SLAT](media-services-manage-live-encoder-enabled-channels.md#default_slate) a skryla příchozí kanál videa. Zvuk je také ztlumený v SLAT. Výchozí hodnota je **false**. 

Použitý obrázek bude použit jako výchozí vlastnost ID prostředku SLAT v okamžiku vytvoření kanálu. Hodnota SLAT bude roztažena tak, aby odpovídala velikosti zobrazované image. 

## <a name="insert-slate--images"></a>Vložit obrázky SLAT
Kodér Live Encoder v kanálu se dá signalizovat, aby se mohl přepnout na obrázek SLAT. Může se také signalizovat, že se má ukončit prochází. 

Live Encoder se dá nakonfigurovat tak, aby se v určitých situacích přepnul na obrázek s příchodem, a když se v některých případech skryje signál příchozího videa, například během přerušení reklamy. Pokud takové SLAT není nakonfigurované, vstupní video se během tohoto přerušení reklamy nemaskuje.

### <a name="duration"></a>Doba trvání
Doba trvání SLAT v sekundách. Aby bylo možné začít SLAT, musí to být nenulová kladná hodnota. Pokud je k dispozici síla k dispozici a je zadána doba trvání nula, bude ukončeno.

### <a name="insert-slate-on-ad-marker"></a>Vložit SLAT do značky reklamy
Když se nastaví na true, toto nastavení nakonfiguruje živý kodér tak, aby při přerušení reklamy vložil obrázek SLAT. Výchozí hodnotou je hodnota true. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Výchozí ID prostředku SLAT

Nepovinný parametr. Určuje ID Assetu Media Services prostředku, který obsahuje obrázek SLAT. Výchozí hodnota je null. 


> [!NOTE] 
> Před vytvořením kanálu by se image SLAT s následujícími omezeními měla nahrát jako vyhrazený prostředek (žádné další soubory by se měly v tomto prostředku používat). Tento obrázek se používá pouze v případě, že živý kodér vkládá hodnotu SLAT z důvodu přerušení reklamy nebo byl explicitně signalizována k vložení položky SLAT. V tuto chvíli není k dispozici možnost použít vlastní image, když Live Encoder vstoupí do stavu "vstupní signál ztracen". Tuto funkci můžete hlasovat [tady](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* Maximálně 1080 v rozlišení.
* Velikost je nejvýše 3 MB.
* Název souboru musí mít příponu *. jpg.
* Obrázek musí být nahrán do Assetu, protože jediný AssetFile v tomto prostředku a tento AssetFile by měl být označený jako primární soubor. Asset nemůže být zašifrovaný do úložiště.

Pokud není zadané **výchozí ID prostředku SLAT** a **značka vložit SLAT na reklamu** je nastavená na **true**, použije se pro skrytí vstupního streamu videa výchozí Azure Media Services obrázek. Zvuk je také ztlumený v SLAT. 

## <a name="channels-programs"></a>Programy kanálu
Kanál je přidružený k programům, které vám umožňují řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálu a programu je velmi podobný tradičnímu médiu, kde kanál obsahuje konstantní datový proud obsahu a program je vymezen na určitou událost s časovým limitem na daném kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka okna archivu také určuje maximální počet časových limitů, které mohou klienti vyhledávat v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružen k Assetu, který ukládá obsah streamu. Asset se namapuje na kontejner objektů blob bloku v účtu Azure Storage a soubory v prostředku se ukládají jako objekty BLOB v tomto kontejneru. Pokud chcete publikovat program, aby mohli vaši zákazníci zobrazit datový proud, musíte pro přidružený prostředek vytvořit Lokátor OnDemand. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte a spusťte nový program pro každou událost, jak je popsáno v části programování aplikací pro živé streamování.

Jakmile budete připraveni začít streamovat a archivovat, spusťte program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program. 

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte program a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud ho program používá. Nejdřív odstraňte program. 

I po zastavení a odstranění programu můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud neodstraníte asset.

Pokud chcete archivovaný obsah zachovat, ale nechcete ho zpřístupňovat pro streamování, odstraňte lokátor streamování.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Získání náhledu z živého kanálu
Když je povolená Live Encoding, můžete teď po dosažení kanálu získat náhled živého kanálu. Může to být cenný nástroj, který ověří, jestli váš živý kanál skutečně dosahuje kanálu. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Stavy kanálu a způsob mapování stavů do režimu fakturace
Aktuální stav kanálu. Mezi možné hodnoty patří:

* **Zastaveno**. Toto je počáteční stav kanálu po jeho vytvoření. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spouští** se. Kanál se spouští. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu Zastaveno.
* **Spuštěno**. Kanál dokáže zpracovávat živé streamy.
* **Zastavuje** se. Kanál se zastavuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Probíhá odstraňování**. Kanál se odstraňuje. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka ukazuje, jak se stavy kanálu mapují na režim fakturace. 

| Stav kanálu | Indikátory uživatelského rozhraní portálu | Účtuje? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (žádné spuštěné programy)<br/>nebo<br/>Streamování (aspoň jeden spuštěný program) |Yes |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |No |

> [!NOTE]
> V současné době je průměrem začátku kanálu přibližně 2 minuty, ale v některých případech může trvat až 20 minut. Resetování kanálu může trvat až 5 minut.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Požadavky
* Když se v kanálu **standardního** typu kódování vyskytne ztráta vstupního kanálu zdroje nebo příspěvků, nahradí se zdrojem videa nebo zvukem chyba SLAT a netichá. Kanál bude dál generovat SLAT, dokud se neobnoví informační kanál vstupů a příspěvků. Doporučujeme, aby živý kanál nebyl ponechán v takovém stavu déle než 2 hodiny. Po tomto okamžiku není zaručeno chování kanálu na vstupním připojení, ani v reakci na příkaz k obnovení není ani jeho chování. Budete muset kanál zastavit, odstranit ho a vytvořit nový.
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Pokaždé, když znovu nakonfigurujete živý kodér, zavolejte na kanálu metodu **resetování** . Před resetováním kanálu musíte program zastavit. Po resetování kanálu restartujte program.
* Kanál lze zastavit pouze v případě, že je ve stavu spuštěno a všechny programy v kanálu byly zastaveny.
* Ve výchozím nastavení můžete k účtu Media Services přidat pouze 5 kanálů. Jedná se o softwarovou kvótu pro všechny nové účty. Další informace najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).
* Vstupní protokol nemůžete změnit, když kanál nebo jeho přidružené programy běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý vstupní protokol.
* Fakturuje se vám jenom v případě, že je kanál ve **spuštěném** stavu. Další informace najdete v [této](media-services-manage-live-encoder-enabled-channels.md#states) části.
* V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. 
* Ujistěte se, že máte koncový bod streamování, ze kterého chcete streamovat obsah ve stavu **spuštěno** .
* Přednastavení kódování používá pojem "Maximální Snímková frekvence" z 30 snímků. Takže pokud je vstupem 60fps/displeji i, vstupní snímky jsou vyřazeny/de prokládané do 30/29.97 fps. Pokud je vstupem 50fps/50i, vstupní snímky jsou vyřazeny/de-prokládané až 25 fps. Pokud má vstup 25 snímků/s, zůstane výstup 25 fps.
* Po dokončení nezapomeňte kanály zastavit. Pokud to neuděláte, bude fakturace pokračovat.

## <a name="known-issues"></a>Známé problémy
* Čas spuštění kanálu byl vylepšen na průměrně 2 minuty, ale v časech zvýšené poptávky může trvat až 20 minut.
* Obrázky SLAT by měly odpovídat omezením uvedeným [zde](media-services-manage-live-encoder-enabled-channels.md#default_slate). Pokud se pokusíte vytvořit kanál s výchozí hodnotou SLAT, která je větší než 1080, požadavek bude nakonec vydávat chybu.
* Znovu.... po skončení streamování nezapomeňte kanály zastavit. Pokud to neuděláte, bude fakturace pokračovat.

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-step"></a>Další krok

Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Doručování živě streamovaných událostí pomocí služby Azure Media Services](media-services-overview.md)

[Vytváření kanálů, které provádějí živé kódování z každé přenosové rychlosti na datový proud s adaptivní přenosovou rychlostí s portálem](media-services-portal-creating-live-encoder-enabled-channel.md)

[Vytváření kanálů, které provádějí živé kódování z každé přenosové rychlosti do datového proudu s adaptivní přenosovou rychlostí pomocí sady .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Správa kanálů pomocí REST API](/rest/api/media/operations/channel)

[Media Services koncepty](media-services-concepts.md)

[Azure Media Services fragmentované specifikace ingestování MP4 v reálném čase](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
