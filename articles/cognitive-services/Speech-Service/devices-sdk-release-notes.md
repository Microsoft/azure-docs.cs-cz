---
title: Dokumentace sady SDK pro řečová zařízení
titleSuffix: Azure Cognitive Services
description: Poznámky k verzi poskytují protokol aktualizací, vylepšení, oprav chyb a změn sady SDK pro řečová zařízení. Tento článek je aktualizován s každou verzí sady SDK zařízení řeči.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371624"
---
# <a name="release-notes-speech-devices-sdk"></a>Poznámky k verzi: Řečová zařízení SDK

V následujících částech jsou uvedeny změny v nejnovějších verzích.

## <a name="speech-devices-sdk-190"></a>Řečová zařízení SDK 1.9.0:

- K dispozici jsou počáteční binární soubory pro [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64).
- Roobo v1 nyní používá Maven pro řeč SDK
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.9.0. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>Řečová zařízení SDK 1.7.0:

- Linux ARM je nyní podporován.
- K dispozici jsou počáteční binární soubory pro [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) (Linux ARM64).
- Uživatelé systému Windows mohou použít `AudioConfig.fromDefaultMicrophoneInput()` nebo `AudioConfig.fromMicrophoneInput(deviceName)` zadat mikrofon, který má být použit.
- Velikost knihovny byla optimalizována.
- Podpora pro vícenásobné rozpoznávání pomocí stejného objektu rozpoznávání řeči/záměru.
- Opravte příležitostné zablokování, ke kterému by došlo při zastavení rozpoznávání.
- Ukázkové aplikace nyní obsahují ukázkový soubor participants.properties, který ukazuje formát souboru.
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.7.0. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Řečová zařízení SDK 1.6.0:

- Podpora [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) ve Windows a Linuxu pomocí běžné [ukázkové aplikace](https://aka.ms/sdsdk-download)
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.6.0. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Řečová zařízení SDK 1.5.1:

- Zahrňte [přepis konverzace](conversation-transcription-service.md) do ukázkové aplikace.
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.5.1. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Řečová zařízení SDK 1.5.0: vydání 2019-May

- Sada SDK pro řečová zařízení je nyní ga a již není bránou náhled.
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.5.0. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Nová technologie klíčových slov přináší významné zlepšení kvality, viz Nejnovější změny.
- Nový kanál pro zpracování zvuku pro lepší rozpoznávání vzdáleného pole.

**Změny způsobující chyby**

- Vzhledem k nové klíčové slovo technologie všechna klíčová slova musí být re-created na naší vylepšené klíčové slovo portálu. Chcete-li zcela odebrat stará klíčová slova ze zařízení odinstalovat starou aplikaci.
  - ADB uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Řečová zařízení SDK 1.4.0: vydání 2019-Duben

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.4.0. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Řečová zařízení SDK 1.3.1: vydání 2019-Mar

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.3.1. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Aktualizované zpracování klíčových slov naleznete v tématu Breaking Changes.
- Ukázková aplikace přidává výběr jazyka pro rozpoznávání řeči i překlad.

**Změny způsobující chyby**

- [Instalace klíčového slova](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) byla zjednodušena, je nyní součástí aplikace a nepotřebuje samostatnou instalaci na zařízení.
- Změnilo se rozpoznávání klíčových slov a jsou podporovány dvě události.
  - `RecognizingKeyword,`označuje, že výsledek řeči obsahuje (neověřený) text klíčového slova.
  - `RecognizedKeyword`, označuje, že rozpoznávání klíčových slov bylo dokončeno s rozpoznáním daného klíčového slova.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Řečová zařízení SDK 1.1.0: vydání 2018-Nov

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.1.0. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Přesnost rozpoznávání řeči vzdáleného pole byla vylepšena pomocí našeho vylepšeného algoritmu zpracování zvuku.
- Ukázková aplikace přidala podporu rozpoznávání řeči v Čínštině.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Řečová zařízení SDK 1.0.1: vydání 2018-Oct

- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 1.0.1. Další informace naleznete v [poznámkách k verzi](https://aka.ms/csspeech/whatsnew).
- Přesnost rozpoznávání řeči se zlepší s naším vylepšeným algoritmem zpracování zvuku
- Jedna chyba nepřetržité rozpoznávání zvukové relace je opravena.

**Změny způsobující chyby**

- V této verzi je zavedena řada nejnovějších změn. Podrobnosti týkající se api naleznete na [této stránce.](https://aka.ms/csspeech/breakingchanges_1_0_0)
- Soubory modelu KWS nejsou kompatibilní s sadou Speech Devices SDK 1.0.1. Existující soubory klíčových slov budou odstraněny po zápisu nových souborů klíčových slov do zařízení.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Řečová zařízení SDK 0.5.0: vydání 2018-Aug

- Vylepšena přesnost rozpoznávání řeči opravou chyby v kódu zpracování zvuku.
- Byla aktualizována součást [sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) na verzi 0.5.0. Další informace naleznete v [poznámkách k verzi](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Řečová zařízení SDK 0.2.12733: vydání 2018-May

The first public preview release of the Cognitive Services Speech Devices SDK.
