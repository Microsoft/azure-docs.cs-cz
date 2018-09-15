---
title: Další informace o kodérů doporučená službou Azure Media Services | Dokumentace Microsoftu
description: Další informace o kodérů doporučená službou media services
services: media-services
keywords: kódování; kodérů; média
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 09/13/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c90d6a5784fe9d80df4fab304b6122d3fa24d0b5
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605160"
---
# <a name="recommended-on-premises-encoders"></a>Doporučené místní kodéry
Když živého streamování pomocí služby Azure Media Services, můžete zadat, jak chcete, aby váš kanál pro příjem vstupního datového proudu. Pokud se rozhodnete použít kodér v místním prostředí pomocí živého kódování kanálu, by měl váš kodér push datový proud s jednou přenosovou rychlostí vysoce kvalitní jako výstup. Pokud se rozhodnete používat pro místní kodér průchodu přes kanál, by měl váš kodér push datového proudu s více přenosovými rychlostmi jako výstup s všechny požadované výstupu kvality. Další informace najdete v tématu [živé streamování pomocí místních kodérů](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services doporučuje používat jednu z následujících kodéry, které mají RTMP jako výstup:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Řez Teradek 756
- TriCaster 8000
- Tricaster Mini HD, High Density-4
- OBS Studio
- VMIX
- xStream
- Přepínání Studio (iOS)

Azure Media Services doporučuje používat jednu z následující kodéry, které mají s více přenosovými rychlostmi fragmentovaný soubor MP4 (technologie Smooth Streaming) jako výstup:
- Média pro Excel Hero za provozu a Hero 4K (UHD/HEVC)
- Ateme TITAN za provozu
- Cisco digitální Media Encoderu 2200
- Elemental Live
- Envivio 4Caster C4 obecné III
- Imagine Communications Selenio MCP3

> [!NOTE]
> Live encoder můžete odeslat datový proud s jednou přenosovou rychlostí průchodu přes kanál, ale tato konfigurace se nedoporučuje, protože neumožňuje pro adaptivní přenosové rychlosti streamování do klienta.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Jak se stát partnerem místní kodér
Jako partner Azure Media Services místní kodér Media Services podporuje váš produkt doporučením váš kodér pro podnikové zákazníky. Pokud chcete stát partnerem kodér v místním prostředí, musíte ověřit kompatibilitu váš kodér v místním prostředí pomocí služby Media Services. Uděláte to tak, proveďte následující ověření:

Předávání ověření kanálu
1. Vytvořit nebo navštivte svůj účet Azure Media Services
2. Vytvoření a spuštění **předávací** kanálu
3. Konfigurovat váš kodér tak, aby nabízel živý datový proud s více přenosovými rychlostmi.
4. Vytvořit publikované živou událost
5. Spusťte váš kodér služby live Encoding přibližně 10 minut
6. Zastavit živou událost
7. Vytvoření, spuštění koncového bodu streamování, například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat archivovaný asset, který k zajištění přehrávání histogramu úplně bez viditelné pro všechny úrovně kvality (nebo můžete také sledovat a ověřit prostřednictvím adresy URL náhledu během živé relace před krokem 6)
8. ID Assetu, záznamu, publikovat adresu URL streamování pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding
9. Resetuje stav kanálu po vytvoření každý vzorek
10. Opakujte kroky 3 až 9 u všech konfigurací s nepodporuje váš kodér (s opakováním a bez ad signalizace/titulky/jiné kódování rychlost)

Živé kódování ověření kanálu
1. Vytvořit nebo navštivte svůj účet Azure Media Services
2. Vytvoření a spuštění **živého kódování** kanálu
3. Konfigurovat váš kodér tak, aby nabízel živý datový proud s jednou přenosovou rychlostí.
4. Vytvořit publikované živou událost
5. Spusťte váš kodér služby live Encoding přibližně 10 minut
6. Zastavit živou událost
7. Vytvoření, spuštění koncového bodu streamování, například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat archivovaný asset, který k zajištění přehrávání histogramu úplně bez viditelné pro všechny úrovně kvality (nebo můžete také sledovat a ověřit prostřednictvím adresy URL náhledu během živé relace před krokem 6)
8. ID Assetu, záznamu, publikovat adresu URL streamování pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding
9. Resetuje stav kanálu po vytvoření každý vzorek
10. Opakujte kroky 3 až 9 u všech konfigurací s nepodporuje váš kodér (s opakováním a bez ad signalizace/titulky nebo různých kódování rychlost)

Životnost ověření
1. Vytvořit nebo navštivte svůj účet Azure Media Services
2. Vytvoření a spuštění **předávací** kanálu
3. Konfigurovat váš kodér tak, aby nabízel živý datový proud s více přenosovými rychlostmi.
4. Vytvořit publikované živou událost
5. Jeden týden nebo i delší dobu spouštění váš kodér služby live Encoding
6. Například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat živé streamování z času na čas (nebo archivovaný asset, který) k zajištění přehrávání nemá žádné viditelné chyb
7. Zastavit živou událost
8. ID Assetu, záznamu, publikovat adresu URL streamování pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding

A konečně, nahrané nastavení odesílání a parametry archivu služby Media Services live e-mailem amsstreaming@microsoft.com. Přijetí služby Media Services provede ověřovacích testů na ukázky z váš kodér služby live Encoding. Obraťte se na Media Services s dotazy týkající se tohoto procesu.