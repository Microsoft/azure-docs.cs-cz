---
title: Dokumentace k sadě Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Poznámky k verzi – co se změnilo v posledních verzích
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: ba1db1ccba6a1849756e75c9b9f7078371da5bfb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464740"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Poznámky k verzi sady Cognitive Services Speech pro zařízení s funkcí Speech
V následujících částech jsou uvedeny změny v posledních verzích.

## <a name="speech-devices-sdk-160"></a>Sada Speech 1.6.0 SDK pro zařízení:

*   Podpora [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) v systémech Windows a Linux s [využitím Common Sample Application](https://aka.ms/sdsdk-download)
*   Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.6.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Sada Speech 1.5.1 SDK pro zařízení:

*   Zahrne do ukázkové aplikace [přepis konverzace](conversation-transcription-service.md) .
*   Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.5.1. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services 1.5.0 Speech SDK: 2019 – května Release

*   Sada Speech Devices SDK je teď GA a už není ve verzi ověřované verze Preview.
*   Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.5.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
*   Nová technologie klíčového slova přináší významná vylepšení kvality, viz přerušující změny.
*   Nový kanál zpracování zvuku, který vylepšuje rozpoznávání daleko se všemi poli.

**Průlomové změny**

*   Z důvodu nové technologie klíčových slov se musí všechna klíčová slova znovu vytvořit na našem vylepšeném portálu klíčových slov. Pokud chcete úplně odebrat stará klíčová slova ze zařízení, odinstalujte starou aplikaci.
    - ADB odinstalovat com. Microsoft. coginitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services 1.4.0 Speech SDK: 2019 – duben – vydání

* Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.4.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services Speech zařízení SDK 1.3.1:2019-březen Release

* Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.3.1. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
*   Bylo aktualizováno zpracování klíčových slov, viz přerušující změny.
*   Ukázková aplikace přidá výběr jazyka pro rozpoznávání řeči i pro překlad.

**Průlomové změny**

*   Zjednodušená [instalace klíčového slova](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) je teď součástí aplikace a nepotřebuje samostatnou instalaci na zařízení.
*   Rozpoznávání klíčového slova se změnilo a podporují se dvě události.
    - RecognizingKeyword označuje, že výsledek rozpoznávání řeči obsahuje text klíčového slova (Neověřeno).
    - RecognizedKeyword označuje, že rozpoznání klíčového slova dokončilo rozpoznávání daného klíčového slova.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Sada Cognitive Services Speech 1.1.0 SDK pro zařízení: 2018. listopadu – vydání

* Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.1.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
* Vylepšili jsme přesnost rozpoznávání řeči v terénu pomocí našeho vylepšeného algoritmu pro zpracování zvuku.
* Ukázková aplikace přidala podporu rozpoznávání řeči v čínštině.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Cognitive Services 1.0.1 Speech SDK: 2018 – říjen – vydání

* Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.0.1. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
* Vylepšení rozpoznávání řeči bude vylepšeno pomocí našeho vylepšeného algoritmu pro zpracování zvuku.  
* Je opravena jedna zvuková relace průběžného rozpoznávání.

**Průlomové změny**

* V této verzi se zavádí určitý počet nejnovějších změn. Podrobnosti o rozhraních API najdete na [této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) .
* Soubory modelu KWS nejsou kompatibilní se sadou Speech SDK pro 1.0.1. Existující soubory klíčových slov budou odstraněny po zápisu nových souborů klíčových slov do zařízení.

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services sada Speech 0.5.0 SDK pro zařízení: 2018-srpen verze

* Vylepšení přesnosti rozpoznávání řeči opravou chyby v kódu zpracování zvuku.
* Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 0.5.0. Další informace najdete v [poznámkách k verzi](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Cognitive Services 0.2.12733 Speech SDK: 2018 – května Release

První verze Public Preview sady Cognitive Services Speech pro zařízení.
