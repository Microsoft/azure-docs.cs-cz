---
title: Recenze, pracovní postupy a koncepty úloh – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte o základních konceptech nástroje revize; hodnocení, pracovních postupů a pracovních míst.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221147"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Recenze moderování obsahu, pracovní postupy a úlohy

Content Moderator kombinuje moderování s pomocí počítače s funkcemi typu human-in-the-loop a vytváří tak optimální proces moderování pro reálné scénáře. Je to prostřednictvím cloudového [nástroje Review](https://contentmoderator.cognitive.microsoft.com). V této příručce se dozvíte o základních konceptech nástroje Revize: recenze, pracovní postupy a úlohy.

## <a name="reviews"></a>Recenze

V recenzi se obsah nahraje do nástroje Revize a zobrazí se na kartě **Revize.** Zde mohou uživatelé změnit použité značky a podle potřeby použít vlastní značky. Když uživatel odešle recenzi, výsledky jsou odeslány do zadaného koncového bodu zpětného volání a obsah je odebrán z webu.

![Stránka nástroje pro kontrolu se otevře v prohlížeči na kartě Revize](./Review-Tool-user-Guide/images/image-workflow-review.png)

Podívejte se na [nástroj revize průvodce,](./review-tool-user-guide/review-moderated-images.md) jak začít vytvářet recenze, nebo se podívejte na [rest api průvodce](./try-review-api-review.md) se dozvíte, jak to udělat programově.

## <a name="workflows"></a>Pracovní postupy

Pracovní postup je cloudový přizpůsobený filtr obsahu. Pracovní postupy se mohou připojit k různým službám, aby filtrovaly obsah různými způsoby a pak podnikají příslušnou akci. Pomocí konektoru Moderátor obsahu může pracovní postup automaticky použít značky moderování a vytvářet recenze s odeslaným obsahem.

### <a name="view-workflows"></a>Zobrazit pracovní postupy

Chcete-li zobrazit existující pracovní postupy, přejděte do [nástroje Revize](https://contentmoderator.cognitive.microsoft.com/) a vyberte **Nastavení** > **pracovních postupů**.

![Výchozí pracovní postup](images/default-workflow-listed.PNG)

Pracovní postupy lze zcela popsat jako řetězce JSON, což je činí programově přístupnými. Pokud vyberete možnost **Upravit** pro pracovní postup a pak vyberete kartu **JSON,** zobrazí se výraz JSON takto:

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

V [průvodci nástrojem revize](./review-tool-user-guide/workflows.md) můžete začít vytvářet a používat pracovní postupy nebo se [dozvíte,](./try-review-api-workflow.md) jak to udělat programově.

## <a name="jobs"></a>Úlohy

Moderování úloha slouží jako druh obálky pro funkce moderování obsahu, pracovnípostupy a recenze. Úloha prohledá váš obsah pomocí rozhraní API pro moderování obrázků Moderátor obsahu nebo rozhraní API pro moderování textu a poté jej zkontroluje podle určeného pracovního postupu. Na základě výsledků pracovního postupu může nebo nemusí vytvořit recenzi obsahu v [nástroji revize](./review-tool-user-guide/human-in-the-loop.md). Zatímco recenze a pracovní postupy lze vytvořit a nakonfigurovat pomocí příslušných rozhraní API, rozhraní API úlohy umožňuje získat podrobnou zprávu o celém procesu (který lze odeslat do zadaného koncového bodu zpětného volání).

Podívejte se na [průvodce rozhraním REST API,](./try-review-api-job.md) kde můžete začít používat úlohy.

## <a name="next-steps"></a>Další kroky

* Otestujte úlohu [konzoly rozhraní API](try-review-api-job.md)úlohy a použijte ukázky kódu rozhraní REST API. Pokud jste obeznámeni s Visual Studio a C#, podívejte se také na [úlohy .NET rychlý start](moderation-jobs-quickstart-dotnet.md). 
* Pro recenze, začít s [konzolou rozhraní API revize](try-review-api-review.md)a použít ukázky kódu rozhraní REST API. Potom naleznete v části recenze [v rychlém startu rozhraní .NET](dotnet-sdk-quickstart.md).
* Pro recenze videa použijte [rychlý start recenze videa](video-reviews-quickstart-dotnet.md)a zjistěte, jak [do recenze videa přidat přepisy](video-transcript-reviews-quickstart-dotnet.md).
