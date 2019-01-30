---
title: Klíčů předplatného
titleSuffix: Language Understanding - Azure Cognitive Services
description: Není nutné k vytvoření klíčů předplatného používat dotazy zdarma prvních 1 000 koncových bodů. Pokud se zobrazí _překročení kvóty_ chyby ve formuláři HTTP 403 a 429, budete muset vytvořit klíč a přiřaďte ho do své aplikace.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 26954c8f839ff0bfb2da484e4fb535f33d4e07ed
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239175"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Použití klíčů předplatného s aplikací LUIS

Není nutné k vytvoření klíčů předplatného používat dotazy zdarma prvních 1 000 koncových bodů. Jakmile se používají tyto dotazy koncového bodu, vytvořte prostředek Azure v [webu Azure portal](http://portal.azure.com), daný prostředek přiřadit aplikace LUIS v [LUIS portál](https://www.luis.ai).

Pokud se zobrazí _překročení kvóty_ chyby ve formuláři HTTP 403 a 429, budete muset vytvořit klíč a přiřaďte ho do své aplikace. 

Pro účely testování a prototypu pouze úroveň free (F0) můžete použijte. Pro produkční systémy, používat [placené](https://aka.ms/luis-price-tier) vrstvy. Nepoužívejte [vytváření klíč](luis-concept-keys.md#authoring-key) za dotazy na koncový bod v produkčním prostředí.

<a name="create-luis-service"></a>

## <a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>Vytvoření klíče koncového bodu Language Understanding na webu Azure Portal

Tato procedura vytváří **Language Understanding** prostředků. Pokud chcete prostředek, který lze použít v rámci služeb Cognitive Services, vytvořte klíč All-in-one **[služby Cognitive Services](../cognitive-services-apis-create-account.md)** místo Language Understanding prostředků. 

Tento klíč by měla sloužit pouze pro dotazy předpovědi s koncový bod. Nepoužívejte tento klíč se změny modelu nebo na aplikaci. 

1. Přihlaste se k  **[webu Azure portal](https://ms.portal.azure.com/)**. 
1. Vyberte zelené **+** přihlásit v horním levém panelu a vyhledejte `Language Understanding` na webu Marketplace, pak vyberte na **Language Understanding** a postupujte podle pokynů  **Vytvoření prostředí** k vytvoření účtu předplatného služba LUIS. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

1. Nakonfigurování odběru s nastavením název účtu, včetně cenové úrovně, atd. 

    ![Volbou Azure API](./media/luis-azure-subscription/azure-api-choice.png) 

1. Jakmile vytvoříte prostředek Language Understanding, můžete zobrazit přístupové klíče vygenerované v **správy prostředků -> klíče**. Jak propojit tento nový prostředek na aplikaci LUIS na portálu služby LUIS se zobrazí v další části. Budete potřebovat název prostředku služby LUIS z kroku 3.

    ![Azure klíče](./media/luis-azure-subscription/azure-keys.png)

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


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Klíč prostředku přiřadit aplikace LUIS portálu LUIS

1. Přihlaste se k portálu služby LUIS, zvolte aplikaci, kterou chcete přidat nový klíč a vyberte **spravovat** v pravé horní nabídce vyberte **klíče a koncových bodů**.

    [ ![Stránku klíče a koncových bodů](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

1. Chcete-li přidat LUIS, vybrat **přiřazení prostředků +**.

    ![Přiřadit prostředek vaší aplikace](./media/luis-manage-keys/assign-key.png)

1. Vyberte Tenanta v dialogovém okně přidružené k e-mailu adresa vaší slouží k přihlášení na web služby LUIS.  

1. Zvolte **název předplatného** spojené s prostředky Azure, které chcete přidat.

1. Vyberte **název prostředku služby LUIS**. 

1. Vyberte **přiřazení prostředků**. 

1. Nalezení nový řádek v tabulce a zkopírujte adresu URL koncového bodu. Je vytvořen správně provést požadavek HTTP GET na koncový bod služby LUIS pro předpověď. 

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

Přidat **kontrola pravopisu = true** parametr querystring a **Bingu – pravopisu – kontrola subscription-key = {YOUR_BING_KEY_HERE}**. Nahradit `{YOUR_BING_KEY_HERE}` vaším klíčem kontrolu pravopisu Bingu.

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

Pro účely automatizace, jako je kanál CI/CD můžete automatizovat přiřazování prostředků LUIS na aplikaci LUIS. Aby bylo možné provést, je třeba provést následující kroky:

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

## <a name="change-pricing-tier"></a>Změna cenové úrovně

1.  V [Azure](https://portal.azure.com), vyhledejte své předplatné služby LUIS. Vyberte předplatné, LUIS.
    ![Vyhledejte své předplatné služby LUIS](./media/luis-usage-tiers/find.png)
1.  Vyberte **cenová úroveň** Chcete-li zobrazit dostupné cenové úrovně. 
    ![Zobrazení cenové úrovně](./media/luis-usage-tiers/subscription.png)
1.  Vyberte cenovou úroveň a vyberte **vyberte** změny se uloží. 
    ![Změnit úroveň služby LUIS platby](./media/luis-usage-tiers/plans.png)
1.  Po dokončení při změně cen se automaticky otevírané okno ověří novou cenovou úroveň. 
    ![Ověřte svou úroveň platby LUIS](./media/luis-usage-tiers/updated.png)
1. Nezapomeňte [přiřadit tento klíč koncového bodu](#assign-endpoint-key) na **publikovat** stránce a používat ho v všechny dotazy na koncový bod. 

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>K vyřešení chyby limit kvóty, pokud klíč překročí využití cenové úrovně
Každá úroveň umožňuje koncový bod požadavků na účet služby LUIS určitou rychlostí. Pokud se o počet žádostí o je vyšší než povolený počet účtu Účtovaná podle objemu dat za minutu nebo za měsíc, požadavky obdrží chybu HTTP "429: Příliš mnoho požadavků."

Každá úroveň umožňuje kumulativní žádostí za měsíc. Pokud celkový počet požadavků jsou vyšší než povolený počet souborů, požadavky obdrží chybu HTTP "403: zakázáno".  

## <a name="viewing-summary-usage"></a>Zobrazení souhrnu využití
Informace o použití LUIS můžete zobrazit v Azure. **Přehled** stránka zobrazuje poslední souhrnné informace, včetně volání a chyby. Pokud provedete požadavku koncového bodu LUIS, pak okamžitě podívejte se **stránka s přehledem**, povolit pro použití se zobrazí až pět minut.

![Zobrazení souhrnu využití](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Přizpůsobení využití grafů
Metriky poskytuje podrobnější pohled na data.

![Výchozí metriky](./media/luis-usage-tiers/metrics-default.png)

Grafy metrik můžete nakonfigurovat pro časové období a typu metrika. 

![Vlastní metriky](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Celkový počet transakcí prahová hodnota výstrahy
Pokud chcete vědět, kdy jste dosáhli určitou transakci prahovou hodnotu, například 10 000 transakcí, můžete vytvořit výstrahu. 

![Výchozí upozornění](./media/luis-usage-tiers/alert-default.png)

Přidat upozornění metriky pro **celkový počet volání** metriky pro určité časové období. Přidáte e-mailové adresy lidí, kteří měli obdržet upozornění. Přidání webhooků pro všechny systémy, které by měla přijímat oznámení. Aplikace logiky můžete spustit také při aktivaci tohoto upozornění. 

## <a name="next-steps"></a>Další postup

Další informace o použití [verze](luis-how-to-manage-versions.md) spravovat změny aplikace LUIS.
