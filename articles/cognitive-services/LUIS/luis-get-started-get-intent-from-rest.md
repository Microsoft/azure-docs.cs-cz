---
title: Jak získat záměr pomocí REST API
description: V tomto článku pomocí dostupné veřejné aplikace LUIS určíte záměr uživatele z konverzačního textu.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: b49d90ffe2a262895727fdd4ef5f8a7950104102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96437010"
---
# <a name="how-to-get-an-intent-using-the-rest-apis"></a>Jak získat záměr pomocí rozhraní REST API

V tomto článku použijete aplikaci LUIS k určení záměru uživatele z konverzačního textu. Odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP aplikace Pizza. Na koncovém bodu LUIS použije model aplikace Pizza k analýze textu přirozeného jazyka pro účely, určení celkového záměru a extrakci dat relevantních pro doménu předmětu aplikace.

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](https://www.luis.ai).

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
