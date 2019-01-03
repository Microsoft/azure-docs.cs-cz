---
title: Další informace o streamování s místními kodéry doporučená službou Media Services – Azure | Dokumentace Microsoftu
description: Další informace o streamování s místními kodéry doporučená službou Media Services
services: media-services
keywords: kódování; kodérů; média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d1110669bd0ca8c0ba0caf34ef41861c500bdd33
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790024"
---
# <a name="recommended-live-streaming-encoders"></a>Doporučené datové proudy kodéry

Ve službě Media Services [Livestream](https://docs.microsoft.com/rest/api/media/liveevents) (kanál) představuje kanál pro zpracování obsahu živého streamování. Livestream přijímat živé vstupní datové proudy v jednom ze dvou způsobů:

* Místní odešle kodér služby live Encoding s více přenosovými rychlostmi RTMP nebo technologie Smooth Streaming (fragmentovaný MP4) Streamovat do Livestream, který není povolené provádět živé kódování pomocí Media Services. Ingestované datové proudy prochází LiveEvents bez dalšího zpracování. Tato metoda je volána **předávací**. Live encoder můžete odeslat datový proud s jednou přenosovou rychlostí průchodu přes kanál, ale tato konfigurace se nedoporučuje, protože neumožňuje pro adaptivní přenosové rychlosti streamování do klienta.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě Streamovat.

* Místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí Livestream, který má povolené provádět živé kódování pomocí Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Livestream potom provede kódování v reálném čase příchozího datového proudu s jednou přenosovou rychlostí na datový proud s více přenosovými rychlostmi (adaptivní) videa.

Podrobné informace o kódování v reálném čase pomocí služby Media Services najdete v tématu [živé streamování pomocí služby Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Kodéry výstupu RTMP

Služba Media Services se doporučuje používat jednu z následující kodéry, které mají RTMP jako výstup. Jsou podporované schémata URL `rtmp://` nebo `rtmps://`.

> [!NOTE]
 > Při streamování přes RTMP, zkontrolujte nastavení brány firewall nebo proxy serveru pro potvrzení, že jsou otevřené odchozí porty TCP 1935 a 1936.<br/>
 Při streamování přes RTMPS, zkontrolujte nastavení brány firewall nebo proxy serveru pro potvrzení, že jsou otevřené odchozí porty TCP 2935 a 2936.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Přepínání Studio (iOS)
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Řez Teradek 756
- TriCaster 8000
- Tricaster Mini HD, High Density-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Kodéry pro kódování, jejichž výstupem fragmentovaný soubor MP4

Služba Media Services se doporučuje používat jednu z následující kodéry, které mají s více přenosovými rychlostmi technologie Smooth Streaming (fragmentovaný soubor MP4) jako výstup. Jsou podporované schémata URL `rtmp://` nebo `rtmps://`.

- Ateme TITAN za provozu
- Cisco digitální Media Encoderu 2200
- Elemental Live
- Envivio 4Caster C4 obecné III
- Imagine Communications Selenio MCP3
- Média pro Excel Hero za provozu a Hero 4K (UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Jak se stát partnerem místní kodér

Jako partner Azure Media Services místní kodér Media Services podporuje váš produkt doporučením váš kodér pro podnikové zákazníky. Pokud chcete stát partnerem místní kodér, musíte ověřit kompatibilitu vaší místní kodér pomocí služby Media Services. Uděláte to tak, proveďte následující ověření:

### <a name="pass-through-liveevent-verification"></a>Předávací ověřování Livestream

1. Vytvořit nebo navštivte svůj účet Azure Media Services.
2. Vytvoření a spuštění **předávací** Livestream.
3. Konfigurovat váš kodér tak, aby nabízel živý datový proud s více přenosovými rychlostmi.
4. Vytvoření publikované živé události.
5. Spusťte váš kodér služby live Encoding přibližně 10 minut.
6. Zastavte živou událost.
7. Vytvoření, spuštění koncového bodu streamování, například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat archivovaný asset, který k zajištění přehrávání histogramu úplně bez viditelné pro všechny úrovně kvality (nebo můžete také sledovat a ověřit prostřednictvím adresy URL náhledu během živé relace před krokem 6).
8. ID Assetu záznamu publikovat adresu URL streamování pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding.
9. Obnovit stav Livestream po vytvoření každý vzorek.
10. Opakujte kroky 3 až 9 u všech konfigurací s nepodporuje váš kodér (s opakováním a bez ad signalizace/titulky nebo jiné kódování rychlosti).

### <a name="live-encoding-liveevent-verification"></a>Živé kódování Livestream ověření

1. Vytvořit nebo navštivte svůj účet Azure Media Services.
2. Vytvoření a spuštění **živého kódování** Livestream.
3. Konfigurovat váš kodér tak, aby nabízel živý datový proud s jednou přenosovou rychlostí.
4. Vytvoření publikované živé události.
5. Spusťte váš kodér služby live Encoding přibližně 10 minut.
6. Zastavte živou událost.
7. Vytvoření, spuštění koncového bodu streamování, například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat archivovaný asset, který k zajištění přehrávání histogramu úplně bez viditelné pro všechny úrovně kvality (nebo můžete také sledovat a ověřit prostřednictvím adresy URL náhledu během živé relace před krokem 6).
8. ID Assetu záznamu publikovat adresu URL streamování pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding.
9. Obnovit stav Livestream po vytvoření každý vzorek.
10. Opakujte kroky 3 až 9 u všech konfigurací s nepodporuje váš kodér (s opakováním a bez ad signalizace/titulky nebo různých kódování rychlost).

### <a name="longevity-verification"></a>Životnost ověření

1. Vytvořit nebo navštivte svůj účet Azure Media Services.
2. Vytvoření a spuštění **předávací** kanálu.
3. Konfigurovat váš kodér tak, aby nabízel živý datový proud s více přenosovými rychlostmi.
4. Vytvoření publikované živé události.
5. Spusťte váš kodér služby live Encoding pro jeden týden nebo i delší dobu.
6. Například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat živé streamování z času na čas (nebo archivovaný asset, který) k zajištění přehrávání nemá žádné viditelné potížím s vykreslováním.
7. Zastavte živou událost.
8. ID Assetu záznamu publikovat adresu URL streamování pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding.

A konečně, nahrané nastavení e-mailu a live parametry archivu služby Azure Media Services na amsstreaming@microsoft.com jako oznámení, že se prošly všechny kontroly připravenosti ověření. Také vaše kontaktní informace pro všechny shrnuje následující. Obraťte se na tým Azure Media Services s dotazy týkající se tohoto procesu.

## <a name="next-steps"></a>Další postup

[Živé streamování pomocí služby Media Services v3](live-streaming-overview.md)
