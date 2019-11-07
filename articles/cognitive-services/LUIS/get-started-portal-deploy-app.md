---
title: 'Rychlý Start: nasazení aplikace pomocí portálu LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak nasadit aplikaci vytvořením prostředku koncového bodu předpovědi, přiřazení prostředku, školení a publikování aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 279ec4e8a6d9a9d473cc511b4ec690391cdbd634
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669426"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Rychlý Start: nasazení aplikace na portálu LUIS

[!INCLUDE [Uses preview portal](./includes/uses-portal-preview.md)]

Když je vaše aplikace LUIS připravená na vrácení utterance předpovědi do klientské aplikace (například robota chatu), musíte aplikaci nasadit do koncového bodu předpovědi.

V tomto rychlém startu se naučíte nasadit aplikaci. Vytvoříte prostředek koncového bodu předpovědi, přiřadíte ho k aplikaci, provedete výuku aplikace a publikujete aplikaci.

## <a name="prerequisites"></a>Požadavky

* Získejte [předplatné Azure](https://azure.microsoft.com/free).
* Dokončete [předchozí portál pro rychlý Start](get-started-portal-build-app.md) nebo [si stáhněte a importujte aplikaci](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Vytvoření prostředku koncového bodu

V Azure Portal vytvoříte prostředek koncového bodu předpovědi. Tento prostředek by se měl používat jenom pro dotazy předpovědi koncových bodů. Nepoužívejte tento prostředek k vytváření změn v aplikaci.

1. Přihlaste se a vytvořte prostředek v [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Nakonfigurujte předplatné s následujícím nastavením:

   |Nastavení|Hodnota|Účel|
   |--|--|--|
   |Name (Název)|`my-luis-resource`|Název prostředku Azure. Tento název budete potřebovat při přiřazení prostředku k aplikaci na portálu LUIS.|
   |Předplatné|Vaše předplatné|Vyberte jedno z předplatných přidružených k vašemu účtu.|
   |Skupina prostředků|`my-resource-group`|Vytvořte novou skupinu prostředků pro všechny prostředky služby vnímání. Až budete s prostředky hotovi, můžete odstranit skupinu prostředků, abyste mohli vyčistit své předplatné. |
   |Umístění pro vytváření obsahu|**Západní USA**|Oblast Azure pro vytváření obsahu.|
   |Vytváření cenové úrovně|**F0**|Výchozí cenová úroveň pro vytváření obsahu.|
   |Umístění modulu runtime|**Západní USA**|Oblast Azure pro předpověď dotazů na koncový bod.|
   |Cenová úroveň za běhu|**S0**|Tato cenová úroveň poskytuje weby s vysokým provozem.|
   | | | |


   ![Volba rozhraní API Azure](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Vyberte **vytvořit** a vytvořte prostředek Azure.

   V další části se dozvíte, jak připojit tento nový prostředek k aplikaci LUIS na portálu LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Přiřazení klíče prostředků k aplikaci LUIS na portálu LUIS

Pokaždé, když vytvoříte nový prostředek pro LUIS, je potřeba přiřadit prostředek k aplikaci LUIS. Po přiřazení Tento krok nebudete muset provádět znovu, dokud nevytvoříte nový prostředek. Můžete vytvořit nový prostředek pro rozšíření oblastí vaší aplikace nebo pro podporu většího počtu předpovědních dotazů.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a v seznamu aplikací vyberte aplikaci **myEnglishApp** .

1. V pravé horní nabídce vyberte **Spravovat** a pak vyberte **prostředky Azure**.

1. Chcete-li přidat LUIS, vyberte **Přidat prostředek předpovědi**.

    ![Pokud chcete přidat prostředek předpovědi LUIS, vyberte Přidat prostředek předpovědi.](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Vyberte svého tenanta, předplatné a název prostředku. Vyberte **přiřadit prostředek**.

   ![Přiřazení prostředku k aplikaci](./media/get-started-portal-deploy-app/assign-resource.png)

1. Při přidávání klíčového obsahu do aplikace proveďte stejný postup.

1. Vyhledejte nový řádek v tabulce pro nový prostředek předpovědi a zkopírujte adresu URL koncového bodu. Je správně vytvořen tak, aby `HTTP GET` požadavek na koncový bod rozhraní API LUIS pro předpověď.

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publikování aplikace do koncového bodu předpovědi

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Požadavek koncového bodu předpovědi

`query=` na konci adresy URL je místo, kde se uživateli připojí utterance k žádosti o získání. Po `query=`zadejte stejný utterance uživatele, který jste použili na konci předchozího rychlého startu:

```Is there a form named hrf-234098```

Ujistěte se, že řetězec dotazu obsahuje následující páry:

* `show-all-intents=true`
* `verbose=true`

V prohlížeči se zobrazí odpověď:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Chcete-li zobrazit stejnou úroveň informací v podokně test, je nutné aplikaci publikovat. Po publikování aplikace vyberte v podokně test možnost **Porovnat s publikováním** . Pokud chcete zobrazit stejný kód JSON jako předchozí krok, použijte **zobrazení zobrazit JSON** v publikovaném podokně test. Tímto způsobem můžete porovnat změny aktuální aplikace, na které pracujete, s aplikací, která je publikovaná do koncového bodu.

[![porovnat aktuálně upravované a publikované verze aplikace](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, vyberte **Moje aplikace** z horní navigační nabídky. V seznamu vyberte zaškrtávací políčko aplikace a pak na panelu nástrojů kontextu nad seznamem vyberte **Odstranit** .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Identifikace běžných záměrů a entit](luis-tutorial-prebuilt-intents-entities.md)
