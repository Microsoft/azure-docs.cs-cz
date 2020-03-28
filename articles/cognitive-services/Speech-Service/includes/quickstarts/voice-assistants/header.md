---
title: 'Úvodní příručka: Vytvoření vlastního hlasového asistenta – služba Řeči'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241140"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k vytvoření vlastní aplikace hlasového asistenta, která se připojí k robotovi, který jste již vytvořili a nakonfigurovali. Pokud potřebujete vytvořit bot, podívejte se [na související výukový program](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) pro komplexnější průvodce.

Po splnění několika předpokladů trvá připojení vlastního hlasového asistenta jen několik kroků:
> [!div class="checklist"]
> * Vytvořte `BotFrameworkConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `DialogServiceConnector` objekt `BotFrameworkConfig` pomocí objektu shora.
> * Pomocí `DialogServiceConnector` objektu spusťte proces naslouchání pro jeden utterance.
> * Zkontrolujte `ActivityReceivedEventArgs` vrácené.
