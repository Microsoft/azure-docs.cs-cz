---
title: Spravovat klíče pro vytváření a koncový bod v LUIS
titleSuffix: Azure Cognitive Services
description: Po vytvoření klíče koncového bodu služby LUIS na webu Azure Portal se přiřadit aplikaci LUIS klíč a získat adresu URL správný koncový bod. Pomocí této adresy URL koncového bodu můžete získat LUIS předpovědi.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 6d3f487fd64744fa390291d7e23d95cd9632cd23
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634935"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Přidat prostředek Azure LUIS do aplikace

Po vytvoření služby LUIS prostředků na webu Azure Portal, přiřazení prostředku k aplikaci LUIS a získat adresu URL správný koncový bod. Pomocí této adresy URL koncového bodu můžete získat LUIS předpovědi.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>

## <a name="assign-resource"></a>Přiřazení prostředků

1. Vytvoření klíče služby LUIS na [webu Azure portal](https://portal.azure.com). Další pokyny najdete v tématu [vytvořit klíč koncového bodu pomocí Azure](luis-how-to-azure-subscription.md).
 
2. Vyberte **spravovat** v pravé horní nabídce vyberte **klíče a koncových bodů**.

    [ ![Stránku klíče a koncových bodů](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. Chcete-li přidat LUIS, vybrat **přiřazení prostředků +**.

    ![Přiřadit prostředek vaší aplikace](./media/luis-manage-keys/assign-key.png)

4. Vyberte Tenanta v dialogovém okně přidružené k e-mailovou adresu, kterou se přihlašujete pomocí webu služby LUIS.  

5. Zvolte **název předplatného** spojené s prostředky Azure, které chcete přidat.

6. Vyberte **název prostředku služby LUIS**. 

7. Vyberte **přiřazení prostředků**. 

8. Nalezení nový řádek v tabulce a zkopírujte adresu URL koncového bodu. Je vytvořen správně provést požadavek HTTP GET na koncový bod služby LUIS pro předpověď. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="unassign-resource"></a>Zrušit přiřazení prostředků
Při její přiřazení zrušit klíče koncového bodu není odstraněn z Azure. Je jenom byl odpojen od služby LUIS. 

Pokud klíče rozhraní koncového bodu je nepřiřazené nebo není přiřazená k aplikaci, některá z žádostí do koncového bodu adresy URL vrátí chybu: `401 This application cannot be accessed with the current subscription`. 

## <a name="include-all-predicted-intent-scores"></a>Zahrnout všechny predikovaného hodnocení záměru
**Zahrnout všechny předpovědět záměru skóre** odpověď na dotaz koncový bod zahrnout skóre předpovědi pro každý záměru umožňuje zaškrtávací políčko. 

Toto nastavení umožňuje chatovací robot nebo LUIS volající aplikace pro programové rozhodnutí podle skóre vrácené záměry. Horní dva příkazy jsou obecně zajímá nejvíce. Pokud hlavní skóre již není žádný záměru, že váš robot můžete položit dotaz zpracování zajistíte, aby konečná možností volby mezi záměru žádný a vysokým skóre záměr. 

Příkazů a jejich výsledky jsou také zahrnuté protokoly koncového bodu. Je možné [exportovat](luis-how-to-start-new-app.md#export-app) tyto protokoly a analyzovat výsledky. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

## <a name="enable-bing-spell-checker"></a>Povolit kontrolu pravopisu Bingu 
V **nastavení adresy url koncového bodu**, **kontrolu pravopisu Bingu** přepínač umožňuje LUIS opravovat slova s překlepem před předpovědi. Vytvoření  **[kontrolu pravopisu Bingu klíč](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Přidat **kontrola pravopisu = true** parametr querystring a **Bingu – pravopisu – kontrola subscription-key = {YOUR_BING_KEY_HERE}** . Nahradit `{YOUR_BING_KEY_HERE}` vaším klíčem kontrolu pravopisu Bingu.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```


## <a name="publishing-regions"></a>Publikování oblastí

Další informace o publikování [oblastech](luis-reference-regions.md) včetně publikování v [Evropa](luis-reference-regions.md#publishing-to-europe), a [Austrálie](luis-reference-regions.md#publishing-to-australia). Publikování oblastech se liší od vytváření oblastí. Vytvoření aplikace ve zdrojové oblasti odpovídající publikování oblast, kterou chcete použít pro koncový bod dotazu.

## <a name="next-steps"></a>Další postup

Publikování aplikace v pomocí svého klíče **publikovat aplikaci** stránky. Pokyny k publikování najdete v tématu [publikovat aplikaci](luis-how-to-publish-app.md).

V tématu [klíče v LUIS](luis-concept-keys.md) koncepce LUIS vytváření obsahu a koncový bod klíče.