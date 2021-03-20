---
title: Postup změny modelu LUIS pomocí REST API
titleSuffix: Azure Cognitive Services
description: V tomto článku přidejte příklad projevy pro změnu modelu a výuku aplikace.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 7346037ad804da4f68a2bc6512494c461d7fc5bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96436177"
---
# <a name="how-to-change-the-luis-model-with-rest-apis"></a>Postup změny modelu LUIS pomocí rozhraní REST API

V tomto článku přidáte příklad projevy do aplikace Pizza a provedete výuku aplikace. Ukázkové promluvy jsou konverzačním textem uživatele namapovaným na záměr. Tím, že poskytnete ukázkové promluvy pro záměry, naučíte službu LUIS, které typy uživatelem zadaného textu patří do kterého záměru.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-model-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-model-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-model-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-model-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-model-rest-python.md)]
::: zone-end
