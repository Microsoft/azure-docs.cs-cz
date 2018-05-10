---
title: Oříznutí videa s Media Encoder Standard - Azure | Microsoft Docs
description: Tento článek ukazuje, jak oříznout videa pomocí procesoru Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 7628f674-2005-4531-8b61-d7a4f53e46ba
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2017
ms.author: anilmur;juliako;
ms.openlocfilehash: 2592316481c9e265fbae20b832beb21ae4905b14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="crop-videos-with-media-encoder-standard"></a>Oříznutí videa pomocí kodéru Media Encoder Standard
Chcete-li oříznout váš vstup videa můžete Media Encoder Standard (MES). Oříznutí je proces kódování pixelů v rámci dané okno a výběrem obdélníkový okno v rámci videa. Následující diagram pomáhá proces znázorněn.

![Oříznutí video](./media/media-services-crop-video/media-services-crop-video01.png)

Předpokládejme, že máte jako vstupní video, které má rozlišení 1920 × 1080 pixelů (poměr stran 16:9), ale má černé pruhy (pilíře polí) v levém dolním a pravém, tak, aby pouze okno 4:3 nebo 1440 × 1080 pixelů obsahuje aktivní video. Můžete pomocí MES oříznout nebo upravte si černé pruhy a kódování 1440 × 1080 oblast.

Oříznutí v MES je předem zpracování fázi, takže oříznutí parametrů v kódování přednastavených týkají původního vstupní video. Kódování je další fázi a použít nastavení šířky a výšky *předem zpracovaných* video a nikoli k původní video. Při navrhování vaší přednastavených musíte udělat následující: (a) vyberte ořezové parametry podle původní vstupní video a (b) vyberte vaše kódování nastavení podle oříznutý video. Pokud se neshodují vaše kódování nastavení oříznutý video, výstup nebudou podle očekávání.

[Následující](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) téma ukazuje, jak vytvořit úlohu kódování s MES a jak určit vlastní přednastavení kódování úlohy. 

## <a name="creating-a-custom-preset"></a>Vytváření vlastní předvolby
V příkladu v diagramu:

1. Původní vstup je 1920 × 1080
2. Musí se výstup 1440 × 1080, který je umístěn na střed v rámci vstupní oříznut
3. To znamená posunem X (1920 – 1440) / 2 = 240 a Y posunutí nula
4. Šířka a výška rámečku ořezové jsou 1440 a 1080, v uvedeném pořadí
5. Ve fázi kódovat požádejte je vytvořit tři vrstvy, jsou rozlišení 1440 × 1080 960 × 720 a 480 x 360, v uvedeném pořadí

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


## <a name="restrictions-on-cropping"></a>Omezení oříznutí
Oříznutí funkce měl by být ručně. Museli byste se načíst vaše vstupní video do vhodný úpravy nástroj, který umožňuje vybrat rámců, které vás zajímají, umístěte kurzor k určení posunutí pro rámečku oříznutí, určete kódování přednastavení, která je přizpůsobená pro tuto konkrétní video, atd. Tato funkce není určena k povolení věcmi, jako jsou: automatické zjišťování a odebrání ohraničení černým letterbox/pillarbox v váš vstup videa.

Následující omezení se vztahují na funkci oříznutí. Pokud nejsou splněny tyto, kódovat úkolů selhat nebo vytvoření neočekávané výstupu.

1. Souřadnic a velikosti obdélníku ořezové mít nevejde se do vstupní video
2. Jak je uvedeno nahoře, šířku a výšku v nastavení kódovat mít tak, aby odpovídaly oříznutý video
3. Oříznutí platí pro videa zaznamenat v režimu na šířku (tj. nevztahuje se na videa s smartphone zaznamenaná uchovávat svisle nebo v režimu na výšku)
4. Funguje nejlépe s progresivní video zachytit pomocí odmocnina pixelů

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Další krok
V tématu Azure Media Services kurzů můžete další informace o skvělé funkce, které nabízí AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
