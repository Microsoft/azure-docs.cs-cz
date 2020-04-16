---
title: Získání sady Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Služba Řeč funguje s širokou škálou zařízení a zdrojů zvuku. Nyní můžete své řečové aplikace posuzovat na další úroveň pomocí odpovídajícího hardwaru a softwaru. V tomto článku se dozvíte, jak získat přístup k sadě SDK řečových zařízení a začít vyvíjet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 756ec976c4643c1cd80552b7fa552b70fd1f5d24
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391272"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Získání sady SDK pro řečových zařízení služeb Cognitive Services

Sada Speech Devices SDK je předem vyladěná knihovna navržená pro práci s účelově vytvořenými vývojovými sadami a různými konfiguracemi mikrofonního pole.

## <a name="choose-a-development-kit"></a>Vyberte si vývojovou soupravu

|Zařízení|Specifikace|Popis|Scénáře|
|--|--|--|--|
|[URbetter T11 Vývojová rada](https://www.alibaba.com/product-detail/URBETTER-Dev-Kits-for-Microsoft-Speech_62358746402.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMI, USB kamera. <br>Linux|Sada SDK pro řečová zařízení na úrovni odvětví, která přizpůsobuje pole Microsoft Mic a podporuje rozšířené vstupně-o, jako je HDMI/Ethernet a další periferní zařízení USB|Přepis konverzace, Vzdělávání, Nemocnice, Roboti, OTT Box, Hlasové Agent, Drive Thru|
|[Roobo Inteligentní Audio Dev Kit](https://ddk.roobo.com)<br>[Instalační program](speech-devices-sdk-roobo-v1.md) / [Rychlý start](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 mic pole, ARM SOC, WIFI, audio out, iO. <br>[Android](speech-devices-sdk-android-quickstart.md)|První sada SDK pro řešení řeči, která přizpůsobí pole Microsoft Mic Array a sadu SDK pro zpracování vysoce kvalitních překladů a řeči|Přepis konverzace, inteligentní reproduktor, hlasový agent, nositelný|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Instalace](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [rychlého spuštění](speech-devices-sdk-windows-quickstart.md)![azure kinect dk](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic Array RGB a hloubkové kamery. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Vývojářská sada s pokročilými senzory umělé inteligence (AI) pro vytváření sofistikovaných modelů počítačového vidění a řeči. Kombinuje nejlepší prostorové mikrofonní pole a hloubkovou kameru ve své třídě s videokamerou a snímačem orientace – to vše v jednom malém zařízení s více režimy, možnostmi a sadami SDK pro řadu výpočetních typů.|Přepis konverzace, robotika, inteligentní budova|
|Roobo Inteligentní Audio Dev Kit 2<br>[Instalace a nastavení](speech-devices-sdk-roobo-v2.md)<br>![Roobo Inteligentní Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 mic pole, ARM SOC, WIFI, Bluetooth, iO. <br>Linux|Sada SDK pro řečová zařízení druhé generace, která poskytuje alternativní operační systém a další funkce v nákladově efektivním referenčním návrhu.|Přepis konverzace, inteligentní reproduktor, hlasový agent, nositelný|


## <a name="download-the-speech-devices-sdk"></a>Stažení sady SDK pro řečová zařízení

Stáhněte si [sadu SDK pro řečová zařízení](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme se sadou SDK pro řečová zařízení](https://aka.ms/sdsdk-quickstart)
