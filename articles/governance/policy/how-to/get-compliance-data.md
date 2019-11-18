---
title: Získat data dodržování zásad
description: Azure hodnocení zásad a efekty určení dodržování předpisů. Přečtěte si, jak získat podrobnosti o dodržování předpisů pro vaše prostředky Azure.
ms.date: 02/01/2019
ms.topic: conceptual
ms.openlocfilehash: 8cb95f0a9479da27ea6b9ef8ec6836f915aa4030
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132806"
---
# <a name="get-compliance-data-of-azure-resources"></a>Získání dat o dodržování předpisů u prostředků Azure

Jednou z největších výhod Azure Policy je insight a ovládací prvky, které nabízí v porovnání s prostředky v rámci předplatného nebo [skupiny pro správu](../../management-groups/overview.md) předplatných. Tento ovládací prvek lze uplatnit v mnoha různými způsoby, jako je například brání prostředky vytváří v nesprávném umístění vynucovat použití značky běžné a jednotné, nebo auditování stávající prostředky pro odpovídající konfigurace a nastavení. Ve všech případech jsou data generována Azure Policy, která vám umožní pochopit stav dodržování předpisů vašeho prostředí.

Přístup k informacím o dodržování předpisů, generovaných zásady a přiřazení iniciativ několika způsoby:

