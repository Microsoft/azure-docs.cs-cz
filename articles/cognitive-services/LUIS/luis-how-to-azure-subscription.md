---
title: Jak používat klíč pro vytváření a modul runtime – LUIS
description: Při prvním použití Language Understanding (LUIS) není nutné vytvářet klíč pro vytváření obsahu. Pokud máte v úmyslu publikovat aplikaci, pak použijte koncový bod za běhu, musíte pro aplikaci vytvořit a přiřadit klíč modulu runtime.
services: cognitive-services
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: d9235b6ef1c7cddbfbbd36f8382439d781af6d5f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82101021"
---
# <a name="create-luis-resources"></a>Vytvoření prostředků LUIS

Prostředky pro vytváření obsahu a modul runtime poskytují ověřování do vaší aplikace LUIS a koncového bodu předpovědi.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Když se přihlásíte na portál LUIS, můžete se rozhodnout, že budete pokračovat v těchto akcích:

* bezplatný [zkušební klíč](#trial-key) – poskytuje vytváření a několik dotazů koncového bodu předpovědi.
* prostředek pro [vytváření obsahu Azure Luis](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) .

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Přihlaste se k portálu LUIS a začněte vytvářet vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a odsouhlaste podmínky použití.
1. Spusťte aplikaci LUIS tak, že zvolíte, který typ klíče pro vytváření LUIS chcete použít: bezplatný zkušební klíč nebo nový klíč pro vytváření obsahu Azure LUIS.

    ![Zvolit typ prostředku pro vytváření Language Understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po dokončení procesu výběru prostředků [vytvořte novou aplikaci](luis-how-to-start-new-app.md#create-new-app-in-luis).

## <a name="trial-key"></a>Zkušební klíč

Zkušební verze (spouštěcí klíč) je k dispozici za vás. Slouží jako ověřovací klíč k dotazování předpovědi modulu runtime koncového bodu, až 1000 dotazů za měsíc.

Je vidět na stránce **nastavení uživatele** i na stránkách **Spravovat > prostředky Azure** na portálu Luis.

Až budete připraveni k publikování koncového bodu předpovědi, [vytvořte](#create-luis-resources) a [přiřaďte](#assign-a-resource-to-an-app) klíče modulu runtime pro vytváření a předpovědi, aby bylo možné nahradit funkci počátečního klíče.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Vytváření prostředků v Azure CLI

Pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) můžete jednotlivé prostředky vytvořit jednotlivě.

Prostředek `kind`:

* Zdroj`LUIS.Authoring`
* Předpovědi`LUIS`

1. Přihlaste se k Azure CLI:

    ```azurecli
    az login
    ```

    Otevře se prohlížeč, který vám umožní vybrat správný účet a zajistit ověřování.

1. Vytvořte **Luis prostředek**pro `LUIS.Authoring`vytváření obsahu s `my-luis-authoring-resource` názvem ve _stávající_ skupině prostředků s názvem `my-resource-group` pro `westus` oblast.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Vytvořte **prostředek koncového bodu předpovědi Luis**typu `LUIS` `my-luis-prediction-resource` s názvem ve _stávající_ skupině prostředků s názvem `my-resource-group` pro `westus` oblast. Pokud chcete vyšší propustnost, než je úroveň Free, změňte `F0` na. `S0` Přečtěte si další informace o [cenových úrovních a propustnosti](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Tento klíč **nepoužívá portál** Luis, dokud se nepřiřazují na portálu Luis v **prostředcích Azure manage->**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Přiřazení prostředku pro vytváření na portálu LUIS pro všechny aplikace

Můžete přiřadit prostředek pro vytváření obsahu pro jednu aplikaci nebo pro všechny aplikace v LUIS. Následující postup přiřadí všechny aplikace k jednomu prostředku pro vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai).
1. V horním navigačním panelu úplně vpravo vyberte svůj uživatelský účet a pak vyberte **Nastavení**.
1. Na stránce **nastavení uživatele** vyberte **Přidat prostředek pro vytváření** a pak vyberte existující prostředek pro vytváření. Vyberte **Uložit**.

## <a name="assign-a-resource-to-an-app"></a>Přiřazení prostředku k aplikaci

Pomocí následujícího postupu můžete k aplikaci přiřadit jediný prostředek, vytváření obsahu nebo předpovědi modulu runtime koncového bodu.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a pak vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejděte na stránku **Správa prostředků Azure spravovat >** .

    ![Vyberte prostředky Azure spravovat > na portálu LUIS a přiřaďte k aplikaci prostředek.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Vyberte kartu předpověď nebo prostředek pro vytváření obsahu a pak vyberte tlačítko **Přidat prostředek předpovědi** nebo **Přidat prostředek pro vytváření** .
1. Vyberte pole ve formuláři, abyste našli správný prostředek, a pak vyberte **Uložit**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Přiřazení prostředku modulu runtime bez použití portálu LUIS

Pro účely automatizace, jako je například kanál CI/CD, možná budete chtít automatizovat přiřazení prostředku modulu runtime LUIS do aplikace LUIS. Abyste to mohli udělat, musíte provést následující kroky:

1. Získat Azure Resource Manager token z tohoto [webu](https://resources.azure.com/api/token?plaintext=true) Vyprší platnost tohoto tokenu, takže ho budete hned používat. Požadavek vrací token Azure Resource Manager.

    ![Vyžádat token Azure Resource Manager a získat token Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Použijte token k vyžádání prostředků modulu runtime LUIS napříč předplatnými z [rozhraní API pro účty Azure Luis](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ke kterým má váš uživatelský účet přístup.

    Toto rozhraní API pro POST vyžaduje následující nastavení:

    |Hlavička|Hodnota|
    |--|--|
    |`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezera.|
    |`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|

    Toto rozhraní API vrátí pole objektů JSON vašich předplatných LUIS, včetně ID předplatného, skupiny prostředků a názvu prostředku, vráceného jako název účtu. Najděte jednu položku v poli, která je prostředkem LUIS, který se má přiřadit k aplikaci LUIS.

1. Přiřaďte token k prostředku LUIS pomocí [přiřazení účtů Azure Luis k](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) rozhraní API aplikace.

    Toto rozhraní API pro POST vyžaduje následující nastavení:

    |Typ|Nastavení|Hodnota|
    |--|--|--|
    |Hlavička|`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezera.|
    |Hlavička|`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|
    |Hlavička|`Content-type`|`application/json`|
    |Dotaz|`appid`|ID aplikace LUIS.
    |Tělo||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e";<br>"Znovu nasource": "Zdrojová položka-2",<br>"Account": "Luis-uswest-S0-2"}|

    Po úspěšném dokončení tohoto rozhraní API vrátí stav 201-Created.

## <a name="unassign-resource"></a>Zrušit přiřazení prostředku

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a pak vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejděte na stránku **Správa prostředků Azure spravovat >** .
1. Vyberte kartu předpověď nebo prostředek vytváření a pak vyberte tlačítko zrušit **přiřazení prostředků** pro daný prostředek.

Když zrušíte přiřazení prostředku, neodstraní se z Azure. Odpojí se jenom od LUIS.

## <a name="reset-authoring-key"></a>Resetování klíčového obsahu

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: Pokud je váš vlastní klíč napadený, resetujte klíč v Azure Portal na stránce **klíče** pro daný prostředek pro vytváření obsahu.

**Pro aplikace, které ještě nebyly migrovány**: klíč se resetuje na všech vašich aplikacích na portálu Luis. Pokud vaše aplikace vytváříte prostřednictvím rozhraní API pro vytváření obsahu, musíte změnit hodnotu OCP-APIM-Subscription-Key na nový klíč.

## <a name="regenerate-azure-key"></a>Znovu vygenerovat klíč Azure

Znovu vygenerujte klíče Azure z Azure Portal na stránce **klíče** .

## <a name="delete-account"></a>Odstranit účet

Informace o tom, jaká data se při odstranění účtu odstraňují, najdete v tématu věnovaném [ukládání a odebírání dat](luis-concept-data-storage.md#accounts) .

## <a name="change-pricing-tier"></a>Změna cenové úrovně

1.  V [Azure](https://portal.azure.com)Najděte své předplatné Luis. Vyberte předplatné LUIS.
    ![Hledání předplatného LUIS](./media/luis-usage-tiers/find.png)
1.  Vyberte **cenovou úroveň** , aby se zobrazily dostupné cenové úrovně.
    ![Zobrazit cenové úrovně](./media/luis-usage-tiers/subscription.png)
1.  Vyberte cenovou úroveň a výběrem **Vybrat** uložte změnu.
    ![Změna úrovně platby LUIS](./media/luis-usage-tiers/plans.png)
1.  Po dokončení změny cen místní okno ověří novou cenovou úroveň.
    ![Ověření úrovně platby LUIS](./media/luis-usage-tiers/updated.png)
1. Nezapomeňte [přiřadit tento klíč koncového bodu](#assign-a-resource-to-an-app) na stránce **publikování** a použít ho ve všech dotazech koncového bodu.

## <a name="viewing-azure-resource-metrics"></a>Zobrazení metrik prostředků Azure

### <a name="viewing-azure-resource-summary-usage"></a>Zobrazení souhrnu využití prostředků Azure
Informace o použití LUIS můžete zobrazit v Azure. Na stránce **Přehled** jsou uvedeny nedávné souhrnné informace, včetně volání a chyb. Pokud provedete požadavek na koncový bod LUIS, potom hned Sledujte **stránku Přehled**, aby se použití zobrazilo až pět minut.

![Zobrazení souhrnného využití](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Přizpůsobení grafů využití prostředků Azure
Metrika poskytuje podrobnější zobrazení dat.

![Výchozí metriky](./media/luis-usage-tiers/metrics-default.png)

Grafy metriky můžete nakonfigurovat pro časové období a typ metriky.

![Vlastní metriky](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Výstraha prahové hodnoty celkového počtu transakcí
Pokud se chcete dozvědět, že jste dosáhli určité prahové hodnoty transakcí, například 10 000 transakcí, můžete vytvořit výstrahu.

![Výchozí výstrahy](./media/luis-usage-tiers/alert-default.png)

Přidejte upozornění metriky pro metriku **celkového počtu volání** za určité časové období. Přidejte e-mailové adresy všech osob, které by měly dostávat upozornění. Přidejte Webhooky pro všechny systémy, které by měly přijímat výstrahy. Aplikaci logiky můžete spustit také při aktivaci výstrahy.

## <a name="next-steps"></a>Další kroky

* Naučte se [používat verze](luis-how-to-manage-versions.md) k řízení životního cyklu aplikace.
* Seznamte se s koncepty, včetně [prostředků pro vytváření](luis-concept-keys.md#authoring-key) a [přispěvatelů](luis-concept-keys.md#contributions-from-other-authors) na tomto prostředku.
* Naučte [se vytvářet prostředky pro](luis-how-to-azure-subscription.md) vytváření obsahu a modul runtime
* Migrace na nový [prostředek pro vytváření](luis-migration-authoring.md)
