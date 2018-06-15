---
title: Dokumentaci k sadě SDK řeči kognitivní služby | Microsoft Docs
description: Poznámky k verzi - co se změnilo v nejnovější verze
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "35343973"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Kognitivní Services SDK řeči 0.4.0: verze 2018 – červen

**Funkční změny**

- AudioInputStream

  Pro rozpoznávání teď spotřebovat datového proudu jako zdroj zvuku. Podrobné informace najdete v tématu související [postupy průvodce](how-to-use-audio-input-streams.md).

- Podrobné výstupní formát

  Při vytváření `SpeechRecognizer`, můžete požádat o `Detailed` nebo `Simple` výstupní formát. `DetailedSpeechRecognitionResult` Obsahuje spolehlivosti skóre, rozpoznaný text, nezpracovaná lexikální formuláře, normalizovaný formuláře a normalizovaný formuláře s maskované vulgárnost.

**Nejnovější změny**

- Změňte `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` v jazyce C#.

**Opravy chyb**

- Opravte problém možná zpětného volání ve vrstvě USP během vypnutí.

- Pokud rozpoznávání rukopisu spotřebovanou zvuk vstupní soubor byl podržíte do popisovače souboru déle, než je nezbytné.

- Odebrat několik blokování mezi message pump a rozpoznávání rukopisu.

- Ještě efektivněji `NoMatch` dojít, pokud vypršel časový limit odpověď ze služby.

- Knihovny foundation média v systému Windows jsou odloženým načtením. Tato knihovna je pouze požadované pro vstup mikrofonu.

- Rychlost odesílání zvuk dat je omezen na o dvakrát původní zvuk rychlosti.

- V systému Windows sestavení C# .NET jsou nyní silným názvem.

- Oprava dokumentace: `Region` požadované informace o vytvoření rozpoznávání rukopisu.

Další ukázky byly přidány a jsou neustále aktualizovány. Nejnovější sadu vzorků, najdete v článku [úložiště GitHub ukázkové sady SDK řeči](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Kognitivní Services SDK řeči 0.2.12733: verze 2018 může

První verze public preview kognitivní řeči SDK služby.
