---
title: Získání sady Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Služba Speech funguje se širokou škálou zařízení a zdrojů zvuku. Nyní můžete své aplikace pro rozpoznávání řeči přenést na další úroveň se shodným hardwarem a softwarem. V tomto článku se dozvíte, jak získat přístup k sadě Speech Devices SDK a začít vyvíjet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 0bc1a7b5e443de0c1a95fa209d2e5a280cf28ef2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87385836"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Získání sady Cognitive Services Speech pro zařízení s Androidem

Sada Speech Devices SDK je předpřipravená knihovna navržená tak, aby fungovala s účelově sestavenými vývojovými sadami a různými konfiguracemi polí mikrofonu.

## <a name="choose-a-development-kit"></a>Zvolit vývojovou sadu

|Zařízení|Specifikace|Description|Scénáře|
|--|--|--|--|
|[Urbetter dev Kit](http://www.urbetter.com/products_56/278.html) ![ URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 pole Mic, ARM SOC, WIFI, Ethernet, HDMI, USB kamera. <br>Linux|Sada SDK zařízení pro rozpoznávání řeči v oboru, která upravuje Microsoft MIC Array a podporuje rozšířené vstupně-výstupní operace, jako je například HDMI/Ethernet a větší množství periferních zařízení USB <br> [Kontaktujte Urbetter](http://www.urbetter.com/products_56/278.html)|Přepis konverzace, vzdělávání, nemocnice, roboty, OTT box, hlasový agent, jednotka s|
|[Sada Roobo Smart audio dev Kit](http://ddk.roobo.com)<br>[Nastavení](speech-devices-sdk-roobo-v1.md)  /  [Rychlý Start](speech-devices-sdk-android-quickstart.md) ![ Sada Roobo Smart audio dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 pole Mic, ARM SOC, Wi-Fi, audio výstup, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|První sada Speech Devices SDK pro přizpůsobení služby Microsoft MIC Array a front-Processing SDK pro vývoj vysoce kvalitních scénářů a řeči|Přepis konverzace, inteligentní mluvčí, hlasový agent, přenosném|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Nastavení](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk)  /  [Rychlý Start](speech-devices-sdk-windows-quickstart.md) ![ Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 videokamery mic a hloubkové kamery <br>[Systém Windows](speech-devices-sdk-windows-quickstart.md) / [Linux](speech-devices-sdk-linux-quickstart.md)|Vývojářská sada s pokročilými senzory umělé Intelligence (AI) pro vytváření sofistikovaných modelů počítačové vize a řeči. Kombinuje nejvhodnější prostorové pole mikrofonu a hloubkovou kameru s videokamerou a snímačem orientace – to vše v jednom malém zařízení s více režimy, možností a sad SDK, aby se vešlo do rozsahu výpočetních typů.|Přepis konverzace, roboty, inteligentní budova|
|Roobo Smart audio dev Kit 2<br>[Nastavení](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart audio dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 pole Mic, ARM SOC, Wi-Fi, Bluetooth, IO. <br>Linux|Sada SDK pro zařízení se hlasovým rozpoznáváním, která poskytuje alternativní operační systém a další funkce, a to s ohledem na cenově výhodné návrh.|Přepis konverzace, inteligentní mluvčí, hlasový agent, přenosném|


## <a name="download-the-speech-devices-sdk"></a>Stáhnout sadu Speech SDK pro zařízení

Stáhněte si [sadu Speech SDK pro zařízení](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme se sadou Speech SDK pro zařízení](https://aka.ms/sdsdk-quickstart)
