---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961457"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči. Služba převod textu na řeč poskytuje řadu možností pro syntetizované hlasy v části [Podpora jazyků pro převod textu na mluvené slovo](../../../language-support.md#text-to-speech). Po splnění několika požadavků vykreslení syntetizované řeči pro výchozí reproduktory provede jenom čtyři kroky:
> [!div class="checklist"]
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt `SpeechSynthesizer` pomocí výše uvedeného objektu `SpeechConfig`.
> * Použití objektu `SpeechSynthesizer` pro mluvený text.
> * Podívejte se na `SpeechSynthesisResult` vrátil chyby.
