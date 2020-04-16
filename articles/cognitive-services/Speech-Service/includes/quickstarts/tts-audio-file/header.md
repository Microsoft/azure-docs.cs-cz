---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400340"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k převodu textu na syntetizovaný projev ve zvukovém souboru. Služba převodu textu na řeč poskytuje mnoho možností pro syntetizované hlasy v části [Podpora jazyka převodu textu na řeč](../../../language-support.md#text-to-speech). Po splnění několika předpokladů syntetizujete řeč do souboru pouze pět kroků:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte objekt konfigurace zvuku, který určuje . Název souboru WAV.
> * Vytvořte `SpeechSynthesizer` objekt pomocí konfiguračních objektů shora.
> * Pomocí `SpeechSynthesizer` objektu převeďte text na syntetizovaný projev a uložte jej do zadaného zvukového souboru.
> * Zkontrolujte `SpeechSynthesizer` vrácené chyby.
