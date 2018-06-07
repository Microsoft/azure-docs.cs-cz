---
title: Získání Data o dodržování předpisů v Azure zásad
description: Azure hodnocení zásad a důsledky určení dodržování předpisů. Další informace o získání podrobností o dodržování zásad.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d36ecb18811901fb781e151c06badc0697c2d769
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654799"
---
# <a name="getting-compliance-data"></a>Získávání Data o dodržování předpisů

Jednou z největších výhod zásad Azure je přehledy a ovládací prvky nabízí v porovnání s prostředky v předplatném nebo [skupiny pro správu](../azure-resource-manager/management-groups-overview.md) předplatných. Tento ovládací prvek můžete provést v mnoha různými způsoby, například brání prostředky vytváří v nesprávného umístění, vynucování využití jednotnou a společnou značka nebo auditování existující prostředky pro příslušné nastavení a konfigurace. Ve všech případech se data generována zásady, které vám umožňují zjistit stav dodržování předpisů vašeho prostředí.

Pro přístup k informacím o dodržování předpisů vygenerovaných zásady a přiřazení iniciativy několika způsoby:

- Pomocí [portálu Azure](#portal)
- Prostřednictvím [příkazového řádku](#command_line) skriptování

Před prohlížení metody k sestav o dodržování předpisů, podíváme se na při aktualizaci informací o dodržování předpisů a četnosti a události, které spouštějí cyklus hodnocení.

## <a name="evaluation-triggers"></a>Vyhodnocení aktivační události

Výsledky cyklus dokončené vyhodnocení se projeví v `Microsoft.PolicyInsights` poskytovatele prostředků prostřednictvím `PolicyStates` a `PolicyEvents` operace. Další informace o možnostech a funkcích rozhraní API REST přehled zásad najdete v tématu [zásad Insights](/rest/api/policy-insights/).

Hodnocení přiřazené zásad a iniciativami v oblasti nastat v důsledku různých událostí:

- Zásady nebo initiative je nově přiřazen obor. V takovém případě trvá přibližně 30 minut pro přiřazení má být použita pro definován obor. Při použití, zahájí cyklus hodnocení pro prostředky v rámci tohoto oboru pro nově přiřazenou zásady nebo initiative a v závislosti na důsledky používaný zásadami nebo iniciativy, prostředky jsou označeny jako vyhovující nebo nevyhovující. Velké zásady nebo initiative vyhodnotit na základě velkého rozsahu prostředků může trvat dobu, takže není žádné předdefinované umožní zjistit, kdy cyklus vyhodnocení dokončí. Po jeho dokončení aktualizované dodržování předpisů výsledky jsou k dispozici v portálu a sady SDK.
- Zásady nebo initiative již přiřazen obor se aktualizuje. Cyklus hodnocení a časování pro tento scénář je stejné jako u nové přiřazení k rozsahu.
- Prostředek se nasadí do obor s přiřazení prostřednictvím Resource Manager, REST, rozhraní příkazového řádku Azure nebo Azure PowerShell. V tomto scénáři vliv událostí (připojit, audit, zakázat, nasazení) a stav kompatibility informace jsou k dispozici v portálu a sady SDK přibližně 15 minut později.
- Cyklus hodnocení standardní dodržování předpisů. Jednou za 24 hodin, jsou automaticky znovu zhodnotí přiřazení. Velké zásady nebo initiative vyhodnotit na základě velkého rozsahu prostředků může trvat dobu, takže není žádné předdefinované umožní zjistit, kdy cyklus vyhodnocení dokončí. Po jeho dokončení aktualizované dodržování předpisů výsledky jsou k dispozici v portálu a sady SDK.

## <a name="how-compliance-works"></a>Jak funguje dodržování předpisů

V přiřazení prostředek je nevyhovující, pokud není postupujte podle zásad nebo iniciativy pravidla. Následující tabulka ukazuje, jak jinou zásadu důsledky pracovat vyhodnocení podmínky pro výsledný stav dodržování předpisů:

| Stav prostředků | Efekt | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Nevyhovující předpisům |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Abyste lépe pochopili, jak jsou prostředky označené jako nekompatibilní, použijeme v příkladu přiřazení zásad vytvořili výše.

Předpokládejme například, že máte skupinu prostředků – ContsoRG, s některé úložiště účty (zvýrazněné červeně), které jsou viditelné na veřejných sítích.

![Účty úložiště, které jsou zveřejněné na veřejných sítích](media/policy-insights/resource-group01.png)

V tomto příkladu potřebujete věnujte pozornost bezpečnostní rizika. Teď, když jste vytvořili přiřazení zásady, vyhodnotí se pro všechny účty úložiště ve skupině prostředků ContosoRG. Ho audity tři účty nevyhovující úložiště v důsledku toho změna stavy jejich k **nevyhovující.**

![Auditovat účty nevyhovující úložiště](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portál

Portál Azure umožňující prezentovat grafické prostředí vizualizace a pochopení stavu dodržování předpisů ve vašem prostředí. Na **zásad** stránky, **přehled** možnost poskytuje podrobnosti o dostupných oborů na dodržování zásad a iniciativami v oblasti. Kromě stavu dodržování předpisů a počet na přiřazení obsahuje graf zobrazující dodržování předpisů za posledních 7 dní. **Dodržování předpisů** stránka obsahuje většinu stejné informace (s výjimkou grafu), ale zadejte další filtrování a řazení možnosti.

![Stránky zásad dodržování předpisů](media/policy-compliance/compliance-page.png)

Zásady nebo initiative lze přiřadit k různými obory, Všimněte si, v tabulce obor pro každé přiřazení a typ definice, kterému byla přiřazena do tohoto oboru. K dispozici jsou taky číslo nevyhovující zásad a nevyhovující prostředky pro každé přiřazení. Kliknutím na zásady nebo initiative v tabulce poskytuje hlubší pohled na dodržování předpisů pro tento konkrétní přiřazení.

![Podrobnosti o zásady dodržování předpisů](media/policy-compliance/compliance-details.png)

Při seznamu prostředků na **nekompatibilní prostředky** kartě odráží stav vyhodnocení existující prostředky pro aktuální přiřazení, události (připojit, audit, zakázat, nasazení) aktivovány žádost o vytvoření prostředku jsou v části **události** kartě.

![Zásady dodržování předpisů události](media/policy-compliance/compliance-events.png)

Klikněte pravým tlačítkem na řádek události chcete shromažďovat další podrobnosti o a vyberte **zobrazit protokoly aktivity**. Na stránce Protokol aktivit otevře a je předem filtrované vyhledávání zobrazuje podrobnosti o přiřazení a události. Protokol aktivit poskytuje další kontext a informace o těchto událostech.

![Protokol aktivit zásady dodržování předpisů](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Příkazový řádek

Stejné informace, které jsou dostupné na portálu můžete načíst pomocí rozhraní REST API přímo (včetně s [ARMClient](https://github.com/projectkudu/ARMClient)) nebo Azure PowerShell pomocí rozhraní REST API. Úplné podrobnosti na volání rozhraní REST API najdete v tématu [Insights zásad](/rest/api/policy-insights/) odkaz. Na stránkách referenční dokumentace rozhraní API REST mít zelené tlačítko, zkuste ho' na každou operaci, která umožňuje vyzkoušet vpravo v prohlížeči.

Pokud chcete použít v následujících příkladech v prostředí Azure PowerShell, vytvořte ověřovací token s tímto kódem příkladu. Potom nahraďte $restUri požadovaný řetězec v příkladech se načíst objekt JSON, který lze potom analyzovat.

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

Pomocí rozhraní REST API, lze provést shrnutí skupiny pro správu, předplatné, skupinu prostředků, prostředků, initiative, zásady, přiřazení na úrovni předplatného nebo přiřazení úrovni skupiny prostředků. Tady je příklad sumarizace na úrovni předplatného pomocí zásad přehledy [shrnout pro předplatné](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Výstup shrnuje předplatné. Příklad níže ve výstupu, souhrnné dodržování předpisů se v části **value.results.nonCompliantResources** a **value.results.nonCompliantPolicies**. Tento požadavek poskytuje další podrobnosti, včetně každé přiřazení, která tvoří nevyhovující čísla a informace o definici pro každé přiřazení. Poskytuje každý objekt zásad v hierarchii **queryResultsUri** , můžete použít k získání dalších podrobností na této úrovni.

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

### <a name="query-for-resources"></a>Dotaz pro prostředky

Pomocí z výše uvedených příkladech **value.policyAssignments.policyDefinitions.results.queryResultsUri** poskytuje nám s ukázkovým identifikátor Uri pro získávání všechny nekompatibilní prostředky pro definici určité zásady. Prohlížení **$filter** hodnota, je stejná IsCompliant (eq) na hodnotu false, je zadána PolicyAssignmentId definice zásady a pak PolicyDefinitionId sám sebe.
Důvod včetně PolicyAssignmentId ve filtru je, protože PolicyDefinitionId může existovat v několik zásad nebo iniciativy přiřazení s různými obory. Zadáním PolicyAssignmentId i PolicyDefinitionId jsme může být explicitní v výsledků, které jsme hledáte. Dříve, použili jsme **nejnovější** pro PolicyStates (jediná povolená hodnota pro **policyStatesSummaryResource** na operátor shrnout pro předplatné), který automaticky nastaví  **z** a **k** časové okno za posledních 24 hodin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Následující příklad odpovědi oříznutí pouze zobrazí jednu nevyhovující prostředků jako stručný výtah (Všimněte si, že @odata.count je ve skutečnosti 15 a odpovídá počet nekompatibilní prostředky z v předchozím příkladu). Podrobné odpovědi obsahuje několik informací o prostředku, zásada (nebo initiative), data a přiřazení. Všimněte si, že také uvidíte, jaké přiřazení parametry byly předány do definice zásady.

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

Pokud prostředek se vytvoří nebo aktualizuje, generuje se výsledek vyhodnocení zásad. Výsledky se nazývají _události zásad_. Použijte následující identifikátor Uri k zobrazení poslední události zásad, které jsou přidružené k předplatnému.

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

Další informace o dotazování události zásad najdete v tématu [události zásad](/rest/api/policy-insights/policyevents) článku.

### <a name="azure-powershell-preview"></a>Prostředí Azure PowerShell (Preview)

Modul Azure PowerShell pro zásady ještě není konečné, ale je nyní k dispozici v galerii prostředí PowerShell jako [náhled verze](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights).
Pokud PowerShellGet je minimálně verze 1.6.0 (povinné pro podporu položky předběžné verze), si můžete stáhnout pomocí verze preview `Install-Module` (ujistěte se, že máte nejnovější [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) nainstalována):

```powershell
# Download preview from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights -AllowPrerelease

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

Modul preview má tři rutiny:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Příklad: Získání stavu souhrnné nejhornější přiřazené zásady s nejvyšším počtem nekompatibilní prostředky.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Příklad: Získání stavu záznamu pro nejvíc nedávno vyhodnotit prostředků (výchozí hodnota je pomocí časového razítka v sestupném pořadí).

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

Příklad: Získávání podrobnosti pro všechny prostředky nekompatibilní virtuální sítě.

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

Příklad: Získávání události související s prostředky nekompatibilní virtuální sítě, které došlo k chybě po určitém datu.

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

**PrincipalOid** pole můžete použít k získání konkrétního uživatele pomocí rutiny prostředí Azure PowerShell `Get-AzureRmADUser`. Nahraďte **{principalOid}** s odpovědí, můžete získat z předchozí příklad.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Pokud máte [analýzy protokolů](../log-analytics/log-analytics-overview.md) pracovní prostor se `AzureActivity` řešení vázaný na vaše předplatné a pak můžete také zobrazit výsledky – dodržování předpisů z cyklus hodnocení pomocí jednoduchých dotazů Kusto a `AzureActivity` tabulky. S podrobnostmi o porušení v analýzy protokolů to také znamená, že výstrahy může nakonfigurovat tak, aby sledovat odlišnosti na konkrétní prostředek, skupinu prostředků nebo i prahovou hodnotu nevyhovujících položek, jako jsou více než 10 za posledních 24 hodin.

![Zásady dodržování předpisů pomocí analýzy protokolů](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>Další postup

- Zkontrolujte [struktury definice zásady](policy-definition.md).
- Zkontrolujte [pochopení účinky zásad](policy-effects.md).
- Zkontrolujte, jaké skupiny pro správu je s [uspořádání prostředků s skupin pro správu Azure](../azure-resource-manager/management-groups-overview.md)