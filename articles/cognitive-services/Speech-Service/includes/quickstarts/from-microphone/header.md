---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3775690802c89805ccf9df1ee6d6717a8818213f
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659176"
---
V tomto rychlém startu pomocí [sady Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) můžete interaktivně rozpoznat řeč ze vstupu mikrofonu a získat přepis textu ze zachyceného zvuku. Tuto funkci lze snadno integrovat do aplikací nebo zařízení pro běžné úlohy rozpoznávání, jako je přepis konverzací. Lze jej také použít pro složitější integrace, jako je použití rozhraní Bot Framework s sadou Speech SDK k vytvoření hlasových asistentů.

Po splnění několika předpokladů, rozpoznání řeči z mikrofonu trvá pouze čtyři kroky:

> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `SpeechRecognizer` objekt `SpeechConfig` pomocí objektu shora.
> * Pomocí `SpeechRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `SpeechRecognitionResult` vrácené.
