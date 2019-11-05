---
title: 'Rychlý Start: nasazení aplikace pomocí portálu LUIS'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak nasadit aplikaci LUIS do koncového bodu předpovědi poté, co je aplikace připravená na vrácení utterance předpovědi do klientské aplikace, jako je například robot pro chat. V tomto rychlém startu se dozvíte, jak nasadit aplikaci tím, že vytvoříte prostředek koncového bodu předpovědi, přiřadíte ho k aplikaci, poškolením aplikace a publikujete aplikaci.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: ecae5c7db02436fe34fec19989f174504fd1e03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488714"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Rychlý Start: nasazení aplikace na portálu LUIS

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]


Když je vaše aplikace LUIS připravená na vrácení utterance předpovědi do klientské aplikace (například robota chatu), musíte aplikaci nasadit do koncového bodu předpovědi.

V tomto rychlém startu se naučíte nasadit aplikaci. Vytvoříte prostředek koncového bodu předpovědi, přiřadíte ho k aplikaci, provedete výuku aplikace a publikujete aplikaci.

## <a name="prerequisites"></a>Požadavky

* Získejte [předplatné Azure](https://azure.microsoft.com/free).
* Dokončete [předchozí portál pro rychlý Start](get-started-portal-build-app.md) nebo [si stáhněte a importujte aplikaci](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Vytvoření prostředku koncového bodu

V Azure Portal vytvoříte prostředek koncového bodu předpovědi. Tento prostředek by se měl používat jenom pro dotazy předpovědi koncových bodů. Nepoužívejte tento prostředek k vytváření změn v aplikaci.

1. Přihlaste se k webu [Azure Portal](https://ms.portal.azure.com/).

1. V levém horním panelu vyberte symbol zelený **+** . Vyhledejte `Cognitive Services` na webu Marketplace a vyberte ji.

1. Nakonfigurujte předplatné s následujícím nastavením:

   |Nastavení|Hodnota|Účel|
   |--|--|--|
   |Name (Název)|`my-cognitive-service-resource`|Název prostředku Azure. Tento název budete potřebovat při přiřazení prostředku k aplikaci na portálu LUIS.|
   |Předplatné|Vaše předplatné|Vyberte jedno z předplatných přidružených k vašemu účtu.|
   |Umístění|**Západní USA**|Oblast Azure pro tento prostředek.|
   |Cenová úroveň|**S0**|Výchozí cenová úroveň pro tento prostředek.|
   |Skupina prostředků|`my-cognitive-service-resource-group`|Vytvořte novou skupinu prostředků pro všechny prostředky služby vnímání. Až budete s prostředky hotovi, můžete odstranit skupinu prostředků, abyste mohli vyčistit své předplatné. |
   | | | |

   ![Volba rozhraní API Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Vyberte **vytvořit** a vytvořte prostředek Azure.

   V další části se dozvíte, jak připojit tento nový prostředek k aplikaci LUIS na portálu LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Přiřazení klíče prostředků k aplikaci LUIS na portálu LUIS

Pokaždé, když vytvoříte nový prostředek pro LUIS, je potřeba přiřadit prostředek k aplikaci LUIS. Po přiřazení Tento krok nebudete muset provádět znovu, dokud nevytvoříte nový prostředek. Můžete vytvořit nový prostředek pro rozšíření oblastí vaší aplikace nebo pro podporu většího počtu předpovědních dotazů.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a v seznamu aplikací vyberte aplikaci **myEnglishApp** .

1. V pravé horní nabídce vyberte **Spravovat** a pak vyberte **prostředky Azure**.

1. Chcete-li přidat LUIS, vyberte **Přidat prostředek předpovědi**.

    <!-- TBD: get screenshot-->

1. Vyberte svého tenanta, předplatné a název prostředku. Vyberte **přiřadit prostředek**.

   ![Přiřazení prostředku k aplikaci](./media/get-started-portal-deploy-app/assign-resource.png)

1. Vyhledejte nový řádek v tabulce a zkopírujte adresu URL koncového bodu. Je správně vytvořen tak, aby `HTTP GET` požadavek na koncový bod rozhraní API LUIS pro předpověď.

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace

Jakmile budete připraveni na jeho testování, prohlaste si aplikaci. Publikujte aplikaci, pokud chcete, aby byla aktuálně proučená verze dostupná pro klientské aplikace z modulu runtime koncového bodu předpovědi dotazů.

1. Pokud se aplikace nepoužívá, vyberte v nabídce vpravo nahoře možnost **vlak** .

1. V horní nabídce vyberte **publikovat** . Vyberte produkční slot a publikujte.

1. Po zobrazení oznamovacího panelu se publikování dokončí.

1. Na stránce Spravovat **prostředky Azure** oddílu Najděte seznam přiřazených prostředků a odpovídajících adres URL koncových bodů.

1. Zkopírujte ukázkový dotaz do okna prohlížeče a přidejte své uživatelské utterance jako parametr `query`.

## <a name="prediction-endpoint-request"></a>Požadavek koncového bodu předpovědi

`query=` na konci adresy URL je krátká pro **dotaz** a je místo, kde se k žádosti Get připojí utterance uživatele. Po `query=`zadejte stejný utterance uživatele, který jste použili na konci předchozího rychlého startu:

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

Chcete-li zobrazit stejnou úroveň informací v podokně test, je nutné aplikaci publikovat. Po publikování aplikace vyberte v podokně test možnost **Porovnat s publikováním** . Pokud chcete zobrazit stejný kód JSON jako předchozí krok, použijte **zobrazení zobrazit JSON** v publikovaném podokně test. Tímto způsobem můžete porovnat aktuální aplikaci, na které pracujete, s aplikací, která je publikovaná do koncového bodu.

[![porovnat aktuálně upravované a publikované verze aplikace](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, vyberte **Moje aplikace** z horní navigační nabídky. V seznamu vyberte zaškrtávací políčko aplikace a pak na panelu nástrojů kontextu nad seznamem vyberte **Odstranit** .

[![odstranit aplikaci ze seznamu Moje aplikace](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Identifikace běžných záměrů a entit](luis-tutorial-prebuilt-intents-entities.md)
