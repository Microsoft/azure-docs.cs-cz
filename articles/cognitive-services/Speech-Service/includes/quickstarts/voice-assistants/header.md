---
title: 'Rychlý Start: Vytvoření vlastního hlasového asistenta – Speech Service'
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
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "80241140"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k vytvoření vlastní aplikace hlasového asistenta, která se připojí ke robotu, kterou už máte vytvořenou a nakonfigurovanou. Pokud potřebujete vytvořit robota, přečtěte si [Související kurz](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) , který vám pomůže komplexnější průvodce.

Po splnění několika předpokladů připojení vlastního hlasového asistenta trvá jenom několik kroků:
> [!div class="checklist"]
> * Vytvořte `BotFrameworkConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte `DialogServiceConnector` objekt pomocí `BotFrameworkConfig` výše uvedeného objektu.
> * Pomocí `DialogServiceConnector` objektu spusťte proces naslouchání pro jeden utterance.
> * Zkontrolujte `ActivityReceivedEventArgs` vrácenou.
