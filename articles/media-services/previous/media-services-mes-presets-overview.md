---
title: Přednastavení úloh pro standard kodéru médií (MES) | Dokumenty společnosti Microsoft
description: Toto téma poskytuje a přehled přednastavení vzorků definovaných službou pro standard kodéru médií (MES).
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 50c52369a5a957a4dd6279cac5079e2dea023106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463399"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Ukázková přednastavení pro standard kodéru médií (MES)

**Standard kodéru médií** definuje sadu přednastavení kódování předdefinovaného systému, které lze použít při vytváření úloh kódování. Pokud chcete kódovat video pro streamování pomocí mediálních služeb, doporučujeme použít přednastavení "Adaptivní streamování". Když zadáte toto přednastavení, standard kodéru médií [automaticky vygeneruje žebřík s přenosovou rychlostí](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Vytváření vlastních přednastavení ze vzorků
Mediální služby plně podporují přizpůsobení všech hodnot v přednastaveních tak, aby vyhovovaly vašim specifickým potřebám a požadavkům kódování. Pokud potřebujete přizpůsobit přednastavení kódování, měli byste začít s jedním z níže uvedených přednastavení systému, které jsou k dispozici v této části jako šablona pro vlastní konfiguraci. Vysvětlení, co jednotlivé prvky v těchto přednastaveních znamenají, a platné hodnoty pro každý prvek naleznete v tématu [standardního schématu kodéru médií.](media-services-mes-schema.md)  
  
> [!NOTE]
>  Při použití přednastavení pro kódování 4k byste `S3` měli získat typ rezervované jednotky. Další informace naleznete v tématu [Jak změnit velikost kódování](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Výchozí nastavení střídání videa v přednastaveních:
Při práci se standardem Media Encoder Standard je ve výchozím nastavení povoleno střídání videa. Pokud bylo video nahráno na mobilním zařízení v režimu Na výšku, tato přednastavení je před kódováním otočí do režimu na šířku.
 
## <a name="available-presets"></a>Dostupná přednastavení: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) vytváří sadu 8 souborů MP4 zarovnaných do GOP v rozsahu od 6000 kbps do 400 kbps a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) vytváří sadu 8 souborů MP4 zarovnaných do GOP, od 6000 kbps do 400 kbps a stereo AAC audio.  
  
 [H264 Multiple Bitrate 16x9 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) vytváří sadu 8 souborů MP4 zarovnaných gop, v rozmezí od 8500 kbps do 200 kbps a stereo AAC audio.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) vytváří sadu 5 souborů MP4 zarovnaných do GOP, od 1900 kbps do 400 kbps a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) vytváří sadu 5 GOP-zarovnaných MP4 souborů, v rozmezí od 1900 kbps do 400 kbps, a stereo AAC audio.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) vytváří sadu 12 souborů MP4 zarovnaných do GOP, od 20000 kbps do 1000 kbps a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) vytváří sadu 12 souborů MP4 zarovnaných do GOP, od 20000 kbps do 1000 kbps a stereo AAC audio.  
  
 [H264 Multiple Bitrate 4x3 pro iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) vytváří sadu 8 souborů MP4 zarovnaných gop, v rozmezí od 8500 kbps do 200 kbps a stereo AAC audio.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) vytváří sadu 5 souborů MP4 zarovnaných do GOP, od 1600 kbps do 400 kbps a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) vytváří sadu 5 GOP-zarovnaných MP4 souborů, v rozmezí od 1600 kbps do 400 kbps, a stereo AAC audio.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) vytváří sadu 6 souborů MP4 zarovnaných do GOP, od 3400 kbps do 400 kbps a zvuku AAC 5.1.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) vytváří sadu 6 souborů MP4 zarovnaných do GOP, od 3400 kbps do 400 kbps a stereo AAC audio.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 6750 kbps a zvuk EmC 5.1.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) vytváří jeden soubor MP4 s přenosovou rychlostí 6750 kbps a stereo AAC audio.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 18000 kbps a zvuk AAC 5.1.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) vytváří jeden soubor MP4 s přenosovým tokem 18000 kbps a stereo AAC audio.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 1800 kbps a zvuk AAC 5.1.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) vytváří jeden soubor MP4 s přenosovou rychlostí 1800 kbps a stereo AAC audio.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 2200 kbps a zvuk AAC 5.1.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) vytváří jeden soubor MP4 s přenosovou rychlostí 2200 kbps a stereo AAC audio.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) vytváří jeden soubor MP4 s přenosovou rychlostí 4500 kbps a zvuk EmC 5.1.  
  
 [H264 Single Bitrate 720p pro Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) přednastavení produkuje jeden soubor MP4 s přenosovou rychlostí 2000 kbps, a stereo AAC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) vytváří jeden soubor MP4 s přenosovou rychlostí 4500 kbps a stereo AAC audio.  
  
 [H264 Single Bitrate High Quality SD pro Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produkuje jeden soubor MP4 s přenosovou rychlostí 500 kbps a stereo AAC audio..  
  
 [H264 Single Bitrate Low Quality SD pro Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produkuje jeden soubor MP4 s přenosovou rychlostí 56 kbps a stereo AAC audio.  
  
 Další informace týkající se kodéry Mediálních služeb najdete [v tématu Kódování na vyžádání pomocí mediálních služeb Azure](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
