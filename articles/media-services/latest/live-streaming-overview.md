---
title: Přehled živé streamování pomocí služby Azure Media Services v3 | Dokumentace Microsoftu
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
ms.date: 06/16/2019
ms.author: juliako
ms.openlocfilehash: 0abc3eec380cccae2672d0e9aa4a3a4c7199362f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295664"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování pomocí služby Azure Media Services v3

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. Ke streamování živých událostí pomocí služby Media Services, budete potřebovat následující:  

- Fotoaparát, který se používá k zachycení živé události.<br/>Inspiraci, instalační program, podívejte se na [videa ozubené kolo nastavení jednoduché a přenosné události]( https://link.medium.com/KNTtiN6IeT).

    Pokud nemáte přístup k fotoaparátu, nástroje, jako [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) je možné generovat živého kanálu z videosouboru.
- Živé video encoder, který převádí signály z kamery (nebo jiné zařízení, jako je přenosný počítač) příspěvek informačního kanálu, který je odeslán do Media Services. Příspěvek kanál může obsahovat signály související s reklamy, jako je například SCTE 35 značky.<br/>Seznam doporučených kodéry streamování najdete v tématu [živé streamování kodérů](recommended-on-premises-live-encoders.md). Také přečtěte si tento blog: [Živé streamování provozu pomocí OBS](https://link.medium.com/ttuwHpaJeT).
- Komponenty ve službě Media Services, která umožňuje ingestovat, ve verzi preview, balení, záznamu, šifrování a vysílat živě přenášená akce vašim zákazníkům nebo do sítě CDN pro další distribuci.

Tento článek obsahuje přehled a pokyny k živé streamování pomocí služby Media Services a odkazy na další související články.
 
> [!NOTE]
> Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging"></a>Dynamické balení

Pomocí Media Services, můžete využít výhod [dynamické balení](dynamic-packaging-overview.md), která umožňuje zobrazit náhled a všesměrového vysílání živé streamy v [formátů MPEG DASH, HLS a Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z příspěvku informačního kanálu který se posílá do služby. Vaši uživatelé můžou přehrávat živé streamování pomocí libovolné kompatibilní hráči HLS, DASH nebo Smooth Streaming. Můžete použít [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) ve vašich webových nebo mobilních aplikací můžete poskytovat datový proud v některém z těchto protokolů.

## <a name="dynamic-encryption"></a>Dynamické šifrování

Dynamické encryption umožňuje šifrovat dynamicky živě nebo na vyžádání obsahu pomocí AES-128 nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. Další informace najdete v tématu [dynamického šifrování](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Dynamic Manifest

Dynamické filtrování se používá k řízení počet stop, formáty, přenosových rychlostí a prezentace časových oken, které se pošlou hráči. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Live typy událostí

[Živé události](https://docs.microsoft.com/rest/api/media/liveevents) zodpovídají za ingestování a zpracování informačních kanálů živého videa. Živá událost může být jeden ze dvou typů: Předávací tak pro živé kódování. Podrobnosti o živém streamování v Media Services v3 najdete v tématu [živé události a Live výstupy](live-events-outputs-concept.md).

### <a name="pass-through"></a>Průchod

![Průchozí](./media/live-streaming/pass-through.svg)

Při použití předávací **živá událost**, můžete spoléhat na vaše místní kodér služby live Encoding pro vygenerování více datový proud videa s přenosovou rychlostí a odeslat, že jako příspěvek informační kanál k živé události (s použitím vstupní protokol RTMP nebo fragmentovaný soubor MP4). Živá událost se potom provede prostřednictvím příchozí datové proudy videa k dynamické packager (koncový bod streamování) bez jakékoli další překódování. Takové vytvoření předávací živé události je optimalizovaná pro dlouho běžící události v reálném čase nebo 24 × 365 lineární živé streamování. 

### <a name="live-encoding"></a>Kódování v reálném čase  

![Kódování v reálném čase](./media/live-streaming/live-encoding.svg)

Pokud používáte cloudové kódování pomocí Media Services, nakonfigurujete by vaše místní kodér služby live Encoding odesílat videa s jednou přenosovou rychlostí jako příspěvek informačního kanálu (až 32Mbps agregace) živé události (s použitím vstupní protokol RTMP nebo fragmentovaný soubor MP4). Živá událost videoúlohy příchozí s jednou přenosovou rychlostí streamování do [více streamů videa s přenosovou rychlostí](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) v různých řešení ke zlepšení doručování a zpřístupňuje je pro doručení pro přehrávání zařízení prostřednictvím standardních oborových protokolů jako jsou MPEG-DASH, Apple HTTP Live Streaming (HLS) a Microsoft Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Živé streamování pracovního postupu v Media Services v3 informace o tom, je nutné nejdříve si přečtěte a koncepce následující: 

- [Koncové body streamování](streaming-endpoint-concept.md)
- [Živé události a výstupy](live-events-outputs-concept.md)
- [Lokátory streamování](streaming-locators-concept.md)

### <a name="general-steps"></a>Obecné kroky

1. Ve vašem účtu Media Services, ujistěte se, že **koncový bod streamování** (původní) je spuštěná. 
2. Vytvoření [živá událost](live-events-outputs-concept.md). <br/>Při vytváření události, můžete je zadat na automatické spuštění. Alternativně můžete spustit událost, když budete chtít spustit streamování.<br/> Když automatické spuštění je nastavena na hodnotu true, živá událost se spustí správné po jeho vytvoření. Účtování začne Jakmile živá událost se spustí. Musíte explicitně volat Stop prostředku živá událost, která zastaví další fakturace. Další informace najdete v tématu [živá událost stavy a fakturace](live-event-states-billing.md).
3. Získání adresy URL ingestování a konfiguraci vaší místní kodér použití adresy URL k odeslání příspěvku informačního kanálu.<br/>Zobrazit [doporučuje kodéry](recommended-on-premises-live-encoders.md).
4. Získat adresu URL ve verzi preview a použít ho k ověření, že je ve skutečnosti přijímají vstup z kodéru.
5. Vytvořte nový **Asset** objektu.
6. Vytvoření **Live výstup** a používat název assetu, kterou jste vytvořili.<br/>**Live výstup** bude archivovat do datového proudu **Asset**.
7. Vytvoření **Lokátor streamování** s [předdefinovaných typů zásad streamování](streaming-policy-concept.md)
8. Seznam cest na **Lokátor streamování** získat zpět adresy URL používat (Toto jsou deterministické).
9. Získání názvu hostitele pro **koncový bod streamování** (původní), kterou chcete z datového proudu.
10. Adresu URL v kroku 8 v kombinaci s názvem hostitele v kroku 9 získat úplnou adresu URL.
11. Pokud budete chtít zastavit, aby vaše **živá událost** viditelná, musíte zastavit streamování událostí a delete **Lokátor streamování**.

## <a name="other-important-articles"></a>Další důležité články

- [Doporučené kodéry](recommended-on-premises-live-encoders.md)
- [Použití cloudového videorekordéru](live-event-cloud-dvr.md)
- [Porovnání funkcí živého typy událostí](live-event-types-comparison.md)
- [Stavy a fakturace](live-event-states-billing.md)
- [Latence](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

* [Živé streamování kurz](stream-live-tutorial-with-api.md)
* [Pokyny k migraci pro přechod ze služby Media Services v2 na v3](migrate-from-v2-to-v3.md)
