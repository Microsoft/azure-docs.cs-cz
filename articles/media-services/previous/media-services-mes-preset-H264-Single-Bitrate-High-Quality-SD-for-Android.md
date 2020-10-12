---
title: Vysoce kvalitní SD H264 s vysokou přenosovou rychlostí pro Android | Microsoft Docs
description: Téma obsahuje přehled o **H264i vysoce kvalitních přenosů s vysokou kvalitou pro** předvolbu úlohy Android.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4a190f24-ec6a-47e7-8bca-6294e064b15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: fa4e31c5786771ef6dba326d58136373b3dee79d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89261525"
---
# <a name="h264-single-bitrate-high-quality-sd-for-android"></a>H264 Single Bitrate High Quality SD for Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` definuje sadu přednastavení kódování, kterou můžete použít při vytváření úloh kódování. Můžete buď použít `preset name` k určení formátu, ve kterém chcete mediální soubor zakódovat. Nebo můžete vytvořit vlastní přednastavení založené na JSON nebo XML (pomocí kódování UTF-8 nebo UTF-16). Pak byste měli předat vlastní předvolbu kodéru. Seznam všech přednastavených názvů podporovaných tímto `Media Encoder Standard` kodérem najdete v tématu [Předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Toto téma ukazuje `H264 Single Bitrate High Quality SD for Android` Předvolby ve formátu XML a JSON.  
  
 Tato předvolba vytvoří jeden soubor MP4 s přenosovou rychlostí 500 kB/s a stereofonním zvukem AAC. Podrobné informace o profilu, přenosové rychlosti, vzorkovací frekvenci atd. z této předvolby najdete v souboru XML nebo JSON definovaném níže. Vysvětlení toho, co každý prvek v těchto přednastaveních znamená, a platné hodnoty pro každý prvek naleznete v tématu [Media Encoder Standard schématu](media-services-mes-schema.md) .  
  
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
          <Bitrate>500</Bitrate>  
          <Width>480</Width>  
          <Height>360</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>500</MaxBitrate>  
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
      "KeyFrameInterval": "00:00:05",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 500,  
          "MaxBitrate": 500,  
          "BufferWindow": "00:00:05",  
          "Width": 480,  
          "Height": 360,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
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
