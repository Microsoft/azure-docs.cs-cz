---
title: Kodéry živého streamování doporučují Media Services
description: Přečtěte si o službě Live streamování pro místní kodéry Doporučené Media Services
services: media-services
keywords: kódování; kodéry; média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: bf282b3fbba0f22cd41c0420014c46ddb7c958eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955135"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Ověřená místní kodéry živého streamování

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V Azure Media Services [živá událost](/rest/api/media/liveevents) (kanál) představuje kanál pro zpracování obsahu živého streamování. Živá událost přijímá živé vstupní proudy jedním ze dvou způsobů.

* On-premises Live Encoder odesílá datový proud s více přenosovými rychlostmi nebo Smooth Streaming (fragmentovaný MP4) do živé události, která není povolena k provádění živého kódování pomocí Media Services. Ingestované datové proudy procházejí živými událostmi bez dalšího zpracování. Tato metoda se nazývá **předávací**. Pro živý kodér doporučujeme, aby odesílal datové proudy s více přenosovými rychlostmi, a nikoli datový proud s jednou přenosovou rychlostí, který umožňuje streamování s adaptivní přenosovou rychlostí na klienta. 

    Pokud pro průchozí živou událost používáte datové proudy s více přenosovými rychlostmi, musí být velikost videa skupinu GOP a fragmenty videa na různých přenosech se musí synchronizovat, aby nedocházelo k neočekávanému chování na straně přehrávání.

  > [!TIP]
  > Použití předávací metody je nejúčinnější způsob, jak provádět živé streamování.
 
* On-premises Live Encoder pošle datový proud s jednou přenosovou rychlostí do živé události, která má povoleno provádět kódování v reálném čase pomocí Media Services v jednom z následujících formátů: RTMP nebo Smooth Streaming (fragmentovaný MP4). Živá událost pak provede živé kódování příchozího datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi (adaptivní).

Tento článek popisuje ověřené místní kodéry živého streamování. Ověřování se provádí prostřednictvím samoobslužného ověření od dodavatele nebo ověření zákazníka. Microsoft Azure Media Services neprovádí úplné nebo přísné testování jednotlivých kodérů a při aktualizacích se neustále znovu neověřuje. Pokyny, jak ověřit váš místní kodér v reálném čase, najdete v tématu [ověření místního kodéru](become-on-premises-encoder-partner.md) .

