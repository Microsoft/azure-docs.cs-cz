---
title: Přehled živého streamování využívajícího službu Azure Media Services | Dokumentace Microsoftu
description: Toto téma s přehledem živého streamování využívajícího službu Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 318ec5a4f9aceaf02048768cbf630eff08feff6b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998004"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Přehled živého streamování využívajícího službu Media Services

> [!NOTE]
> Od 12. května 2018, živé kanály budou nadále podporu přenosového streamu RTP/MPEG-2 protokol ingestování. Migrujte prosím z RTP/MPEG-2 na RTMP nebo MP4 s fragmentací (Smooth Streaming) ingestovací protokoly.

## <a name="overview"></a>Přehled

Při doručování živě streamovaných událostí pomocí Azure Media Services se běžně podílejí následující komponenty:

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

Při doručování obsahu zákazníkům je vaším cílem poskytování vysoce kvalitního videa pro různá zařízení v různých síťových podmínkách. Za tím účelem můžete kodéry překódujte svůj datový proud na datový proud videa s více přenosovými rychlostmi (adaptivní přenosová rychlost).  Pokud chcete zajistit o streamování na různá zařízení, použijte [dynamické balení](media-services-dynamic-packaging-overview.md) ve službě Media Services a dynamicky znovu zabalte datový proud do různých protokolů. Služba Media Services podporuje doručování následujících adaptivní přenosové rychlosti streamování technologií: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Ve službě Azure Media Services se o zpracování všech funkcí živého streamování včetně ingestování, formátování, DVR, zabezpečení, škálovatelnosti a redundance starají **kanály**, **programy** a **koncové body streamování**.

**Kanál** představuje cestu pro zpracování obsahu živého streamování. Kanál může přijímat živé vstupní datové proudy následujícími způsoby:

* Místní kodéru pro kódování v reálném čase odešle **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) s více přenosovými rychlostmi do kanálu, který je nakonfigurovaný na **průchozí** doručování. **Průchozí** doručování nastává, když ingestované datové proudy prochází **kanálem** bez dalšího zpracování. Následující kodéry, které výstupu technologie Smooth Streaming s více přenosovými rychlostmi můžete použít: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco a Elemental. Následující kodéry výstupu RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek a Transkodéry tricaster.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí do kanálu, který má povolené provádět živé kódování pomocí Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Následující kodéry s výstupem RTMP se ví, pracovat s kanály tohoto typu: Telestream Wirecast, FMLE. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

Od verze Media Services 2.10, když vytvoříte kanál, můžete určit, jakým způsobem chcete, aby pro váš kanál pro příjem vstupního datového proudu a určuje, jestli chcete pro kanál provádět živé kódování datového proudu. Máte dvě možnosti:

* **Žádný** (průchozí) – zadejte tuto hodnotu, pokud budete chtít použít místní kodér služby live Encoding které bude výstupního datového proudu s více přenosovými rychlostmi (průchozí datový proud). V takovém případě příchozího datového proudu předává do výstup bez jakékoli kódování. Toto je chování kanál před 2.10 verzí.  
* **Standardní** – tuto hodnotu zvolte, pokud máte v plánu používat Media Services ke kódování živého datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi. Tato metoda je úspornější pro vertikální navýšení kapacity rychle pro úlohy s řídkým události. Mějte na paměti, že fakturace dopad kódování v reálném čase a měli pamatovat opuštění živého kódování kanálu ve stavu "Spuštěno" budou účtovat poplatky.  Doporučuje se po dokončení se vyhnout poplatkům za velmi hodinové živě streamované události okamžitě zastavit spuštěné kanálů.

## <a name="comparison-of-channel-types"></a>Porovnání typů kanálů

Následující tabulka poskytuje návod k porovnání dvou typů kanálů, které jsou podporované ve službě Media Services

