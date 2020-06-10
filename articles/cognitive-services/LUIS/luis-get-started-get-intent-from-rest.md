---
title: 'Rychlý Start: získání záměru pomocí rozhraní REST API – LUIS'
description: V tomto REST API rychlého startu pomocí dostupné veřejné aplikace LUIS určete záměr uživatele z konverzačního textu.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 5d43a1aee9e3f355a3cfcab927d87571798677e7
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608549"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Rychlý Start: získání záměru pomocí rozhraní REST API

V tomto rychlém startu použijete aplikaci LUIS k určení záměru uživatele z konverzačního textu. Odešlete záměr uživatele jako text do koncového bodu předpovědi HTTP aplikace Pizza. Na koncovém bodu LUIS použije model aplikace Pizza k analýze textu přirozeného jazyka pro účely, určení celkového záměru a extrakci dat relevantních pro doménu předmětu aplikace.

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
