---
title: Přehled živého streamování využívajícího službu Azure Media Services | Dokumentace Microsoftu
description: Tento článek poskytuje přehled živého streamování využívajícího službu Azure Media Services v3.
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
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: b8725dfcb2a337750c6e2a78ba7571114b8e3cd3
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407179"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování pomocí služby Azure Media Services v3

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. Ke streamování živých událostí pomocí služby Media Services, budete potřebovat následující:  

- Fotoaparát, který se používá k zachycení živé události.<br/>Inspiraci, instalační program, podívejte se na [videa ozubené kolo nastavení jednoduché a přenosné události]( https://link.medium.com/KNTtiN6IeT).
- Živé video encoder, který převádí signály z kamery (nebo jiné zařízení, jako je přenosný počítač) příspěvek informačního kanálu, který je odeslán do Media Services. Příspěvek kanál může obsahovat signály související s reklamy, jako je například SCTE 35 značky.<br/>Seznam doporučených kodéry streamování najdete v tématu [živé streamování kodérů](recommended-on-premises-live-encoders.md). Také přečtěte si tento blog: [Živé streamování provozu pomocí OBS](https://link.medium.com/ttuwHpaJeT).
- Komponenty ve službě Media Services, která umožňuje ingestovat, ve verzi preview, balení, záznamu, šifrování a vysílat živě přenášená akce vašim zákazníkům nebo do sítě CDN pro další distribuci.

Tento článek obsahuje přehled a pokyny k živé streamování pomocí služby Media Services a odkazy na další související články.

> [!NOTE]
> V současné době nelze použít na webu Azure portal ke správě prostředků v3. Použití [rozhraní REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref), nebo jeden z podporovaných [sady SDK](developers-guide.md).

## <a name="dynamic-packaging"></a>Dynamické balení

Díky Media Services můžete využít výhod dynamického Packaging](dynamic-packaging-overview.md), který umožňuje zobrazení náhledu a všesměrového vysílání živé streamy v [formátů MPEG DASH, HLS a Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z příspěvku informační kanál, který odešlete do služby. Vaši uživatelé můžou přehrávat živé streamování pomocí libovolné kompatibilní hráči HLS, DASH nebo Smooth Streaming. Můžete použít [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) ve vašich webových nebo mobilních aplikací můžete poskytovat datový proud v některém z těchto protokolů.

## <a name="dynamic-encryption"></a>Dynamické šifrování

Dynamické encryption umožňuje šifrovat dynamicky živě nebo na vyžádání obsahu pomocí AES-128 nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. Další informace najdete v tématu [dynamického šifrování](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Dynamic Manifest

Dynamické filtrování se používá k řízení počet stop, formáty, přenosových rychlostí a prezentace časových oken, které se pošlou hráči. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Live typy událostí

Živá událost může být jeden ze dvou typů: Předávací tak pro živé kódování. Podrobnosti o živém streamování v Media Services v3 najdete v tématu [živé události a Live výstupy](live-events-outputs-concept.md).

### <a name="pass-through"></a>Průchod

![Předávací](./media/live-streaming/pass-through.svg)

Při použití předávací **živá událost**, můžete spoléhat na vaše místní kodér služby live Encoding pro vygenerování více datový proud videa s přenosovou rychlostí a odeslat, že jako příspěvek kanálu pro živá událost (pomocí protokolu RTMP nebo fragmentovaný soubor MP4). Živá událost se potom provede prostřednictvím příchozí datové proudy videa bez dalšího zpracování. Takové vytvoření předávací živé události je optimalizovaná pro dlouho běžící události v reálném čase nebo 24 × 365 lineární živé streamování. 

### <a name="live-encoding"></a>Kódování v reálném čase  

![živé kódování](./media/live-streaming/live-encoding.svg)

Pokud používáte živé kódování pomocí Media Services, nakonfigurujete by vaše místní kodér služby live Encoding odesílat videa s jednou přenosovou rychlostí jako příspěvek informačního kanálu živá událost (pomocí protokolu RTMP nebo fragmentovaný soubor Mp4). Živá událost kóduje této příchozí s jednou přenosovou rychlostí na datový proud stream [více přenosovými rychlostmi datový proud videa](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), zpřístupní pro doručení pro přehrávání zařízení prostřednictvím protokolů, jako jsou MPEG-DASH, HLS a Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Živé streamování pracovního postupu v Media Services v3 informace o tom, je nutné nejdříve si přečtěte a koncepce následující: 

- [Koncové body streamování](streaming-endpoint-concept.md)
- [Události v reálném čase a živé výstupy](live-events-outputs-concept.md)
- [Lokátory streamování](streaming-locators-concept.md)

### <a name="general-steps"></a>Obecné kroky

1. Ve vašem účtu Media Services, ujistěte se, **koncový bod streamování** běží. 
2. Vytvoření [živá událost](live-events-outputs-concept.md). <br/>Při vytváření události, můžete je zadat na automatické spuštění. Alternativně můžete spustit událost, když budete chtít spustit streamování.<br/> Když automatické spuštění je nastavena na hodnotu true, živá událost se spustí správné po jeho vytvoření. Účtování začne Jakmile živá událost se spustí. Musíte explicitně volat Stop prostředku živá událost, která zastaví další fakturace. Další informace najdete v tématu [živá událost stavy a fakturace](live-event-states-billing.md).
3. Získání adresy URL ingestování a konfiguraci vaší místní kodér použití adresy URL k odeslání příspěvku informačního kanálu.<br/>Zobrazit [doporučuje kodéry](recommended-on-premises-live-encoders.md).
4. Získat adresu URL ve verzi preview a použít ho k ověření, že je ve skutečnosti přijímají vstup z kodéru.
5. Vytvořte nový **Asset** objektu.
6. Vytvoření **Live výstup** a používat název assetu, kterou jste vytvořili.<br/>**Live výstup** bude archivovat do datového proudu **Asset**.
7. Vytvoření **Lokátor streamování** pomocí integrované **streamování zásad** typy.<br/>Pokud máte v úmyslu šifrování obsahu, přečtěte si [Content protection přehled](content-protection-overview.md).
8. Seznam cest na **Lokátor streamování** získat zpět adresy URL používat (Toto jsou deterministické).
9. Získání názvu hostitele pro **koncový bod streamování** chcete z datového proudu.
10. Adresu URL v kroku 8 v kombinaci s názvem hostitele v kroku 9 získat úplnou adresu URL.
11. Pokud budete chtít zastavit, aby vaše **živá událost** viditelná, musíte zastavit streamování událostí a delete **Lokátor streamování**.

## <a name="other-important-articles"></a>Další důležité články

- [Doporučené kodéry](recommended-on-premises-live-encoders.md)
- [Použití cloudového DVR](live-event-cloud-dvr.md)
- [Porovnání funkcí živého typy událostí](live-event-types-comparison.md)
- [Stavy a fakturace](live-event-states-billing.md)
- [Latence](live-event-latency.md)

## <a name="next-steps"></a>Další postup

* [Živé streamování kurz](stream-live-tutorial-with-api.md)
* [Pokyny k migraci pro přechod ze služby Media Services v2 na v3](migrate-from-v2-to-v3.md)
