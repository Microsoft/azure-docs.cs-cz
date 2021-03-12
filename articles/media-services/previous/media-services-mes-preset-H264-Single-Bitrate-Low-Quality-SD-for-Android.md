---
title: H264 s jednou přenosovou rychlostí – nízká kvalita pro Android | Microsoft Docs
description: Téma obsahuje přehled o **H264 jedné přenosové rychlosti s nízkou kvalitou pro** předvolbu úlohy Android.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 67d3446d-e3b8-419f-bbf8-e5149c108531
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4c32fc01b2011d49dc50a67edc98227f5b31750c
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011693"
---
# <a name="h264-single-bitrate-low-quality-sd-for-android"></a>H264 Single Bitrate Low Quality SD for Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` definuje sadu přednastavení kódování, kterou můžete použít při vytváření úloh kódování. Můžete buď použít `preset name` k určení formátu, ve kterém chcete mediální soubor zakódovat. Nebo můžete vytvořit vlastní přednastavení založené na JSON nebo XML (pomocí kódování UTF-8 nebo UTF-16). Pak byste měli předat vlastní předvolbu kodéru. Seznam všech přednastavených názvů podporovaných tímto `Media Encoder Standard` kodérem najdete v tématu [Předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Toto téma ukazuje `H264 Single Bitrate Low Quality SD for Android` Předvolby ve formátu XML a JSON.  
  
 Tato předvolba vytvoří jeden soubor MP4 s přenosovou rychlostí 56 kb/s a stereofonním zvukem AAC. Podrobné informace o profilu, přenosové rychlosti, vzorkovací frekvenci atd. z této předvolby najdete v souboru XML nebo JSON definovaném níže. Vysvětlení toho, co každý prvek v těchto přednastaveních znamená, a platné hodnoty pro každý prvek naleznete v tématu [Media Encoder Standard schématu](media-services-mes-schema.md) .  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:05</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>56</Bitrate>  
          <Width>176</Width>  
          <Height>144</Height>  
          <FrameRate>12/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>2</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>56</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>HEAACV2</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>24</Bitrate>  
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
      "KeyFrameInterval": "00:00:05",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Baseline",  
          "Level": "2",  
          "Bitrate": 56,  
          "MaxBitrate": 56,  
          "BufferWindow": "00:00:05",  
          "Width": 176,  
          "Height": 144,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "12/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "HEAACV2",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 24,  
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
