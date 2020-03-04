---
title: Kodéry živého streamování doporučují Media Services – Azure | Microsoft Docs
description: Přečtěte si o službě Live streamování pro místní kodéry Doporučené Media Services
services: media-services
keywords: kódování; kodéry; média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 43d2a56757948f6005420d2b44f9bea963613d17
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252179"
---
# <a name="recommended-live-streaming-encoders"></a>Doporučené kodéry živého streamování

V Azure Media Services [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) (kanál) představuje kanál pro zpracování obsahu živého streamování. Živá událost přijímá živé vstupní proudy jedním ze dvou způsobů.

* On-premises Live Encoder odesílá datový proud s více přenosovými rychlostmi nebo Smooth Streaming (fragmentovaný MP4) do živé události, která není povolena k provádění živého kódování pomocí Media Services. Ingestované datové proudy procházejí živými událostmi bez dalšího zpracování. Tato metoda se nazývá **předávací**. Pro živý kodér doporučujeme, aby odesílal datové proudy s více přenosovými rychlostmi, a nikoli datový proud s jednou přenosovou rychlostí, který umožňuje streamování s adaptivní přenosovou rychlostí na klienta. 

    Pokud pro průchozí živou událost používáte datové proudy s více přenosovými rychlostmi, musí být velikost videa skupinu GOP a fragmenty videa na různých přenosech se musí synchronizovat, aby nedocházelo k neočekávanému chování na straně přehrávání.

  > [!NOTE]
  > Použití předávací metody je nejúčinnější způsob, jak provádět živé streamování.
 
* On-premises Live Encoder pošle datový proud s jednou přenosovou rychlostí do živé události, která má povoleno provádět kódování v reálném čase pomocí Media Services v jednom z následujících formátů: RTMP nebo Smooth Streaming (fragmentovaný MP4). Živá událost pak provede živé kódování příchozího datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi (adaptivní).

Podrobné informace o živém kódování pomocí Media Services najdete v tématu [živé streamování s Media Services V3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Požadavky kodéru

Kodéry musí podporovat protokol TLS 1,2 při použití protokolů HTTPS nebo RTMP.

## <a name="live-encoders-that-output-rtmp"></a>Živé kodéry, které mají výstup RTMP

Služba Media Services doporučuje používat jeden z následujících kodérů pro kódování v reálném čase, které mají RTMP jako výstup. Podporovaná schémata URL jsou `rtmp://` nebo `rtmps://`.

Při streamování přes RTMP zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 1935 a 1936.<br/><br/>
Při streamování přes RTMPS zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 2935 a 2936.

> [!NOTE]
> Kodéry musí podporovat protokol TLS 1,2 při použití protokolů RTMP.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Element-Live (verze 2.14.15 a vyšší)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Wirecast (verze 13.0.2 nebo vyšší) z důvodu požadavku TLS 1,2)
- Wirecast S-Stream (podporuje se jenom RTMP)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 a Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Živé kodéry, jejichž výstupem je fragment MP4

Media Services doporučuje použít jeden z následujících živých kodérů, které mají s více přenosovými rychlostmi Smooth Streaming (fragmentované MP4) jako výstup. Podporovaná schémata URL jsou `http://` nebo `https://`.

> [!NOTE]
> Kodéry musí podporovat protokol TLS 1,2 při použití protokolů HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Element-Live (verze 2.14.15 a vyšší z důvodu požadavku TLS 1,2)
- Envivio 4Caster C4 Gen III 
- Představte si Communications Selenio MCP3
- Media Excel Hero Live a Hero 4K (UHD/HEVC)
- [FFmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Pokud vytváříte streamování živých událostí v několika jazycích (například jedna anglická zvuková stopa a jedna Španělská zvuková stopa), můžete toho dosáhnout pomocí Media Encoder Live kodéru nakonfigurovaného tak, aby odesílal živý kanál do předávací živé události.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurace nastavení místního kodéru pro Live Encoder

Informace o tom, jaká nastavení jsou platná pro váš typ živé události, najdete v tématu [porovnání typů událostí typu Live](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Požadavky na přehrávání

Aby bylo možné přehrávat obsah, musí být k dispozici zvukový a obrazový Stream. Přehrávání datového proudu, který je jen pro video, se nepodporuje.

### <a name="configuration-tips"></a>Tipy pro konfiguraci

- Kdykoli je to možné, použijte standardní kabelové internetové připojení.
- Při určování požadavků na šířku pásma Zdvojnásobte přenosové rychlosti streamování. I když není povinné, toto jednoduché pravidlo pomáhá zmírnit dopad zahlcení sítě.
- Při použití softwarových kodérů uzavřete všechny nepotřebné programy.
- Změna konfigurace kodéru po zahájení práce má na událost negativní vliv. Změny konfigurace můžou způsobit, že dojde k nestabilitě události. 
- Ujistěte se, že máte k nastavení události dostatek času. Pro vysoce škálovatelné události doporučujeme spustit nastavení hodinu před událostí.

## <a name="see-also"></a>Viz také

[Stát se místním partnerem kodéru](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>Další kroky

[Živé streamování s Media Services V3](live-streaming-overview.md)