Podrobné informace o živém kódování pomocí Media Services najdete v tématu [živé streamování s Media Services V3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Požadavky kodéru

Kodéry musí podporovat protokol TLS 1,2 při použití protokolů HTTPS nebo RTMP.

## <a name="live-encoders-that-output-rtmp"></a>Živé kodéry, které mají výstup RTMP

Služba Media Services doporučuje používat jeden z následujících kodérů pro kódování v reálném čase, které mají RTMP jako výstup. Podporovaná schémata URL jsou `rtmp://` nebo `rtmps://` .

Při streamování přes RTMP zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 1935 a 1936.<br/><br/>
Při streamování přes RTMPS zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 2935 a 2936.

> [!NOTE]
> Kodéry musí podporovat protokol TLS 1,2 při použití protokolů RTMPs.

- Adobe Flash Media Live Encoder 3.2
- [Antix digitální](http://www.antixdigital.com/) StreamZ Live (dřív představa Communications SelenioFlex Live)
- [Blackmagic ATEM Mini a ATEM Mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Element-Live (verze 2.14.15 a vyšší)
- [FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 a Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Wirecast (verze 13.0.2 nebo vyšší) z důvodu požadavku TLS 1,2)
- Wirecast S-Stream (podporuje se jenom RTMP. Nepodporují se žádné RTMP v důsledku nedostatku TLS 1.2 +).
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> Výše uvedený seznam kodérů je pouze seznam doporučení. Společnost Microsoft netestuje nebo neověřuje kodéry na základě nepřetržitého provozu a aktualizace nebo zásadní změny mohou být zavedeny pomocí dodavatelů kodéru nebo open source projektů, které by mohly přerušit kompatibilitu. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Živé kodéry, které zapsaly fragmenty MP4 (Smooth Streaming ingestování)

Media Services doporučuje použít jeden z následujících živých kodérů, které mají s více přenosovými rychlostmi Smooth Streaming (fragmentované MP4) jako výstup. Podporovaná schémata URL jsou `http://` nebo `https://` .

> [!NOTE]
> Kodéry musí podporovat protokol TLS 1,2 při použití protokolů HTTPS.

- Ateme TITAN Live
- [Antix digitální](http://www.antixdigital.com/) StreamZ Live (dřív představa Communications SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Element-Live (verze 2.14.15 a vyšší z důvodu požadavku TLS 1,2)
- Envivio 4Caster C4 Gen III 
- [FFmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live a Hero 4K (UHD/HEVC)

> [!TIP]
>  Pokud vytváříte streamování živých událostí v několika jazycích (například jedna anglická zvuková stopa a jedna Španělská zvuková stopa), můžete toho dosáhnout pomocí Media Encoder Live kodéru nakonfigurovaného tak, aby odesílal živý kanál do předávací živé události.

> [!WARNING]
> Výše uvedený seznam kodérů je pouze seznam doporučení. Společnost Microsoft netestuje ani neověřuje kodéry na základě neustálého spuštění a podpora nebo chyby mohou být zavedeny pomocí dodavatelů kodéru nebo open source projektů, které přeruší kompatibilitu. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurace nastavení místního kodéru pro Live Encoder

Informace o tom, jaká nastavení jsou platná pro váš typ živé události, najdete v tématu [porovnání typů událostí typu Live](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Požadavky pro přehrávání

Aby bylo možné přehrávat obsah, musí být k dispozici zvukový a obrazový Stream. Přehrávání datového proudu, který je jen pro video, se nepodporuje.

### <a name="configuration-tips"></a>Konfigurační tipy

- Kdykoli je to možné, použijte kabelové internetové připojení.
- Při určování požadavků na šířku pásma Zdvojnásobte přenosové rychlosti streamování. I když není povinné, toto jednoduché pravidlo pomáhá zmírnit dopad zahlcení sítě.
- Při používání softwarových kodérů zavřete všechny nepotřebné programy.
- Změna konfigurace kodéru po zahájení práce má na událost negativní vliv. Změny konfigurace můžou způsobit, že dojde k nestabilitě události. 
- Vždy otestujte a ověřte novější verze kodéru softwaru, aby se zajistila kompatibilita s Azure Media Services. Společnost Microsoft neověřuje znovu kodéry v tomto seznamu a většina ověřování provádí výrobci softwaru přímo jako "samy Certifikac".
- Ujistěte se, že máte k nastavení události dostatek času. Pro vysoce škálovatelné události doporučujeme spustit nastavení hodinu před událostí.
- Použijte výstup videa H. 264 a zvukový kodek AAC-LC.
- Platí pro podporovaná rozlišení a kmitočty snímků pro typ živé události, které vysíláte (například 60fps je aktuálně zamítnutá).
- Zajistěte, aby existoval klíčový snímek nebo skupinu GOP dočasné zarovnání napříč kvalitou videa.
- Ujistěte se, že pro každou kvalitu videa existuje jedinečný název datového proudu.
- Pro optimální výkon adaptivní přenosové rychlosti doporučujeme použít striktní kódování CBR.

> [!IMPORTANT]
> Sledujte fyzickou podmínku počítače (CPU/paměť/atd.), protože nahrávání fragmentů do cloudu zahrnuje vstupně-výstupní operace procesoru a vstupně-výstupních operací. Pokud v kodéru změníte nějaká nastavení, nezapomeňte nastavit, aby se změna projevila v kanálech/živé události.

## <a name="see-also"></a>Viz také

[Živé streamování s Media Services V3](live-streaming-overview.md)

## <a name="next-steps"></a>Další kroky

[Jak ověřit kodér](become-on-premises-encoder-partner.md)
