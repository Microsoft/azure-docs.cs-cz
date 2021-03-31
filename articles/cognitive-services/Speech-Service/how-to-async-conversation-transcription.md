---
title: Asynchronní přepis konverzace – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat přepis asynchronní konverzace pomocí služby Speech. Dostupné jenom pro jazyky Java a C#.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934633"
---
# <a name="asynchronous-conversation-transcription"></a>Asynchronní přepis konverzace

V tomto článku se v přepisu asynchronní konverzace ukazuje použití rozhraní **RemoteConversationTranscriptionClient** API. Pokud jste nakonfigurovali přepis konverzace na asynchronní přepis a máte `conversationId` , můžete získat přepis přidružený k danému `conversationId` pomocí rozhraní **RemoteConversationTranscriptionClient** API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchronní vs. v reálném čase a asynchronní

Pomocí asynchronního přepisu zařadíte do proudu zvuk konverzace, ale v reálném čase nebudete vracet žádný přepis. Místo toho se po odeslání zvuku pomocí příkazu zobrazí `conversationId` dotaz na `Conversation` stav asynchronního přepisu. Po přípravě asynchronního přepisu získáte `RemoteConversationTranscriptionResult` .

V reálném čase plus asynchronní získáte přepis v reálném čase, ale také získáte přepis pomocí dotazu s `conversationId` (podobně jako asynchronní scénář).

K provedení asynchronního přepisu se vyžadují dva kroky. Prvním krokem je nahrání zvuku a výběr buď asynchronního, nebo plus v reálném čase. Druhým krokem je získání výsledků přepisu.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)
