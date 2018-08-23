---
title: Získání dat dodržování předpisů v Azure Policy
description: Azure hodnocení zásad a efekty určení dodržování předpisů. Zjistěte, jak získat podrobnosti o dodržování předpisů.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/29/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bd3eeb5ebb9b30ac315fee1597348f3bd34f3bb6
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056640"
---
# <a name="getting-compliance-data"></a>Získání dat dodržování předpisů

Jednou z největších výhod Azure Policy je insight a ovládací prvky, které nabízí v porovnání s prostředky v rámci předplatného nebo [skupiny pro správu](../azure-resource-manager/management-groups-overview.md) předplatných. Tento ovládací prvek lze uplatnit v mnoha různými způsoby, jako je například brání prostředky vytváří v nesprávném umístění vynucovat použití značky běžné a jednotné, nebo auditování stávající prostředky pro odpovídající konfigurace a nastavení. Ve všech případech se data vygeneruje zásady, aby vám pomohl pochopit stavu dodržování předpisů vašeho prostředí.

Přístup k informacím o dodržování předpisů, generovaných zásady a přiřazení iniciativ několika způsoby:

- Použití [webu Azure Portal](#portal)
- Prostřednictvím [příkazového řádku](#command_line) skriptování

Před zobrazením metody k vytvoření sestavy dodržování předpisů, Podívejme se na při aktualizaci informací o dodržování předpisů a četnost a události, které aktivují cyklu hodnocení.

> [!WARNING]
> Pokud se hlásí stav dodržování předpisů jako **"Není k dispozici"**, ověřte, že **Microsoft.policyinsights do** zaregistrovaný poskytovatel prostředků a že uživatel má ovládací prvek odpovídající přístup na základě rolí (RBAC) oprávnění, jak je popsáno [tady](azure-policy-introduction.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Vyhodnocení aktivační události

Výsledky cyklus dokončené vyhodnocení se projeví v `Microsoft.PolicyInsights` poskytovatele prostředků prostřednictvím `PolicyStates` a `PolicyEvents` operace. Další informace o možnostech a funkcích zásad Insights REST API najdete v tématu [Policy Insights](/rest/api/policy-insights/).

Hodnocení přiřazených zásad a iniciativy nastat v důsledku různých událostí:

- Obor je nově přiřazení zásady nebo iniciativa. Pokud k tomu dojde, trvá přibližně 30 minut, než přiřazení použije definovaného oboru. Po použití, začíná cyklus hodnocení pro prostředky v daném oboru proti nově přiřazené zásady nebo iniciativa a to v závislosti na účinky používány zásadami nebo iniciativy, prostředky se označí jako vyhovující nebo nevyhovující předpisům. Velké zásady nebo iniciativa vyhodnocení proti velké oboru prostředků může trvat dobu, tak se dokončí bez předdefinovaných očekávají při cyklu hodnocení. Po jeho dokončení, jsou k dispozici v portálu a sady SDK aktualizované dodržování předpisů výsledky.
- Zásady nebo iniciativa, které jsou přiřazeny k oboru se aktualizuje. Cyklus hodnocení a časování pro tento scénář je stejná jako nové přiřazení do oboru.
- Prostředek se nasadí do rozsahu pomocí přiřazení prostřednictvím Resource Manageru, REST, rozhraní příkazového řádku Azure nebo Azure Powershellu. V tomto scénáři efekt událostí (připojit, audit, odepření, nasazení) a informace o stav souladu s předpisy pro jednotlivé prostředky k dispozici v portálu a sady SDK přibližně 15 minut později. Tato událost nezpůsobí vyhodnocení další prostředky.
- Cyklus hodnocení standardní dodržování předpisů. Jednou za 24 hodin, jsou automaticky znovu zhodnotí přiřazení. Velké zásady nebo iniciativa vyhodnocení proti velké oboru prostředků může trvat dobu, tak se dokončí bez předdefinovaných očekávají při cyklu hodnocení. Po jeho dokončení, jsou k dispozici v portálu a sady SDK aktualizované dodržování předpisů výsledky.

## <a name="how-compliance-works"></a>Jak funguje dodržování předpisů

Prostředek je v přiřazení, nesplňuje předpisy, pokud jeho není postupujte z zásad nebo iniciativ pravidla. Následující tabulka ukazuje, jak různé zásady účinky pracovat s vyhodnocením podmínek pro výsledný stav dodržování předpisů:

| Stav prostředku | Efekt | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Předpokládejme například, že máte skupinu prostředků – ContsoRG, některé účty úložiště (zvýrazněné červeně), které jsou vystaveny veřejné sítě.

![Účty úložiště, které jsou zveřejněné na veřejných sítích](media/policy-insights/resource-group01.png)

V tomto příkladu potřebujete dávejte pozor na bezpečnostní rizika. Teď, když vytvoříte přiřazení zásady, se vyhodnotí pro všechny účty úložiště ve skupině prostředků ContosoRG. Audity 3 účty úložiště nedodržují předpisy, v důsledku toho změnou jejich stavů k **nedodržující předpisy.**

![Auditovat účty úložiště nedodržující předpisy](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portál

Na webu Azure portal prezentuje grafické prostředí vizualizace a pochopení stavu dodržování předpisů ve vašem prostředí. Na **zásady** stránky, **přehled** možnost obsahuje podrobnosti o dostupných oborů dodržování zásad a iniciativy. Kromě stavu dodržování předpisů a počet na přiřazení obsahuje graf zobrazující dodržování předpisů za posledních sedm dní. **Dodržování předpisů** stránka obsahuje řadu tytéž informace (s výjimkou graf), ale poskytnout další filtrování a řazení možnosti.

![Stránky zásad dodržování předpisů](media/policy-compliance/compliance-page.png)

Jak zásady nebo iniciativa lze přiřadit k různým oborům, mějte na paměti v tabulce obor pro každé přiřazení a typ definice, která byla přiřazena do tohoto oboru. Jsou k dispozici také počet nekompatibilní zásady a nekompatibilní prostředky pro každé přiřazení. Kliknutím na zásady nebo iniciativa v tabulce poskytuje hlubší pohled na dodržování předpisů pro tento konkrétní přiřazení.

![Podrobnosti o zásadách dodržování předpisů](media/policy-compliance/compliance-details.png)

Zatímco seznamu prostředků na **nekompatibilní prostředky** kartu odráží stav vyhodnocení stávající prostředky pro aktuálního přiřazení tak, události (připojit, audit, odepření, nasazení) aktivuje požadavek na vytvoření prostředku jsou v části **události** kartu.

![Události zásad dodržování předpisů](media/policy-compliance/compliance-events.png)

Klikněte pravým tlačítkem na řadu událostí, které chcete shromažďovat další podrobnosti o a vyberte **zobrazit protokoly aktivit**. Na stránce Protokol aktivit se otevře a je předem vyfiltrované na hledání zobrazující podrobnosti pro přiřazení a události. Protokol aktivit obsahuje další kontext a informace o těchto událostech.

![Protokol aktivit zásad dodržování předpisů](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Příkazový řádek

Stejné informace, které je k dispozici na portálu můžete získat přímo pomocí rozhraní REST API (včetně [ARMClient](https://github.com/projectkudu/ARMClient)) nebo Azure Powershellu pomocí rozhraní REST API. Úplné podrobnosti o rozhraní REST API najdete v tématu [Policy Insights](/rest/api/policy-insights/) odkaz. Stránky s referenčními informacemi rozhraní REST API služby jste zelené tlačítko "Vyzkoušet" na každé operace, která umožňuje vyzkoušet přímo v prohlížeči.

Pokud chcete použít v následujících příkladech v prostředí Azure PowerShell, vytvořte ověřovací token s tímto kódem v příkladu. Potom nahraďte $restUri požadovaný řetězec v příkladech se načíst objekt JSON, který pak může být analyzován.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Shrnutí výsledků

Pomocí rozhraní REST API, shrnutí může provádět skupiny pro správu, předplatné, skupinu prostředků, prostředků, iniciativy, zásady, přiřazení úrovně předplatného nebo úroveň přiřazení skupiny prostředků. Tady je příklad sumarizace na úrovni předplatného pomocí zásad Insight [shrnutí pro předplatné](/rest/api/policy-insights/policystates/summarizeforsubscription):

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

Když použijeme příklad výše, **value.policyAssignments.policyDefinitions.results.queryResultsUri** nám k dispozici ukázkový identifikátor Uri pro získání definice konkrétní zásady všechny prostředky nedodržují předpisy. Podíváme **$filter** hodnota rovná IsCompliant (eq) na hodnotu false, PolicyAssignmentId zadaný pro definici zásady a potom PolicyDefinitionId samotný.
Důvod pro zahrnutí PolicyAssignmentId ve filtru je vzhledem k tomu, PolicyDefinitionId může existovat několik zásad nebo přiřazení iniciativ s různými obory. Zadáním PolicyAssignmentId a PolicyDefinitionId můžeme být explicitní ve výsledcích, který jsme hledali. Dříve jsme použili **nejnovější** pro PolicyStates (pouze povolené hodnoty pro **policyStatesSummaryResource** na operátor shrnutí pro předplatné), který automaticky nastaví  **z** a **k** časové období posledních 24 hodin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Následující příklad odpovědi oříznutí zobrazuje jen jeden prostředek nedodržující předpisy pro zkrácení (Všimněte si, že @odata.count je ve skutečnosti 15 a odpovídá počtu nekompatibilní prostředky jako v předchozím příkladu). Podrobnou odpověď obsahuje několik částí dat o prostředek, zásada (nebo initiative) a přiřazení. Všimněte si, že můžete také zjistit, jaké parametry se přiřazení bylo předáno definice zásady.

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

Další informace o dotazování události zásad najdete v článku [události zásad](/rest/api/policy-insights/policyevents) článku.

### <a name="azure-powershell"></a>Azure PowerShell

Modul Azure Powershellu pro zásady je dostupná v galerii prostředí PowerShell jako [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights). Použití modulu PowerShellGet, můžete nainstalovat pomocí modulu `Install-Module -Name AzureRM.PolicyInsights` (ujistěte se, že máte nejnovější [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) nainstalovaný):

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

Modul obsahuje tři rutiny:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Příklad: Při zjišťování stavu souhrnu nejvyššího přiřazené zásady s nejvyšší počet nekompatibilních prostředků.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Příklad: Získání stavu záznamu pro nejčastěji nedávno vyhodnotit prostředků (výchozí hodnota je pomocí časového razítka v sestupném pořadí).

```powershell
PS > Get-AzureRmPolicyState -Top 1

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

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

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

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

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

**PrincipalOid** pole můžete použít k získání určitého uživatele pomocí rutiny prostředí Azure PowerShell `Get-AzureRmADUser`. Nahraďte **{principalOid}** s odpovědí, získáte z předchozího příkladu.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Pokud máte [Log Analytics](../log-analytics/log-analytics-overview.md) pracovního prostoru s `AzureActivity` řešení vázáno k předplatnému potom můžete také zobrazit výsledky bez dodržování předpisů v cyklu hodnocení pomocí jednoduchých dotazů Kusto a `AzureActivity` tabulky. S podrobnostmi o nedodržení předpisů ve službě Log Analytics zároveň to znamená, že výstrahy je možné nakonfigurovat sledování nedodržení předpisů na konkrétní prostředek, skupinu prostředků nebo dokonce prahová hodnota nevyhovující předpisům položek, jako jsou více než 10 za posledních 24 hodin.

![Zásady dodržování předpisů pomocí Log Analytics](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>Další postup

- Projděte si [strukturu definic zásad](policy-definition.md).
- Projděte si [Vysvětlení efektů zásad](policy-effects.md).
- Připomenutí skupin pro správu v článku [Uspořádání prostředků pomocí skupin pro správu Azure](../azure-resource-manager/management-groups-overview.md)