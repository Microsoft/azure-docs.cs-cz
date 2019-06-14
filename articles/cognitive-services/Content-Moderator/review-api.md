---
title: Recenze, pracovních postupů a úlohy Principy – Content Moderator
titlesuffix: Azure Cognitive Services
description: Další informace o úlohy, pracovní postupy a revize
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607256"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Moderování obsahu kontroly, pracovní postupy a úlohy

Content Moderator kombinuje moderování s podporou počítače s možnostmi lidských v the smyčky se vytvořit proces optimální moderování pro reálné situace. Dělá to pomocí cloudové [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com). V této příručce se dozvíte o klíčových konceptech nástroj pro recenze: revize, pracovní postupy a úlohy.

## <a name="reviews"></a>Recenze

Při kontrole, obsah se nahraje do nástroje pro recenze a zobrazí se v části **zkontrolujte** kartu. Z tohoto místa můžete uživatelům změnit značky použité a použít vlastní vlastní značky podle potřeby. Když uživatel odešle přezkoumání, výsledky se posílají do zadaného zpětného volání koncového bodu a obsah je odebrán z webu.

![Zkontrolujte nástroj webu otevřete v prohlížeči, na kartě Revize](./Review-Tool-user-Guide/images/image-workflow-review.png)

Najdete v článku [Průvodce nástroj pro revize](./review-tool-user-guide/review-moderated-images.md) začít vytváření recenzí nebo najdete v článku [pokyny k rozhraní REST API](./try-review-api-review.md) k zjistěte, jak to provést prostřednictvím kódu programu.

## <a name="workflows"></a>Pracovní postupy

Pracovní postup je založené na cloudu vlastní filtr pro obsah. Pracovní postupy můžete připojit k různým službám v různých způsobů filtrování obsahu a poté přijmout vhodná opatření. S konektorem Content Moderator můžete pracovní postup automaticky moderování klíčová slova a vytvořit kontroly s obsah odeslaný.

### <a name="view-workflows"></a>Zobrazení pracovních postupů

Chcete-li zobrazit stávajících pracovních postupů, přejděte na [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) a vyberte **nastavení** > **pracovních postupů**.

![Výchozí pracovní postup](images/default-workflow-listed.PNG)

Pracovní postupy mohou být zcela popsány jako řetězce JSON, které jsou přístupné prostřednictvím kódu programu. Pokud vyberete **upravit** možnost pracovního postupu a pak vyberte **JSON** kartu, uvidíte výraz JSON podobný tomuto:

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

Najdete v článku [Průvodce nástroj pro revize](./review-tool-user-guide/workflows.md) začít vytváření a používání pracovních postupů nebo najdete v článku [pokyny k rozhraní REST API](./try-review-api-workflow.md) k zjistěte, jak to provést prostřednictvím kódu programu.

## <a name="jobs"></a>Úlohy

Moderování úlohy slouží jako typ obálky pro funkce moderování obsahu, pracovní postupy a kontroly. Úloha zkontroluje obsah pomocí moderování obrázků Content Moderator API nebo rozhraní API pro moderování textu a pak ověří proti určené pracovního postupu. Na základě výsledků pracovní postup může nebo nemusí vytvořit kontrolu pro obsah v [nástroj pro recenze](./review-tool-user-guide/human-in-the-loop.md). Zatímco recenze a pracovních postupů lze vytvořit a nakonfigurovat jejich příslušných rozhraní API, úloha rozhraní API umožňuje získat podrobnou sestavu o celý proces (která může být odeslána do zadaného zpětného volání koncového bodu).

Zobrazit [pokyny k rozhraní REST API](./try-review-api-job.md) a začněte využívat úlohy.

## <a name="next-steps"></a>Další postup

* Testovací verze [rozhraní API pro úlohu konzoly](try-review-api-job.md)a používat rozhraní REST API ukázky kódu. Pokud jste obeznámeni s Visual Studio a C#, podívejte [rychlý úvod k .NET úlohy](moderation-jobs-quickstart-dotnet.md). 
* U revizí, vám umožní začít [revizi rozhraní API konzoly](try-review-api-review.md)a používat rozhraní REST API ukázky kódu. Potom se podívejte [rychlý úvod k .NET revize](moderation-reviews-quickstart-dotnet.md).
* Video revize používat [rychlého startu zkontrolujte Video](video-reviews-quickstart-dotnet.md)a zjistěte, jak [přidat záznamy o studiu videa kontrolou](video-transcript-reviews-quickstart-dotnet.md).
