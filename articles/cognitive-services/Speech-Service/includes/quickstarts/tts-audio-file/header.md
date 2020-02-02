---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961536"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči ve zvukovém souboru. Služba převod textu na řeč poskytuje řadu možností pro syntetizované hlasy v části [Podpora jazyků pro převod textu na mluvené slovo](../../../language-support.md#text-to-speech). Po splnění některých požadavků vysyntetizuje řeč do souboru jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt konfigurace zvuku, který určuje. Název souboru WAV.
> * Vytvořte objekt `SpeechSynthesizer` pomocí výše uvedených objektů konfigurace.
> * Pomocí objektu `SpeechSynthesizer` převeďte text na syntetizované rozpoznávání řeči a uložte ho do zadaného zvukového souboru.
> * Zkontrolujte, `SpeechSynthesizer` vrátil chyby.
