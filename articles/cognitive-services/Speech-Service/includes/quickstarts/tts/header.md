---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961457"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k převodu textu na syntetizovanou řeč. Služba převodu textu na řeč poskytuje mnoho možností pro syntetizované hlasy v části [Podpora jazyka převodu textu na řeč](../../../language-support.md#text-to-speech). Po splnění několika předpokladů trvá vykreslování syntetizované řeči do výchozích reproduktorů pouze čtyři kroky:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `SpeechSynthesizer` objekt `SpeechConfig` pomocí objektu shora.
> * Použití `SpeechSynthesizer` objektu k vysmání textu.
> * Zkontrolujte `SpeechSynthesisResult` vrácené chyby.
