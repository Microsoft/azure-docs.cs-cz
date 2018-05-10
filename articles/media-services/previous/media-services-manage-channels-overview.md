---
title: Přehled živé streamování využívající Azure Media Services | Microsoft Docs
description: Toto téma poskytuje přehled živé streamování využívající Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 2d3219f6f2617fec941e308febb2e47efa184170
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="overview-of-live-streaming-using-azure-media-services"></a>Přehled živé streamování využívající Azure Media Services

> [!NOTE]
> Od 12 může 2018 za provozu kanály se už podporu datový proud RTP/MPEG-2 transport ingestování. Proveďte prosím migraci z RTP/MPEG-2 RTMP nebo fragmentovaných MP4 (technologie Smooth Streaming) ingestování protokoly.

## <a name="overview"></a>Přehled
Při doručování živě streamovaných událostí pomocí služby Azure Media Services se běžně podílejí následující komponenty:

* Kamera používaná k vysílání události.
* Kodér videa pro kódování v reálném čase, který převádí signály z kamery do datových proudů, které se odesílají do služby živého streamování.

    Volitelně několik živých, časově synchronizovaných kodérů. V případě důležitých živých událostí, které vyžadují velmi vysokou dostupnost a kvalitní uživatelský dojem, doporučujeme využívat redundantní kodéry v režimu aktivní/aktivní s časovou synchronizací, abyste dosáhli bezproblémového převzetí služeb při selhání bez ztráty dat.
* Služba živého streamování, která umožňuje následující:

  * ingestovat živý obsah pomocí různých protokolů pro živé streamování (například RTMP nebo technologie Smooth Streaming),
  * (volitelně) kódovat datový proud na datový proud s adaptivní přenosovou rychlostí
  * zobrazit náhled živého datového proudu,
  * zaznamenávat a ukládat ingestovaný obsah, abyste ho mohli streamovat později (video na vyžádání)
  * doručovat obsah prostřednictvím běžných streamovacích protokolů (například MPEG DASH, Smooth, HLS) přímo k vašim zákazníkům nebo do sítě pro doručování obsahu (CDN) pro další distribuci.

**Microsoft Azure Media Services** (AMS) nabízí možnost ingestovat, kódovat, zobrazovat náhled, ukládat a doručovat obsah vašeho živého streamování.

Při doručování obsahu zákazníkům je vaším cílem poskytování vysoce kvalitního videa pro různá zařízení v různých síťových podmínkách. Jak toho docílit, použijte kodéry pro kódovat datový proud na datový proud videa s více přenosovými rychlostmi (adaptivní přenosová rychlost).  Pokud chcete zajistit o streamování na různá zařízení, použijte [dynamické balení](media-services-dynamic-packaging-overview.md) ve službě Media Services a dynamicky znovu zabalte datový proud do různých protokolů. Služba Media Services podporuje doručování následujících technologií streamování s adaptivní přenosovou rychlostí: HTTP Live Streaming (HLS), technologie Smooth Streaming, MPEG DASH.

Ve službě Azure Media Services se o zpracování všech funkcí živého streamování včetně ingestování, formátování, DVR, zabezpečení, škálovatelnosti a redundance starají **kanály**, **programy** a **koncové body streamování**.

**Kanál** představuje cestu pro zpracování obsahu živého streamování. Kanál může přijímat živé vstupní datové proudy následujícími způsoby:

* Místní kodéru pro kódování v reálném čase odešle **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) s více přenosovými rychlostmi do kanálu, který je nakonfigurovaný na **průchozí** doručování. **Průchozí** doručování nastává, když ingestované datové proudy prochází **kanálem** bez dalšího zpracování. Můžete použít následující kodéry, které výstupu technologie Smooth Streaming více přenosovými rychlostmi: MediaExcel, Ateme, představte si komunikace, Envivio, Cisco a Elemental. Následující kodéry výstupu RTMP: Adobe Flash média Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek a čase transkodéry.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Místní kodér za provozu odešle datový proud s jednou přenosovou rychlostí do kanálu, který je povoleno provádět kódování v reálném čase pomocí služby Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Pro práci s kanály tohoto typu jsou známé následující kodéry s výstupu RTMP: Telestream Wirecast, FMLE. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

Od verze Media Services 2.10, když vytvoříte kanál, můžete zadat způsobem, který chcete použít pro kanál pro příjem vstupního datového proudu a zda chcete pro kanál provést kódování v reálném čase z datového proudu. Máte dvě možnosti:

* **Žádný** (průchozí) – zadejte tuto hodnotu, pokud budete chtít použít kodér místní za provozu, který bude výstupního datového proudu více přenosovými rychlostmi (průchozí datový proud). V takovém případě příchozího datového proudu předána do výstupu bez jakékoli kódování. Toto je chování kanál před vydáním verze 2.10.  
* **Standardní** – vyberte tuto hodnotu, pokud máte v úmyslu použít Media Services ke kódování živého datového proudu jednou přenosovou rychlostí do datového proudu více přenosovými rychlostmi. Tato metoda je vyplatí pro Rychlé škálování pro nepravidelným události. Mějte na paměti, že je fakturace dopad pro kódování v reálném čase a by měl pamatovat, že ponechat živého kanálu kódování ve stavu "Spuštění" bude platit poplatky fakturace.  Doporučuje se po dokončení náklady na velmi hodinové živé streamování události okamžitě zastavit spuštěný kanálů.

## <a name="comparison-of-channel-types"></a>Porovnání typů kanálu
Následující tabulka poskytuje vodítko k porovnání dvou kanál typy podporované ve službě Media Services

