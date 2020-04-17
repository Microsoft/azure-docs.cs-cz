---
title: Další informace o kodeku doporučených službou Azure Media Services | Dokumenty společnosti Microsoft
description: V tomto článku jsou uvedeny místní kodéry doporučené službou Azure Media Services.
services: media-services
keywords: kódování;kodéry;média
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 3c42070525fc60e45b976620513929c3d5a32341
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535090"
---
# <a name="recommended-on-premises-encoders"></a>Doporučené místní kodéry

Při živém streamování pomocí Služby Azure Media Services můžete určit, jak má váš kanál přijímat vstupní datový proud. Pokud se rozhodnete použít místní kodér s kanálem kódování živého prostředí, kodér by měl jako výstup vysunout datový proud s vysokou kvalitou s jedním datovým tokem. Pokud se rozhodnete použít místní kodér s průchodem kanálem, kodér by měl tlačit datový proud s více přenosovými rychlostmi jako výstup se všemi požadovanými výstupními vlastnostmi. Další informace naleznete v [tématu Živé streamování s místními kodéry](media-services-live-streaming-with-onprem-encoders.md).

## <a name="encoder-requirements"></a>Požadavky na kodér

Kodéry musí podporovat TLS 1.2 při použití protokolů HTTPS nebo RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Živé kodéry, které vydezkují RTMP 

Azure Media Services doporučuje používat jeden z následujících živých kodérů, které mají RTMP jako výstup:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (verze 13.0.2 nebo vyšší z důvodu požadavku TLS 1.2)

  Kodéry musí při použití protokolů RTMPS podporovat protokoly TLS 1.2.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Živé kodéry, které výstup fragmentované MP4 

Azure Media Services doporučuje používat jeden z následujících živých kodérů, které mají vícebitovou rychlost fragmentované MP4 (plynulé streamování) jako výstup:

- Media Excel Hero Live a Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elementární Live (verze 2.14.15 a vyšší kvůli požadavku TLS 1.2)

  Kodéry musí při používání protokolů HTTPS podporovat protokoly TLS 1.2.
- Envivio 4Caster C4 Gen III
- Představte si, že komunikace Selenio MCP3

> [!NOTE]
> Živý kodér může odesílat datový proud s jedním přenosovým tokem do kanálu, ale tato konfigurace se nedoporučuje, protože neumožňuje adaptivní datový tok datových proudů do klienta.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Jak se stát partnerem místního kodéru

Jako partner azure media services na místní kodéru, Media Services propaguje váš produkt tím, že doporučuje váš kodér pro podnikové zákazníky. Chcete-li se stát partnerem místního kodéru, musíte ověřit kompatibilitu místního kodéru se službami Media Services. Chcete-li tak učinit, proveďte následující ověření:

Průchod ověřením kanálu
1. Vytvoření nebo návštěva účtu Azure Media Services
2. Vytvoření a spuštění **předávacího** kanálu
3. Nakonfigurujte kodér tak, aby tlačil živý stream s více přenosovou rychlostí.
4. Vytvoření publikované živé události
5. Spusťte živý kodér přibližně 10 minut
6. Zastavení živé události
7. Vytvořit, spustit koncový bod streamování, pomocí přehrávače, jako je [Azure Media Player](https://aka.ms/azuremediaplayer) sledovat archivovaný datový zdroj, aby se zajistilo, že přehrávání nemá žádné viditelné závady pro všechny úrovně kvality (Nebo alternativně sledovat a ověřovat prostřednictvím url náhledu během živé relace před krokem 6)
8. Zaznamenejte ID datového zdroje, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi použitou z živého kodéru
9. Obnovení stavu kanálu po vytvoření každé ukázky
10. Opakujte kroky 3 až 9 pro všechny konfigurace podporované kodérem (s signalizací reklamy/titulky/různé rychlosti kódování)

Ověření kanálu živého kódování
1. Vytvoření nebo návštěva účtu Azure Media Services
2. Vytvoření a spuštění **živého kanálu kódování**
3. Nakonfigurujte kodér tak, aby tlačil živý datový proud s jedním datovým proudem.
4. Vytvoření publikované živé události
5. Spusťte živý kodér přibližně 10 minut
6. Zastavení živé události
7. Vytvořit, spustit koncový bod streamování, pomocí přehrávače, jako je [Azure Media Player](https://aka.ms/azuremediaplayer) sledovat archivovaný datový zdroj, aby se zajistilo, že přehrávání nemá žádné viditelné závady pro všechny úrovně kvality (Nebo alternativně sledovat a ověřovat prostřednictvím url náhledu během živé relace před krokem 6)
8. Zaznamenejte ID datového zdroje, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi použitou z živého kodéru
9. Obnovení stavu kanálu po vytvoření každé ukázky
10. Opakujte kroky 3 až 9 pro všechny konfigurace podporované kodérem (s a bez signálu reklamy / titulky / různé rychlosti kódování)

Ověření dlouhověkosti
1. Vytvoření nebo návštěva účtu Azure Media Services
2. Vytvoření a spuštění **předávacího** kanálu
3. Nakonfigurujte kodér tak, aby tlačil živý stream s více přenosovou rychlostí.
4. Vytvoření publikované živé události
5. Spusťte živý kodér po dobu jednoho týdne nebo déle
6. Pomocí přehrávače, jako je [Azure Media Player,](https://aka.ms/azuremediaplayer) můžete čas od času sledovat živé vysílání (nebo archivovaný datový zdroj), abyste zajistili, že přehrávání nebude mít žádné viditelné závady.
7. Zastavení živé události
8. Zaznamenejte ID datového zdroje, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi použitou z živého kodéru

Nakonec pošlete zaznamenané nastavení a živé archivní parametry amsstreaming@microsoft.comna Media Services e-mailem . Po obdržení media services provádí ověřovací testy vzorků z vašeho živého kodéru. Jakékoli dotazy týkající se tohoto procesu vám mohou být vzneseny v případě, že se na vás obrátí mediální služby.
