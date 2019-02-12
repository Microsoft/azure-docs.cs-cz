---
title: Předvolby úloh pro Media Encoderu Standard (MES) | Dokumentace Microsoftu
description: Téma poskytuje přehled a definovaných službou ukázka předvolby pro Media Encoder Standard (MES).
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 9d32397773a5ede4ddc2a27c367f2750078e28c6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998096"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Ukázka předvolby pro Media Encoderu Standard (MES)

**Media Encoder Standard** definuje sadu předdefinovaných systému kódování přednastavení, můžete použít při vytváření úloh kódování. Doporučujeme použít "adaptivní streamování" přednastavení, pokud chcete ke kódování videa pro streamování pomocí služby Media Services. Pokud zadáte tento přednastavení kodéru Media Encoder Standard budou [automaticky generovat přenosových](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Vytváření vlastních předvoleb z ukázky
Služba Media Services plně podporuje všechny hodnoty v předvolbách pro splnění potřeb kódování a požadavky na přizpůsobení. Pokud budete muset přizpůsobit přednastavení kódování, měli byste začít s některou z níže přednastavení systému, které jsou k dispozici v této části jako šablonu pro vaši vlastní konfiguraci. Vysvětlení co každý prvek v tyto prostředky přednastavení a platné hodnoty pro každý prvek, najdete v článku [schéma Media Encoderu Standard](media-services-mes-schema.md) tématu.  
  
> [!NOTE]
>  Při použití přednastavený kontext pro kóduje 4 kB, měli byste získat `S3` vyhrazený typ jednotky. Další informace najdete v tématu [postupy škálování kódování](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Video otočení výchozí nastavení v předvolby:
Při práci s kodéru Media Encoder Standard, je standardně povolená videa otočení. Pokud vaše video je zaznamenané v mobilních zařízení v režimu na výšku, pak tyto předvolby se otočí je do režimu na šířku před kódováním.
 
## <a name="available-presets"></a>K dispozici předvolby: 

 [H264 Multiple Bitrate 1080p zvuk 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [Předvolby H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a stereo AAC zvuku.  
  
 [Předvolby H264 Multiple Bitrate 16 x 9 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 8500 kb/s až 200 kb/s a stereo AAC zvuku.  
  
 [Předvolby H264 Multiple Bitrate 16 x 9 SD zvuk 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1900 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [Předvolby H264 Multiple Bitrate 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1900 kb/s až 400 kb/s a stereo AAC zvuku.  
  
 [Předvolby H264 Multiple Bitrate 4 kB zvuk 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) vytvoří sadu 12 soubory MP4 zarovnaný GOP od 20000 kb/s až 1 000 kB/s a zvuku AAC 5.1.  
  
 [Předvolby H264 Multiple Bitrate 4 KB](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) vytvoří sadu 12 soubory MP4 zarovnaný GOP od 20000 kb/s až 1 000 kB/s a stereo AAC zvuku.  
  
 [Předvolby H264 Multiple Bitrate 4 x 3 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 8500 kb/s až 200 kb/s a stereo AAC zvuku.  
  
 [Předvolby H264 Multiple Bitrate 4 x 3 SD zvuk 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [Předvolby H264 Multiple Bitrate 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a stereo AAC zvuku.  
  
 [H264 Multiple Bitrate 720p zvuk 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [Předvolby H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a stereo AAC zvuku.  
  
 [S jednou přenosovou rychlostí H264 1080p zvuk 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 6750 kb/s a zvuku AAC 5.1.  
  
 [Předvolby H264 s jednou přenosovou rychlostí 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) vytváří jeden soubor MP4 s přenosovou rychlostí 6750 kb/s a stereo AAC zvuku.  
  
 [Předvolby H264 s jednou přenosovou rychlostí 4 kB zvuk 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 18000 kb/s a zvuku AAC 5.1.  
  
 [Předvolby H264 s jednou přenosovou rychlostí 4 KB](media-services-mes-preset-H264-Single-Bitrate-4K.md) vytváří jeden soubor MP4 s přenosovou rychlostí 18000 kb/s a stereo AAC zvuku.  
  
 [Předvolby H264 s jednou přenosovou rychlostí 4 x 3 SD zvuk 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 1 800 kB/s a zvuku AAC 5.1.  
  
 [SD H264 s jednou přenosovou rychlostí 4 x 3](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) vytváří jeden soubor MP4 s přenosovou rychlostí 1 800 kB/s a stereo AAC zvuku.  
  
 [Předvolby H264 s jednou přenosovou rychlostí 16 x 9 SD zvuk 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 2 200 kb/s a zvuku AAC 5.1.  
  
 [SD H264 s jednou přenosovou rychlostí 16 x 9](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) vytváří jeden soubor MP4 s přenosovou rychlostí 2 200 kb/s a stereo AAC zvuku.  
  
 [S jednou přenosovou rychlostí H264 720p zvuk 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 4500 kb/s a zvuku AAC 5.1.  
  
 [Předvolby H264 s jednou přenosovou rychlostí 720p pro Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) přednastavení vytváří jeden soubor MP4 s přenosovou rychlostí 2 000 kB/s a AAC sterea.  
  
 [Předvolby H264 s jednou přenosovou rychlostí 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) vytváří jeden soubor MP4 s přenosovou rychlostí 4500 kb/s a stereo AAC zvuku.  
  
 [Předvolby H264 jeden s přenosovou rychlostí vysoce kvalitní SD pro Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) vytváří jeden soubor MP4 s přenosovou rychlostí 500 kb/s a stereo AAC zvuku...  
  
 [Předvolby H264 jeden s přenosovou rychlostí nízká kvalita SD pro Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) vytváří jeden soubor MP4 s přenosovou rychlostí 56 kb/s a stereo AAC zvuku.  
  
 Další informace týkající se služby Media Services kodérů najdete v tématu [kódování na vyžádání pomocí Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
