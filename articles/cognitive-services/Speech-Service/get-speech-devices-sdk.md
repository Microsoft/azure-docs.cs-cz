---
title: Získání sady Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Hlasové služby pracovat s širokou škálu zařízení a zdrojů zvuku. Teď může trvat vašich aplikací rozpoznávání řeči na další úroveň díky odpovídající hardware a software. V tomto článku se dozvíte, jak získat přístup k zařízení sadou SDK pro řeč a začít s vývojem.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cff35398509c021a3d2ccf708e2705ab7e259d34
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719808"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Zařízení řeči služeb Cognitive Services SDK

Sadou SDK pro řeč zařízení je knihovna pretuned navrženy pro práci s účelovému development Kit a různé konfigurace pole mikrofon.

## <a name="choose-a-development-kit"></a>Zvolte Development kit

|Zařízení|Specifikace|Popis|Scénáře|
|--|--|--|--|
|[Roobo Smart zvuku Dev Kit](https://ddk.roobo.com)</br>[Instalační program](speech-devices-sdk-roobo-v1.md) / [rychlý Start](speech-devices-sdk-android-quickstart.md)![Roobo Smart zvuku Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic Array, ARM SOC, WIFI, Audio Out, IO. </br>[Android](speech-devices-sdk-android-quickstart.md)|První sada SDK zařízení řeči přizpůsobit Microsoft Mic Array a dopředu zpracování SDK pro vývoj vysoce kvalitních určené k transkripci řeči a scénáře|Přepis konverzace, inteligentní mluvčího, hlasové agenta na nošení|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)</br>[Instalační program](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [rychlý Start](speech-devices-sdk-windows-quickstart.md)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 RGB pole povinná kontrola úrovně důvěryhodnosti a hloubka kamery. </br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Sady pro vývojáře s senzorů pokročilé umělé inteligence (AI) pro vytváření modelů sofistikované počítačového zpracování obrazu a řeči. Kombinuje kamera pole a hloubka prostorových mikrofon ve své třídě nejlepší s kamera a orientaci snímač – vše v jednom malé zařízení s několika režimy, možnosti a sady SDK tak, aby vyhovovaly celou řadu typy výpočtů.|Budovy chytře přepis, robotika, konverzace|
|Roobo Smart zvuku Dev Kit 2![Roobo Smart zvuku Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 pole mic, ARM SOC, Wi-Fi, Bluetooth, vstupně-výstupních operací. </br>Linux|2\. generace sadou SDK pro řeč zařízení, která poskytuje alternativní operačního systému a další funkce v nákladově efektivní referenční návrh.|Přepis konverzace, inteligentní mluvčího, hlasové agenta na nošení|
|Vývojářská deska URbetter T11![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 pole mic, ARM SOC, Wi-Fi, sítě Ethernet, HDMI, fotoaparát USB. </br>Linux|Úroveň oboru zařízení sadou SDK pro řeč, které se přizpůsobí pole Microsoft Mic a podporuje rozšířené vstupně-výstupních operací, jako je například HDMI/Ethernet a další periferní zařízení USB|Přepis konverzace, vzdělávání, nemocnice, roboti OTT pole hlasové agenta, disk, až po|

## <a name="download-the-speech-devices-sdk"></a>Stáhněte si zařízení řeči SDK

Stáhněte si [řeči zařízení SDK](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme se sadou SDK pro řeč zařízení](https://aka.ms/sdsdk-quickstart)
