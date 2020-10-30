---
title: Přehled živého streamování pomocí Azure Media Services | Microsoft Docs
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
ms.openlocfilehash: f2e899a9d98d43f826bfa63e62458adf1601f071
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042992"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Přehled živého streamování pomocí Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

## <a name="overview"></a>Přehled

Při doručování událostí živého streamování pomocí Azure Media Services jsou obvykle zapojeny následující komponenty:

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

Pomocí Media Services můžete využít výhod [dynamického balení](media-services-dynamic-packaging-overview.md), což vám umožní vysílat živé streamy ve formátu MPEG pomlčky, HLS a Smooth Streaming z informačního kanálu příspěvků, který se posílá do služby. Vaši uživatelé můžou přehrát živý datový proud pomocí libovolných kompatibilních přehrávačů HLS, POMLČEK nebo Smooth Streaming. Ve webových nebo mobilních aplikacích můžete použít Azure Media Player k doručování datového proudu v libovolném z těchto protokolů.

> [!NOTE]
> Od 12. května 2018 už živé kanály nebudou podporovat protokol ingestování přenosového streamu RTP/MPEG-2. Migrujte prosím z protokolu RTP/MPEG-2 na RTMP nebo fragmentujte protokoly ingesta MP4 (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Koncové body streamování, kanály, programy

Ve službě Azure Media Services se o zpracování všech funkcí živého streamování včetně ingestování, formátování, DVR, zabezpečení, škálovatelnosti a redundance starají **kanály** , **programy** a **koncové body streamování** .

**Kanál** představuje cestu pro zpracování obsahu živého streamování. Kanál může přijímat živé vstupní datové proudy následujícími způsoby:

* Místní kodéru pro kódování v reálném čase odešle **RTMP** nebo **technologie Smooth Streaming** (fragmentovaný soubor MP4) s více přenosovými rychlostmi do kanálu, který je nakonfigurovaný na **průchozí** doručování. **Průchozí** doručování nastává, když ingestované datové proudy prochází **kanálem** bez dalšího zpracování. Můžete použít následující živé kodéry, které mají výstup s více přenosovými rychlostmi Smooth Streaming: MediaExcel, Ateme, představovat komunikaci, Envivio, Cisco a prvky. Následující kodéry pro výstup v reálném čase: Stream Wirecast, Haivision, Teradek TriCaster.  Kodér pro kódování v reálném čase může také odesílat datový proud s jednou přenosovou rychlostí do kanálu, který nemá povolené kódování v reálném čase, ale tato konfigurace se nedoporučuje. Služba Media Services doručí datový proud zákazníkům na vyžádání.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě streamovat při pořádání několika událostí po delší dobu, když jste už investovali do místních kodérů. Viz podrobnosti o [cenách](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* On-premises Live Encoder odesílá datový proud s jednou přenosovou rychlostí do kanálu, který má povolené kódování v reálném čase, pomocí Media Services v jednom z následujících formátů: RTMP nebo Smooth Streaming (fragmentovaný MP4). Pro práci s kanály tohoto typu je známo, že tyto živé kodéry s využitím výstupu RTMP: Wirecast Stream. Kanál potom provede kódování v reálném čase pro příchozí datový proud s jednou přenosovou rychlostí v reálném čase na datový proud videa s více přenosovými rychlostmi (adaptivní). Služba Media Services doručí datový proud zákazníkům na vyžádání.

Počínaje verzí Media Services 2,10 můžete při vytváření kanálu určit, jak chcete, aby kanál přijímal vstupní datový proud a zda chcete, aby kanál prováděl živé kódování vašeho datového proudu. Máte dvě možnosti:

* **None** (pass-through) – tuto hodnotu zadejte, pokud plánujete použít místní kodér Live Encoder, který bude výstupem datového proudu s více přenosovými rychlostmi (předávací proud). V tomto případě příchozí datový proud předaný do výstupu bez kódování. Toto chování kanálu před vydáním verze 2,10.  
* **Standard** – tuto hodnotu vyberte, pokud chcete použít Media Services ke kódování živého datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi. Tato metoda je výhodnější pro rychlé škálování pro nečasté události. Mějte na paměti, že pro živé kódování je potřeba fakturace a že byste měli mít na paměti, že když zachováte živý kanál kódování ve stavu spuštěno, budou se účtovat poplatky za účtování.  Doporučuje se, abyste ihned zastavili spuštěné kanály po dokončení události živého streamování, abyste se vyhnuli dodatečným hodinovým poplatkům.

## <a name="comparison-of-channel-types"></a>Porovnání typů kanálů

Následující tabulka poskytuje návod pro porovnání dvou typů kanálů podporovaných v Media Services

| Doporučené | Předávací kanál | Kanál Standard |
| --- | --- | --- |
| Vstup s jednou přenosovou rychlostí je kódovaný do více přenosových rychlostí v cloudu. |No |Yes |
| Maximální rozlišení, počet vrstev |1080p, 8 vrstev, 60 + fps |720p, 6 vrstev, 30 snímků za sekundu |
| Vstupní protokoly |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Cena |Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) a klikněte na kartu živé video. |Zobrazit [stránku s cenami](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximální doba běhu |non |8 hodin |
| Podpora pro vložení SLAT |No |Yes |
| Podpora pro signalizaci AD |No |Yes |
| Předávací titulky CEA 608/708 |Yes |Yes |
| Podpora pro neuniformní vstupní GOPs |Yes |Ne – vstup musí být pevně 2sec GOPs. |
| Podpora pro vstup variabilní frekvence snímků |Yes |Ne – vstupní hodnota musí být pevná snímková frekvence.<br/>Vedlejší variace jsou tolerovány, například při vysokém pohybu scény. Kodér ale nemůže přetahovat na 10 snímků za sekundu. |
| Automatické shutoff kanálů při ztrátě vstupního kanálu |No |V případě, že není spuštěn žádný program, po 12 hodinách. |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů (průchozí)

Následující diagram znázorňuje hlavní části platformy AMS, které se podílejí na **průchozím** pracovním postupu.

![Diagram, který zobrazuje hlavní části platformy A M S pro pracovní postup předávacího procesu.](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Další informace najdete v článku o [práci s kanály, které přijímají živé streamování s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Práce s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services

Následující diagram znázorňuje hlavní část platformy AMS, které se podílejí na pracovním postupu živého streamování, ve kterém má kanál povolené kódování v reálném čase pomocí služby Media Services.

![Živý pracovní postup](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Popis kanálu a jeho souvisejících komponent

### <a name="channel"></a>Kanál

V Media Services [kanál](/rest/api/media/operations/channel)s zodpovídá za zpracování obsahu živého streamování. Kanál poskytuje vstupní koncový bod (adresa URL pro příjem), který pak poskytnete pro živý překódování. Kanál přijímá živé vstupní proudy z živého překódování a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více starají. Kanály také poskytují koncový bod verze Preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením.

Při vytváření kanálu můžete získat adresu URL pro ingestování a adresu URL náhledu. Aby se tyto adresy URL dostaly, kanál se nemusí nacházet ve stavu spuštěno. Až budete připraveni začít nahrávat data z živého přesílaného do kanálu, je nutné spustit kanál. Jakmile živý převedený kód začne ingestovat data, můžete zobrazit náhled streamu.

Každý účet Media Services může obsahovat více kanálů, více programů a několik starají. V závislosti na potřebách šířky pásma a zabezpečení může být služba StreamingEndpoint vyhrazená pro jeden nebo více kanálů. Libovolný StreamingEndpoint může vyžádat z libovolného kanálu.

Při vytváření kanálu můžete zadat povolené IP adresy v jednom z následujících formátů: IpV4 adresa se 4 čísly, rozsah adres CIDR.

### <a name="program"></a>Program
[Program](/rest/api/media/operations/program) umožňuje řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují programy. Vztah kanálů a programů se velmi podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.
Nastavením vlastnosti **ArchiveWindowLength** můžete zadat počet hodin, po které chcete zachovávat zaznamenaný obsah pro program. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin.

ArchiveWindowLength také určuje maximální dobu, po kterou mohou klienti vyhledávat v čase z aktuální živé pozice. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho mohou klientské manifesty růst.

Každý program je přidružený k assetu. Pro publikování programu musíte vytvořit Lokátor pro přidružený Asset. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

## <a name="billing-implications"></a>Důsledky fakturace
Kanál začíná po přechodu do stavu "spuštěno" prostřednictvím rozhraní API.  

Následující tabulka ukazuje, jak se stavy kanálu mapují na fakturační stavy v rozhraní API a Azure Portal. Všimněte si, že se stavy v uživatelském rozhraní API a portálu mírně liší. Jakmile je kanál ve stavu spuštěno prostřednictvím rozhraní API nebo ve stavu "připraveno" nebo "Streaming" ve Azure Portal, bude faktura aktivní.

Chcete-li zastavit vyúčtování kanálu, je nutné zastavit kanál přes rozhraní API nebo v Azure Portal.
Zodpovídáte za zastavování kanálů po dokončení práce s kanálem. Při zastavení kanálu dojde k pokračování fakturace.

> [!NOTE]
> Když pracujete se standardními kanály, AMS automaticky shutoff všechny kanály, které jsou pořád ve stavu spuštěno, 12 hodin po ztrátě vstupního kanálu a neběží žádné programy. Stále se ale bude účtovat čas, kdy byl kanál ve stavu spuštěno.
>
>

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Stavy kanálu a jejich mapování na režim fakturace
Aktuální stav kanálu. Mezi možné hodnoty patří:

* **Zastaveno** . Toto je počáteční stav kanálu po jeho vytvoření (Pokud jste na portálu nevybrali Automatické spuštění). V tomto stavu nedochází k žádnému fakturaci. V tomto stavu je možné aktualizovat vlastnosti kanálu, ale streamování není povoleno.
* **Spouští** se. Kanál se spouští. V tomto stavu nedochází k žádnému fakturaci. V tomto stavu nejsou povolené žádné aktualizace ani streamování. Pokud dojde k chybě, kanál se vrátí do stavu Zastaveno.
* **Spuštěno** . Kanál dokáže zpracovávat živé streamy. Nyní je využití fakturace. Aby se zabránilo dalšímu fakturaci, je nutné kanál zastavit.
* **Zastavuje** se. Kanál se zastavuje. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povolené žádné aktualizace ani streamování.
* **Probíhá odstraňování** . Kanál se odstraňuje. V tomto přechodném stavu nedochází k žádnému účtování. V tomto stavu nejsou povolené žádné aktualizace ani streamování.

Následující tabulka ukazuje, jak se stavy kanálu mapují na režim fakturace.

| Stav kanálu | Indikátory uživatelského rozhraní portálu | Účtuje se fakturace? |
| --- | --- | --- |
| Spouštění |Spouštění |Ne (přechodný stav) |
| Spuštěno |Připraveno (žádné spuštěné programy)<br/>nebo<br/>Streamování (aspoň jeden spuštěný program) |ANO |
| Zastavování |Zastavování |Ne (přechodný stav) |
| Zastaveno |Zastaveno |No |

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Související témata
[Azure Media Services fragmentované specifikace ingestování MP4 v reálném čase](../media-services-fmp4-live-ingest-overview.md)

[Práce s kanály, které jsou povolené k provádění Live Encoding s Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md)

[Kvóty a omezení](media-services-quotas-and-limitations.md).  

[Media Services koncepty](media-services-concepts.md)
