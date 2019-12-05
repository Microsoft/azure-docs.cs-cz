---
title: 'Rychlý Start: rozpoznávání řeči ze zvukového souboru – Speech Service'
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
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819302"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k rozpoznávání řeči ze zvukového souboru. Po splnění několika požadavků se rozpoznávání řeči ze souboru provede jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt ````AudioConfig````, který určuje. Název souboru WAV.
> * Vytvořte objekt ````SpeechRecognizer```` pomocí výše uvedených objektů ````SpeechConfig```` a ````AudioConfig````.
> * Pomocí objektu ````SpeechRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````SpeechRecognitionResult````.
