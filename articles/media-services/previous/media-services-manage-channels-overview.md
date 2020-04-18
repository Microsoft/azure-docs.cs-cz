---
title: Přehled živého streamování pomocí Mediálníslužby Azure | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled živého streamování pomocí Microsoft Azure Media Services.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f875b4a5c4f1322f4a992dc3738ab1ce6431149d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641129"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Přehled živého streamování pomocí mediálních služeb

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Přehled

Při doručování událostí živého streamování pomocí Služby Azure Media Services se běžně používají následující součásti:

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

Pomocí služby Media Services můžete využít [dynamické balení](media-services-dynamic-packaging-overview.md), které umožňuje vysílat živé přenosy ve formátech MPEG DASH, HLS a Smooth Streaming z kanálu příspěvků odesílaného službě. Vaši diváci mohou přehrávat živý přenos pomocí libovolných přehrávačů kompatibilních s HLS, DASH nebo Smooth Streaming. Azure Media Player můžete použít ve svých webových nebo mobilních aplikacích k doručování datového proudu v některém z těchto protokolů.

> [!NOTE]
> května 2018 již živé kanály nebudou podporovat protokol ingestestování datového proudu rtp/MPEG-2. Migrujte z protokolů s ingestováním RTP/MPEG-2 do RTMP nebo fragmentovaných protokolů s ingestováním MP4 (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Streamování koncových bodů, kanálů, programů

Ve službě Azure Media Services se o zpracování všech funkcí živého streamování včetně ingestování, formátování, DVR, zabezpečení, škálovatelnosti a redundance starají **kanály**, **programy** a **koncové body streamování**.

**Kanál** představuje cestu pro zpracování obsahu živého streamování. Kanál může přijímat živé vstupní datové proudy následujícími způsoby:

* Místní kodéru pro kódování v reálném čase odešle **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) s více přenosovými rychlostmi do kanálu, který je nakonfigurovaný na **průchozí** doručování. **Průchozí** doručování nastává, když ingestované datové proudy prochází **kanálem** bez dalšího zpracování. Můžete použít následující živé kodéry, které vypracovává plynulé streamování s více přenosovými rychlostmi: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco a Elemental. Následující živé kodéry výstup RTMP: Telestream Wirecast, Haivision, Teradek transcoders.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Místní živý kodér odešle datový proud s jedním datovým proudem do kanálu, který je povolen k provádění živého kódování se službou Media Services v jednom z následujících formátů: RTMP nebo Plynulé streamování (fragmentované MP4). Následující živé kodéry s výstupem RTMP jsou známy pro práci s kanály tohoto typu: Telestream Wirecast. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

Počínaje vydáním služby Media Services 2.10 můžete při vytváření kanálu určit, jakým způsobem má kanál přijímat vstupní datový proud a zda chcete, aby kanál prováděl živé kódování datového proudu. Máte dvě možnosti:

* **Žádný** (předávací) – Zadejte tuto hodnotu, pokud máte v plánu použít místní živý kodér, který bude výstup vícedatový datový proud (předávací datový proud). V tomto případě příchozí datový proud prošel do výstupu bez kódování. Toto je chování kanálu před vydáním 2.10.  
* **Standardní** – Tuto hodnotu zvolte, pokud chcete pomocí mediálních služeb zakódovat živý datový proud s přenosovým tokem do datového proudu s více přenosovou rychlostí. Tato metoda je ekonomičtější pro rychlé škálování pro řídké události. Uvědomte si, že existuje dopad na fakturaci pro živé kódování a měli byste si uvědomit, že opuštění kanálu živého kódování ve stavu "Spuštěno" bude účtovat fakturační poplatky.  Doporučujeme okamžitě zastavit spuštěné kanály po dokončení živého streamování, abyste se vyhnuli dalším hodinovým poplatkům.

## <a name="comparison-of-channel-types"></a>Porovnání typů kanálů

Následující tabulka obsahuje návod k porovnání dvou typů kanálů podporovaných ve službě Media Services

