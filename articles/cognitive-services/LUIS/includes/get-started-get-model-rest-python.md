---
title: 'Získat model s voláním REST v C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368476"
---
## <a name="prerequisites"></a>Požadavky

* Azure Language Understanding – vytváření klíče 32 znaků prostředku a vytváření adresy URL koncového bodu. Vytvořte pomocí [portálu Azure nebo](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) [příkazového příkazového příkazu Kontu Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importujte aplikaci [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z úložiště GitHub, které rozumí kognitivním službám.
* ID aplikace LUIS pro naimportovanou aplikaci TravelAgent ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* [Python 3.6](https://www.python.org/downloads/) nebo novější.
* [Kód visual studia](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programová změna modelu

1. Vytvořte nový soubor s názvem `model.py`. Přidejte následující kód:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Nahraďte hodnoty `YOUR-` začínající vlastními hodnotami.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Váš 32 znak ový klíč.|
    |`YOUR-ENDPOINT`| Koncový bod adresy URL pro vytváření. Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Při vytváření prostředku nastavíte název prostředku.|
    |`YOUR-APP-ID`| ID aplikace LUIS. |

    Přiřazené klíče a prostředky jsou viditelné na portálu LUIS v části Spravovat na stránce **prostředků Azure.** ID aplikace je k dispozici ve stejném oddílu Správa na stránce **Nastavení aplikace.**

1. Pomocí příkazového řádku ve stejném adresáři, ve kterém jste soubor vytvořili, zadejte následující příkaz pro spuštění souboru:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu odstraňte soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Doporučené postupy pro aplikaci](../luis-concept-best-practices.md)
