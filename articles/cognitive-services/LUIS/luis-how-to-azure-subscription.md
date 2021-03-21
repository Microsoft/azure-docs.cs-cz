---
title: Použití klíčů pro vytváření a běhu – LUIS
description: Při prvním použití LUIS nemusíte vytvářet klíč pro vytváření obsahu. Pokud chcete aplikaci publikovat a potom použít koncový bod za běhu, musíte pro aplikaci vytvořit a přiřadit klíč modulu runtime.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988162"
---
# <a name="create-luis-resources"></a>Vytvoření prostředků LUIS

Prostředky modulu runtime předpovědi pro vytváření a dotazování poskytují ověřování do vaší aplikace Language Understanding (LUIS) a koncového bodu předpovědi.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Prostředky LUIS

LUIS umožňuje tři typy prostředků Azure a jeden jiný prostředek než Azure:

|Prostředek|Účel|Služba rozpoznávání `kind`|Služba rozpoznávání `type`|
|--|--|--|--|
|Vytváření prostředku|Umožňuje vytváření, správu, výuku, testování a publikování aplikací. Pokud máte v úmyslu vytvářet aplikace LUIS prostřednictvím kódu programu nebo z portálu LUIS, [vytvořte prostředek pro vytváření Luis](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) . Než propojíte prostředky pro vytváření obsahu Azure s vaší aplikací, musíte [svůj účet Luis migrovat](luis-migration-authoring.md#what-is-migration) . Oprávnění k vytváření prostředků můžete řídit přiřazením osob [do role Přispěvatel](#contributions-from-other-authors). <br><br> Pro prostředek pro vytváření LUIS je k dispozici jedna úroveň:<br> <ul> <li>**Bezplatný F0 vytváření prostředků**, který vám poskytne 1 000 000 volných transakcí pro vytváření a 1 000 bezplatné požadavky na koncový bod předpovědi testování za měsíc. |`LUIS.Authoring`|`Cognitive Services`|
|Prostředek předpovědi| Po publikování aplikace LUIS použijte k vyřízení požadavků na koncový bod předpovědi pro dotazování prostředku nebo klíče předpovědi. Vytvořte předpověď LUIS prostředku předtím, než klientská aplikace požaduje předpovědi nad požadavky 1 000, které poskytla služba Authoring nebo Starter Resource. <br><br> Pro prostředek předpovědi jsou k dispozici dvě úrovně:<br><ul> <li> **F0 předpověď Free**, což vám poskytne 10 000 bezplatné požadavky na koncový bod předpovědi měsíčně.<br> <li> **Prostředek předpovědi úrovně Standard S0**, což je placená úroveň. [Přečtěte si další informace o cenách.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Zdroj počáteční/zkušební verze|Umožňuje vytváření, správu, výuku, testování a publikování aplikací. Tento prostředek se vytvoří ve výchozím nastavení, pokud při prvním přihlášení k LUIS zvolíte možnost prostředek počátečního prostředku. Počáteční klíč bude nakonec zastaralý. Všichni LUIS uživatelé budou muset [migrovat své účty](luis-migration-authoring.md#what-is-migration) a propojit své Luis aplikace s prostředky pro vytváření obsahu. Na rozdíl od vytváření prostředků vám tento prostředek neuděluje oprávnění k řízení přístupu na základě role v Azure. <br><br> Stejně jako u zdrojů pro vytváření počátečního prostředku vám poskytne 1 000 000 volných transakcí pro vytváření a 1 000 bezplatné požadavky na koncový bod předpovědi testování.|-|Nejedná se o prostředek Azure.|
|[Cognitive Services klíč prostředku s více službami](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Požadavky koncového bodu předpovědi dotazu sdílené s LUIS a dalšími podporovanými službami pro rozpoznávání.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS poskytuje dva typy prostředků F0 (Free úrovně): jeden pro vytváření transakcí a jeden pro předpovědi transakcí. Pokud máte vystaralou kvótu pro transakce předpovědi, ujistěte se, že používáte prostředek předpovědi F0, který vám poskytne 10 000 bezplatných transakcí měsíčně, a ne prostředek pro vytváření, který vám poskytne 1 000 předpověď transakcí měsíčně.

Po dokončení procesu vytváření prostředků Azure [přiřaďte prostředek](#assign-a-resource-to-an-app) k aplikaci na portálu Luis.

> [!important]
> Aplikace LUIS byste měli vytvářet v [oblastech](luis-reference-regions.md#publishing-regions) , kde chcete publikovat a dotazovat.

## <a name="resource-ownership"></a>Vlastnictví prostředku

Prostředek Azure, jako je prostředek LUIS, je vlastněn předplatným, které prostředek obsahuje.

Chcete-li změnit vlastnictví prostředku, můžete provést jednu z následujících akcí:
* Přeneste [vlastnictví](../../cost-management-billing/manage/billing-subscription-transfer.md) svého předplatného.
* Exportujte aplikaci LUIS jako soubor a pak aplikaci importujte na jiné předplatné. Export je k dispozici na stránce **Moje aplikace** na portálu Luis.

## <a name="resource-limits"></a>Omezení prostředků

### <a name="authoring-key-creation-limits"></a>Vytváření omezení vytváření klíčů

V rámci každého předplatného můžete vytvořit až 10 klíčů pro tvorbu na oblast. Oblasti publikování se liší od oblastí vytváření obsahu. Ujistěte se, že jste v oblasti vytváření obsahu vytvořili aplikaci, která odpovídá oblasti publikování, kde chcete, aby se vaše klientská aplikace nacházela. Informace o tom, jak se oblasti vytváření mapují na oblasti publikování, najdete v tématu [oblasti vytváření a publikování](luis-reference-regions.md). 

Další informace o omezeních klíčů najdete v tématu [omezení klíčů](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>Chyby při omezeních použití klíče

Omezení využití jsou založená na cenové úrovni.

Pokud překročíte kvótu počtu transakcí za sekundu, zobrazí se chyba HTTP 429. Pokud překročíte kvótu transakcí za měsíc (TPM), zobrazí se chyba HTTP 403.


### <a name="reset-an-authoring-key"></a>Resetování klíč pro vytváření

Pro [migrované zdrojové aplikace prostředků](luis-migration-authoring.md) : Pokud dojde k ohrožení bezpečnosti vašeho vývojového klíče, resetujte klíč v Azure Portal na stránce **klíče** pro prostředek pro vytváření.

U aplikací, které se nemigrují: klíč se resetuje na všech vašich aplikacích na portálu LUIS. Pokud vaše aplikace vytváříte prostřednictvím rozhraní API pro vytváření obsahu, musíte změnit hodnotu `Ocp-Apim-Subscription-Key` na nový klíč.

### <a name="regenerate-an-azure-key"></a>Znovu vygenerovat klíč Azure

Klíč Azure můžete znovu vygenerovat na stránce **klíče** v Azure Portal.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Vlastnictví aplikace, přístup a zabezpečení

Aplikace je definovaná pomocí prostředků Azure, které určují předplatné vlastníka.

Aplikaci LUIS můžete přesunout. Pomocí Azure Portal nebo rozhraní příkazového řádku Azure vám pomůžou tyto prostředky:

* [Přesunutí aplikace mezi LUIS prostředky pro vytváření obsahu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Přesunutí prostředku do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Přesunutí prostředku v rámci stejného předplatného nebo napříč předplatnými](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Příspěvky od jiných autorů

Pro [migrované zdrojové aplikace prostředků](luis-migration-authoring.md) : _přispěvatelé_ můžete spravovat pro prostředek pro vytváření obsahu v Azure Portal pomocí stránky **řízení přístupu (IAM)** . Naučte [se, jak přidat uživatele](luis-how-to-collaborate.md) pomocí e-mailové adresy spolupracovníka a role přispěvatele.

Pro aplikace, které ještě nebyly migrovány: můžete spravovat všechny _spolupracovníky_ na stránce **Spravovat > spolupracovníky** na portálu Luis.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Přístup k předpovědi dotazů pro privátní a veřejné aplikace

U privátních aplikací je k dispozici přístup za běhu předpovědi dotazů pro vlastníky a přispěvatele. Pro veřejné aplikace je přístup za běhu dostupný pro uživatele, kteří mají vlastní [službu pro rozpoznávání](../cognitive-services-apis-create-account.md) Azure nebo prostředek [Luis](#create-resources-in-the-azure-portal) runtime a ID veřejné aplikace.

V současné době není k dispozici katalog veřejných aplikací.

### <a name="authoring-permissions-and-access"></a>Vytváření oprávnění a přístup
Přístup k aplikaci z portálu [Luis](luis-reference-regions.md#luis-website) nebo [rozhraní API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087) řídí prostředek pro vytváření obsahu Azure.

Vlastník a všichni přispěvatelé mají přístup k vytváření aplikací.

|Přístup pro vytváření obsahu zahrnuje:|Poznámky|
|--|--|
|Přidat nebo odebrat klíče koncového bodu||
|Exportovat verzi||
|Exportovat protokoly koncových bodů||
|Importovat verzi||
|Nastavit aplikaci jako veřejnou|Když je aplikace veřejná, může dotazovat aplikace kdokoli, kdo má klíč pro vytváření nebo klíč koncového bodu.|
|Upravit model|
|Publikovat|
|Kontrola služby Endpoint projevy pro [aktivní učení](luis-how-to-review-endpoint-utterances.md)|
|Trénování|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Prediktivní přístup k modulu runtime koncového bodu

Přístup pro dotazování koncového bodu předpovědi je řízen nastavením na stránce **informace o aplikaci** v části **Spravovat** .

|[Soukromý koncový bod](#runtime-security-for-private-apps)|[Veřejný koncový bod](#runtime-security-for-public-apps)|
|:--|:--|
|K dispozici pro vlastníka a přispěvatele|Dostupné pro vlastníka, přispěvatele a někoho jiného, kdo ví, že ID aplikace|

Můžete určit, kdo uvidí klíč LUIS runtime, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, propojení mezi robotem a LUIS je již bezpečnější. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (přes něco jako [Azure AD](https://azure.microsoft.com/services/active-directory/)). Když se zavolá rozhraní API na straně serveru a ověří se ověření a autorizaci, předejte volání do LUIS. Tato strategie nebrání útokům prostředníkem. Ale nezakazuje klíč a adresu URL koncového bodu od vašich uživatelů, umožňují sledovat přístup a umožňují přidat protokolování odpovědí koncových bodů (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Zabezpečení běhového prostředí pro privátní aplikace

Modul runtime privátní aplikace je k dispozici pouze v následujících klíčích:

|Klíč a uživatel|Vysvětlení|
|--|--|
|Klíč pro vytváření vlastníka| Až 1 000 přístupů do koncového bodu|
|Spolupracovníci/klíče pro vytváření přispěvatelů| Až 1 000 přístupů do koncového bodu|
|Libovolný klíč přiřazený LUIS autorem nebo spolupracovníka/přispěvatelem|Na základě úrovně použití klíče|

### <a name="runtime-security-for-public-apps"></a>Zabezpečení běhového prostředí pro veřejné aplikace

Pokud je vaše aplikace nakonfigurovaná jako veřejná, může se dotazovat na _libovolný_ platný klíč Luis (nebo klíč koncového bodu Luis), pokud klíč nepoužil celou kvótu koncového bodu.

Uživatel, který není vlastníkem nebo přispěvatelem, může získat přístup k modulu runtime veřejné aplikace pouze v případě, že je dané ID aplikace. LUIS nemá veřejný trh ani jiný způsob, jak si uživatelé můžou vyhledat veřejnou aplikaci.

Veřejná aplikace je publikovaná ve všech oblastech. Takže uživatel s klíčem prostředku LUIS podle oblasti má přístup k aplikaci v jakékoli oblasti, která je přidružená k klíči prostředků.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Řízení přístupu ke koncovému bodu předpovědi dotazu

Můžete určit, kdo může zobrazit klíč koncového bodu LUIS předpovědi, voláním v prostředí serveru na serveru. Pokud používáte LUIS z robota, propojení mezi robotem a LUIS je již bezpečnější. Pokud voláte koncový bod LUIS přímo, měli byste vytvořit rozhraní API na straně serveru (například [funkci](https://azure.microsoft.com/services/functions/)Azure) s řízeným přístupem (přes něco jako [Azure AD](https://azure.microsoft.com/services/active-directory/)). Když se zavolá rozhraní API na straně serveru a ověří se ověřování a autorizace, předejte volání do LUIS. Tato strategie nebrání útokům prostředníkem. Ale zakazuje vám koncový bod od vašich uživatelů, umožňuje sledovat přístup a umožňuje přidat protokolování odpovědí koncových bodů (například [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Přihlaste se k portálu LUIS a začněte vytvářet vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a odsouhlaste podmínky použití.
1. Začněte vytvářet aplikaci LUIS výběrem klíče pro vytváření obsahu Azure LUIS:

   ![Snímek obrazovky, který zobrazuje úvodní obrazovku](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Až skončíte s procesem výběru prostředků, [vytvořte novou aplikaci](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Vytváření prostředků v Azure CLI

Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) můžete jednotlivé prostředky vytvořit jednotlivě.

Prostředek `kind` :

* Zdroj `LUIS.Authoring`
* Předpovědi `LUIS`

1. Přihlaste se k Azure CLI:

    ```azurecli
    az login
    ```

    Tento příkaz otevře prohlížeč, abyste mohli vybrat správný účet a zajistit ověřování.

1. Vytvořte LUIS zdroj obsahu typu `LUIS.Authoring` s názvem `my-luis-authoring-resource` . Vytvořte ji ve _stávající_ skupině prostředků s názvem `my-resource-group` pro `westus` oblast.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Vytvořte prostředek koncového bodu předpovědi LUIS typu `LUIS` s názvem `my-luis-prediction-resource` . Vytvořte ji ve _stávající_ skupině prostředků s názvem `my-resource-group` pro `westus` oblast. Pokud chcete vyšší propustnost, než poskytuje úroveň Free, změňte `F0` na `S0` . [Přečtěte si další informace o cenových úrovních a propustnosti.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Tyto klíče se nepoužívají na portálu Luis, dokud nebudou přiřazené na stránce **Správa**  >  **prostředků Azure** na portálu Luis.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Přiřazení prostředků na portálu LUIS

Můžete přiřadit prostředek pro vytváření obsahu pro jednu aplikaci nebo pro všechny aplikace v LUIS. Následující postup přiřadí všechny aplikace k jednomu prostředku pro vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai).
1. V pravém horním rohu vyberte svůj uživatelský účet a pak vyberte **Nastavení**.
1. Na stránce **nastavení uživatele** vyberte **Přidat prostředek pro vytváření obsahu** a pak vyberte existující prostředek pro vytváření. Vyberte **Uložit**.

## <a name="assign-a-resource-to-an-app"></a>Přiřazení prostředku k aplikaci

>[!NOTE]
>Pokud nemáte předplatné Azure, nebudete moct přiřadit ani vytvořit nový prostředek. Budete muset vytvořit [bezplatný účet Azure](https://azure.microsoft.com/en-us/free/) a pak se vrátit na Luis a vytvořit nový prostředek z portálu.

Pomocí tohoto postupu můžete vytvořit prostředek pro vytváření obsahu nebo předpovědi nebo ho přiřadit k aplikaci: 

1. Přihlaste se k [portálu Luis](https://www.luis.ai). Vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejít na **Správa**  >  **prostředků Azure**:

    ![Snímek obrazovky, který ukazuje stránku prostředků Azure.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Na kartě prostředek **předpovědi** nebo **prostředek vytváření obsahu** vyberte tlačítko **Přidat prostředek předpovědi** nebo **Přidat prostředek pro vytváření** .
1. Použijte pole ve formuláři k vyhledání správného prostředku a pak vyberte **Uložit**.
1. Pokud nemáte existující prostředek, můžete ho vytvořit tak, že vyberete **vytvořit nový prostředek Luis?** v dolní části okna.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Přiřazení prostředku modulu runtime pro předpověď dotazu bez použití portálu LUIS

Pro automatizované procesy, jako jsou kanály CI/CD, možná budete chtít automatizovat přiřazení prostředku modulu runtime LUIS do aplikace LUIS. Uděláte to tak, že provedete tyto kroky:

1. Získat Azure Resource Manager token z [tohoto webu](https://resources.azure.com/api/token?plaintext=true) Vyprší platnost tohoto tokenu, proto ho použijte hned. Požadavek vrací token Azure Resource Manager.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Snímek obrazovky, který zobrazuje web pro vyžádání tokenu Azure Resource Manager.](./media/luis-manage-keys/get-arm-token.png)

1. Použijte token k vyžádání prostředků modulu runtime LUIS napříč předplatnými. Použijte [rozhraní API pro získání Luis účtů Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ke kterým má váš uživatelský účet přístup.

    Toto rozhraní API pro POST vyžaduje následující hodnoty:

    |Hlavička|Hodnota|
    |--|--|
    |`Authorization`|Hodnota `Authorization` je `Bearer {token}` . Před hodnotou tokenu musí být slovo `Bearer` a mezera.|
    |`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|

    Rozhraní API vrací pole objektů JSON, které reprezentují Vaše předplatná LUIS. Vrácené hodnoty zahrnují ID předplatného, skupinu prostředků a název prostředku, které se vrátí jako `AccountName` . Vyhledejte položku v poli, která je prostředkem LUIS, který chcete přiřadit k aplikaci LUIS.

1. Přiřaďte token k LUIS prostředku pomocí [přiřazení účtů Azure Luis k aplikačnímu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) rozhraní API.

    Toto rozhraní API pro POST vyžaduje následující hodnoty:

    |Typ|Nastavení|Hodnota|
    |--|--|--|
    |Hlavička|`Authorization`|Hodnota `Authorization` je `Bearer {token}` . Před hodnotou tokenu musí být slovo `Bearer` a mezera.|
    |Hlavička|`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|
    |Hlavička|`Content-type`|`application/json`|
    |Dotaz|`appid`|ID aplikace LUIS.
    |Text||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e";<br>"Znovu nasource": "Zdrojová položka-2",<br>"Account": "Luis-uswest-S0-2"}|

    Po úspěšném dokončení tohoto rozhraní API se vrátí `201 - created status` .

## <a name="unassign-a-resource"></a>Zrušení přiřazení prostředku

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a pak vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejít na **Správa**  >  **prostředků Azure**.
1. Na kartě prostředek **předpovědi** nebo **prostředek vytváření obsahu** vyberte tlačítko zrušit **přiřazení prostředků** pro daný prostředek.

Když zrušíte přiřazení prostředku, neodstraní se z Azure. Odpojí se od LUIS.


## <a name="delete-an-account"></a>Odstranění účtu

Informace o tom, jaká data se při odstranění účtu odstraňují, najdete v tématu věnovaném [ukládání a odebírání dat](luis-concept-data-storage.md#accounts) .

## <a name="change-the-pricing-tier"></a>Změna cenové úrovně

1.  V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte předplatné Luis:

    ![Snímek obrazovky, který zobrazuje LUIS předplatné v Azure Portal.](./media/luis-usage-tiers/find.png)
1.  Vyberte **cenovou úroveň** , abyste viděli dostupné cenové úrovně:

    ![Snímek obrazovky zobrazující položku nabídky cenová úroveň](./media/luis-usage-tiers/subscription.png)
1.  Vyberte cenovou úroveň a potom kliknutím na **Vybrat** změny uložte.

    ![Snímek obrazovky, který ukazuje, jak vybrat a uložit cenovou úroveň.](./media/luis-usage-tiers/plans.png)

    Po dokončení cenové změny se v automaticky otevíraném okně ověří aktualizace cenové úrovně:

    ![Snímek obrazovky s místním oknem, který ověřuje aktualizaci cen](./media/luis-usage-tiers/updated.png)
1. Nezapomeňte [přiřadit tento klíč koncového bodu](#assign-a-resource-to-an-app) na stránce **publikování** a použít ho ve všech dotazech koncového bodu.

## <a name="view-azure-resource-metrics"></a>Zobrazit metriky prostředků Azure

### <a name="view-a-summary-of-azure-resource-usage"></a>Zobrazení souhrnu využití prostředků Azure
Informace o použití LUIS můžete zobrazit v Azure Portal. Na stránce **Přehled** se zobrazuje souhrn, včetně posledních volání a chyb. Pokud provedete požadavek na koncový bod LUIS, umožněte, aby se změna zobrazila až pět minut.

![Snímek obrazovky zobrazující stránku s přehledem](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Přizpůsobení grafů využití prostředků Azure
Stránka **metriky** poskytuje podrobnější pohled na data:

![Snímek obrazovky zobrazující stránku metriky](./media/luis-usage-tiers/metrics-default.png)

Grafy metriky můžete nakonfigurovat pro konkrétní časové období a typ metriky:

![Snímek obrazovky, který zobrazuje přizpůsobený graf.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Výstraha prahové hodnoty celkového počtu transakcí
Pokud chcete zjistit, kdy se dosáhne určité prahové hodnoty transakce, například 10 000 transakcí, můžete vytvořit výstrahu:

![Snímek obrazovky zobrazující stránku pravidla výstrahy](./media/luis-usage-tiers/alert-default.png)

Přidejte upozornění metriky pro metriku **celkového počtu volání** za určité časové období. Přidejte e-mailové adresy všech uživatelů, kteří mají výstrahu přijmout. Přidejte Webhooky pro všechny systémy, které by měly přijímat výstrahy. Aplikaci logiky můžete spustit také při aktivaci výstrahy.

## <a name="next-steps"></a>Další kroky

* Naučte se [používat verze](luis-how-to-manage-versions.md) k řízení životního cyklu aplikace.
* Migrujte na nový [prostředek pro vytváření](luis-migration-authoring.md).
