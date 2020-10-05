---
title: 'Rychlý Start: nasazení aplikace pomocí portálu LUIS'
description: V tomto rychlém startu se dozvíte, jak nasadit aplikaci vytvořením prostředku koncového bodu předpovědi, přiřazení prostředku, školení a publikování aplikace.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/06/2020
ms.openlocfilehash: 6126c6e37ad43ed6456b224d043dc6695fa2d00e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91298404"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Rychlý Start: nasazení aplikace na portálu LUIS

Když je vaše aplikace LUIS připravená na vrácení utterance předpovědi do klientské aplikace (například robota chatu), musíte aplikaci nasadit do koncového bodu předpovědi.

V tomto rychlém startu se naučíte nasadit aplikaci. Vytvoříte prostředek koncového bodu předpovědi, přiřadíte ho k aplikaci, provedete výuku aplikace a publikujete aplikaci.

## <a name="prerequisites"></a>Předpoklady

* Získejte [předplatné Azure](https://azure.microsoft.com/free).
* Dokončete [předchozí portál pro rychlý Start](get-started-portal-build-app.md) nebo [si stáhněte a importujte aplikaci](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* Pokud máte aplikace, které předem nastavily ověřování prostředků Azure, [migrujte na prostředek Azure](luis-migration-authoring.md). Některé stránky portálu při ověřování e-mailu vypadají jinak.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Přiřazení klíče prostředků k aplikaci LUIS na portálu LUIS

Pokaždé, když vytvoříte nový prostředek pro vytváření obsahu nebo předpovědi pro LUIS, je potřeba přiřadit prostředek k aplikaci LUIS. Po přiřazení Tento krok nebudete muset provádět znovu, dokud nevytvoříte nový prostředek. Můžete vytvořit nový prostředek pro rozšíření oblastí vaší aplikace nebo pro podporu většího počtu předpovědních dotazů.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a v seznamu aplikací vyberte aplikaci **myEnglishApp** .

1. V pravé horní nabídce vyberte **Spravovat** a pak vyberte **prostředky Azure**.

1. Chcete-li přidat LUIS, vyberte **Přidat prostředek předpovědi**.

    ![Pokud chcete přidat prostředek předpovědi LUIS, vyberte Přidat prostředek předpovědi.](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Vyberte svého tenanta, předplatné a název prostředku. Vyberte **přiřadit prostředek**.

   > [!div class="mx-imgBorder"]
   > ![Přiřazení prostředku k aplikaci](./media/get-started-portal-deploy-app/assign-resource.png)

1. Při přidávání klíčového obsahu do aplikace proveďte stejný postup.

1. Vyhledejte nový řádek v tabulce pro nový prostředek předpovědi a zkopírujte adresu URL koncového bodu. Je správně sestavena tak, aby vytvořila `HTTP GET` požadavek na koncový bod rozhraní API Luis pro předpověď.

> [!TIP]
> Pokud máte v úmyslu k vylepšení aplikace LUIS používat aktivní výuku, vyberte možnost **změnit parametry dotazu** a vyberte **Uložit protokoly**. Tato akce změní ukázkovou adresu URL přidáním `log=true` parametru QueryString. Kopírování a použití změněné ukázkové adresy URL dotazu při provádění prediktivních dotazů na koncový bod modulu runtime.

## <a name="train-the-app"></a>Trénování aplikace

Pokud jste aplikaci změnili, protože jste ji proučeni jako poslední, [prohlaste](get-started-portal-build-app.md) si aplikaci.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publikování aplikace do koncového bodu předpovědi

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Požadavek koncového bodu předpovědi

Na portálu na `query=` konci adresy URL je místo, kde se utterance uživatele připojí k žádosti o získání. Po `query=` zadání zadejte stejný utterance uživatele, který jste použili na konci předchozího rychlého startu:

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

[![Porovnat aktuálně upravované a publikované verze aplikace](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, vyberte **Moje aplikace** z horní navigační nabídky. V seznamu vyberte zaškrtávací políčko aplikace a pak na panelu nástrojů kontextu nad seznamem vyberte **Odstranit** .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Identifikace běžných záměrů a entit](luis-tutorial-prebuilt-intents-entities.md)