- Použití [webu Azure portal](#portal)
- Prostřednictvím [příkazového řádku](#command-line) skriptování

Před zobrazením metody k vytvoření sestavy dodržování předpisů, Podívejme se na při aktualizaci informací o dodržování předpisů a četnost a události, které aktivují cyklu hodnocení.

> [!WARNING]
> Pokud je stav dodržování předpisů hlášen jako **neregistrované**, ověřte, zda je poskytovatel prostředků **Microsoft. PolicyInsights** zaregistrován a zda má uživatel příslušná oprávnění řízení přístupu na základě role (RBAC), jak je popsáno v části [RBAC v Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Vyhodnocení aktivační události

Jsou k dispozici v výsledky cyklus dokončené vyhodnocení `Microsoft.PolicyInsights` poskytovatele prostředků prostřednictvím `PolicyStates` a `PolicyEvents` operace. Další informace o operacích REST API Azure Policy Insights najdete v tématu [Azure Policy Insights](/rest/api/policy-insights/).

Hodnocení přiřazených zásad a iniciativy nastat v důsledku různých událostí:

- Obor je nově přiřazení zásady nebo iniciativa. Trvá přibližně 30 minut, než přiřazení použije definovaného oboru. Po použití, začíná cyklus hodnocení pro prostředky v daném oboru proti nově přiřazené zásady nebo iniciativa a to v závislosti na účinky používány zásadami nebo iniciativy, prostředky se označí jako vyhovující nebo nevyhovující předpisům. Velké zásady nebo iniciativa vyhodnocení proti velké oboru prostředků může trvat dobu. V důsledku toho neexistuje žádné předdefinované očekávají při dokončení cyklu hodnocení. Po jeho dokončení, jsou k dispozici v portálu a sady SDK aktualizované dodržování předpisů výsledky.

- Zásady nebo iniciativa, které jsou přiřazeny k oboru se aktualizuje. Cyklus hodnocení a časování pro tento scénář je stejná jako nové přiřazení do oboru.

- Prostředek se nasadí do rozsahu pomocí přiřazení prostřednictvím Resource Manageru, REST, rozhraní příkazového řádku Azure nebo Azure Powershellu. V tomto scénáři efekt událostí (připojit, audit, odepření, nasazení) a informace o stav souladu s předpisy pro jednotlivé prostředky k dispozici v portálu a sady SDK přibližně 15 minut později. Tato událost nezpůsobí vyhodnocení další prostředky.

- Cyklus hodnocení standardní dodržování předpisů. Jednou za 24 hodin, přiřazení se automaticky znovu vyhodnoceny. Velké zásady nebo iniciativa mnoho prostředků, může trvat dobu, tak se dokončí bez předdefinovaných očekávají při cyklu hodnocení. Po jeho dokončení, jsou k dispozici v portálu a sady SDK aktualizované dodržování předpisů výsledky.

- Poskytovatel prostředků [Konfigurace hosta](../concepts/guest-configuration.md) se aktualizuje s podrobnostmi o dodržování předpisů spravovaným prostředkem.

- Kontroly na vyžádání

### <a name="on-demand-evaluation-scan"></a>Skenování hodnocení na vyžádání

Volání rozhraní REST API můžete spustit skenování hodnocení pro předplatné nebo skupinu prostředků. Tato kontrola je asynchronní proces. V důsledku toho koncový bod REST spustit skenování nečeká kontrolu dokončení reagovat. Místo toho poskytuje dotaz na stav vyhodnocení požadovaný identifikátor URI.

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- `{YourRG}` -Nahraďte názvem vaší skupiny prostředků
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

Kontrola podporuje vyhodnocení prostředků v rámci předplatného nebo skupiny prostředků. Spustit kontrolu oborem pomocí rozhraní REST API **příspěvek** příkazu následující identifikátor URI struktury:

- Předplatné

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Skupina prostředků

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Volání se vrátí **202 přijato** stav. Zahrnutý v odpovědi je záhlaví **umístění** vlastnost v následujícím formátu:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` je generována staticky oboru požadavku. Pokud obor již běží kontroly na vyžádání, není spuštěná novou kontrolu. Místo toho novou žádost o je k dispozici stejné `{ResourceContainerGUID}` **umístění** identifikátor URI pro stav. Rozhraní REST API **získat** příkaz **umístění** vrátí identifikátor URI **202 přijato** zatímco probíhá vyhodnocení. Po dokončení skenování hodnocení vrátí **200 OK** stav. Text dokončení kontroly je odpověď ve formátu JSON se stavem:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Jak funguje dodržování předpisů

V přiřazení, prostředek je **nekompatibilní** pokud jeho není postupujte z pravidel zásady nebo iniciativa.
Následující tabulka ukazuje, jak různé zásady účinky pracovat s vyhodnocením podmínek pro výsledný stav dodržování předpisů:

| Stav prostředku | Účinek | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Pravda | Neodpovídající |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Nepravda | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | Pravda | Neodpovídající |
| Nová | Audit, AuditIfNotExist\* | Nepravda | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Předpokládejme například, že máte skupinu prostředků – ContsoRG, některé účty úložiště (zvýrazněné červeně), které jsou vystaveny veřejné sítě.

![Účty úložiště, které jsou zveřejněné na veřejných sítích](../media/getting-compliance-data/resource-group01.png)

V tomto příkladu potřebujete dávejte pozor na bezpečnostní rizika. Teď, když vytvoříte přiřazení zásady, se vyhodnotí pro všechny účty úložiště ve skupině prostředků ContosoRG. Audity 3 účty úložiště nedodržují předpisy, v důsledku toho změnou jejich stavů k **nevyhovující předpisům.**

![Auditovat účty úložiště nedodržující předpisy](../media/getting-compliance-data/resource-group03.png)

Kromě **kompatibilní** a **nekompatibilní**, zásady a prostředky mají tři stavy ostatních:

- **Konfliktní**: dvě nebo víc zásad existuje konfliktní pravidla. Například dvě zásady připojení stejnou značku s různými hodnotami.
- **Nezahájeno**: pro zásadu nebo prostředek nebyl zahájen cyklus hodnocení.
- **Nezaregistrováno**: není zaregistrovaný poskytovatel prostředků Azure zásad nebo přihlášený účet nemá oprávnění ke čtení dat dodržování předpisů.

Azure Policy používá pole **typ** a **název** v definici k určení, jestli se jedná o shodu prostředku. Když odpovídá na prostředek, je považován za příslušné a je ve stavu buď **kompatibilní** nebo **nekompatibilní**. Pokud **typ** nebo **název** je jediná vlastnost v definici, pak všechny prostředky jsou považovány za použitelné a jsou vyhodnocovány.

Procentní hodnoty dodržování předpisů se určí **kompatibilní** prostředky podle _celkový počet prostředků_.
_Celkový počet prostředků_ je definován jako součet hodnot **kompatibilní**, **nekompatibilní**, a **konfliktní** prostředky. Celkové dodržování předpisů čísla jsou součet různé prostředky, které jsou **kompatibilní** dělený součet všech různých zdrojů. Na následujícím obrázku jsou 20 různých prostředků, které jsou k dispozici a je pouze jedna **nekompatibilní**. Celkové dodržování předpisů prostředkem je 95 % (19 z 20).

![Příklad dodržování zásad na stránce dodržování předpisů](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

Na webu Azure portal prezentuje grafické prostředí vizualizace a pochopení stavu dodržování předpisů ve vašem prostředí. Na **zásady** stránky, **přehled** možnost obsahuje podrobnosti o dostupných oborů dodržování zásad a iniciativy. Stav dodržování předpisů a počet na přiřazení obsahuje graf zobrazující dodržování předpisů za posledních sedm dní. **Dodržování předpisů** stránka obsahuje řadu tytéž informace (s výjimkou graf), ale poskytnout další filtrování a řazení možnosti.

![Příklad stránky dodržování předpisů Azure Policy](../media/getting-compliance-data/compliance-page.png)

Protože zásady nebo iniciativa lze přiřadit k různým oborům, tabulka obsahuje obor pro každé přiřazení a typ definice, která byla přiřazena. Jsou k dispozici také počet nekompatibilních prostředků a nekompatibilní zásady pro každé přiřazení. Kliknutím na zásady nebo iniciativa v tabulce poskytuje hlubší pohled na dodržování předpisů pro tento konkrétní přiřazení.

![Příklad stránky s podrobnostmi o kompatibilitě Azure Policy](../media/getting-compliance-data/compliance-details.png)

Seznam prostředků **dodržování předpisů prostředkem** karta zobrazuje stav vyhodnocení stávající prostředky pro aktuálního přiřazení. Výchozí nastavení na kartě **nekompatibilní**, ale dají se filtrovat.
Události (připojit, audit, odepření, nasazení) aktivuje požadavek na vytvoření prostředku se zobrazí pod **události** kartu.

> [!NOTE]
> Pro zásady stroje AKS je zobrazený prostředek skupina prostředků.

![Příklad událostí dodržování předpisů Azure Policy](../media/getting-compliance-data/compliance-events.png)

V případě prostředků [režimu poskytovatele prostředků](../concepts/definition-structure.md#resource-provider-modes) na kartě **Kompatibilita prostředků** vyberte prostředek, klikněte na něj pravým tlačítkem myši a vyberte **Zobrazit podrobnosti** o dodržování předpisů. otevře se podrobnosti o kompatibilitě komponent. Tato stránka také nabízí karty k zobrazení zásad, které jsou přiřazeny k tomuto prostředku, událostem, událostem komponenty a historii změn.

![Příklad podrobností o kompatibilitě komponent Azure Policy](../media/getting-compliance-data/compliance-components.png)

Zpět na stránce kompatibilita prostředků klikněte pravým tlačítkem na řádek události, pro kterou chcete získat další podrobnosti, a vyberte **Zobrazit protokoly aktivit**. Na stránce Protokol aktivit se otevře a je předem vyfiltrované na hledání zobrazující podrobnosti pro přiřazení a události. Protokol aktivit obsahuje další kontext a informace o těchto událostech.

![Příklad protokolu aktivit Azure Policy dodržování předpisů](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Pochopení nedodržování předpisů

<a name="change-history-preview"></a>

Pokud je zjištěno, že prostředky nejsou **kompatibilní**, existuje mnoho možných důvodů. Informace o tom, proč prostředek **nedodržuje předpisy** nebo najít odpovědné změny, najdete v tématu [určení nedodržení předpisů](./determine-non-compliance.md).

## <a name="command-line"></a>Příkazový řádek

Stejné informace, které jsou k dispozici na portálu, lze načíst pomocí REST API (včetně [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell a Azure CLI (Preview).
Úplné podrobnosti o REST API najdete v referenčních informacích k [Azure Policy Insights](/rest/api/policy-insights/) . Stránky s referenčními informacemi rozhraní REST API služby jste zelené tlačítko "Vyzkoušet" na každé operace, která umožňuje vyzkoušet přímo v prohlížeči.

Použijte ARMClient nebo podobný nástroj pro zpracování ověřování v Azure pro REST API příklady.

### <a name="summarize-results"></a>Shrnutí výsledků

Pomocí rozhraní REST API shrnutí může provádět kontejneru, definice nebo přiřazení. Tady je příklad sumarizace na úrovni předplatného, který používá Shrnutí Azure Policyho přehledu [pro předplatné](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Výstup obsahuje souhrn předplatného. Následující ukázkový výstup souhrnné dodržování předpisů jsou v rámci **value.results.nonCompliantResources** a **value.results.nonCompliantPolicies**. Tento požadavek poskytuje další podrobnosti, včetně každé přiřazení, která se skládá z čísel nedodržují předpisy a informace o definici pro každé přiřazení. Nabízí každý objekt zásad v hierarchii **queryResultsUri** , který slouží k získání dalších podrobností na této úrovni.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Dotaz na prostředky

V příkladu výše **value.policyAssignments.policyDefinitions.results.queryResultsUri** najdete vzorek identifikátoru Uri pro všechny prostředky nedodržují předpisy pro definici určité zásady. Podíváme **$filter** hodnota rovná IsCompliant (eq) na hodnotu false, PolicyAssignmentId zadaný pro definici zásady a potom PolicyDefinitionId samotný. Důvod pro zahrnutí PolicyAssignmentId ve filtru je vzhledem k tomu, PolicyDefinitionId může existovat několik zásad nebo přiřazení iniciativ s různými obory. Zadáním PolicyAssignmentId a PolicyDefinitionId můžeme být definován v požadovaných výsledků hledáme. Dříve se pro PolicyStates jsme použili **nejnovější**, který automaticky nastaví **z** a **k** časové období posledních 24 hodin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Následující příklad odpovědi se ořízne na jeden prostředek nedodržující předpisy pro zkrácení. Podrobnou odpověď má několik částí dat o prostředek, zásady nebo iniciativa a přiřazení. Všimněte si, že můžete také zjistit, jaké parametry se přiřazení bylo předáno definice zásady.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Zobrazení událostí

Pokud se prostředek vytvoří nebo aktualizuje, se vygeneruje výsledek vyhodnocení zásad. Výsledky jsou volány _události zásad_. Chcete-li zobrazit poslední události zásad, které jsou přidružené k předplatnému pomocí následující identifikátor Uri.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Další informace o dotazování na události zásad najdete v článku referenční informace o [událostech Azure Policy](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell modul pro Azure Policy je na Galerie prostředí PowerShell k dispozici jako [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Použití modulu PowerShellGet, můžete nainstalovat pomocí modulu `Install-Module -Name Az.PolicyInsights` (ujistěte se, že máte nejnovější [prostředí Azure PowerShell](/powershell/azure/install-az-ps) nainstalovaný):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Modul má následující rutiny:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Příklad: Při zjišťování stavu souhrnu nejvyššího přiřazené zásady s nejvyšší počet nekompatibilních prostředků.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Příklad: Získání stavu záznamu pro nejčastěji nedávno vyhodnotit prostředků (výchozí hodnota je pomocí časového razítka v sestupném pořadí).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Příklad: Získávání podrobností pro všechny prostředky virtuální sítě jako nevyhovující.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Příklad: Získání události související s prostředky nedodržují předpisy virtuální sítě, ke kterým došlo po určitém datu.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

**PrincipalOid** pole můžete použít k získání určitého uživatele pomocí rutiny prostředí Azure PowerShell `Get-AzADUser`. Nahraďte **{principalOid}** s odpovědí, získáte z předchozího příkladu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Pokud máte [pracovní prostor Log Analytics](../../../log-analytics/log-analytics-overview.md) s `AzureActivity` z [Activity log Analyticsho řešení](../../../azure-monitor/platform/activity-log-collect.md) svázaného s vaším předplatným, můžete si také zobrazit výsledky nedodržování předpisů z cyklu vyhodnocení pomocí jednoduchých dotazů Kusto a tabulky `AzureActivity`. S podrobnostmi v protokolech Azure Monitor můžete výstrahy nakonfigurovat tak, aby sledovaly nedodržování předpisů.


![Azure Policy dodržování předpisů pomocí protokolů Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).