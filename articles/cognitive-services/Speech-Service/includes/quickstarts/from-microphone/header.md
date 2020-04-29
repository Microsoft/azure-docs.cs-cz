---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400780"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu rozpoznávání řeči od vstupu mikrofonu a získáte přepis textu ze zachyceného zvuku. Tato funkce se dá snadno integrovat do aplikací nebo zařízení pro běžné úlohy rozpoznávání, jako jsou zdlouhavého přepisování konverzace. Dá se použít také pro složitější integrace, jako je použití bot frameworku se sadou Speech SDK pro vytváření hlasových asistentů.

Po splnění několika požadavků se rozpoznávání řeči pouze v mikrofonu provede čtyřmi kroky:

> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte `SpeechRecognizer` objekt pomocí výše uvedeného `SpeechConfig` objektu.
> * Pomocí `SpeechRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `SpeechRecognitionResult` vrácenou.
