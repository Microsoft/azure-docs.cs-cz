---
title: Přehled živého streamování
description: Tento článek obsahuje přehled živého streamování pomocí Azure Media Services V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: c82c646e4befef9d409bb8e59a442642913ada22
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641518"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování s Azure Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services vám umožní doručovat živé události zákazníkům v cloudu Azure. K streamování živých událostí pomocí Media Services budete potřebovat následující:  

- Fotoaparát, který se používá k zachycení živé události.<br/>V případě nápadů při instalaci se podívejte na [Nastavení jednoduchého a přenosného kormidelního videa událostí]( https://link.medium.com/KNTtiN6IeT).

    Pokud nemáte přístup k kameře, můžete k vygenerování živého datového kanálu z videosouboru použít nástroje, jako je [Wirecast](https://www.telestream.net/wirecast/overview.htm) .
- Živý kodér videa, který převede signály z fotoaparátu (nebo jiného zařízení, jako je notebook) do informačního kanálu příspěvků, který je odeslán do Media Services. Informační kanál příspěvku může zahrnovat signály týkající se reklamy, jako jsou například značky SCTE-35.<br/>Seznam doporučených kodérů živého streamování najdete v tématu [kodéry živého streamování](recommended-on-premises-live-encoders.md). Podívejte se také na tento blog: [živá streamovaná výroba pomocí OBS](https://link.medium.com/ttuwHpaJeT).
- Komponenty v Media Services, které umožňují ingestovat, zobrazovat náhled, zabalit, nahrávat, šifrovat a vysílat živou událost vašim zákazníkům nebo do sítě CDN pro další distribuci.

Pro zákazníky, kteří chtějí doručovat obsah do rozsáhlých internetových cílových skupin, doporučujeme povolit CDN pro [koncový bod streamování](streaming-endpoint-concept.md).

Tento článek poskytuje přehled a pokyny pro živé streamování pomocí Media Services a odkazy na další relevantní články.
 
> [!NOTE]
> Pomocí [Azure Portal](https://portal.azure.com/) můžete spravovat V3 [Live události](live-events-outputs-concept.md), zobrazit [prostředky](assets-concept.md)v3 a získat informace o přístupu k rozhraním API. Pro všechny ostatní úlohy správy (například transformace a úlohy) použijte [REST API](/rest/api/media/), [CLI](/cli/azure/ams)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging-and-delivery"></a>Dynamické balení a doručování

Pomocí Media Services můžete využít výhod [dynamického balení](dynamic-packaging-overview.md), které umožňuje zobrazit náhled a vysílání vašich živých streamů v [formátech MPEG pomlčky, HLS a Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z informačního kanálu příspěvku, který se posílá do služby. Vaši uživatelé můžou přehrát živý datový proud pomocí libovolných kompatibilních přehrávačů HLS, POMLČEK nebo Smooth Streaming. Ve webových nebo mobilních aplikacích můžete použít [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) k doručování datového proudu v libovolném z těchto protokolů.

## <a name="dynamic-encryption"></a>Dynamické šifrování

Dynamické šifrování vám umožní dynamicky šifrovat živý a na vyžádání obsahu pomocí AES-128 nebo kteréhokoli ze tří hlavních systémů DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine a Apple FairPlay. Media Services taky poskytuje službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) autorizovaným klientům. Další informace najdete v tématu [dynamické šifrování](content-protection-overview.md).

> [!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="dynamic-filtering"></a>Dynamické filtrování

Dynamické filtrování se používá k řízení počtu běhů, formátů, přenosových rychlostí a oken doby prezentace, které se odesílají přehrávačům. Další informace najdete v tématu [filtry a dynamické manifesty](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Typy živých událostí

[Živé události](/rest/api/media/liveevents) jsou zodpovědné za ingestování a zpracování aktivních informačních kanálů. Živá událost může být nastavená na *předávací* (místní živý kodér posílá datový proud s více přenosovými rychlostmi) nebo *živé kódování* (místní kodér Live Encoder posílá datový proud s jednou přenosovou rychlostí). Podrobnosti o živém streamování v Media Services V3 najdete v tématu [živé události a živé výstupy](live-events-outputs-concept.md).

### <a name="pass-through"></a>Průchod

![Diagram znázorňující, jak se ingestují a zpracovávají informační kanály videa a zvuku z předávací živé události.](./media/live-streaming/pass-through.svg)

Při použití předávací **živé události** se spoléháte na váš místní živý kodér a vygenerujete datový proud s více přenosovými rychlostmi a pošlete ho jako kanál příspěvků do živé události (pomocí RTMP nebo fragmentování vstupního protokolu-MP4). Živá událost pak provede přes příchozí streamy videa do dynamického balíčku (koncového bodu streamování) bez dalšího překódování. Taková předávací živá událost je optimalizovaná pro dlouhotrvající živé události nebo 24x365 lineární živé streamování. 

### <a name="live-encoding"></a>Kódování v reálném čase  

![Kódování v reálném čase](./media/live-streaming/live-encoding.svg)

Pokud používáte kódování cloudu s Media Services, nakonfigurujete svůj místní kodér Live Encoder tak, aby odesílal video s jednou přenosovou rychlostí (až do 32Mbps agregace) do živé události (pomocí RTMP nebo fragmentu vstupního protokolu-MP4). Živá událost překóduje datový proud příchozího přenosu dat s jednou přenosovou rychlostí na [více datových proudů](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) v různých rozlišeních a zvyšuje tak jejich doručování a zpřístupňuje je pro přehrávání přes standardní protokoly, jako jsou MPEG-pomlčky, Apple HTTP Live Streaming (HLS) a Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Živý přepis (Preview)

Živý přepis je funkce, kterou můžete použít s dynamickými událostmi, které jsou buď předávací, nebo živé kódování. Další informace najdete v tématu [živý přepis](live-transcription.md). Když je tato funkce povolená, služba použije funkci [řeči k textu](../../cognitive-services/speech-service/speech-to-text.md) Cognitive Services k přepisovat mluveného slova v příchozím zvukovém textu. Tento text je pak k dispozici pro doručování s videem a zvukem v protokolech MPEG-POMLČKy a HLS.

> [!NOTE]
> V současné době je živý přepis dostupný jako funkce ve verzi Preview v Západní USA 2.

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Abyste pochopili pracovní postup živého streamování v Media Services V3, musíte si nejdřív projít a porozumět následujícím koncepcím: 

- [Koncové body streamování](streaming-endpoint-concept.md)
- [Živé události a výstupy](live-events-outputs-concept.md)
- [Lokátory streamování](streaming-locators-concept.md)

### <a name="general-steps"></a>Obecné kroky

1. Ujistěte se, že je v účtu Media Services spuštěný **koncový bod streamování** (Origin). 
2. Vytvořte [živou událost](live-events-outputs-concept.md). <br/>Při vytváření události můžete zadat automatické spuštění. Alternativně můžete událost spustit, až budete připraveni ke spuštění streamování.<br/> Když je automatické spuštění nastavené na true, spustí se živá událost hned po vytvoření. Fakturace začne ihned po spuštění živé události. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události. Další informace najdete v tématu [stavy událostí Live a fakturace](live-event-states-billing.md).
3. Získejte adresy URL pro příjem a nakonfigurujte místní kodér tak, aby používal adresu URL k odeslání informačního kanálu příspěvku.<br/>Viz [Doporučené živé kodéry](recommended-on-premises-live-encoders.md).
4. Získejte adresu URL náhledu a použijte ji k ověření, že vstup z kodéru je skutečně přijatý.
5. Vytvoří nový objekt **assetu** . 

    Každý živý výstup je přidružen k assetu, který používá k nahrání videa do přidruženého kontejneru úložiště objektů BLOB v Azure. 
6. Vytvořte **živý výstup** a použijte název assetu, který jste vytvořili, aby bylo možné datový proud archivovat do assetu.

    Živé výstupy začínají při vytváření a při odstranění se zastaví. Když odstraníte živý výstup, neodstraníte základní Asset a obsah v assetu.
7. Vytvořte **Lokátor streamování** s [integrovanými typy zásad streamování](streaming-policy-concept.md).

    Chcete-li publikovat živý výstup, je nutné vytvořit Lokátor streamování pro přidružený Asset. 
8. Seznam cest na **lokátoru streamování** pro vrácení adres URL, které se mají použít (tyto adresy jsou deterministické).
9. Získejte název hostitele **koncového bodu streamování** (zdroje), ze kterého chcete streamovat.
10. Kombinací adresy URL z kroku 8 s názvem hostitele v kroku 9 získáte úplnou adresu URL.
11. Pokud chcete přestat dělat **živý událost** , musíte zastavit streamování události a odstranit **Lokátor streamování**.
12. Pokud jste dokončili streamování událostí a chcete dříve zřízené prostředky vyčistit, postupujte podle následujícího návodu.

    * Zastavte odesílání datového proudu z kodéru.
    * Zastaví živou událost. Jakmile se živá událost zastaví, neúčtují se žádné poplatky. Když bude potřeba kanál znovu spustit, bude mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
    * Koncový bod streamování můžete zastavit, pokud nechcete pokračovat v poskytování archivu živé události jako Stream na vyžádání. Pokud je živá událost v zastaveném stavu, neúčtují se žádné poplatky.

Asset, do kterého se živý výstup archivuje, se automaticky stal Assetem na vyžádání, když se odstraní živý výstup. Aby bylo možné zastavit živou událost, je nutné odstranit všechny živé výstupy. Pomocí volitelného příznaku [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) můžete automaticky odebrat živé výstupy při zastavení. 

> [!TIP]
> Viz [kurz živého streamování](stream-live-tutorial-with-api.md), článek prověřuje kód, který implementuje výše popsané kroky.

## <a name="other-important-articles"></a>Další důležité články

- [Doporučené kodéry pro kódování v reálném čase](recommended-on-premises-live-encoders.md)
- [Použití cloudového DVR](live-event-cloud-dvr.md)
- [Porovnání funkcí typů živých událostí](live-event-types-comparison.md)
- [Stavy a fakturace](live-event-states-billing.md)
- [Latence](live-event-latency.md)

## <a name="live-streaming-questions"></a>Dotazy živého streamování

Přečtěte si článek o [online streamování dotazů](questions-collection.md#live-streaming) .
