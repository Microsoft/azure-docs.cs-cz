---
title: 'Získání modelu pomocí volání REST v jazyce C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368476"
---
## <a name="prerequisites"></a>Požadavky

* Azure Language Understanding – vytváření znaků a vytváření kódu URL koncového bodu prostředku 32. Vytvořte pomocí [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) nebo [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importujte aplikaci [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z úložiště GitHub-Services-Language-porozumění.
* ID aplikace LUIS pro naimportovanou aplikaci TravelAgent ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* [Python 3,6](https://www.python.org/downloads/) nebo novější.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programové změny modelu

1. Vytvořte nový soubor s názvem `model.py`. Přidejte následující kód:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Nahraďte hodnoty začínající `YOUR-` vlastními hodnotami.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Klíč pro vytváření znaků 32.|
    |`YOUR-ENDPOINT`| Váš koncový bod adresy URL pro vytváření Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Název prostředku se nastaví při vytváření prostředku.|
    |`YOUR-APP-ID`| Vaše ID aplikace LUIS |

    Přiřazené klíče a prostředky jsou zobrazené na portálu LUIS v části Správa na stránce **prostředky Azure** . ID aplikace je k dispozici ve stejné části pro správu na stránce **nastavení aplikace** .

1. Pomocí příkazového řádku ve stejném adresáři, ve kterém jste vytvořili soubor, zadejte následující příkaz, který soubor spustí:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro aplikaci](../luis-concept-best-practices.md)
