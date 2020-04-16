---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400375"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k převodu textu na syntetizovanou řeč. Služba převodu textu na řeč poskytuje mnoho možností pro syntetizované hlasy v části [Podpora jazyka převodu textu na řeč](../../../language-support.md#text-to-speech). Po splnění několika předpokladů trvá vykreslování syntetizované řeči do výchozích reproduktorů pouze čtyři kroky:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `SpeechSynthesizer` objekt `SpeechConfig` pomocí objektu shora.
> * Použití `SpeechSynthesizer` objektu k vysmání textu.
> * Zkontrolujte `SpeechSynthesisResult` vrácené chyby.
