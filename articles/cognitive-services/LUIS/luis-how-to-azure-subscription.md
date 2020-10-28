---
title: Jak používat klíč pro vytváření a modul runtime – LUIS
description: Při prvním použití Language Understanding (LUIS) není nutné vytvářet klíč pro vytváření obsahu. Pokud máte v úmyslu publikovat aplikaci, pak použijte koncový bod za běhu, musíte pro aplikaci vytvořit a přiřadit klíč modulu runtime.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eecc93558625d3ae891ca589424aec218036adae
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744709"
---
# <a name="create-luis-resources"></a>Vytvoření prostředků LUIS

Materiály pro modul runtime pro vytváření a předpověď dotazů poskytují ověřování do vaší aplikace LUIS a koncového bodu předpovědi.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Prostředky LUIS

LUIS umožňuje tři typy prostředků Azure a jeden jiný prostředek než Azure:

|Prostředek|Účel|Služba rozpoznávání `kind`|Služba rozpoznávání `type`|
|--|--|--|--|
|Vytváření prostředku|Umožňuje vytváření, správu, výuku, testování a publikování aplikací. Pokud máte v úmyslu vytvářet aplikace LUIS programtically nebo na portálu LUIS, [vytvořte prostředek pro vytváření Luis](luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) . Musíte nejdřív [migrovat svůj účet Luis](luis-migration-authoring.md#what-is-migration) , aby bylo možné propojit prostředky pro vytváření obsahu Azure s vaší aplikací. Oprávnění k vytváření prostředků můžete řídit přiřazením osob k [roli Přispěvatel](#contributions-from-other-authors). <br><br> Pro prostředek pro vytváření LUIS je k dispozici jedna úroveň:<br> * **Bezplatný F0 vytváření prostředků** , který vám poskytne nemilion transakcí vytváření a 1000 bezplatné požadavky koncového bodu předpovědi testování měsíčně. |`LUIS.Authoring`|`Cognitive Services`|
|Prostředek předpovědi| Po publikování aplikace LUIS použijte k vyřízení požadavků na koncový bod předpovědi pro dotazování prostředku nebo klíče předpovědi. Vytvořte LUIS předpověď prostředku předtím, než klientská aplikace požaduje předpovědi nad požadavky 1 000, které poskytuje vytváření nebo spouštěcí prostředek. <br><br> Pro prostředek předpovědi jsou avialble dvě úrovně:<br> * **F0 předpověď Free** , který vám dává 10 000 bezplatné požadavky na koncový bod předpovědi měsíčně<br> * **Prostředek předpovědi úrovně Standard S0** , což je placená úroveň. [Další informace o cenách najdete v podrobnostech.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Zdroj počáteční/zkušební verze|Umožňuje vytváření, správu, výuku, testování a publikování aplikací. Tato funkce se vytvoří pomocí výchozí, pokud při prvním přihlášení k LUIS TP zvolíte možnost prostředek Starter. Spouštěcí klíč bude ale nakonec zastaralý a všichni LUIS uživatelé budou muset [migrovat své účty](luis-migration-authoring.md#what-is-migration) a propojit své Luis aplikace s prostředky pro vytváření obsahu. Tento prostředek vám neuděluje oprávnění k řízení přístupu na základě role Azure, jako je prostředek pro vytváření obsahu. <br><br> Stejně jako u zdrojového prostředku vám počáteční zdroj poskytuje nemilion transakcí vytváření a 1000 požadavků na koncový bod předpovědi bezplatného testování.|-|Nejedná se o prostředek Azure.|
|[Klíč prostředku více služeb pro službu rozpoznávání](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Požadavky koncového bodu předpovědi dotazu sdílené s LUIS a dalšími podporovanými Cognitive Services.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> Existují dva typy prostředků F0 (Free úrovně), které LUIS poskytuje. Jednu pro vytváření transakcí a jednu pro transakce předpovědi. Pokud máte vystaralou kvótu pro transakce předpovědi, ujistěte se, že jste ve skutečnosti používali prostředek předpovědi F0, který vám poskytne 10 000 bezplatných transakcí měsíčně, a ne prostředek pro vytváření, který vám poskytne 1000 předpověď transakcí měsíčně.

Po dokončení procesu vytváření prostředků Azure [přiřaďte prostředek](#assign-a-resource-to-an-app) k aplikaci na portálu Luis.

Je důležité vytvářet aplikace LUIS v [oblastech](luis-reference-regions.md#publishing-regions) , kde chcete publikovat a dotazovat.

## <a name="resource-ownership"></a>Vlastnictví prostředku

Prostředek Azure, jako je třeba LUIS, je vlastněn předplatným, které tento prostředek obsahuje.

Chcete-li přenést vlastnictví prostředku, můžete:
* Přenos [vlastnictví](../../cost-management-billing/manage/billing-subscription-transfer.md) předplatného
* Exportujte aplikaci LUIS jako soubor a pak importujte aplikaci v jiném předplatném. Export je k dispozici na stránce **Moje aplikace** na portálu Luis.


## <a name="resource-limits"></a>Omezení prostředků

### <a name="authoring-key-creation-limits"></a>Vytváření omezení vytváření klíčů

Pro každou oblast a předplatné můžete vytvořit až 10 klíčů pro vytváření obsahu.

Podívejte se na téma [omezení klíčů](luis-limits.md#key-limits) a [oblasti Azure](luis-reference-regions.md).

Oblasti publikování se liší od oblastí vytváření obsahu. Ujistěte se, že jste vytvořili aplikaci v oblasti vytváření obsahu odpovídající oblasti publikování, kterou chcete najít v klientské aplikaci.

### <a name="key-usage-limit-errors"></a>Chyby omezení použití klíče

Omezení využití jsou založená na cenové úrovni.

Pokud překročíte kvótu počtu transakcí za sekundu, zobrazí se chyba HTTP 429. Pokud překročíte kvótu počtu transakcí za měsíc, zobrazí se chyba HTTP 403.


### <a name="reset-authoring-key"></a>Resetování klíčového obsahu

Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací: Pokud je váš vlastní klíč napadený, resetujte klíč v Azure Portal na stránce **klíče** pro daný prostředek pro vytváření obsahu.

Pro aplikace, které ještě nebyly migrovány: klíč se resetuje na všech vašich aplikacích na portálu LUIS. Pokud vaše aplikace vytváříte prostřednictvím rozhraní API pro vytváření obsahu, musíte změnit hodnotu OCP-APIM-Subscription-Key na nový klíč.

### <a name="regenerate-azure-key"></a>Znovu vygenerovat klíč Azure

Znovu vygenerujte klíče Azure z Azure Portal na stránce **klíče** .


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Vlastnictví aplikace, přístup a zabezpečení

Aplikace je definovaná pomocí prostředků Azure, které určuje předplatné vlastníka.

Aplikaci LUIS můžete přesunout. V Azure Portal nebo Azure CLI použijte následující dokumentaci:

* [Přesun aplikace mezi LUIS vytváření prostředků](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Přesunout prostředek do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Přesunutí prostředku v rámci stejného předplatného nebo napříč předplatnými](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Příspěvky od jiných autorů

Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací: _přispěvatelé_ se spravují v Azure Portal pro vytváření prostředků pomocí stránky **řízení přístupu (IAM)** . Naučte [se, jak přidat uživatele](luis-how-to-collaborate.md)pomocí e-mailové adresy spolupracovníka a role _přispěvatele_ .

Pro aplikace, které ještě nebyly migrovány: všechny _spolupracovníci_ jsou spravováni na portálu Luis ze stránky **spolupracovníci pro správu >** .

### <a name="query-prediction-access-for-private-and-public-apps"></a>Přístup k předpovědi dotazů pro privátní a veřejné aplikace

Pro **soukromou** aplikaci je k dispozici přístup za běhu předpovědi dotazů pro vlastníky a přispěvatele. V případě **veřejné** aplikace je přístup za běhu dostupný všem, kdo má vlastní prostředek [služby pro rozpoznávání](../cognitive-services-apis-create-account.md) Azure nebo [Luis](#create-resources-in-the-azure-portal) runtime a má ID veřejné aplikace.

V současné době není k dispozici katalog veřejných aplikací.

### <a name="authoring-permissions-and-access"></a>Vytváření oprávnění a přístup
Přístup k aplikaci z portálu [Luis](luis-reference-regions.md#luis-website) nebo [rozhraní API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087) řídí prostředek pro vytváření obsahu Azure.

Vlastník a všichni přispěvatelé mají přístup k vytváření aplikací.

|Přístup pro vytváření obsahu zahrnuje|Poznámky|
|--|--|
|Přidat nebo odebrat klíče koncového bodu||
|Export verze||
|Exportovat protokoly koncových bodů||
|Import verze||
|Nastavit aplikaci jako veřejnou|Když je aplikace veřejná, může se uživatel dotazovat na aplikaci s vytvářením nebo klíčovým bodem.|
|Upravit model|
|Publikovat|
|Kontrola služby Endpoint projevy pro [aktivní učení](luis-how-to-review-endpoint-utterances.md)|
|Trénování|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Prediktivní přístup k modulu runtime koncového bodu

Přístup k dotazu na koncový bod předpovědi je řízen nastavením na stránce **informace o aplikaci** v části **Spravovat** .

|[Soukromý koncový bod](#runtime-security-for-private-apps)|[Veřejný koncový bod](#runtime-security-for-public-apps)|
|:--|:--|
|K dispozici pro vlastníka a přispěvatele|Dostupné pro vlastníka, přispěvatele a někoho jiného, kdo zná ID aplikace|

Můžete určit, kdo uvidí klíč LUIS runtime, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, propojení mezi robotem a LUIS je už zabezpečené. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (například [AAD](https://azure.microsoft.com/services/active-directory/)). Když se zavolá rozhraní API na straně serveru a ověří se ověření a autorizaci, předejte volání do LUIS. I když tato strategie nebrání útokům prostředníkem, zařadí klíč a adresu URL koncového bodu vašim uživatelům, umožní vám sledovat přístup a umožňuje přidat protokolování odpovědí koncového bodu (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Zabezpečení běhového prostředí pro privátní aplikace

Modul runtime privátní aplikace je k dispozici pouze pro následující:

|Klíč a uživatel|Vysvětlení|
|--|--|
|Klíč pro vytváření vlastníka| Až 1000 přístupů do koncového bodu|
|Spolupracovníci/klíče pro vytváření přispěvatelů| Až 1000 přístupů do koncového bodu|
|Libovolný klíč přiřazený LUIS autorem nebo spolupracovníka/přispěvatelem|Na základě úrovně použití klíče|

### <a name="runtime-security-for-public-apps"></a>Zabezpečení běhového prostředí pro veřejné aplikace

Jakmile je aplikace nakonfigurovaná jako veřejná, _jakýkoli_ platný LUISový klíč nebo klíč koncového bodu Luis se může dotazovat na vaši aplikaci, pokud klíč nepoužil celou kvótu koncového bodu.

Uživatel, který není vlastníkem nebo přispěvatelem, může k modulu runtime veřejné aplikace přistupovat jenom v případě, že má dané ID aplikace. LUIS nemá veřejný _trh_ ani jiný způsob hledání veřejné aplikace.

Veřejná aplikace je publikovaná ve všech oblastech, aby uživatel s klíčem prostředků LUIS založeným na oblasti měl přístup k aplikaci v jakékoli oblasti, která je spojená s klíčem prostředku.


### <a name="securing-the-query-prediction-endpoint"></a>Zabezpečení koncového bodu prediktivního dotazování

Můžete určit, kdo může zobrazit klíč koncového bodu LUIS předpovědi, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, propojení mezi robotem a LUIS je už zabezpečené. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (například [AAD](https://azure.microsoft.com/services/active-directory/)). Když se zavolá rozhraní API na straně serveru a ověří se ověřování a autorizace, předejte volání do LUIS. I když tato strategie nebrání útokům prostředníkem, zařadí váš koncový bod vašim uživatelům, umožní vám sledovat přístup a umožňuje přidat protokolování odpovědí koncových bodů (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Přihlaste se k portálu LUIS a začněte vytvářet vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a odsouhlaste podmínky použití.
1. Spusťte aplikaci LUIS tak, že vyberete svůj klíč pro vytváření obsahu Azure LUIS.

   ![Zvolit typ prostředku pro vytváření Language Understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po dokončení procesu výběru prostředků [vytvořte novou aplikaci](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Vytváření prostředků v Azure CLI

Pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) můžete jednotlivé prostředky vytvořit jednotlivě.

Prostředek `kind` :

* Zdroj `LUIS.Authoring`
* Předpovědi `LUIS`

1. Přihlaste se k Azure CLI:

    ```azurecli
    az login
    ```

    Otevře se prohlížeč, který vám umožní vybrat správný účet a zajistit ověřování.

1. Vytvořte **Luis prostředek pro vytváření obsahu** `LUIS.Authoring` s názvem `my-luis-authoring-resource` ve _stávající_ skupině prostředků s názvem `my-resource-group` pro `westus` oblast.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Vytvořte **prostředek koncového bodu předpovědi Luis** typu `LUIS` s názvem `my-luis-prediction-resource` ve _stávající_ skupině prostředků s názvem `my-resource-group` pro `westus` oblast. Pokud chcete vyšší propustnost, než je úroveň Free, změňte `F0` na `S0` . Přečtěte si další informace o [cenových úrovních a propustnosti](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Tento klíč **nepoužívá portál** Luis, dokud se nepřiřazují na portálu Luis v **prostředcích Azure manage->** .

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Přiřazení prostředků na portálu LUIS

Můžete přiřadit prostředek pro vytváření obsahu pro jednu aplikaci nebo pro všechny aplikace v LUIS. Následující postup přiřadí všechny aplikace k jednomu prostředku pro vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai).
1. V horním navigačním panelu úplně vpravo vyberte svůj uživatelský účet a pak vyberte **Nastavení** .
1. Na stránce **nastavení uživatele** vyberte **Přidat prostředek pro vytváření** a pak vyberte existující prostředek pro vytváření. Vyberte **Uložit** .

## <a name="assign-a-resource-to-an-app"></a>Přiřazení prostředku k aplikaci

Upozorňujeme, že pokud nemáte předplatné Azure, nebudete moct přiřadit nebo vytvořit nový prostředek. Musíte nejdřív projít a vytvořit [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/en-us/free/) a pak se vrátit na Luis a vytvořit nový prostředek z portálu.

K aplikaci můžete přiřadit nebo vytvořit předpověď nebo prostředek předpovědi pomocí následujícího postupu:

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a pak vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejděte na stránku **Správa prostředků Azure > pro správu** .

    ![Vyberte prostředky Azure spravovat > na portálu LUIS a přiřaďte k aplikaci prostředek.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Vyberte kartu předpověď nebo prostředek pro vytváření obsahu a pak vyberte tlačítko **Přidat prostředek předpovědi** nebo **Přidat prostředek pro vytváření** .
1. Vyberte pole ve formuláři, abyste našli správný prostředek, a pak vyberte **Save (Uložit** ).
1. Pokud nemáte existující prostředek, můžete ho vytvořit výběrem možnosti vytvořit nový prostředek LUIS?. z dolní části okna


### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Přiřazení prostředku modulu runtime předpovědi pro dotaz bez použití portálu LUIS

Pro účely automatizace, jako je například kanál CI/CD, možná budete chtít automatizovat přiřazení prostředku modulu runtime LUIS do aplikace LUIS. Abyste to mohli udělat, musíte provést následující kroky:

1. Získat Azure Resource Manager token z tohoto [webu](https://resources.azure.com/api/token?plaintext=true) Vyprší platnost tohoto tokenu, takže ho budete hned používat. Požadavek vrací token Azure Resource Manager.

    ![Vyžádat token Azure Resource Manager a získat token Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Použijte token k vyžádání prostředků modulu runtime LUIS napříč předplatnými z [rozhraní API pro účty Azure Luis](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ke kterým má váš uživatelský účet přístup.

    Toto rozhraní API pro POST vyžaduje následující nastavení:

    |Záhlaví|Hodnota|
    |--|--|
    |`Authorization`|Hodnota `Authorization` je `Bearer {token}` . Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezera.|
    |`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|

    Toto rozhraní API vrátí pole objektů JSON vašich předplatných LUIS, včetně ID předplatného, skupiny prostředků a názvu prostředku, vráceného jako název účtu. Najděte jednu položku v poli, která je prostředkem LUIS, který se má přiřadit k aplikaci LUIS.

1. Přiřaďte token k prostředku LUIS pomocí [přiřazení účtů Azure Luis k](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) rozhraní API aplikace.

    Toto rozhraní API pro POST vyžaduje následující nastavení:

    |Typ|Nastavení|Hodnota|
    |--|--|--|
    |Záhlaví|`Authorization`|Hodnota `Authorization` je `Bearer {token}` . Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezera.|
    |Záhlaví|`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|
    |Záhlaví|`Content-type`|`application/json`|
    |Dotaz|`appid`|ID aplikace LUIS.
    |Text||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e";<br>"Znovu nasource": "Zdrojová položka-2",<br>"Account": "Luis-uswest-S0-2"}|

    Po úspěšném dokončení tohoto rozhraní API vrátí stav 201-Created.

## <a name="unassign-resource"></a>Zrušit přiřazení prostředku

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a pak vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejděte na stránku **Správa prostředků Azure spravovat >** .
1. Vyberte kartu předpověď nebo prostředek vytváření a pak vyberte tlačítko zrušit **přiřazení prostředků** pro daný prostředek.

Když zrušíte přiřazení prostředku, neodstraní se z Azure. Odpojí se jenom od LUIS.


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
Informace o použití LUIS můžete zobrazit v Azure. Na stránce **Přehled** jsou uvedeny nedávné souhrnné informace, včetně volání a chyb. Pokud provedete požadavek na koncový bod LUIS, potom hned Sledujte **stránku Přehled** , aby se použití zobrazilo až pět minut.

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
* Migrace na nový [prostředek pro vytváření](luis-migration-authoring.md)
