---
title: Jak oříznout videa pomocí standardu Media Encoder Standard – Azure | Dokumenty společnosti Microsoft
description: Oříznutí je proces výběru obdélníkového okna v rámci snímku videa a kódování pouze obrazových bodů v tomto okně. Tento článek ukazuje, jak oříznout videa pomocí standardu Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 059816284e39c65bb772bd02f066d73da624722f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887760"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Oříznutí videa pomocí kodéru Media Encoder Standard  

Ke oříznutí vstupního videa můžete použít standard kodéru médií (MES). Oříznutí je proces výběru obdélníkového okna v rámci snímku videa a kódování pouze obrazových bodů v tomto okně. Následující diagram pomáhá ilustrovat proces.

![Oříznutí videa](./media/media-services-crop-video/media-services-crop-video01.png)

Předpokládejme, že máte jako vstup video, které má rozlišení 1920x1080 pixelů (poměr stran 16: 9), ale má černé pruhy (pole pilířů) vlevo a vpravo, takže aktivní video obsahuje pouze okno 4:3 nebo 1440x1080 pixelů. Mes můžete použít k oříznutí nebo úpravě černých pruhů a kódování oblasti 1440x1080.

Oříznutí v MES je fáze předběžného zpracování, takže parametry oříznutí v přednastavení kódování platí pro původní vstupní video. Kódování je následující fáze a nastavení šířky a *výšky* se vztahuje na předem zpracované video, nikoli na původní video. Při navrhování přednastavení je třeba provést následující kroky: (a) vybrat parametry oříznutí na základě původního vstupního videa a (b) vybrat nastavení kódování na základě oříznutého videa. Pokud neodpovídáte nastavení kódování oříznutému videu, výstup nebude tak, jak očekáváte.

[Následující](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) téma ukazuje, jak vytvořit úlohu kódování s MES a jak určit vlastní přednastavení úlohy kódování. 

## <a name="creating-a-custom-preset"></a>Vytvoření vlastního přednastavení
V příkladu znázorněném na obrázku:

1. Původní vstup je 1920x1080
2. Je třeba jej oříznout na výstup 1440x1080, který je vycentrován ve vstupním rámu
3. To znamená posun X (1920 – 1440)/2 = 240 a posun Y nula
4. Šířka a výška obdélníku oříznutí jsou 1440 a 1080, resp.
5. Ve fázi kódování je třeba vytvořit tři vrstvy, jsou rozlišení 1440x1080, 960x720 a 480x360, resp.

### <a name="json-preset"></a>Přednastavení JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


## <a name="restrictions-on-cropping"></a>Omezení pěstování plodin
Funkce oříznutí má být ruční. Budete muset načíst vstupní video do vhodného editačního nástroje, který vám umožní vybrat rámce zájmu, umístit kurzor k určení odsazení pro ořezový obdélník, určit přednastavení kódování, které je vyladěno pro dané video atd. Tato funkce není určena k tomu, aby umožnila věci jako: automatická detekce a odstranění černých okrajů letterbox / pillarbox ve vstupním videu.

Následující omezení platí pro funkci oříznutí. Pokud tyto nejsou splněny, může selhat úloha kódování nebo vytvořit neočekávaný výstup.

1. Souřadnice a velikost obdélníku oříznutí se musí vejít do vstupního videa
2. Jak bylo uvedeno výše, šířka & výška v nastavení kódování musí odpovídat oříznutému videu
3. Oříznutí se vztahuje na videa pořízená v režimu na šířku (tj. nevztahuje se na videa nahraná pomocí chytrého telefonu drženého svisle nebo v režimu na výšku)
4. Funguje nejlépe s progresivním videem pořízeným čtvercovými pixely

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Další krok
Podívejte se na výukové trasy Azure Media Services, které vám pomůžou získat informace o skvělých funkcích nabízených AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
