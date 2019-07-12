---
title: Klíčů předplatného
titleSuffix: Language Understanding - Azure Cognitive Services
description: Není nutné k vytvoření klíčů předplatného používat dotazy zdarma prvních 1 000 koncových bodů. Pokud se zobrazí _překročení kvóty_ chyby ve formuláři HTTP 403 a 429, budete muset vytvořit klíč a přiřaďte ho do své aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: dedc498ebc910b448b1684136c288b2045780e00
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797955"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Použití klíčů předplatného s aplikací LUIS

Při prvním použití Language Understanding (LUIS), není nutné k vytvoření klíčů předplatného. 1000 koncový bod dotazy jsou uvedeny na začátku. 

Pro účely testování a prototypu pouze úroveň free (F0) můžete použijte. Pro produkční systémy, používat [placené](https://aka.ms/luis-price-tier) vrstvy. Nepoužívejte [vytváření klíč](luis-concept-keys.md#authoring-key) za dotazy na koncový bod v produkčním prostředí.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Vytvoření predikcí koncový bod runtimový prostředek na webu Azure Portal

Můžete vytvořit [předpovědi koncový bod prostředku](get-started-portal-deploy-app.md#create-the-endpoint-resource) na webu Azure Portal. Tento prostředek by měla sloužit pouze pro dotazy předpovědi s koncový bod. Tento prostředek nepoužívají vytváření změny do aplikace.

Můžete vytvořit umožňující porozumět jazyku prostředku nebo prostředku služeb Cognitive Services. Při vytváření prostředku Language Understanding, je vhodné postpend prostředek zadejte název prostředku. 

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

### <a name="using-resource-from-luis-portal"></a>Použití prostředků z portálu služby LUIS

Pokud používáte prostředků z portálu služby LUIS, není potřeba znát klíč a umístění. Místo toho je potřeba vědět tenanta prostředků, předplatné a název prostředku.

Jakmile [přiřadit](#assign-resource-key-to-luis-app-in-luis-portal) váš prostředek do vaší aplikace LUIS v portálu LUIS, klíč a umístění jsou k dispozici jako součást dotazu předpovědi koncový bod adresy URL v části Správa **klíče a koncových bodů nastavení** stránky.
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Použití prostředků z rozhraní REST API nebo sady SDK

Pokud používáte prostředku z REST API(s) nebo SDK, je potřeba vědět klíč a umístění. Tyto informace je k dispozici jako součást dotazu předpovědi koncový bod adresy URL v části Správa **klíče a koncových bodů nastavení** stránce i webu Azure portal, na stránkách přehledu a klíče prostředku.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Klíč prostředku přiřadit aplikace LUIS portálu LUIS

Pokaždé, když vytvoříte nový prostředek pro LUIS, budete muset [přiřazení prostředku k aplikaci LUIS](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). Po přiřazení, nebudete muset znovu proveďte tento krok, není-li vytvořit nový prostředek. Můžete vytvořit nový prostředek rozbalte oblasti vaší aplikace nebo pro podporu větší počet dotazy předpovědi.

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
V **nastavení adresy url koncového bodu**, **kontrolu pravopisu Bingu** přepínač umožňuje LUIS opravovat slova s překlepem před předpovědi. Vytvoření  **[kontrolu pravopisu Bingu klíč](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

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

## <a name="fix-http-status-code-403-and-429"></a>Oprava stavový kód HTTP 403 a 429

Získáte chyby 403 a 429 stavové kódy překročíte transakcí za sekundu nebo transakcí za měsíc pro svou cenovou úroveň.

### <a name="when-you-receive-an-http-403-error-status-code"></a>Když se zobrazí kód stav chyby HTTP 403

Pokud použijete všechny tyto bezplatné 1000 koncový bod dotazy nebo překročí měsíční kvóta transakce svou cenovou úroveň, obdržíte kód stav chyby HTTP 403. 

Chcete-li tuto chybu opravit, musíte buď [změnit svou cenovou úroveň](luis-how-to-azure-subscription.md#change-pricing-tier) na vyšší úroveň nebo [vytvořit nový prostředek](get-started-portal-deploy-app.md#create-the-endpoint-resource) a [přiřazení k vaší aplikaci](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Řešení této chyby patří:

* V [webu Azure portal](https://portal.azure.com), na vaší Language Understanding prostředků, na **správy prostředků -> cenová úroveň**, změnit svou cenovou úroveň na vyšší úroveň TPS. Nemusíte dělat nic. portálu Language Understanding, pokud váš prostředek je už přiřazený k aplikaci umožňující porozumět jazyku.
*  Pokud využití překročí nejvyšší cenová úroveň, přidáte další prostředky Language Understanding se nástroj pro vyrovnávání zatížení před nimi. [Language Understanding kontejneru](luis-container-howto.md) s Kubernetes a Docker Compose vám ho mohl pomoct to.

### <a name="when-you-receive-an-http-429-error-status-code"></a>Když se zobrazí kód stavu HTTP 429 chyby

Tento kód stavu je vrácena, pokud vaše transakce za sekundu překračuje svou cenovou úroveň.  

Řešení zahrnují:

* Je možné [zvyšte svou cenovou úroveň](#change-pricing-tier), pokud nejsou na nejvyšší úrovni.
* Pokud využití překročí nejvyšší cenová úroveň, přidáte další prostředky Language Understanding se nástroj pro vyrovnávání zatížení před nimi. [Language Understanding kontejneru](luis-container-howto.md) s Kubernetes a Docker Compose vám ho mohl pomoct to.
* Vaší žádosti o aplikace klienta se může bran [zásady opakování](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) lze implementovat sami získat tímto stavovým kódem. 

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
