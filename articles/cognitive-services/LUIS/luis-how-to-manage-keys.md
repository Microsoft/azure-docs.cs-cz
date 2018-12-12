---
title: Správa klíčů
titleSuffix: Language Understanding - Azure Cognitive Services
description: Po vytvoření klíče koncového bodu služby LUIS na webu Azure Portal se přiřadit aplikaci LUIS klíč a získat adresu URL správný koncový bod. Pomocí této adresy URL koncového bodu můžete získat LUIS předpovědi.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088098"
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
<a name="assign-resource"></a>


## <a name="assign-resource-in-luis-portal"></a>Přiřazení prostředků na portálu služby LUIS

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

### <a name="unassign-resource"></a>Zrušit přiřazení prostředků
Při její přiřazení zrušit klíče koncového bodu není odstraněn z Azure. Je jenom byl odpojen od služby LUIS. 

Pokud klíče rozhraní koncového bodu je nepřiřazené nebo není přiřazená k aplikaci, některá z žádostí do koncového bodu adresy URL vrátí chybu: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Zahrnout všechny predikovaného hodnocení záměru
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

### <a name="enable-bing-spell-checker"></a>Povolit kontrolu pravopisu Bingu 
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


### <a name="publishing-regions"></a>Publikování oblastí

Další informace o publikování [oblastech](luis-reference-regions.md) včetně publikování v [Evropa](luis-reference-regions.md#publishing-to-europe), a [Austrálie](luis-reference-regions.md#publishing-to-australia). Publikování oblastech se liší od vytváření oblastí. Vytvoření aplikace ve zdrojové oblasti odpovídající publikování oblast, kterou chcete použít pro koncový bod dotazu.

## <a name="assign-resource-without-luis-portal"></a>Přiřazení prostředků bez portál LUIS

Pro účely automatizace, jako je kanál CI/CD můžete automatizovat přiřazování prostředků LUIS na aplikaci LUIS. V pořadí, je třeba provést následující kroky:

1. Získání tokenu Azure Resource Manageru z tohoto [webu](https://resources.azure.com/api/token?plaintext=true). Vypršení platnosti tohoto tokenu tak okamžitě použít. Požadavek vrátí token Azure Resource Manageru.

    ![Požádat o token Azure Resource Manageru a zobrazit token Azure Resource Manageru](./media/luis-manage-keys/get-arm-token.png)

1. Použití tokenu k žádosti o služby LUIS prostředky napříč předplatnými, od [získat LUIS azure účty rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), váš uživatelský účet má přístup k. 

    Toto rozhraní API příspěvku vyžaduje následující nastavení:

    |Hlavička|Hodnota|
    |--|--|
    |`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezerou.| 
    |`Ocp-Apim-Subscription-Key`|Vaše [vytváření klíč](luis-how-to-account-settings.md).|

    Toto rozhraní API vrátí pole objektů JSON z vašich předplatných LUIS, včetně ID předplatného, skupiny prostředků a název prostředku, vrátí jako název účtu. Najdete jednu položku v poli, který je zdrojem LUIS přiřazení k aplikaci LUIS. 

1. Přiřadit prostředek LUIS s token [přiřazení LUIS účty azure k aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) rozhraní API. 

    Toto rozhraní API příspěvku vyžaduje následující nastavení:

    |Typ|Nastavení|Hodnota|
    |--|--|--|
    |Hlavička|`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezerou.|
    |Hlavička|`Ocp-Apim-Subscription-Key`|Vaše [vytváření klíč](luis-how-to-account-settings.md).|
    |Hlavička|`Content-type`|`application/json`|
    |Řetězec dotazu|`appid`|ID aplikace LUIS. 
    |Tělo||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e"<br>"ResourceGroup": "resourcegroup-2"<br>"AccountName": "luis-uswest-S0-2"}|

    Po úspěšné toto rozhraní API vrátí stav 201 – vytvořeno. 

## <a name="next-steps"></a>Další postup

Publikování aplikace v pomocí svého klíče **publikovat aplikaci** stránky. Pokyny k publikování najdete v tématu [publikovat aplikaci](luis-how-to-publish-app.md).

V tématu [klíče v LUIS](luis-concept-keys.md) koncepce LUIS vytváření obsahu a koncový bod klíče.
