---
title: Kodéry živého streamování doporučené mediálními službami – Azure | Dokumenty společnosti Microsoft
description: Informace o místních kodérech živého streamování doporučených službou Media Services
services: media-services
keywords: kódování;kodéry;média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0676b6b183c64dcd0fb15b87de48a4afed3a0011
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641809"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Testované místní kodéry živého streamování

Ve službě Azure Media Services [představuje živá událost](https://docs.microsoft.com/rest/api/media/liveevents) (kanál) kanál pro zpracování obsahu živého streamování. Živá událost přijímá živé vstupní proudy jedním ze dvou způsobů.

* Místní živý kodér odesílá vícebitový datový proud RTMP nebo plynulého streamování (fragmentovaný protokol MP4) do živé události, která není povolena k provádění živého kódování pomocí služby Media Services. Ingestované datové proudy procházejí živými událostmi bez dalšího zpracování. Tato metoda se nazývá **předávací**. Doporučujeme, aby živý kodér odesílal datové proudy s více přenosovým tokem namísto datového proudu s jedním datovým tokem do předávací živé události, aby bylo možné do klienta přenášet datový tok. 

    Pokud používáte datové proudy s více přenosovými rychlostmi pro předávací živou událost, musí být synchronizována velikost videa GOP a fragmenty videa na různých datových tocích, aby se zabránilo neočekávanému chování na straně přehrávání.

  > [!TIP]
  > Použití metody průchozí je nejekonomičtější způsob, jak provést živé vysílání.
 
* Místní živý kodér odešle datový proud s jedním přenosovým tokem do živé události, který je povolen k živému kódování pomocí služby Media Services v jednom z následujících formátů: RTMP nebo Plynulé streamování (fragmentované MP4). Živá událost pak provádí živé kódování příchozího datového proudu s jedním přenosovým tokem do datového proudu videa s více přenosovou rychlostí (adaptivní).

Tento článek popisuje testované místní živé streamování kodérů. Pokyny k ověření místního živého kodéru najdete v [tématu Ověření místního kodéru.](become-on-premises-encoder-partner.md)

Podrobné informace o živém kódování pomocí služby Media Services naleznete v [tématu Živé vysílání pomocí služby Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Požadavky na kodér

Kodéry musí podporovat TLS 1.2 při použití protokolů HTTPS nebo RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Živé kodéry, které vydezkují RTMP

Služba Media Services doporučuje používat jeden z následujících kodérů pro kódování v reálném čase, které mají RTMP jako výstup. Podporovaná schémata `rtmp://` adres `rtmps://`URL jsou nebo .

Při streamování přes RTMP zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 1935 a 1936.<br/><br/>
Při streamování přes RTMPS zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 2935 a 2936.

> [!NOTE]
> Kodéry musí při použití protokolů RTMPS podporovat protokoly TLS 1.2.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elementární Live (verze 2.14.15 a vyšší)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (verze 13.0.2 nebo vyšší z důvodu požadavku TLS 1.2)
- Telestream Wirecast S (podporuje se pouze RTMP)
- Teradek Slice 756
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hrdina 7 a Hrdina 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Živé kodéry, které výstup fragmentované MP4

Služba Media Services doporučuje jako výstup použít jeden z následujících živých kodérů, které mají vícebitové plynulé streamování (fragmentované MP4). Podporovaná schémata `http://` adres `https://`URL jsou nebo .

> [!NOTE]
> Kodéry musí při používání protokolů HTTPS podporovat protokoly TLS 1.2.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elementární Live (verze 2.14.15 a vyšší kvůli požadavku TLS 1.2)
- Envivio 4Caster C4 Gen III 
- Představte si, že komunikace Selenio MCP3
- Media Excel Hero Live a Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Pokud vysíláte živé události ve více jazycích (například jednu zvukovou stopu v angličtině a jednu zvukovou stopu ze španělštiny), můžete toho dosáhnout pomocí živého kodéru aplikace Media Excel nakonfigurovaného k odeslání živého přenosu do předávací živé události.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurace místního nastavení živého kodéru

Informace o tom, jaká nastavení jsou platná pro typ živé události, naleznete v [tématu Porovnání typů živých událostí](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Požadavky pro přehrávání

Chcete-li přehrávat obsah, musí být k dispozici datový proud zvuku i videa. Přehrávání datového proudu pouze pro video není podporováno.

### <a name="configuration-tips"></a>Konfigurační tipy

- Kdykoli je to možné, použijte kabelové internetové připojení.
- Při určování požadavků na šířku pásma zdvojnásobte datové přenosové rychlosti streamování. Ačkoli toto jednoduché pravidlo není povinné, pomáhá zmírnit dopad přetížení sítě.
- Při použití softwarových kodérů uzavřete všechny nepotřebné programy.
- Změna konfigurace kodéru poté, co začne tlačit, má negativní vliv na událost. Změny konfigurace může způsobit, že událost nestabilní. 
- Ujistěte se, že si dáváte dostatek času na nastavení události. U událostí ve velkém měřítku doporučujeme spustit instalaci hodinu před událostí.
- Použijte výstup videa H.264 a zvukového kodeku AAC.
- Ujistěte se, že je klíčový snímek nebo GOP časové zarovnání přes video vlastnosti.
- Ujistěte se, že pro každou kvalitu videa existuje jedinečný název datového proudu.
- Pro optimální výkon adaptivního přenosové rychlosti používejte přísné kódování CBR doporučené.

> [!IMPORTANT]
> Sledujte fyzický stav stroje (CPU / Memory / etc) jako nahrávání fragmentů do cloudu zahrnuje CPU a IO operace. Pokud změníte nastavení v kodéru, být některé obnovit kanály / živá událost pro změnu se projeví.

## <a name="see-also"></a>Viz také

[Živé vysílání pomocí mediálních služeb v3](live-streaming-overview.md)

## <a name="next-steps"></a>Další kroky

[Jak ověřit kodér](become-on-premises-encoder-partner.md)
