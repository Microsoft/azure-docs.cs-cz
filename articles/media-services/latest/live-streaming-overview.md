---
title: Přehled živého streamování pomocí Mediální služby Azure v3 | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled živého streamování pomocí Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068024"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování pomocí Mediální služby Azure v3

Azure Media Services vám umožňuje doručovat živé události svým zákazníkům v cloudu Azure. Chcete-li streamovat živé události pomocí služby Media Services, potřebujete následující:  

- Kamera, která se používá k zachycení živé události.<br/>Pro nastavení nápady, podívejte se na [jednoduché a přenosné události video zařízení nastavení]( https://link.medium.com/KNTtiN6IeT).

    Pokud nemáte přístup ke kameře, nástroje, jako je [Telestream Wirecast,](https://www.telestream.net/wirecast/overview.htm) mohou být použity ke generování živého přenosu ze souboru videa.
- Kodér živého videa, který převádí signály z kamery (nebo jiného zařízení, například přenosného počítače) na informační kanál příspěvků odeslaný do služby Media Services. Příspěvek může obsahovat signály související s reklamou, jako jsou značky SCTE-35.<br/>Seznam doporučených kodérů živého streamování naleznete v tématu [živé streamování kodéry](recommended-on-premises-live-encoders.md). Také, podívejte se na tento blog: [Live streaming produkce s OBS](https://link.medium.com/ttuwHpaJeT).
- Součásti ve službě Media Services, které umožňují ingestovat, zobrazit náhled, zabalit, zaznamenat, zašifrovat a vysílat živou událost zákazníkům nebo cdn pro další distribuci.

Tento článek poskytuje přehled a pokyny pro živé vysílání s Media Services a odkazy na další relevantní články.
 
> [!NOTE]
> [Na portálu Azure](https://portal.azure.com/) můžete spravovat živé [události](live-events-outputs-concept.md)v3 , zobrazit [datové zdroje](assets-concept.md)v3 , získat informace o přístupu k virtuálním i matům. Pro všechny ostatní úlohy správy (například Transformace a úlohy) použijte [rozhraní REST API](https://docs.microsoft.com/rest/api/media/), ROZHRANÍ [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging"></a>Dynamické balení

Pomocí služby Media Services můžete využít výhod [dynamického balení](dynamic-packaging-overview.md), které umožňuje zobrazit náhled a vysílat živé přenosy ve [formátech MPEG DASH, HLS a Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z kanálu příspěvků odesílaného službě. Vaši diváci mohou přehrávat živý přenos pomocí libovolných přehrávačů kompatibilních s HLS, DASH nebo Smooth Streaming. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) můžete použít ve svých webových nebo mobilních aplikacích k doručování datového proudu v některém z těchto protokolů.

## <a name="dynamic-encryption"></a>Dynamické šifrování

Dynamické šifrování umožňuje dynamicky šifrovat živý obsah nebo obsah na vyžádání pomocí AES-128 nebo některého ze tří hlavních systémů správy digitálních práv (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Mediální služby také poskytují službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) oprávněným klientům. Další informace naleznete [v tématu Dynamické šifrování](content-protection-overview.md).

> [!NOTE]
> Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="dynamic-manifest"></a>Dynamický manifest

Dynamické filtrování se používá k řízení počtu stop, formátů, přenosových rychlostí a časových oken prezentace, které jsou odesílány přehrávačům. Další informace naleznete v [tématu filtry a dynamické manifesty](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Typy živých událostí

[Živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za ingestování a zpracování informačních kanálů živého videa. Živou událost lze nastavit buď na *předávací* (místní živý kodér odešle datový proud s více datovým tokem) nebo *živé kódování* (místní živý kodér odešle jeden datový proud datového toku). Podrobnosti o živém vysílání ve službě Media Services v3 naleznete v tématu [Živé události a živé výstupy](live-events-outputs-concept.md).

### <a name="pass-through"></a>Průchod

![Průchozí](./media/live-streaming/pass-through.svg)

Při použití předávací **živé události**se spoléháte na místní živý kodér, který vygeneruje datový proud videa s více přenosovou rychlostí a odešle te jako příspěvek do živé události (pomocí vstupního protokolu RTMP nebo fragmentovaného MP4). Živá událost pak nese prostřednictvím příchozí video proudy do dynamického packager (Streaming Endpoint) bez dalšího překódování. Taková předávací živá událost je optimalizována pro dlouhotrvající živé události nebo lineární živé vysílání 24x365. 

### <a name="live-encoding"></a>Kódování v reálném čase  

![Kódování v reálném čase](./media/live-streaming/live-encoding.svg)

Při použití kódování cloudu pomocí služby Media Services byste nakonfigurovali místní živý kodér tak, aby odesílal jedno video o přenosové rychlosti jako kanál příspěvku (až 32 Mbit/s) do živé události (pomocí vstupního protokolu RTMP nebo fragmentovaného MP4). Živá událost překóduje příchozí datový tok do [více datových proudů datového toku](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) v různých rozlišeních, aby se zlepšilo doručování, a zpřístupní je pro doručování pro přehrávací zařízení prostřednictvím standardních protokolů, jako je MPEG-DASH, Apple HTTP Live Streaming (HLS) a Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Živý přepis (náhled)

Živý přepis je funkce, kterou můžete použít s živými událostmi, které jsou buď předávací, nebo živé kódování. Další informace naleznete v [tématu live transcription](live-transcription.md). Pokud je tato funkce povolena, služba používá funkci [převodu řeči na text](../../cognitive-services/speech-service/speech-to-text.md) služby Cognitive Services k přepisu mluvených slov v příchozím zvuku do textu. Tento text je pak k dispozici pro doručení spolu s video a audio v MPEG-DASH a HLS protokoly.

> [!NOTE]
> V současné době je živý přepis k dispozici jako funkce náhledu v západní USA 2.

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Chcete-li porozumět pracovnímu postupu živého streamování ve službě Media Services v3, musíte nejprve zkontrolovat a pochopit následující koncepty: 

- [Koncové body streamování](streaming-endpoint-concept.md)
- [Živé události a výstupy](live-events-outputs-concept.md)
- [Lokátory streamování](streaming-locators-concept.md)

### <a name="general-steps"></a>Obecné kroky

1. V účtu Media Services zkontrolujte, zda je spuštěn **koncový bod streamování** (Origin). 
2. Vytvořte [živou událost](live-events-outputs-concept.md). <br/>Při vytváření události můžete určit její automatické spuštění. Případně můžete spustit událost, když jste připraveni začít streamovat.<br/> Je-li automatické spuštění nastaveno na hodnotu true, živá událost bude spuštěna ihned po vytvoření. Fakturace se spustí, jakmile se spustí živá událost. Chcete-li zastavit další fakturaci, je nutné explicitně volat Stop na prostředku živé události. Další informace naleznete v tématu [Stavy živých událostí a fakturace](live-event-states-billing.md).
3. Získejte ingestující adresy URL a nakonfigurujte místní kodér tak, aby k odeslání kanálu příspěvků používal adresu URL.<br/>Viz [doporučené živé kodéry](recommended-on-premises-live-encoders.md).
4. Získejte adresu URL náhledu a použijte ji k ověření, že vstup z kodéru je skutečně přijímán.
5. Vytvořte nový objekt **Asset.** 

    Každý živý výstup je přidružený k datovému zdroji, který používá k nahrávání videa do přidruženého kontejneru úložiště objektů blob Azure. 
6. Vytvořte **živý výstup** a použijte název datového zdroje, který jste vytvořili, aby bylo možné datový proud archivovat do datového zdroje.

    Živé výstupy se spustí při vytváření a zastaví se při odstranění. Když odstraníte živý výstup, neodstraníte podkladový datový zdroj a obsah v datovém zdroji.
7. Vytvořte **lokátor streamování** s [předdefinovanými typy zásad streamování](streaming-policy-concept.md).

    Chcete-li publikovat živý výstup, musíte vytvořit lokátor streamování pro přidružený datový zdroj. 
8. Seznam cesty na **streamování Lokátor** získat zpět adresy URL použít (to jsou deterministické).
9. Získejte název hostitele pro **koncový bod streamování** (Origin), ze kterýchcete streamovat.
10. Zkombinujte adresu URL z kroku 8 s názvem hostitele v kroku 9, abyste získali úplnou adresu URL.
11. Chcete-li zastavit zobrazení **živé události,** musíte zastavit streamování události a odstranit **lokátor streamování**.
12. Pokud jste dokončili streamování událostí a chcete dříve zřízené prostředky vyčistit, postupujte podle následujícího návodu.

    * Zastavte odesílání datového proudu z kodéru.
    * Zastavte živou událost. Jakmile je živá událost zastavena, nebudou jí účtovány žádné poplatky. Když bude potřeba kanál znovu spustit, bude mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
    * Pokud nechcete pokračovat v poskytování archivu živé události ve formě datového proudu na vyžádání, můžete koncový bod streamování zastavit. Pokud je živá událost v zastaveném stavu, nebudou jí účtovány žádné poplatky.

Datový zdroj, na který je živý výstup archivován, se při odstranění živého výstupu automaticky stane aktivem na vyžádání. Před zastavením živé události je nutné odstranit všechny živé výstupy. Můžete použít volitelný příznak [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) automaticky odebrat živé výstupy na stop. 

> [!TIP]
> Viz [Živé streamování kurz](stream-live-tutorial-with-api.md), článek zkoumá kód, který implementuje výše popsané kroky.

## <a name="other-important-articles"></a>Další důležité články

- [Doporučené kodéry pro kódování v reálném čase](recommended-on-premises-live-encoders.md)
- [Použití cloudového DVR](live-event-cloud-dvr.md)
- [Porovnání typů živých událostí](live-event-types-comparison.md)
- [Stavy a fakturace](live-event-states-billing.md)
- [Latence](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Podívejte se na článek [Nejčastější dotazy.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="next-steps"></a>Další kroky

* [Rychlý start živého streamování] (live-events-wirecast-quickstart.md(
* [Živé streamování tutorial](stream-live-tutorial-with-api.md)
* [Pokyny pro migraci pro přechod z Media Services v2 na v3](migrate-from-v2-to-v3.md)
