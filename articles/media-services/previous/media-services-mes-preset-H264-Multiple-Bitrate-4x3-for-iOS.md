---
title: Předvolby H264 Multiple Bitrate 4 x 3 pro iOS | Dokumentace Microsoftu
description: Téma s přehledem **H264 Multiple Bitrate 4 x 3 pro iOS** přednastavení úloh.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 7f587c46-bda5-49e6-abad-203380e1d1ab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: ca88af6b62b984e0a61326cdebcb2d7411e49ed3
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998463"
---
# <a name="h264-multiple-bitrate-4x3-for-ios"></a>Předvolby H264 Multiple Bitrate 4 x 3 pro iOS
`Media Encoder Standard` definuje sadu kódování přednastavení, která můžete použít při vytváření úloh kódování. Můžete použít `preset name` zadat do formátu, který chcete zakódovat váš soubor média. Nebo můžete vytvořit vlastní JSON nebo XML na základě předvoleb (pomocí kódování UTF-8 nebo UTF-16. By pak úspěšně prošel zpracováním vlastní přednastaveny tak, aby kodér. Seznam přednastavených názvy všech podporovaných touto `Media Encoder Standard` kodér, naleznete v tématu [předvolby úloh pro Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Toto téma ukazuje, `H264 Multiple Bitrate 4x3 for iOS` přednastavení ve formátu XML a JSON.  
  
 Tato předvolba vytvoří sadu 8 soubory MP4 zarovnaný GOP od 8500 kb/s až 200 kb/s a stereo AAC zvuku. Podrobné informace o profilech s přenosovou rychlostí, vzorkování rychlost atd. to přednastavení, zkontrolujte XML nebo JSON, definovaná níže. Vysvětlení co každý prvek v tyto prostředky přednastavení a platné hodnoty pro každý prvek, najdete v článku [schéma Media Encoderu Standard](media-services-mes-schema.md) tématu.  
  
> [!NOTE]
>  Při úpravě `Width` a `Height` hodnot v rámci vrstvy, ujistěte se, poměr stran zůstala konzistentní vzhledem k aplikacím. Příklad: 1920x1080, 1280x720, 1080x576, 640x360. Neměli byste používat kombinaci poměry stran, jako například: 1280x720, 720x480, 640x360.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:03</KeyFrameInterval>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>8500</Bitrate>  
          <Width>1920</Width>  
          <Height>1440</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>High</Profile>  
          <Level>5</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>8500</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>6500</Bitrate>  
          <Width>1280</Width>  
          <Height>960</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Main</Profile>  
          <Level>3.2</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>6500</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>5000</Bitrate>  
          <Width>1280</Width>  
          <Height>960</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Main</Profile>  
          <Level>3.2</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>5000</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>3500</Bitrate>  
          <Width>960</Width>  
          <Height>720</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Main</Profile>  
          <Level>3.1</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>3500</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>1200</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3.1</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>1200</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>600</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>600</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>400</Bitrate>  
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
          <MaxBitrate>400</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>200</Bitrate>  
          <Width>400</Width>  
          <Height>300</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>200</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>HEAACV2</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>64</Bitrate>  
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
      "KeyFrameInterval": "00:00:03",  
      "H264Layers": [  
        {  
          "Profile": "High",  
          "Level": "5",  
          "Bitrate": 8500,  
          "MaxBitrate": 8500,  
          "BufferWindow": "00:00:05",  
          "Width": 1920,  
          "Height": 1440,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Main",  
          "Level": "3.2",  
          "Bitrate": 6500,  
          "MaxBitrate": 6500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 960,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Main",  
          "Level": "3.2",  
          "Bitrate": 5000,  
          "MaxBitrate": 5000,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 960,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Main",  
          "Level": "3.1",  
          "Bitrate": 3500,  
          "MaxBitrate": 3500,  
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
          "Profile": "Baseline",  
          "Level": "3.1",  
          "Bitrate": 1200,  
          "MaxBitrate": 1200,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 600,  
          "MaxBitrate": 600,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 400,  
          "MaxBitrate": 400,  
          "BufferWindow": "00:00:05",  
          "Width": 480,  
          "Height": 360,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 200,  
          "MaxBitrate": 200,  
          "BufferWindow": "00:00:05",  
          "Width": 400,  
          "Height": 300,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "HEAACV2",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 64,  
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
