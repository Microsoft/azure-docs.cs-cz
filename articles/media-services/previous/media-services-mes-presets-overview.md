---
title: Přednastavení úloh pro Media Encoder Standard (MES) | Microsoft Docs
description: Téma nabízí a Přehled služby definované ukázka přednastavení pro Media Encoder Standard (MES).
author: Juliako
manager: cfow
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: juliako
ms.openlocfilehash: 4c518771d95dcb571da294f8a752e7b5740ef19f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Ukázka přednastavení pro Media Encoder Standard (MES)

**Media Encoder Standard** definuje sadu předdefinovaných systému kódování přednastavení můžete použít při vytváření úlohy kódování. Doporučujeme používat "Adaptivní streamování" předvolby, pokud chcete zakódovat video pro streamování pomocí služby Media Services. Pokud zadáte přednastavené, budou Media Encoder Standard [automaticky generovat žebříku přenosovou rychlostí](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Vytváření vlastních předvoleb z ukázky
Služba Media Services plně podporuje přizpůsobení všech hodnot v přednastavení ke splnění vašich potřeb kódování a požadavky. Pokud potřebujete přizpůsobit předvolby kódování, měli byste začít s jedním z níže přednastavení systému, které jsou uvedeny v této části jako šablona pro vaši vlastní konfiguraci. Vysvětlení co každý prvek v těchto přednastavení znamená a platné hodnoty pro každý element, najdete v článku [Media Encoder Standard schématu](media-services-mes-schema.md) tématu.  
  
> [!NOTE]
>  Pokud používáte jedno z přednastavení pro kóduje 4k, měli byste obdržet `S3` vyhrazený typ jednotky. Další informace najdete v tématu [postup škálování kódování](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Video otočení výchozí nastavení v přednastavení:
Při práci s Media Encoder Standard, videa otočení ve výchozím nastavení zapnutá. Pokud byla zaznamenána videa na mobilní zařízení v režimu na výšku, pak tato přednastavení bude otočit je do režimu na šířku před kódování.
 
## <a name="available-presets"></a>K dispozici přednastavení: 

 [H264 Multiple Bitrate 1080p zvuk 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 6000 kb/s až 400 kb/s a stereofonní zvuk AAC.  
  
 [H264 Multiple Bitrate 16 x 9 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 8500 kb/s až 200 kb/s a stereofonní zvuk AAC.  
  
 [Zvuk SD H264 Multiple Bitrate 16 x 9 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1900 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1900 kb/s až 400 kb/s a stereofonní zvuk AAC.  
  
 [Zvuk H264 Multiple Bitrate 4K 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) vytvoří sadu 12 soubory MP4 zarovnaný GOP od 20000 kb/s až 1 000 kB/s a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) vytvoří sadu 12 soubory MP4 zarovnaný GOP od 20000 kb/s až 1 000 kB/s a stereofonní zvuk AAC.  
  
 [H264 Multiple Bitrate 4 x 3 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) vytvoří sadu 8 soubory MP4 zarovnaný GOP od 8500 kb/s až 200 kb/s a stereofonní zvuk AAC.  
  
 [Zvuk SD H264 Multiple Bitrate 4 x 3 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) vytvoří sadu 5 soubory MP4 zarovnaný GOP od 1600 kb/s až 400 kb/s a stereofonní zvuk AAC.  
  
 [Zvuk 720p H264 Multiple Bitrate 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) vytvoří sadu 6 soubory MP4 zarovnaný GOP od 3400 kb/s až 400 kb/s a stereofonní zvuk AAC.  
  
 [Jednou přenosovou rychlostí H264 1080p zvuk 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 6750 kb/s a zvuku AAC 5.1.  
  
 [H264 jeden Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 6750 kb/s a stereofonní zvuk AAC.  
  
 [Zvuk H264 jednou přenosovou rychlostí 4K 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 18000 kb/s a zvuku AAC 5.1.  
  
 [H264 jednou přenosovou rychlostí 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 18000 kb/s a stereofonní zvuk AAC.  
  
 [Zvuk SD H264 jednou přenosovou rychlostí 4 x 3 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 1 800 kB/s a zvuku AAC 5.1.  
  
 [H264 jednou přenosovou rychlostí 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 1 800 kB/s a stereofonní zvuk AAC.  
  
 [Zvuk SD H264 jednou přenosovou rychlostí 16 x 9 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 2200 kb/s a zvuku AAC 5.1.  
  
 [H264 jednou přenosovou rychlostí 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 2200 kb/s a stereofonní zvuk AAC.  
  
 [Jednou přenosovou rychlostí H264 720p zvuk 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 4500 kb/s a zvuku AAC 5.1.  
  
 [H264 jednou přenosovou rychlostí 720p pro Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) přednastavených vytváří jednoho souboru MP4 s přenosovou rychlostí 2000 kb/s a stereo AAC.  
  
 [H264 jednou přenosovou rychlostí 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 4500 kb/s a stereofonní zvuk AAC.  
  
 [H264 jeden přenosovou rychlostí vysoké kvality SD pro Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 500 kb/s a stereofonní zvuk AAC...  
  
 [H264 jeden přenosovou rychlostí nižší kvalita SD pro Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) vytváří jednoho souboru MP4 s přenosovou rychlostí 56 kb/s a stereofonní zvuk AAC.  
  
 Další informace související s kodéry Media Services najdete v tématu [kódování na vyžádání pomocí Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
