---
title: Použití klíčů pro vytváření a runtime – LUIS
description: Při prvním použití jazyka (LUIS), není nutné vytvořit klíč vytváření. Když máte v úmyslu publikovat aplikaci, pak použijte koncový bod runtime, musíte vytvořit a přiřadit klíč runtime k aplikaci.
services: cognitive-services
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: ea2799e7b8bb18ad4a729a70ae1477cde9f97e95
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754341"
---
# <a name="create-luis-resources"></a>Vytvoření prostředků luis

Vytváření a runtime prostředky poskytují ověřování pro vaši aplikaci LUIS a koncový bod předpovědi.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Když se přihlásíte k portálu LUIS, můžete pokračovat:

* zdarma [zkušební klíč](#trial-key) - poskytuje vytváření a několik dotazů koncového bodu předpovědi.
* prostředek vytváření Azure [LUIS.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Přihlaste se k portálu LUIS a začněte vytvářet

1. Přihlaste se k [portálu LUIS](https://www.luis.ai) a odsouhlaste podmínky použití.
1. Začněte s aplikací LUIS výběrem typu vývojového klíče LUIS, který chcete použít: bezplatný zkušební klíč nebo nový klíč pro vytváření Azure LUIS.

    ![Výběr prostředku vytváření language understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po dokončení procesu výběru zdrojů [vytvořte novou aplikaci](luis-how-to-start-new-app.md#create-new-app-in-luis).

## <a name="trial-key"></a>Zkušební klíč

Zkušební (startovací) klíč je k dispozici pro vás. Používá se jako ověřovací klíč k dotazování na běh koncového bodu předpověď, až 1000 dotazů za měsíc.

Je viditelná na stránce **Nastavení uživatele** i na stránkách **spravovat -> prostředků Azure** na portálu LUIS.

Až budete připraveni publikovat koncový bod předpověď, [vytvořit](#create-luis-resources) a [přiřadit](#assign-a-resource-to-an-app) vytváření a předpověď runtime klíče, nahradit funkce klíče startéru.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Vytváření prostředků v azure cli

Pomocí [azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vytvořit každý prostředek jednotlivě.

Zdroj `kind`:

* Vytváření:`LUIS.Authoring`
* Předpověď:`LUIS`

1. Přihlaste se k azure cli:

    ```azurecli
    az login
    ```

    Tím se otevře prohlížeč, který vám umožní vybrat správný účet a poskytnout ověření.

1. Vytvořte **vývojový prostředek**LUIS `LUIS.Authoring`, `my-luis-authoring-resource` druhu , pojmenované `my-resource-group` v `westus` _existující_ skupině prostředků s názvem pro oblast.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Vytvořte **prostředek koncového bodu předpovědi LUIS**, druhu `my-resource-group` `westus` `LUIS`, pojmenované `my-luis-prediction-resource` v _existující_ skupině prostředků s názvem pro oblast. Pokud chcete vyšší propustnost než `F0` úroveň `S0`free, změňte na . Přečtěte si další informace o [cenových úrovních a propustnosti](luis-boundaries.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Tyto klíče **nejsou** používány portálem LUIS, dokud nejsou přiřazeny na portálu LUIS na **spravovat -> prostředků Azure**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Přiřazení vývojového prostředku na portálu LUIS pro všechny aplikace

Můžete přiřadit zdrojovací prostředek pro jednu aplikaci nebo pro všechny aplikace v LUIS. Následující postup přiřadí všechny aplikace k jednomu prostředku vytváření.

1. Přihlaste se k [portálu LUIS](https://www.luis.ai).
1. Na horním navigačním panelu zcela vpravo vyberte svůj uživatelský účet a pak vyberte **Nastavení**.
1. Na stránce **Nastavení uživatele** vyberte Přidat zdroj pro **vytváření a** vyberte existující zdroj pro vytváření. Vyberte **Uložit**.

## <a name="assign-a-resource-to-an-app"></a>Přiřazení prostředku k aplikaci

Aplikaci s následujícím postupem můžete přiřadit jeden prostředek, vytváření nebo předpověď koncového bodu.

1. Přihlaste se k [portálu LUIS](https://www.luis.ai)a vyberte aplikaci ze seznamu **Moje aplikace.**
1. Přejděte na stránku **Spravovat –> prostředků Azure.**

    ![Vyberte spravovat -> prostředky Azure na portálu LUIS a přiřaďte k aplikaci prostředek.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Vyberte kartu Předpověď nebo Vytváření prostředků a pak vyberte **tlačítko Přidat prostředek předpovědi** nebo **Přidat zdroj pro vytváření.**
1. Vyberte pole ve formuláři, chcete-li najít správný zdroj, vyberte **uložit**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Přiřazení runtime prostředku bez použití portálu LUIS

Pro účely automatizace, jako je například kanál CI/CD, můžete automatizovat přiřazení prostředku runtime LUIS do aplikace LUIS. Chcete-li to provést, musíte provést následující kroky:

1. Získejte token Azure Resource Manager z tohoto [webu](https://resources.azure.com/api/token?plaintext=true). Platnost tohoto tokenu vyprší, proto jej okamžitě použijte. Požadavek vrátí token Azure Resource Manager.

    ![Vyžádání tokenu Správce prostředků Azure a přijetí tokenu Azure Resource Manageru](./media/luis-manage-keys/get-arm-token.png)

1. Pomocí tokenu můžete požádat o prostředky runtime LUIS napříč předplatnými, z [rozhraní API účtů Získat LUIS azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ke kterému má váš uživatelský účet přístup.

    Toto rozhraní POST API vyžaduje následující nastavení:

    |Hlavička|Hodnota|
    |--|--|
    |`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu `Bearer` musí předcházet slovo a mezeru.|
    |`Ocp-Apim-Subscription-Key`|Váš autorský klíč.|

    Toto rozhraní API vrátí pole JSON objektů vašeho předplatného LUIS, včetně ID předplatného, skupiny prostředků a názvu prostředku, vrácené jako název účtu. Najděte jednu položku v poli, která je prostředek LUIS přiřadit k aplikaci LUIS.

1. Přiřaďte token prostředku LUIS pomocí přiřazení účtů LUIS azure k rozhraní API [aplikace.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515)

    Toto rozhraní POST API vyžaduje následující nastavení:

    |Typ|Nastavení|Hodnota|
    |--|--|--|
    |Hlavička|`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu `Bearer` musí předcházet slovo a mezeru.|
    |Hlavička|`Ocp-Apim-Subscription-Key`|Váš autorský klíč.|
    |Hlavička|`Content-type`|`application/json`|
    |Querystring|`appid`|ID aplikace LUIS.
    |Tělo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Když je toto rozhraní API úspěšné, vrátí stav 201 - vytvořeno.

## <a name="unassign-resource"></a>Zrušení přiřazení zdroje

1. Přihlaste se k [portálu LUIS](https://www.luis.ai)a vyberte aplikaci ze seznamu **Moje aplikace.**
1. Přejděte na stránku **Spravovat –> prostředků Azure.**
1. Vyberte kartu Předpověď nebo Vytváření prostředků a vyberte tlačítko **Zrušit přiřazení prostředku** pro prostředek.

Když odezníte přiřazení prostředku, neodstraní se z Azure. Je pouze odpojen od služby LUIS.

## <a name="reset-authoring-key"></a>Obnovit klíč pro vytváření

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací:** Pokud je váš klíč pro vytváření ohrožena, resetovat klíč na portálu Azure na stránce **klíče** pro tento zdroj vytváření.

**Pro aplikace, které ještě nemigrovaly**: klíč se resetuje ve všech vašich aplikacích na portálu LUIS. Pokud vytvářet aplikace prostřednictvím vytváření rozhraní API, budete muset změnit hodnotu Ocp-Apim-Subscription-Key na nový klíč.

## <a name="regenerate-azure-key"></a>Regenerovat klíč Azure

Znovu vygenerujte klíče Azure z webu Azure portal na stránce **Klíče.**

## <a name="delete-account"></a>Odstranit účet

Informace o tom, jaká data se při odstranění účtu odstraní, najdete v tématu [Ukládání a odebírání](luis-concept-data-storage.md#accounts) dat.

## <a name="change-pricing-tier"></a>Změna cenové úrovně

1.  V [Azure](https://portal.azure.com)najděte své předplatné LUIS. Vyberte předplatné LUIS.
    ![Vyhledání předplatného SLUŽBY LUIS](./media/luis-usage-tiers/find.png)
1.  Chcete-li zobrazit dostupné cenové úrovně, vyberte **cenovou úroveň.**
    ![Zobrazit cenové úrovně](./media/luis-usage-tiers/subscription.png)
1.  Vyberte cenovou úroveň a vyberte **Vybrat,** chcete-li uložit změnu.
    ![Změna platební úrovně LUIS](./media/luis-usage-tiers/plans.png)
1.  Po dokončení změny cen se automaticky otevírané okno ověří novou cenovou úroveň.
    ![Ověření platební úrovně LUIS](./media/luis-usage-tiers/updated.png)
1. Nezapomeňte [přiřadit tento klíč koncového bodu](#assign-a-resource-to-an-app) na stránce **Publikovat** a použít jej ve všech dotazech koncového bodu.

## <a name="viewing-azure-resource-metrics"></a>Zobrazení metrik prostředků Azure

### <a name="viewing-azure-resource-summary-usage"></a>Zobrazení využití souhrnu prostředků Azure
Informace o využití LUIS můžete zobrazit v Azure. Stránka **Přehled** zobrazuje nejnovější souhrnné informace včetně volání a chyb. Pokud provedete požadavek koncového bodu LUIS, okamžitě sledovat **stránku Přehled**, vyznat až pět minut pro využití se zobrazí.

![Zobrazení souhrnného využití](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Přizpůsobení grafů využití prostředků Azure
Metriky poskytuje podrobnější zobrazení dat.

![Výchozí metriky](./media/luis-usage-tiers/metrics-default.png)

Můžete nakonfigurovat grafy metrik pro časové období a typ metriky.

![Vlastní metriky](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Výstraha prahu celkových transakcí
Pokud chcete vědět, kdy jste dosáhli určité prahové hodnoty transakce, například 10 000 transakcí, můžete vytvořit výstrahu.

![Výchozí výstrahy](./media/luis-usage-tiers/alert-default.png)

Přidejte upozornění metriky pro **metriku celkový počet hovorů** za určité časové období. Přidejte e-mailové adresy všech lidí, kteří by měli upozornění obdržet. Přidejte webhooky pro všechny systémy, které by měly přijímat výstrahu. Můžete také spustit aplikaci logiky při aktivaci výstrahy.

## <a name="next-steps"></a>Další kroky

* Přečtěte [si, jak řídit](luis-how-to-manage-versions.md) životní cyklus aplikace pomocí verzí.
* Seznamte se s koncepty, včetně [zdrojového zdroje pro vytváření](luis-concept-keys.md#authoring-key) a [přispěvatelů](luis-concept-keys.md#contributions-from-other-authors) k tomuto prostředku.
* Informace [o vytváření](luis-how-to-azure-subscription.md) zdrojů pro vytváření a runtime
* Migrace do nového [zdrojového zdroje pro vytváření](luis-migration-authoring.md)