| Funkce | Průchozí kanál | Běžný kanál |
| --- | --- | --- |
| Jednou přenosovou rychlostí vstup je zakódován do více přenosových rychlostí v cloudu |Ne |Ano |
| Maximální rozlišení, počet vrstev |1080p, 8 vrstvy 60 + snímků za sekundu |720p, 6 vrstev, 30 snímků za sekundu |
| Vstupní protokoly |RTMP funkce Smooth Streaming |RTMP funkce Smooth Streaming |
| Cena |Najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Live Video" |Najdete v článku [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximální doba běhu |Nepřetržitě |8 hodin |
| Podpora pro vkládání slaty |Ne |Ano |
| Podpora pro signalizace ad |Ne |Ano |
| Průchozí CEA 608/708 titulky |Ano |Ano |
| Možnost obnovení z stručný míst v příspěvku informačního kanálu |Ano |Ne (kanál zahájíte slating po 6 + sekundách bez vstupní data) |
| Podpora pro vstupní GOPs neuniformní |Ano |Ne – vstup musí být vyřešeny 2 sekundu GOPs |
| Podpora pro vstup míra proměnné rámce |Ano |Ne – vstup je potřeba opravit obnovovací frekvence.<br/>Malé změny jsou například tolerovat během scény vysoké pohybu. Ale kodér nelze vyřadit do 10 snímků za sekundu. |
| Auto přístupnými kanálů při vstupu kanálu dojde ke ztrátě |Ne |Po 12 hodinách, pokud neexistuje žádné Program spuštěn |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů (průchozí)
Následující diagram znázorňuje hlavní části platformy AMS, které se podílejí na **průchozím** pracovním postupu.

![Živý pracovní postup](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Další informace najdete v článku o [práci s kanály, které přijímají živé streamování s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Práce s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services
Následující diagram znázorňuje hlavní část platformy AMS, které se podílejí na pracovním postupu živého streamování, ve kterém má kanál povolené kódování v reálném čase pomocí služby Media Services.

![Živý pracovní postup](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Popis kanálu a jeho souvisejících součástí
### <a name="channel"></a>Kanál
Ve službě Media Services [kanál](https://docs.microsoft.com/rest/api/media/operations/channel)s jsou zodpovědná za zpracování obsahu živého streamování. Kanál, který obsahuje vstupní koncový bod (ingestovanou adresu URL), pak poskytnete za provozu převaděč. Kanál přijímá živé vstupní datové proudy ze za provozu převaděč a zpřístupní se k vysílání datového proudu prostřednictvím jeden nebo více koncové body streamování. Kanály taky zadat koncový bod preview (URL náhledu), který používáte pro zobrazení náhledu a ověřit před další zpracování a doručení datového proudu.

Když vytvoříte kanál, mohou získat adresu URL ingestování a URL náhledu. Pokud chcete získat tyto adresy URL, kanál nemá být ve stavu spuštěna. Pokud jste připravení začít s nabízet data z provozu převaděč do kanálu, je nutné spustit kanál. Jakmile se převaděč za provozu spustí příjem dat, můžete zobrazit náhled datového proudu.

Každý účet Media Services může obsahovat více typů komunikačních kanálů, více programů a více koncové body streamování. Podle potřeb šířky pásma a zabezpečení může být vyhrazený StreamingEndpoint služby pro jeden nebo více kanálů. Všechny StreamingEndpoint můžete načítat z jakékoli kanálu.

### <a name="program"></a>Program
A [Program](https://docs.microsoft.com/rest/api/media/operations/program) vám umožňuje řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálů a programů se velmi podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.
Můžete určit počet hodin, které chcete uchovávat zaznamenaný obsah programu nastavením **ArchiveWindowLength** vlastnost. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje že maximální množství času klientů můžete prohledávat směrem zpět v čase od aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružený k assetu. Chcete-li publikovat program musíte vytvořit lokátor pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

## <a name="billing-implications"></a>Dopady na fakturaci
Kanál, který začíná fakturace, jakmile je přechodů mezi stavy "Spuštění" prostřednictvím rozhraní API.  

Následující tabulka ukazuje, jak kanál stavy mapovány fakturace stavů v portálu rozhraní API a Azure. Upozorňujeme, že jsou mírně liší rozhraní API a portál UX stavy Jakmile kanál, který je ve stavu "Spuštěná" prostřednictvím rozhraní API nebo ve stavu "Připraveno" nebo "Streaming" na webu Azure portal fakturace bude aktivní.

Chcete-li ukončit kanál z jste další fakturace, budete muset zastavte kanál prostřednictvím rozhraní API nebo na portálu Azure.
Jste zodpovědní za zastavení kanálů, když jste hotovi s kanálem. Nepodařilo se zastavit kanál způsobí trvalá fakturace.

> [!NOTE]
> Při práci s kanály, Standard, AMS automaticky přístupnými žádné kanálu, který je stále ve stavu "Spuštění" 12 hodin po vstupní kanál dojde ke ztrátě a neexistují žádné programy spuštěné. Ale bude stále se fakturuje době kanál byla ve stavu "Spuštění".
>
>

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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Azure Media Services fragmentovaných MP4 Live Ingestování specifikace](media-services-fmp4-live-ingest-overview.md)

[Práce s kanály, které mají povolené kódování v reálném službou Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)

[Kvóty a omezení](media-services-quotas-and-limitations.md).  

[Koncepty služby Media Services](media-services-concepts.md)
