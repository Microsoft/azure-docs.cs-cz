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
ms.date: 02/01/2019
ms.author: juliako
ms.openlocfilehash: e90dd052f6a4af83d2dd794dd405a4700da75bde
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656330"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Živé streamování pomocí služby Azure Media Services v3

Azure Media Services umožňuje doručovat živé události do vašich zákazníků v cloudu Azure. Ke streamování živých událostí pomocí služby Media Services, budete potřebovat následující:  

- Fotoaparát, který se používá k zachycení živé události.<br/>Inspiraci, instalační program, podívejte se na [videa ozubené kolo nastavení jednoduché a přenosné události]( https://link.medium.com/KNTtiN6IeT).
- Živé video encoder, který převádí signály z kamery (nebo jiné zařízení, jako je přenosný počítač) příspěvek informačního kanálu, který je odeslán do Media Services. Příspěvek kanál může obsahovat signály související s reklamy, jako je například SCTE 35 značky.<br/>Seznam doporučených kodéry streamování najdete v tématu [živé streamování kodérů](recommended-on-premises-live-encoders.md). Také přečtěte si tento blog: [Živé streamování provozu pomocí OBS](https://link.medium.com/ttuwHpaJeT).
- Komponenty ve službě Media Services, která umožňuje ingestovat, ve verzi preview, balení, záznamu, šifrování a vysílat živě přenášená akce vašim zákazníkům nebo do sítě CDN pro další distribuci.

Pomocí Media Services, můžete využít výhod **dynamické balení**, která umožňuje zobrazit náhled a všesměrového vysílání živé streamy v [formátů MPEG DASH, HLS a Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) z příspěvku informačního kanálu že odesíláte do služby. Vaši uživatelé můžou přehrávat živé streamování pomocí libovolné kompatibilní hráči HLS, DASH nebo Smooth Streaming. Můžete použít [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) ve vašich webových nebo mobilních aplikací můžete poskytovat datový proud v některém z těchto protokolů.

Služba Media Services umožňuje doručovat obsah zašifrovaný dynamicky (**dynamického šifrování**) s Advanced Encryption Standard (AES-128) nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM licence autorizovaným klientům. Další informace o tom, jak šifrování obsahu pomocí služby Media Services najdete v tématu [ochrana obsahu – přehled](content-protection-overview.md)

Můžete také použít dynamické filtrování, který slouží k řízení počet stop, formátů, přenosových rychlostí, a prezentace časových oken, které se pošlou hráči. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

Tento článek obsahuje přehled a pokyny k živé streamování pomocí služby Media Services.

## <a name="prerequisites"></a>Požadavky

Informace o tom živého streamování pracovního postupu v Media Services v3, je nutné si prošli a porozuměli následující pojmy: 

- [Koncové body streamování](streaming-endpoint-concept.md)
- [Události v reálném čase a živé výstupy](live-events-outputs-concept.md)

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Tady jsou kroky pro pracovní postup živého streamování:

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
