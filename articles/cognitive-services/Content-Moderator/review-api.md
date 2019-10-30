---
title: Přehledy, pracovní postupy a koncepty úloh – Content Moderator
titleSuffix: Azure Cognitive Services
description: Další informace o recenzích, pracovních postupech a úlohách
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 917636c8ae339e06321c169979611f74276ca523
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044003"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Recenze, pracovní postupy a úlohy pro moderování obsahu

Content Moderator kombinuje moderování počítačů s využitím možností funkce Real-in-loop a vytvoří tak optimální proces moderování pro scénáře reálného světa. Provede to pomocí cloudového [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com). V této příručce se dozvíte o základních konceptech nástroje pro kontrolu: Recenze, pracovní postupy a úlohy.

## <a name="reviews"></a>Recenze

V recenzi se obsah nahraje do nástroje pro revize a zobrazí se na kartě **Revize** . Z tohoto místa mohou uživatelé měnit použité značky a podle potřeby používat vlastní značky. Když uživatel kontrolu odešle, výsledky se odešlou do zadaného koncového bodu zpětného volání a obsah se odebere z webu.

![Kontrola webu nástroje otevřeného v prohlížeči, na kartě kontrola](./Review-Tool-user-Guide/images/image-workflow-review.png)

Další informace o tom, jak to provést prostřednictvím kódu programu, najdete v [Průvodci pro kontrolu](./review-tool-user-guide/review-moderated-images.md) a Naučte se vytvářet recenze nebo si přečtěte [Průvodce REST API](./try-review-api-review.md) .

## <a name="workflows"></a>Pracovní postupy

Pracovní postup je cloudově přizpůsobený filtr pro obsah. Pracovní postupy se můžou připojit k nejrůznějším službám a filtrovat obsah různými způsoby a pak provést příslušné akce. Pomocí konektoru Content Moderator může pracovní postup automaticky použít značky moderování a vytvářet recenze s odeslaným obsahem.

### <a name="view-workflows"></a>Zobrazit pracovní postupy

Pokud chcete zobrazit stávající pracovní postupy, použijte [Nástroj pro revizi](https://contentmoderator.cognitive.microsoft.com/) a vyberte **Nastavení**  > **pracovní postupy**.

![Výchozí pracovní postup](images/default-workflow-listed.PNG)

Pracovní postupy mohou být zcela popsány jako řetězce JSON, díky kterým jsou přístupné programově. Pokud pro svůj pracovní postup vyberete možnost **Upravit** a pak vyberete kartu **JSON** , zobrazí se výraz JSON podobný následujícímu:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

V [Průvodci pro kontrolu](./review-tool-user-guide/workflows.md) můžete začít vytvářet a používat pracovní postupy, případně si přečtěte [Průvodce REST API](./try-review-api-workflow.md) , kde se dozvíte, jak to provést programově.

## <a name="jobs"></a>Úlohy

Úloha moderování slouží jako typ obálky pro funkčnost Moderování obsahu, pracovních postupů a revizí. Úloha prohledá obsah pomocí rozhraní API pro moderování imagí Content Moderator nebo rozhraní API pro moderování textu a pak ho zkontroluje oproti určenému pracovnímu postupu. Na základě výsledků pracovního postupu může nebo nemusí v [nástroji pro revize](./review-tool-user-guide/human-in-the-loop.md)vytvořit recenzi obsahu. I když lze vytvořit a nakonfigurovat jak recenze, tak pracovní postupy s odpovídajícími rozhraními API, vám rozhraní API úlohy umožní získat podrobnou sestavu celého procesu (který lze odeslat do zadaného koncového bodu zpětného volání).

Pokud chcete začít používat úlohy, přečtěte si [příručku pro REST API](./try-review-api-job.md) .

## <a name="next-steps"></a>Další kroky

* Otestujte [konzolu rozhraní API úlohy](try-review-api-job.md)a použijte ukázky kódu REST API. Pokud jste obeznámeni se sadou Visual Studio C#a, podívejte se také na [úlohy rychlý Start pro úlohy .NET](moderation-jobs-quickstart-dotnet.md). 
* V části recenze začněte s [konzolou recenze rozhraní API](try-review-api-review.md)a použijte ukázky kódu REST API. Pak se podívejte na část recenze v [rychlém startu .NET](dotnet-sdk-quickstart.md).
* V případě recenze videí použijte [rychlý Start pro kontrolu videa](video-reviews-quickstart-dotnet.md)a Naučte se, jak do [recenze videa přidat přepisy](video-transcript-reviews-quickstart-dotnet.md).
