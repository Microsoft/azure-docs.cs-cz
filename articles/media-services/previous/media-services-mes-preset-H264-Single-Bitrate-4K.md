---
title: H264 Single Bitrate 4K Media Encoder Standard přednastavení - Azure | Dokumenty společnosti Microsoft
description: Článek poskytuje přehled přednastavení úlohy Standard pro mediální kodér "H264 Single Bitrate 4K".
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 8e437aea-8193-49a0-9ff2-4fd391c80972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c1ba173f97353a5ffdd4a9b58f99f6f817f963ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895893"
---
# <a name="h264-single-bitrate-4k"></a>H264 Single Bitrate 4K
`Media Encoder Standard`definuje sadu přednastavení kódování, které můžete použít při vytváření úloh kódování. Můžete buď použít `preset name` k určení formátu, do kterého chcete mediální soubor zakódovat. Nebo můžete vytvořit vlastní přednastavení JSON nebo XML (pomocí kódování UTF-8 nebo UTF-16. Vlastní přednastavení byste pak předali kodéru. Seznam všech přednastavených názvů podporovaných `Media Encoder Standard` tímto kodérem naleznete [v tématu Přednastavení úloh pro standard kodéru médií](media-services-mes-presets-overview.md).  
  
 Toto téma `H264 Single Bitrate 4K` zobrazuje přednastavení ve formátu XML a JSON.  
  
 Toto přednastavení vytváří jeden soubor MP4 s přenosovou rychlostí 18000 kbps a stereo AAC audio. Podrobné informace o profilu, přenosovérychlosti, vzorkovací frekvenci atd. Vysvětlení, co jednotlivé prvky v těchto přednastaveních znamenají, a platné hodnoty pro každý prvek naleznete v tématu [standardního schématu kodéru médií.](media-services-mes-schema.md)  
  
> [!NOTE]
>  Měli byste dostat typ rezervované jednotky Premium s kódy 4K. Další informace naleznete v tématu [Jak změnit velikost kódování](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
```
