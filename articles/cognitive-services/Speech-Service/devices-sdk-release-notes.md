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
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: af66f2ec56551a5177cd9323d216e9bf4b0c41be
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021843"
---
# <a name="release-notes-speech-devices-sdk"></a>Poznámky k verzi: sada Speech SDK pro zařízení

V následujících částech jsou uvedeny změny v posledních verzích.

## <a name="speech-devices-sdk-1110"></a>Sada Speech 1.11.0 SDK pro zařízení:

- Podpora [libovolného pole mikrofonu geometrií](how-to-devices-microphone-array-configuration.md) a nastavení pracovního úhlu prostřednictvím [konfiguračního souboru](https://aka.ms/sdsdk-micarray-json).
- Podpora pro [URBETTER DDK](http://www.urbetter.com/products_56/278.html).
- Vydané binární soubory pro [mluvčí GGEC](https://aka.ms/sdsdk-download-speaker) , který se používá v našem [příkladu hlasového pomocníka](https://aka.ms/sdsdk-speaker).
- Vydané binární soubory pro [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) a [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) pro maliny PI a podobná zařízení.
- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.11.0. Další informace najdete v [poznámkách k verzi](./releasenotes.md).

## <a name="speech-devices-sdk-190"></a>Sada Speech 1.9.0 SDK pro zařízení:

- K dispozici jsou počáteční binární soubory pro [URBETTER DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64).
- Roobo v1 nyní používá Maven pro sadu Speech SDK
- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.9.0. Další informace najdete v [poznámkách k verzi](./releasenotes.md).

## <a name="speech-devices-sdk-170"></a>Sada Speech 1.7.0 SDK pro zařízení:

- Systém Linux ARM je nyní podporován.
- K dispozici jsou počáteční binární soubory pro [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) (Linux ARM64).
- Uživatelé systému Windows můžou použít `AudioConfig.fromDefaultMicrophoneInput()` nebo `AudioConfig.fromMicrophoneInput(deviceName)` k určení mikrofonu, který se má použít.
- Velikost knihovny byla optimalizována.
- Podpora pro rozpoznávání vícenásobného zapnutí pomocí stejného objektu pro rozpoznávání řeči nebo záměru.
- Opravte občasné problémy, kdy proces přestal při zastavování rozpoznávání reagovat.
- Ukázkové aplikace teď obsahují ukázkové účastníky. soubor vlastností, které demonstrují formát souboru.
- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.7.0. Další informace najdete v [poznámkách k verzi](./releasenotes.md).

## <a name="speech-devices-sdk-160"></a>Sada Speech 1.6.0 SDK pro zařízení:

- Podpora [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) v systémech Windows a Linux s [využitím Common Sample Application](./speech-devices-sdk.md)
- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.6.0. Další informace najdete v [poznámkách k verzi](./releasenotes.md).

## <a name="speech-devices-sdk-151"></a>Sada Speech 1.5.1 SDK pro zařízení:

- Zahrne do ukázkové aplikace [přepis konverzace](./conversation-transcription.md) .
- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.5.1. Další informace najdete v [poznámkách k verzi](./releasenotes.md).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0:2019 – může vydávat

- Sada Speech Devices SDK je teď GA a už není ve verzi ověřované verze Preview.
- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.5.0. Další informace najdete v [poznámkách k verzi](./releasenotes.md).
- Nová technologie klíčového slova přináší významná vylepšení kvality, viz přerušující změny.
- Nový kanál zpracování zvuku, který vylepšuje rozpoznávání daleko se všemi poli.

**Změny způsobující chyby**

- Z důvodu nové technologie klíčových slov se musí všechna klíčová slova znovu vytvořit na našem vylepšeném portálu klíčových slov. Pokud chcete úplně odebrat stará klíčová slova ze zařízení, odinstalujte starou aplikaci.
  - ADB odinstalovat com. Microsoft. cognitiveservices Account. Speech. Samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0:2019-APR – verze

- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.4.0. Další informace najdete v [poznámkách k verzi](./releasenotes.md).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1:2019-březen Release

- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.3.1. Další informace najdete v [poznámkách k verzi](./releasenotes.md).
- Bylo aktualizováno zpracování klíčových slov, viz přerušující změny.
- Ukázková aplikace přidá výběr jazyka pro rozpoznávání řeči i pro překlad.

**Změny způsobující chyby**

- Zjednodušená [instalace klíčového slova](./custom-keyword-basics.md) je teď součástí aplikace a nepotřebuje samostatnou instalaci na zařízení.
- Rozpoznávání klíčového slova se změnilo a podporují se dvě události.
  - `RecognizingKeyword,` indikuje, že výsledek rozpoznávání řeči obsahuje text klíčového slova (Neověřeno).
  - `RecognizedKeyword`označuje, že rozpoznávání klíčových slov dokončilo rozpoznání daného klíčového slova.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0:2018. listopadu – vydání

- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.1.0. Další informace najdete v [poznámkách k verzi](./releasenotes.md).
- Vylepšili jsme přesnost rozpoznávání řeči v terénu pomocí našeho vylepšeného algoritmu pro zpracování zvuku.
- Ukázková aplikace přidala podporu rozpoznávání řeči v čínštině.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1:2018 – říjen – vydání

- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 1.0.1. Další informace najdete v [poznámkách k verzi](./releasenotes.md).
- Vylepšení rozpoznávání řeči bude vylepšeno pomocí našeho vylepšeného algoritmu pro zpracování zvuku.
- Je opravena jedna zvuková relace průběžného rozpoznávání.

**Změny způsobující chyby**

- V této verzi se zavádí určitý počet nejnovějších změn. Podrobnosti o rozhraních API najdete na [této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) .
- Soubory modelu KWS nejsou kompatibilní se sadou Speech SDK pro 1.0.1. Existující soubory klíčových slov budou odstraněny po zápisu nových souborů klíčových slov do zařízení.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0:2018 – srpen Release

- Vylepšení přesnosti rozpoznávání řeči opravou chyby v kódu zpracování zvuku.
- Byla aktualizována součást [sady Speech SDK](./speech-sdk.md) na verzi 0.5.0. Další informace najdete v [poznámkách k verzi](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733:2018 – může vydávat

První verze Public Preview sady Cognitive Services Speech pro zařízení.