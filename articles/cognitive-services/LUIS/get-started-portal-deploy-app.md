---
title: 'Úvodní příručka: Nasazení aplikace pomocí portálu LUIS'
description: Tento rychlý start ukazuje, jak nasadit aplikaci vytvořením prostředku koncového bodu předpověď, přiřazení prostředků, školení a publikování aplikace.
ms.topic: quickstart
ms.date: 04/06/2020
ms.openlocfilehash: aaf86766c2357c5382b78cd4a35fd4b159e5c0f3
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756303"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Úvodní příručka: Nasazení aplikace na portálu LUIS

Když vaše aplikace LUIS je připraven vrátit předpovědi utterance do klientské aplikace (například chatovací ho bot), je třeba nasadit aplikaci do koncového bodu předpověď.

V tomto rychlém startu se naučíte nasadit aplikaci. Můžete vytvořit prostředek koncového bodu předpověď, přiřadit prostředek k aplikaci, trénování aplikace a publikovat aplikaci.

## <a name="prerequisites"></a>Požadavky

* Získejte [předplatné Azure](https://azure.microsoft.com/free).
* Dokončete [předchozí portál ový rychlý start](get-started-portal-build-app.md) nebo si [aplikaci stáhněte a importujte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Pokud máte aplikace, které předdatem ověřování prostředků Azure, [migrujte do prostředku Azure](luis-migration-authoring.md). Některé stránky portálu vypadají jinak, když je v platnosti ověřování e-mailem.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Přiřazení klíče prostředku k aplikaci LUIS na portálu LUIS

Pokaždé, když vytvoříte nový prostředek vytváření nebo předpovědi dotazu pro LUIS, je třeba přiřadit prostředek k aplikaci LUIS. Po jeho přiřazení nebude nutné tento krok provést znovu, pokud nevytvoříte nový zdroj. Můžete vytvořit nový prostředek pro rozšíření oblastí aplikace nebo pro podporu vyššího počtu dotazů předpověď.

1. Přihlaste se k [portálu LUIS preview](https://preview.luis.ai) a zvolte aplikaci **myEnglishApp** ze seznamu aplikací.

1. V pravé horní nabídce vyberte **Spravovat** a pak vyberte **Azure Resources**.

1. Chcete-li přidat službu LUIS, vyberte **přidat prostředek předpovědi**.

    ![Chcete-li přidat prostředek předpověď LUIS, vyberte Přidat prostředek předpověď](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Vyberte svého klienta, předplatné a název prostředku. Vyberte **Přiřadit zdroj**.

   ![Přiřazení zdroje k aplikaci](./media/get-started-portal-deploy-app/assign-resource.png)

1. Stejným postupem přidejte do aplikace klíč pro vytváření.

1. Najděte nový řádek v tabulce pro nový prostředek předpověď a zkopírujte adresu URL koncového bodu. Je správně sestavena tak, `HTTP GET` aby požadavek na koncový bod rozhraní API LUIS pro předpověď.

> [!TIP]
> Pokud chcete použít aktivní učení ke zlepšení aplikace LUIS, vyberte **Změnit parametry dotazu** a vyberte **Uložit protokoly**. Tato akce změní ukázkovou `log=true` adresu URL přidáním parametru querystring. Zkopírujte a použijte adresu URL změněného příkladu dotazu při vytváření dotazů předpověď do koncového bodu runtime.

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publikování aplikace do koncového bodu předpovědi

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Požadavek koncového bodu předpověď

Na portálu `query=` preview je na konci adresy URL, kde je projev uživatele připojen k požadavku GET. Za `query=`, zadejte stejný projev uživatele použitý na konci předchozího rychlého startu:

```Is there a form named hrf-234098```

Ujistěte se, že řetězec dotazu obsahuje následující dvojice:

* `show-all-intents=true`
* `verbose=true`

Prohlížeč zobrazuje odpověď:

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

Chcete-li zobrazit stejnou úroveň informací v testovacím podokně, musíte aplikaci publikovat. Po publikování aplikace vyberte **Porovnat s publikovaným** v testovacím podokně. Pomocí **zobrazení Zobrazit JSON** v publikovaném testovacím podokně zobrazíte stejný json jako v předchozím kroku. Tímto způsobem můžete porovnat změny aktuální aplikace, na které pracujete, s aplikací, která je publikovaná do koncového bodu.

[![Porovnat aktuálně úpravy a publikovanou verzi aplikace](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi s tímto rychlým startem, vyberte **moje aplikace** v horní navigační nabídce. Zaškrtněte políčko aplikace ze seznamu a pak vyberte **Odstranit** z pruhu nástrojů kontextu nad seznamem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Identifikace společných záměrů a entit](luis-tutorial-prebuilt-intents-entities.md)
