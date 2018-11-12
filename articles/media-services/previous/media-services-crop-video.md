---
title: Oříznutí videa pomocí kodéru Media Encoder Standard – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak chcete provést oříznutí videa pomocí kodéru Media Encoder Standard.
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
ms.date: 10/29/2018
ms.author: anilmur;juliako;
ms.openlocfilehash: 3a8888148e1f58cdf2b1893c624b74ec6d6c93da
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282539"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Oříznutí videa pomocí kodéru Media Encoder Standard
Media Encoder Standard (MES) slouží k oříznutí váš vstup videa. Oříznutí je proces výběru obdélníkové okna nejvyšší úrovně v rámci video a kódování pixely v rámci tohoto okna. Následující diagram pomůže ukazují proces.

![Oříznutí videa](./media/media-services-crop-video/media-services-crop-video01.png)

Předpokládejme, že máte jako vstup videa, která má rozlišení 1920 × 1080 pixelů (poměr 16:9), ale má černé pruhy (pillar polí) na levé a pravé, aby pouze časové období 4:3 nebo 1440 × 1080 pixelů obsahuje aktivní video. Můžete použití MES k oříznutí nebo upravit černé pruhy a kódování 1440 × 1080 oblasti.

Oříznutí v MES je předběžného zpracování fáze, takže oříznutí parametry v předvolba kódování platí pro původní vstupního videa. Kódování je další fází a použít nastavení šířky a výšky *předem zpracovaných* videa a ne do původního videa. Při navrhování předvolbu musíte udělat následující: (a) vyberte parametry Oříznout podle původního vstupního videa a (b) vyberte váš kódování nastavení podle oříznutý videa. Pokud se neshodují vašeho kódování nastavení oříznutý videa, výstup se tak, jak očekáváte.

[Následující](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) téma ukazuje, jak vytvořit úlohu kódování pomocí MES a zadat vlastní předvolbu pro úlohu kódování. 

## <a name="creating-a-custom-preset"></a>Vytváří se vlastní předvolba
V tomto příkladu je vidět na obrázku:

1. Původní vstup je 1920 × 1080
2. Je potřeba se ořízne na výstup 1440 × 1080, který je v rámci vstupní zarovnaný na střed
3. To znamená, že X posun (1920 – 1440) / 2 = 240 a Y posunutí nula
4. Šířku a výšku obdélníku oříznutí jsou 1440 a 1080, respektive
5. Ve fázi kódovat zadání se vytvoří tři vrstvy, jsou řešení 1440 × 1080, 960 × 720 a 480 x 360, respektive

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


## <a name="restrictions-on-cropping"></a>Omezení pro oříznutí
Oříznutí funkce je určená na ruční. Je třeba načíst vaše vstupní video do vhodné úpravy nástroj, který umožňuje vybrat snímků, které vás zajímají, umístěte kurzor myši k určení posunutí pro obdélník oříznutí, určit použitá předvolba kódování, který je vyladěný pro tohoto konkrétního videa, atd. Tato funkce není určena k povolení věci, jako je: automatické rozpoznání a odstranění černé letterbox/pillarbox ohraničení v vaše vstupní video.

Tato omezení platí pro funkci oříznutí. Pokud se nesplní tyto, můžete kódovat úloh selhat nebo neočekávaný výstup.

1. Souřadnice a velikost obdélník oříznutí se musí vejít do vstupního videa
2. Jak je uvedeno výše, šířku a výšku v kódovat nastavení musí odpovídat oříznutý videa
3. Oříznutí platí pro videa zaznamenat v režimu na šířku (to znamená nevztahuje se na videa, které jsou zaznamenány s smartphone uchovávány svisle nebo v režimu na výšku)
4. Funguje nejlépe s progresivní video zachytit pomocí Čtvereček pixelů

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Další krok
Podívejte se na Azure Media Services postupy výuky vám pomůžou získat informace o skvělých funkcí, které nabízí AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
