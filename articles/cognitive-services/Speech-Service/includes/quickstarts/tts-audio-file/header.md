---
title: 'Rychlý Start: syntetizace řeči do zvukového souboru – Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469170"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči ve zvukovém souboru. Po splnění některých požadavků vysyntetizuje řeč do souboru jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt konfigurace zvuku, který určuje. Název souboru WAV.
> * Vytvořte objekt ````SpeechSynthesizer```` pomocí výše uvedených objektů konfigurace.
> * Pomocí objektu ````SpeechSynthesizer```` převeďte text na syntetizované rozpoznávání řeči a uložte ho do zadaného zvukového souboru.
> * Zkontrolujte, ````SpeechSynthesizer```` vrátil chyby.
