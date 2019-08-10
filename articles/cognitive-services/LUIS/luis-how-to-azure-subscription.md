---
title: Klíče předplatného – LUIS
titleSuffix: Azure Cognitive Services
description: Nemusíte vytvářet klíče předplatného, abyste mohli používat bezplatné dotazy na koncový bod od prvního 1000. Pokud se vám zobrazí zpráva o nedostatku _kvóty_ ve formě HTTP 403 nebo 429, je potřeba vytvořit klíč a přiřadit ho k aplikaci.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: 1f8b84722c881cee1fe196e5a614b58cf3c19031
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932859"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Použití klíčů předplatného s aplikací LUIS

Když poprvé použijete Language Understanding (LUIS), nemusíte vytvářet klíče předplatného. Máte 1000 dotazů koncového bodu, které mají začít. 

Pro účely testování a prototypu pouze úroveň free (F0) můžete použijte. Pro produkční systémy, používat [placené](https://aka.ms/luis-price-tier) vrstvy. Nepoužívejte [vytváření klíč](luis-concept-keys.md#authoring-key) za dotazy na koncový bod v produkčním prostředí.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Vytvořit předpověď prostředku modulu runtime koncového bodu v Azure Portal

V Azure Portal vytvoříte [prostředek koncového bodu předpovědi](get-started-portal-deploy-app.md#create-the-endpoint-resource) . Tento prostředek by se měl používat jenom pro dotazy předpovědi koncových bodů. Nepoužívejte tento prostředek k vytváření změn v aplikaci.

Můžete vytvořit prostředek Language Understanding nebo prostředek Cognitive Services. Pokud vytváříte prostředek Language Understanding, dobrým zvykem je postpend typ prostředku do názvu prostředku. 

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

### <a name="using-resource-from-luis-portal"></a>Používání prostředků z portálu LUIS

Pokud prostředek používáte z portálu LUIS, nemusíte znát svůj klíč a umístění. Místo toho potřebujete znát svůj tenant, předplatné a název prostředku.

Po [přiřazení](#assign-resource-key-to-luis-app-in-luis-portal) prostředku k aplikaci Luis na portálu Luis se klíč a umístění poskytují jako součást adresy URL koncového bodu prediktivního dotazování na stránce Správa **klíčů a nastavení koncového bodu** oddílu.
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Používání prostředků z REST API nebo SDK

Pokud používáte prostředek z REST API (s) nebo sady SDK, musíte znát svůj klíč a umístění. Tyto informace jsou k dispozici jako součást adresy URL koncového bodu předpovědi dotazů na stránce Správa **klíčů a nastavení koncových bodů oddílu a** také v Azure Portal na stránkách přehled a klíče prostředku.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Přiřazení klíče prostředků k aplikaci LUIS na portálu LUIS

Pokaždé, když vytvoříte nový prostředek pro LUIS, je potřeba [přiřadit prostředek k aplikaci Luis](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). Po přiřazení Tento krok nebudete muset provádět znovu, dokud nevytvoříte nový prostředek. Můžete vytvořit nový prostředek pro rozšíření oblastí vaší aplikace nebo pro podporu většího počtu předpovědních dotazů.

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

Přidejte parametr " **kontrolu pravopisu = true** QueryString" a **Bing-check-Subscription-Key = {YOUR_BING_KEY_HERE}** . Nahradit `{YOUR_BING_KEY_HERE}` vaším klíčem kontrolu pravopisu Bingu.

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

Pro účely automatizace, jako je kanál CI/CD můžete automatizovat přiřazování prostředků LUIS na aplikaci LUIS. Abyste to mohli udělat, musíte provést následující kroky:

1. Získání tokenu Azure Resource Manageru z tohoto [webu](https://resources.azure.com/api/token?plaintext=true). Vypršení platnosti tohoto tokenu tak okamžitě použít. Požadavek vrátí token Azure Resource Manageru.

    ![Vyžádat token Azure Resource Manager a získat token Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

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

## <a name="change-pricing-tier"></a>Změnit cenovou úroveň

1.  V [Azure](https://portal.azure.com), vyhledejte své předplatné služby LUIS. Vyberte předplatné LUIS.
    ![Vyhledejte své předplatné služby LUIS](./media/luis-usage-tiers/find.png)
1.  Vyberte **cenovou úroveň** , aby se zobrazily dostupné cenové úrovně. 
    ![Zobrazení cenové úrovně](./media/luis-usage-tiers/subscription.png)
1.  Vyberte cenovou úroveň a výběrem **Vybrat** uložte změnu. 
    ![Změnit úroveň služby LUIS platby](./media/luis-usage-tiers/plans.png)
1.  Po dokončení při změně cen se automaticky otevírané okno ověří novou cenovou úroveň. 
    ![Ověřte svou úroveň platby LUIS](./media/luis-usage-tiers/updated.png)
1. Nezapomeňte [přiřadit tento klíč koncového bodu](#assign-endpoint-key) na **publikovat** stránce a používat ho v všechny dotazy na koncový bod. 

## <a name="fix-http-status-code-403-and-429"></a>Opravte stavový kód HTTP 403 a 429

Stavové kódy chyb 403 a 429 získáte, když překročíte transakce za sekundu nebo transakce za měsíc pro vaši cenovou úroveň.

### <a name="when-you-receive-an-http-403-error-status-code"></a>Když obdržíte stavový kód chyby HTTP 403

Když použijete všechny tyto bezplatné dotazy na koncový bod 1000 nebo překročíte kvótu měsíčních transakcí vaší cenové úrovně, obdržíte kód stavu chyby HTTP 403. 

Pokud chcete tuto chybu opravit, musíte [změnit svou cenovou úroveň](luis-how-to-azure-subscription.md#change-pricing-tier) na vyšší úroveň nebo [vytvořit nový prostředek](get-started-portal-deploy-app.md#create-the-endpoint-resource) a [přiřadit ho k aplikaci](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Mezi řešení této chyby patří:

* V [Azure Portal](https://portal.azure.com)v prostředku Language Understanding na **cenové úrovni správa prostředků – >** změňte cenovou úroveň na vyšší úroveň TPS. Pokud je váš prostředek už přiřazený k aplikaci Language Understanding, nemusíte dělat nic na Language Understandingovém portálu.
*  Pokud vaše využití přesáhne nejvyšší cenovou úroveň, přidejte další Language Understanding prostředky s nástrojem pro vyrovnávání zatížení před nimi. K tomu může pomáhat [kontejner Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose.

### <a name="when-you-receive-an-http-429-error-status-code"></a>Když obdržíte stavový kód chyby HTTP 429

Tento stavový kód se vrátí, když vaše transakce za sekundu překročí vaši cenovou úroveň.  

Mezi řešení patří:

* Pokud nejste na nejvyšší úrovni, můžete [zvýšit svou cenovou úroveň](#change-pricing-tier).
* Pokud vaše využití přesáhne nejvyšší cenovou úroveň, přidejte další Language Understanding prostředky s nástrojem pro vyrovnávání zatížení před nimi. K tomu může pomáhat [kontejner Language Understanding](luis-container-howto.md) s Kubernetes nebo Docker Compose.
* Pomocí [zásady opakování](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) , kterou sami implementujete, můžete své požadavky na klientskou aplikaci vymezit tak, že se zobrazí tento stavový kód. 

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
