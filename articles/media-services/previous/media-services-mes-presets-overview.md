---
title: Přednastavení úloh pro Media Encoder Standard (status) | Microsoft Docs
description: Téma obsahuje přehled předdefinovaných vzorů definovaných službou pro Media Encoder Standard (status).
author: IngridAtMicrosoft
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 74e557aa02f5ab83828262209653bf8a78fdd2d0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011642"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Přednastavené ukázky pro Media Encoder Standard (status)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

**Media Encoder Standard** definuje sadu předem definovaných předvoleb kódování systému, které můžete použít při vytváření úloh kódování. Pokud chcete zakódovat video pro streamování pomocí Media Services, doporučuje se použít přednastavení adaptivního streamování. Když zadáte tuto předvolbu, Media Encoder Standard [automaticky vygeneruje žebřík přenosové rychlosti](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Vytváření vlastních přednastavení z ukázek
Media Services plně podporuje přizpůsobení všech hodnot v předvolbách, aby splňovaly vaše specifické požadavky na kódování a požadavky. Pokud potřebujete přizpůsobit předdefinované kódování, měli byste začít s jedním z níže uvedených systémů, které jsou v této části k dispozici jako šablona pro vlastní konfiguraci. Vysvětlení toho, co každý prvek v těchto přednastaveních znamená, a platné hodnoty pro každý prvek naleznete v tématu [Media Encoder Standard schématu](media-services-mes-schema.md) .  
  
> [!NOTE]
>  Při použití předvolby pro kódování 4k byste měli získat `S3` typ rezervované jednotky. Další informace najdete v tématu [Jak škálovat kódování](./media-services-scale-media-processing-overview.md).  

#### <a name="video-rotation-default-setting-in-presets"></a>Výchozí nastavení rotace videa v předvolbách:
Při práci s Media Encoder Standard je ve výchozím nastavení zapnuté otočení videa. Pokud se vaše video nahrálo v režimu na výšku v mobilním zařízení, pak se tyto předvolby před kódováním otočí do režimu na šířku.
 
## <a name="available-presets"></a>Dostupné předvolby: 

 [H264 s více přenosovými rychlostmi 1080P zvuk 5,1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) vytvoří sadu souborů MP4 s 8 skupinu GOP, od 6000 KB/s až 400 KB/s a zvukem ve formátu AAC 5,1.  
  
 [H264 s více přenosovými rychlostmi 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) se vytvoří sada souborů MP4 s 8 skupinu GOP, od 6000 do 400 KB/s a stereofonního zvuku AAC.  
  
 [H264 s více přenosovými rychlostmi 16x9 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) vznikne sadu 8 souborů MP4 zarovnaných na skupinu GOP, od 8500 do 200 KB/s a stereofonního zvuku AAC.  
  
 [H264 s více přenosovými rychlostmi 16X9 SD 5,1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) vytvoří sadu souborů MP4 s ZAROVNÁNÍm skupinu GOP v rozsahu od 1900 KB/s až 400 KB/s a zvukem ve formátu AAC 5,1.  
  
 [H264 s více přenosovými rychlostmi 16X9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) vytvoří sadu souborů MP4 zarovnaných na skupinu GOP, od 1900 do 400 KB/s a stereofonního zvuku AAC.  
  
 [H264 s více přenosovými rychlostmi 4k zvuk 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) vytvoří sadu souborů MP4 s 15 skupinu GOPy v rozsahu od 20000 KB/s až 1000 kb/s a zvukem ve formátu AAC 5,1.  
  
 [H264 s více přenosovými rychlostmi 4k](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) vytvoří sadu souborů MP4 s ZAROVNÁNÍm skupinu GOP v rozsahu od 20000 do 1000 kb/s a stereofonního zvuku AAC.  
  
 [H264 s více přenosovými rychlostmi 4x3 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) vznikne sadu 8 souborů MP4 zarovnaných na skupinu GOP, od 8500 do 200 KB/s a stereofonního zvuku AAC.  
  
 [H264 s více přenosovými rychlostmi 4X3 SD 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) vytvoří sadu souborů MP4 s ZAROVNÁNÍm skupinu GOP v rozsahu od 1600 KB/s až 400 KB/s a zvukem ve formátu AAC 5,1.  
  
 [H264 s více přenosovými rychlostmi 4X3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) vytvoří sadu souborů MP4 zarovnaných na skupinu GOP, od 1600 do 400 KB/s a stereofonního zvuku AAC.  
  
 [H264 s více přenosovými rychlostmi – zvuk 5,1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) vytvoří sadu souborů MP4 s skupinu GOP zarovnanými do rozsahu od 3400 KB/s až 400 KB/s a zvukem AAC 5,1.  
  
 [H264 Multiple přenosu 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) vytvoří sadu souborů MP4 s skupinu GOP zarovnanými do rozsahu od 3400 do až 400 KB/s a stereofonního zvuku AAC.  
  
 [H264 s jednou přenosovou rychlostí 1080P audio 5,1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 6750 KB/s a AAC 5,1 zvuk.  
  
 [H264 s jednou přenosovou rychlostí](media-services-mes-preset-H264-Single-Bitrate-1080p.md) : vytvoří jeden soubor MP4 s přenosovou rychlostí 6750 KB/s a stereofonním zvukem AAC.  
  
 [H264 s jednou přenosovou rychlostí 4k zvuk 5,1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 18000 KB/s a AAC 5,1 zvuk.  
  
 [H264 s jednou přenosovou rychlostí 4k](media-services-mes-preset-H264-Single-Bitrate-4K.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 18000 KB/s a stereofonním zvukem AAC.  
  
 [H264 s jednou přenosovou rychlostí 4X3 SD 5,1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 1800 KB/s a AAC 5,1 zvuk.  
  
 [H264 s jednou přenosovou rychlostí 4X3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 1800 KB/s a stereofonním zvukem AAC.  
  
 [H264 s jednou přenosovou rychlostí 16X9 SD 5,1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 2200 KB/s a AAC 5,1 zvuk.  
  
 [H264 s jednou přenosovou rychlostí 16X9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 2200 KB/s a stereofonním zvukem AAC.  
  
 [H264 s jednou přenosovou rychlostí 720p – zvukový 5,1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 4500 KB/s a AAC 5,1 zvuk.  
  
 [H264 Single přenosová rychlost 720p pro Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 2000 KB/s a stereo AAC.  
  
 [H264 Single přenosová rychlost](media-services-mes-preset-H264-Single-Bitrate-720p.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 4500 KB/s a stereofonním zvukem AAC.  
  
 [H264 s jednou přenosovou rychlostí – vysoce kvalitní SD pro Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 500 kB/s a stereofonním zvukem AAC.  
  
 [H264 s jednou přenosovou rychlostí: nízká kvalita SD pro Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) vytvoří jeden soubor MP4 s přenosovou rychlostí 56 kb/s a stereofonním zvukem AAC.  
  
 Další informace týkající se Media Services Encoder najdete v tématu [kódování na vyžádání pomocí Azure Media Services](./media-services-encode-asset.md).