| Funkce | Průchozí kanál | Standardní kanál |
| --- | --- | --- |
| Vstup s jedním přenosovým tokem je v cloudu zakódován do více přenosových rychlostí |Ne |Ano |
| Maximální rozlišení, počet vrstev |1080p, 8 vrstev, 60+fps |720p, 6 vrstev, 30 fps |
| Vstupní protokoly |RTMP, plynulé streamování |RTMP, plynulé streamování |
| Price |Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na záložku "Živé video" |Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximální doba běhu |24x7 |8 hodin |
| Podpora pro vkládání břidlic |Ne |Ano |
| Podpora signalizace reklamy |Ne |Ano |
| Průchozí titulky CEA 608/708 |Ano |Ano |
| Podpora nejednotných vstupních žvatláků |Ano |Ne – vstup musí být opraven 2sec GOPs |
| Podpora pro vstup s proměnnou snímkovou frekvencí |Ano |Ne – vstup musí být pevný kmitočet snímků.<br/>Drobné variace jsou tolerovány, například při vysokých pohybových scénách. Kodér však nemůže klesnout na 10 snímků za sekundu. |
| Automatické vypnutí kanálů při ztrátě vstupního informačního kanálu |Ne |Po 12 hodinách, pokud není spuštěn žádný program |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů (průchozí)

Následující diagram znázorňuje hlavní části platformy AMS, které se podílejí na **průchozím** pracovním postupu.

![Živý pracovní postup](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Další informace najdete v článku o [práci s kanály, které přijímají živé streamování s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Práce s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services

Následující diagram znázorňuje hlavní část platformy AMS, které se podílejí na pracovním postupu živého streamování, ve kterém má kanál povolené kódování v reálném čase pomocí služby Media Services.

![Živý pracovní postup](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Popis kanálu a jeho souvisejících součástí

### <a name="channel"></a>Channel

Ve službě Media Services jsou [kanály](https://docs.microsoft.com/rest/api/media/operations/channel)s odpovědné za zpracování živého streamování obsahu. Kanál poskytuje vstupní koncový bod (ingestovat URL), který pak poskytnout živé transkodéru. Kanál přijímá živé vstupní proudy z živého transkodéru a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více StreamingEndpoints. Kanály také poskytují koncový bod náhledu (adresu URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL ingestování a adresu URL náhledu. Chcete-li získat tyto adresy URL, kanál nemusí být ve stavu spuštění. Když jste připraveni začít tlačit data z živého transkodéru do kanálu, kanál musí být spuštěn. Jakmile živý transkodér začne příjem dat, můžete zobrazit náhled datového proudu.

Každý účet media services může obsahovat více kanálů, více programů a více StreamingEndpoints. V závislosti na šířce pásma a potřebách zabezpečení mohou být služby StreamingEndpoint vyhrazeny pro jeden nebo více kanálů. Všechny StreamingEndpoint můžete vytáhnout z libovolného kanálu.

Při vytváření kanálu můžete určit povolené IP adresy v jednom z následujících formátů: IpV4 adresa se 4 čísly, rozsah adres CIDR.

### <a name="program"></a>Program
[Program](https://docs.microsoft.com/rest/api/media/operations/program) umožňuje řídit publikování a ukládání segmentů v živém přenosu. Kanály spravují programy. Vztah kanálů a programů se velmi podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.
Počet hodin, po které chcete zachovat zaznamenaný obsah programu, můžete určit nastavením vlastnosti **ArchiveWindowLength.** Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje maximální množství času, které mohou klienti hledat zpět v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho může růst manifestů klienta.

Každý program je přidružený k assetu. Chcete-li publikovat program, musíte vytvořit lokátor pro přidružený datový zdroj. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

## <a name="billing-implications"></a>Důsledky fakturace
Kanál začne fakturace, jakmile je stav přechody na "Spuštěno" přes rozhraní API.  

Následující tabulka ukazuje, jak se stavy kanálu mapují na fakturační stavy v rozhraní API a na webu Azure Portal. Všimněte si, že stavy se mírně liší mezi rozhraní API a portálu UX. Jakmile je kanál ve stavu "Spuštěno" prostřednictvím rozhraní API nebo ve stavu "Připraveno" nebo "Streamování" na webu Azure Portal, fakturace bude aktivní.

Chcete-li kanálu zabránit v dalším účtování, musíte kanál zastavit prostřednictvím rozhraní API nebo na webu Azure Portal.
Jste zodpovědní za zastavení kanálů po dokončení kanálu. Pokud kanál nezastavíte, bude fakturace pokračovat.

> [!NOTE]
> Při práci se standardními kanály ams automaticky vypne jakýkoli kanál, který je stále ve stavu "Spuštěno" 12 hodin po ztrátě vstupního kanálu a nejsou spuštěny žádné programy. Bude vám však stále účtována doba, po kterou byl kanál ve stavu Spuštěno.
>
>

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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Specifikace fragmentace živého ingestu služby Azure Media Services](../media-services-fmp4-live-ingest-overview.md)

[Práce s kanály, které mají povoleno živé kódování pomocí Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)

[Kvóty a omezení](media-services-quotas-and-limitations.md).  

[Koncepty mediálních služeb](media-services-concepts.md)
