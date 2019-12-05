---
title: 'Rychlý Start: syntetizace řeči do zvukového souboru – Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817864"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči ve zvukovém souboru. Po splnění některých požadavků vysyntetizuje řeč do souboru jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt konfigurace zvuku, který určuje. Název souboru WAV.
> * Vytvořte objekt ````SpeechSynthesizer```` pomocí výše uvedených objektů konfigurace.
> * Pomocí objektu ````SpeechSynthesizer```` převeďte text na syntetizované rozpoznávání řeči a uložte ho do zadaného zvukového souboru.
> * Zkontrolujte, ````SpeechSynthesizer```` vrátil chyby.
