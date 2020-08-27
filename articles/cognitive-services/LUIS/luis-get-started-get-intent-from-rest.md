---
title: 'Rychlý Start: získání záměru pomocí rozhraní REST API – LUIS'
description: V tomto REST API rychlého startu pomocí dostupné veřejné aplikace LUIS určete záměr uživatele z konverzačního textu.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 977348fcaf0cabf92268d71282222e1652321fac
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919266"
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
