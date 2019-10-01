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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f640921e6f48559db3f1414551d6ed974df15e4f
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703223"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Rychlý Start: nasazení aplikace na portálu LUIS

Když je vaše aplikace LUIS připravená na vrácení utterance předpovědi do klientské aplikace (například robota chatu), musíte aplikaci nasadit do koncového bodu předpovědi.

V tomto rychlém startu se naučíte nasadit aplikaci. Vytvoříte prostředek koncového bodu předpovědi, přiřadíte ho k aplikaci, provedete výuku aplikace a publikujete aplikaci.

## <a name="prerequisites"></a>Předpoklady

* Získejte [předplatné Azure](https://azure.microsoft.com/free).
* Dokončete [předchozí portál pro rychlý Start](get-started-portal-build-app.md) nebo [si stáhněte a importujte aplikaci](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Vytvoření prostředku koncového bodu

V Azure Portal vytvoříte prostředek koncového bodu předpovědi. Tento prostředek by se měl používat jenom pro dotazy předpovědi koncových bodů. Nepoužívejte tento prostředek k vytváření změn v aplikaci.

1. Přihlaste se na web [Azure Portal](https://ms.portal.azure.com/).

1. V levém horním panelu vyberte symbol zelený **+** . Na webu Marketplace vyhledejte `Cognitive Services` a vyberte ji.

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

1. Vyhledejte nový řádek v tabulce a zkopírujte adresu URL koncového bodu. Je správně sestavena tak, aby na koncový bod LUIS API vytvořila požadavek `HTTP GET` pro předpověď.

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace

Jakmile budete připraveni na jeho testování, prohlaste si aplikaci. Publikujte aplikaci, pokud chcete, aby byla aktuálně proučená verze dostupná pro klientské aplikace z modulu runtime koncového bodu předpovědi dotazů.

1. Pokud se aplikace nepoužívá, vyberte v nabídce vpravo nahoře možnost **vlak** .

1. V horní nabídce vyberte **publikovat** . Přijměte výchozí nastavení prostředí a vyberte **publikovat**.

1. Když se v horní části okna prohlížeče zobrazí indikátor oznámení zelený úspěch, vyberte v **seznamu koncových bodů odkaz**.

   ![Oznamovací panel aplikace se úspěšně publikoval v prohlížeči.](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Na stránce **klíče a nastavení koncového bodu** vyhledejte v dolní části seznam přiřazených prostředků a odpovídající adresy URL koncových bodů.

1. Vyberte adresu URL koncového bodu přidruženou k vašemu novému názvu prostředku. Tato akce otevře webový prohlížeč se správnou vytvořenou adresou URL, která vytvoří požadavek `GET` do modulu runtime koncového bodu předpovědi.

## <a name="prediction-endpoint-request"></a>Požadavek koncového bodu předpovědi

<!-- V3FIX -->

@No__t-0 na konci adresy URL je pro **dotaz** krátký a je místo, kde se utterance uživatele připojí k žádosti Get. Po `q=` zadejte stejný utterance uživatel, který jste použili na konci předchozího rychlého startu:

```Is there a form named hrf-234098```

V prohlížeči se zobrazí odpověď, což je stejný kód JSON, který vaše klientská aplikace dostane:

```JSON
{
"query": "Is there a form named hrf-234098",
"topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9768753
},
"intents": [
    {
    "intent": "FindForm",
    "score": 0.9768753
    },
    {
    "intent": "None",
    "score": 0.0216071066
    }
],
"entities": [
    {
    "entity": "hrf-234098",
    "type": "Human Resources Form Number",
    "startIndex": 22,
    "endIndex": 31
    }
    ]
}
```

Tato odpověď poskytuje další informace, než je výchozí podokno testu v předchozím kurzu. Chcete-li zobrazit stejnou úroveň informací v podokně test, je nutné aplikaci publikovat. Po publikování aplikace vyberte v podokně test možnost **Porovnat s publikováním** . Pokud chcete zobrazit stejný kód JSON jako předchozí krok, použijte **zobrazení zobrazit JSON** v publikovaném podokně test. Tímto způsobem můžete porovnat aktuální aplikaci, na které pracujete, s aplikací, která je publikovaná do koncového bodu.

[@no__t 1Compare aktuálně upravuje a publikovaná verze aplikace](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)




## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, vyberte **Moje aplikace** z horní navigační nabídky. V seznamu vyberte zaškrtávací políčko aplikace a pak na panelu nástrojů kontextu nad seznamem vyberte **Odstranit** .

[aplikace ![Delete ze seznamu Moje aplikace](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Identifikace běžných záměrů a entit](luis-tutorial-prebuilt-intents-entities.md)