| Funkce | Průchozí kanál | Běžný kanál |
| --- | --- | --- |
| Vstup s jednou přenosovou rychlostí je zakódován do více přenosových rychlostí v cloudu |Ne |Ano |
| Maximální rozlišení, počet vrstev |1080p, 8 vrstvy, více než 60 snímků za sekundu |720p, 6 vrstvy, 30 snímků za sekundu |
| Vstupní protokoly |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Cena |Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu "Živé Video" |Zobrazit [stránce s cenami](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximální doba běhu |Nepřetržitě |8 hodin |
| Podpora vkládaní slaty |Ne |Ano |
| Podpora pro signalizaci ad |Ne |Ano |
| Předávací titulky CEA 608/708 |Ano |Ano |
| Podpora pro vstupní GOPs nerovnoměrné |Ano |Ne – vstup musí být nastaven 2 sekundu GOPs |
| Podpora pro vstup míra proměnné rámce |Ano |Ne – vstup musí být stanovena snímkovou frekvenci.<br/>Malé změny jsou například tolerovat při vysoké pohybu scény. Ale kodér nelze umístit do 10 snímků za sekundu. |
| Dojde ke ztrátě Auto přístupnými kanálů při zadání informačního kanálu |Ne |Po 12 hodinách, pokud není spuštěn žádný Program |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů (průchozí)

Následující diagram znázorňuje hlavní části platformy AMS, které se podílejí na **průchozím** pracovním postupu.

![Živý pracovní postup](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Další informace najdete v článku o [práci s kanály, které přijímají živé streamování s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Práce s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services

Následující diagram znázorňuje hlavní část platformy AMS, které se podílejí na pracovním postupu živého streamování, ve kterém má kanál povolené kódování v reálném čase pomocí služby Media Services.

![Živý pracovní postup](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Popis kanálu a jeho souvisejících součástí.

### <a name="channel"></a>Kanál

Ve službě Media Services [kanál](https://docs.microsoft.com/rest/api/media/operations/channel)s zodpovídají za zpracování obsahu živého streamování. Kanál obsahuje vstupní koncový bod (adresa URL ingestu), pak poskytnete živé převaděč. Kanál obdrží živé vstupní datové proudy z živého převaděč a zpřístupňuje je prostřednictvím jednoho nebo více koncové body streamování. Kanály také poskytuje koncový bod ve verzi preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL ingestování a adresu URL ve verzi preview. Pokud chcete získat tyto adresy URL, nemá kanál ve spuštěném stavu. Pokud jste připravení začít zapisovat data z živého převaděč do kanálu, musí se spustit kanál. Po spuštění ingestovat data živého převaděč můžete zobrazit náhled datového proudu.

Každý účet Media Services může obsahovat několik kanálů, více programů a více koncové body streamování. V závislosti na požadavcích šířku pásma a zabezpečení služby StreamingEndpoint může být vyhrazen pro jeden nebo více kanálů. Žádné StreamingEndpoint můžete načítat jakémkoli jiném kanálu.

Při vytváření kanálu, můžete zadat povolené IP adresy v jednom z následujících formátů: Adresu IpV4 s 4 číslice, rozsah adres CIDR.

### <a name="program"></a>Program
A [Program](https://docs.microsoft.com/rest/api/media/operations/program) vám umožňuje řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálů a programů se velmi podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.
Můžete určit počet hodin, které chcete uchovávat zaznamenaný obsah programu nastavením **ArchiveWindowLength** vlastnost. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje, že maximální množství času klientů můžete posunout zpátky v čase od aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každý program je přidružený k assetu. Program publikovat, musíte vytvořit lokátor pro přidružený asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

## <a name="billing-implications"></a>Vliv na fakturaci
Kanál začne fakturace, jakmile je přechodů mezi stavy na "Spuštěno" prostřednictvím rozhraní API.  

Následující tabulka uvádí přiřazení stavů kanálu stavům fakturace na portálu Azure a rozhraní API. Mějte na paměti, stavy se mírně liší mezi rozhraním API a portál uživatelské prostředí Jakmile je kanál ve stavu "Spuštěna" prostřednictvím rozhraní API nebo ve stavu "Připraveno" nebo "Streaming" na webu Azure Portal bude aktivováno fakturace.

Pokud chcete zastavit kanál z můžete dále fakturace, je nutné zastavit kanál prostřednictvím rozhraní API nebo na webu Azure Portal.
Zodpovídáte za ukončení kanálů, jakmile budete hotovi s kanálem. Nepodařilo se zastavit kanál způsobí trvalé fakturace.

> [!NOTE]
> Při práci s kanály Standard, AMS automaticky přístupnými jakémkoli jiném kanálu, který je stále ve stavu "Spuštěno" po ztratí vstupní kanál a nejsou k dispozici žádné programy spuštěné 12 hodin. Ale můžete pořád účtovat čas, kdy kanál byl ve stavu "Spuštěno".
>
>

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

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Specifikace Ingestování fragmentovaného MP4 za služby Azure Media Services](media-services-fmp4-live-ingest-overview.md)

[Práce s kanály, které mají povolené kódování v reálném pomocí Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)

[Kvóty a omezení](media-services-quotas-and-limitations.md).  

[Media Services Concepts](media-services-concepts.md)
