---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961536"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k převodu textu na syntetizovaný projev ve zvukovém souboru. Služba převodu textu na řeč poskytuje mnoho možností pro syntetizované hlasy v části [Podpora jazyka převodu textu na řeč](../../../language-support.md#text-to-speech). Po splnění několika předpokladů syntetizujete řeč do souboru pouze pět kroků:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte objekt konfigurace zvuku, který určuje . Název souboru WAV.
> * Vytvořte `SpeechSynthesizer` objekt pomocí konfiguračních objektů shora.
> * Pomocí `SpeechSynthesizer` objektu převeďte text na syntetizovaný projev a uložte jej do zadaného zvukového souboru.
> * Zkontrolujte `SpeechSynthesizer` vrácené chyby.
