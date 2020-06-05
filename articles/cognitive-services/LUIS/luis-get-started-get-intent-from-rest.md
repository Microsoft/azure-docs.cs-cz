---
title: 'Rychlý Start: získání záměru pomocí rozhraní REST API – LUIS'
description: V tomto REST API rychlého startu pomocí dostupné veřejné aplikace LUIS určete záměr uživatele z konverzačního textu.
ms.topic: quickstart
ms.date: 05/18/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 919308bbb557247516e85017909df9caed8eafdf
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418010"
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
