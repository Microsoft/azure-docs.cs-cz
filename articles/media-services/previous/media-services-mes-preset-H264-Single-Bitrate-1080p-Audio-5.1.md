---
title: H264 s jednou přenosovou rychlostí 1080p audio 5,1 | Microsoft Docs
description: Téma obsahuje přehled přednastavené úlohy **H264 s jednou přenosovou rychlostí 1080P zvuk 5,1** .
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: b42238de-2a3c-4683-ae7f-7ce19ad5162e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: cc15d0cdf397ac8d61f26d3e076662caa1dc2766
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014940"
---
# <a name="h264-single-bitrate-1080p-audio-51"></a>H264 Single Bitrate 1080p Audio 5.1


[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` definuje sadu přednastavení kódování, kterou můžete použít při vytváření úloh kódování. Můžete buď použít `preset name` k určení formátu, ve kterém chcete mediální soubor zakódovat. Nebo můžete vytvořit vlastní přednastavení založené na JSON nebo XML (pomocí kódování UTF-8 nebo UTF-16). Pak byste měli předat vlastní předvolbu kodéru. Seznam všech přednastavených názvů podporovaných tímto `Media Encoder Standard` kodérem najdete v tématu [Předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Toto téma ukazuje `H264 Single Bitrate 1080p Audio 5.1` Předvolby ve formátu XML a JSON..  
  
 Tato předvolba vytvoří jeden soubor MP4 s přenosovou rychlostí 6750 KB/s a zvukem AAC 5,1. Podrobné informace o profilu, přenosové rychlosti, vzorkovací frekvenci atd. z této předvolby najdete v souboru XML nebo JSON definovaném níže. Vysvětlení toho, co každý prvek znamená, a platné hodnoty pro každý prvek naleznete v [Media Encoder Standard schématu](media-services-mes-schema.md).  
  
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
          <Bitrate>6750</Bitrate>  
          <Width>1920</Width>  
          <Height>1080</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>6750</MaxBitrate>  
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
          "Bitrate": 6750,  
          "MaxBitrate": 6750,  
          "BufferWindow": "00:00:05",  
          "Width": 1920,  
          "Height": 1080,  
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
