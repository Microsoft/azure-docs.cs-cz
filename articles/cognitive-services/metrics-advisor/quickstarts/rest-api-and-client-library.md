---
title: Knihovny klienta Poradce pro metriky REST API
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete své aplikace připojit k rozhraní API služby Advisory metrik z Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186838"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Rychlý Start: použití knihoven klientů nebo rozhraní REST API k přizpůsobení řešení

Začněte s poradcem pro REST API nebo klientské knihovny. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

Použijte poradce metriky k provedení těchto akcí:

* Přidání datového kanálu ze zdroje dat
* Ověřit stav ingestování
* Konfigurace zjišťování a výstrah 
* Dotaz na výsledky detekce anomálií
* Diagnostika anomálií


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

- [Použití webového portálu](web-portal.md)
- [Zprovoznění datových kanálů](../how-tos/onboard-your-data.md)
    - [Správa datových kanálů](../how-tos/manage-data-feeds.md)
    - [Konfigurace pro různé zdroje dat](../data-feeds-from-different-sources.md)
- [Konfigurace metrik a doladění konfigurace zjišťování](../how-tos/configure-metrics.md)
- [Úprava detekce anomálií pomocí zpětné vazby](../how-tos/anomaly-feedback.md)