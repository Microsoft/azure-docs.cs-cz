---
title: Předvolby H264 Jeden s přenosovou rychlostí 4 kB zvuk 5.1 | Dokumentace Microsoftu
description: Téma s přehledem **H264 s jednou přenosovou rychlostí 4 kB zvuk 5.1** přednastavení úloh.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 72cb95ac-2cd6-4ef4-9938-8f22cea04920
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: b2dab421d52f822378309b99166ce4441999a727
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250117"
---
# <a name="h264-single-bitrate-4k-audio-51"></a>Předvolby H264 S jednou přenosovou rychlostí 4 kB zvuku 5.1
`Media Encoder Standard` definuje sadu kódování přednastavení, která můžete použít při vytváření úloh kódování. Můžete použít `preset name` zadat do formátu, který chcete zakódovat váš soubor média. Nebo můžete vytvořit vlastní JSON nebo XML na základě předvoleb (pomocí kódování UTF-8 nebo UTF-16. By pak úspěšně prošel zpracováním vlastní přednastaveny tak, aby kodér. Seznam přednastavených názvy všech podporovaných touto `Media Encoder Standard` kodér, naleznete v tématu [předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Toto téma ukazuje, `H264 Single Bitrate 4K Audio 5.1` přednastavení (ve formátu XML a JSON).  
  
 Tato předvolba vytváří jeden soubor MP4 s přenosovou rychlostí 18000 kb/s a zvuku AAC 5.1. Podrobné informace o profilech s přenosovou rychlostí, vzorkování rychlost atd. to přednastavení, zkontrolujte XML nebo JSON, definovaná níže. Vysvětlení znamená, co každý prvek a platné hodnoty pro každý prvek, najdete v článku [schéma Media Encoderu Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  Měli byste obdržet rezervovaných jednotek Premium kóduje typ s 4 kB. Další informace najdete v tématu [postupy škálování kódování](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">  
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
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
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
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
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
