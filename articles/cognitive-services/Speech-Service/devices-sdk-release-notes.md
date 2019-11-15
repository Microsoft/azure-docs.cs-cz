---
title: Dokumentace k sadě Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Poznámky k verzi obsahují protokol aktualizací, vylepšení, oprav chyb a změny v sadě Speech SDK pro zařízení. Tento článek je aktualizovaný pomocí všech vydání sady Speech Devices SDK.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: wellsi
ms.openlocfilehash: 3a74f3eb9aece3535f5505d69833a074c2dd0ed2
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091198"
---
# <a name="release-notes-speech-devices-sdk"></a>Poznámky k verzi: sada Speech SDK pro zařízení

V následujících částech jsou uvedeny změny v posledních verzích.

## <a name="speech-devices-sdk-170"></a>Sada Speech 1.7.0 SDK pro zařízení:

*   Systém Linux ARM je nyní podporován.
*   K dispozici jsou počáteční binární soubory pro Roobov2 (Linux ARM64).
*   Uživatelé systému Windows můžou použít AudioConfig. fromDefaultMicrophoneInput () nebo AudioConfig. fromMicrophoneInput (název_zařízení) k určení mikrofonu, který se má použít.
*   Velikost knihovny byla optimalizována.
*   Podpora pro rozpoznávání vícenásobného zapnutí pomocí stejného objektu pro rozpoznávání řeči nebo záměru.
*   Opravte příležitostné zablokování, ke kterému by došlo při zastavování rozpoznávání.
*   Ukázkové aplikace teď obsahují ukázkové účastníky. soubor vlastností, které demonstrují formát souboru.
*   Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.7.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Sada Speech 1.6.0 SDK pro zařízení:

- Podpora [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) v systémech Windows a Linux s [využitím Common Sample Application](https://aka.ms/sdsdk-download)
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.6.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Sada Speech 1.5.1 SDK pro zařízení:

- Zahrne do ukázkové aplikace [přepis konverzace](conversation-transcription-service.md) .
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.5.1. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0:2019 – může vydávat

- Sada Speech Devices SDK je teď GA a už není ve verzi ověřované verze Preview.
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.5.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Nová technologie klíčového slova přináší významná vylepšení kvality, viz přerušující změny.
- Nový kanál zpracování zvuku, který vylepšuje rozpoznávání daleko se všemi poli.

**Rozbíjející změny v**

- Z důvodu nové technologie klíčových slov se musí všechna klíčová slova znovu vytvořit na našem vylepšeném portálu klíčových slov. Pokud chcete úplně odebrat stará klíčová slova ze zařízení, odinstalujte starou aplikaci.
  - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0:2019-APR – verze

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.4.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1:2019-březen Release

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.3.1. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Bylo aktualizováno zpracování klíčových slov, viz přerušující změny.
- Ukázková aplikace přidá výběr jazyka pro rozpoznávání řeči i pro překlad.

**Rozbíjející změny v**

- Zjednodušená [instalace klíčového slova](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) je teď součástí aplikace a nepotřebuje samostatnou instalaci na zařízení.
- Rozpoznávání klíčového slova se změnilo a podporují se dvě události.
  - RecognizingKeyword označuje, že výsledek rozpoznávání řeči obsahuje text klíčového slova (Neověřeno).
  - RecognizedKeyword označuje, že rozpoznání klíčového slova dokončilo rozpoznávání daného klíčového slova.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0:2018. listopadu – vydání

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.1.0. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Vylepšili jsme přesnost rozpoznávání řeči v terénu pomocí našeho vylepšeného algoritmu pro zpracování zvuku.
- Ukázková aplikace přidala podporu rozpoznávání řeči v čínštině.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1:2018 – říjen – vydání

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.0.1. Další informace najdete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Vylepšení rozpoznávání řeči bude vylepšeno pomocí našeho vylepšeného algoritmu pro zpracování zvuku.
- Je opravena jedna zvuková relace průběžného rozpoznávání.

**Rozbíjející změny v**

- V této verzi představeny počet nejnovější změny. Podrobnosti o rozhraních API najdete na [této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) .
- Soubory modelu KWS nejsou kompatibilní se sadou Speech SDK pro 1.0.1. Existující soubory klíčových slov budou odstraněny po zápisu nových souborů klíčových slov do zařízení.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0:2018 – srpen Release

- Vylepšení přesnosti rozpoznávání řeči opravou chyby v kódu zpracování zvuku.
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 0.5.0. Další informace najdete v [poznámkách k verzi](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733:2018 – může vydávat

První verze Public Preview sady Cognitive Services Speech pro zařízení.
