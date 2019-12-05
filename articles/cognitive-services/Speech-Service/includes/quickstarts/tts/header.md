---
title: 'Rychlý Start: syntetizace řeči – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí sady Speech SDK syntetizovat řeč
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818135"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči. Po splnění několika požadavků vykreslení syntetizované řeči pro výchozí reproduktory provede jenom čtyři kroky:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt ````SpeechSynthesizer```` pomocí výše uvedeného objektu ````SpeechConfig````.
> * Použití objektu ````SpeechSynthesizer```` pro mluvený text.
> * Podívejte se na ````SpeechSynthesisResult```` vrátil chyby.
