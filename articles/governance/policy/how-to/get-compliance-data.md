---
title: Získání dat o dodržování předpisů zásad
description: Vyhodnocení a efekty zásad Azure určují dodržování předpisů. Přečtěte si, jak získat podrobnosti o dodržování předpisů vašich prostředků Azure.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280635"
---
# <a name="get-compliance-data-of-azure-resources"></a>Získání dat o dodržování předpisů prostředků Azure

Jednou z největších výhod Azure Policy je přehled a řízení, které poskytuje přes prostředky v předplatném nebo [skupině pro správu](../../management-groups/overview.md) předplatných. Tento ovládací prvek lze uplatnit mnoha různými způsoby, například zabránění vytváření prostředků v nesprávném umístění, vynucení běžného a konzistentního využití značek nebo auditování existujících prostředků pro příslušné konfigurace a nastavení. Ve všech případech jsou data generována zásadami Azure, abyste mohli pochopit stav dodržování předpisů vašeho prostředí.

Existuje několik způsobů, jak získat přístup k informacím o dodržování předpisů generovaným přiřazením zásad a iniciativ:

- Používání [portálu Azure](#portal)
- Prostřednictvím skriptování [příkazového řádku](#command-line)

Než se podíváte na metody pro hlášení dodržování předpisů, podívejme se na to, kdy jsou aktualizovány informace o dodržování předpisů a četnost a události, které aktivují cyklus hodnocení.

> [!WARNING]
> Pokud je stav dodržování předpisů hlášen jako **Není registrován**, ověřte, zda je poskytovatel prostředků **Microsoft.PolicyInsights** registrovaný a zda má uživatel příslušná oprávnění řízení přístupu (RBAC) na základě rolí, jak je popsáno v [RBAC v zásadách Azure](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Aktivační události vyhodnocení

Výsledky dokončeného cyklu hodnocení jsou `Microsoft.PolicyInsights` k dispozici `PolicyStates` `PolicyEvents` v zprostředkovatele prostředků prostřednictvím a operace. Další informace o operacích rozhraní REST ROZHRANÍ AZURE Policy Insights NAJDETE V [TÉMATU Přehledy zásad Azure](/rest/api/policy-insights/).

Hodnocení přidělených politik a iniciativ promítá v důsledku různých akcí:

- Zásada nebo iniciativa je nově přiřazena k oboru. Trvá přibližně 30 minut, než se přiřazení použije na definovaný obor. Jakmile je použita, cyklus hodnocení začíná pro prostředky v rámci tohoto oboru proti nově přiřazené zásady nebo iniciativy a v závislosti na účincích používaných zásady nebo iniciativy, prostředky jsou označeny jako kompatibilní nebo nekompatibilní. Rozsáhlá zásada nebo iniciativa vyhodnocená na podporu velkého rozsahu prostředků může nějakou dobu trvat. Jako takové neexistuje žádné předem definované očekávání, kdy bude cyklus hodnocení dokončen. Po dokončení jsou aktualizované výsledky dodržování předpisů k dispozici na portálu a sadách SDK.

- Zásada nebo iniciativa, která je již přiřazena k oboru, je aktualizována. Cyklus hodnocení a časování pro tento scénář je stejný jako pro nové přiřazení k oboru.

- Prostředek se nasadí do oboru s přiřazením prostřednictvím Správce prostředků, REST, Azure CLI nebo Azure PowerShell. V tomto scénáři událost efektu (připojit, auditovat, odepřít, nasadit) a kompatibilní informace o stavu pro jednotlivé prostředky k dispozici na portálu a sady SDK přibližně o 15 minut později. Tato událost nezpůsobí vyhodnocení jiných prostředků.

- Standardní cyklus hodnocení shody. Jednou za 24 hodin jsou přiřazení automaticky přehodnocena. Velká zásada nebo iniciativa mnoha zdrojů může nějakou dobu trvat, takže neexistuje žádné předem definované očekávání, kdy bude cyklus hodnocení dokončen. Po dokončení jsou aktualizované výsledky dodržování předpisů k dispozici na portálu a sadách SDK.

- Zprostředkovatel [prostředků konfigurace hosta](../concepts/guest-configuration.md) je aktualizován s podrobnostmi o dodržování předpisů spravovaným prostředkem.

- Prohledávání na vyžádání

### <a name="on-demand-evaluation-scan"></a>Prohledávání hodnocení na vyžádání

Vyhodnocení hledání předplatného nebo skupiny prostředků lze spustit s voláním rozhraní REST API. Tato prohledávací je asynchronní proces. Jako takový koncový bod REST pro spuštění prohledávky nečeká, dokud není prohledána dokončena, aby odpověděla. Místo toho poskytuje identifikátor URI dotaz na stav požadovaného vyhodnocení.

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- `{YourRG}`- Nahradit názvem skupiny prostředků
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

Prohledávací akce podporuje vyhodnocení prostředků v předplatném nebo ve skupině prostředků. Spusťte prohledávač podle oboru pomocí příkazu REST API **POST** pomocí následujících struktur URI:

- Předplatné

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Skupina prostředků

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Volání vrátí stav **Přijato 202.** Součástí hlavičky odpovědi je vlastnost **Location** s následujícím formátem:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}`je staticky generována pro požadovaný obor. Pokud obor již prohlepá na vyžádání, není spuštěna nová prohledávání. Místo toho je nový požadavek `{ResourceContainerGUID}` k dispozici stejné **umístění** URI pro stav. Příkaz REST API **GET** na identifikátor URI **umístění** vrátí **202 Přijato,** zatímco probíhá hodnocení. Po dokončení vyhodnocení vrátí stav **200 OK.** Tělo dokončeného skenování je odpověď JSON se stavem:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Jak dodržování předpisů funguje

V přiřazení je zdroj **nekompatibilní,** pokud nedodržuje pravidla zásad nebo iniciativy.
Následující tabulka ukazuje, jak různé účinky zásad fungují s vyhodnocením podmínky pro výsledný stav dodržování předpisů:

| Stav zdroje | Účinek | Hodnocení politiky | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Neodpovídající |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Neodpovídající |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Předpokládejme například, že máte skupinu prostředků – ContsoRG, s některými účty úložiště (zvýrazněné červeně), které jsou vystaveny veřejným sítím.

![Účty úložiště vystavené veřejným sítím](../media/getting-compliance-data/resource-group01.png)

V tomto příkladu je třeba mít na pozoru před bezpečnostními riziky. Teď, když jste vytvořili přiřazení zásad, je vyhodnocena pro všechny účty úložiště ve skupině prostředků ContosoRG. Kontroluje tři nekompatibilní účty úložiště, a tím mění jejich stavy na **Nevyhovující.**

![Auditované nekompatibilní účty úložiště](../media/getting-compliance-data/resource-group03.png)

Kromě **kompatibilní** a **nevyhovující**, zásady a prostředky mají tři další stavy:

- **Konfliktní**: Existují dvě nebo více zásad s konfliktními pravidly. Například dvě zásady připojené ke stejné značce s různými hodnotami.
- **Nezahájeno**: Cyklus hodnocení nebyl spuštěn pro zásadu nebo prostředek.
- **Není registrováno**: Zprostředkovatel prostředků zásad Azure nebyl zaregistrován nebo účet, který je přihlášen, nemá oprávnění ke čtení dat o dodržování předpisů.

Azure Policy používá pole **typu** a **názvu** v definici k určení, jestli se prostředek shoduje. Pokud se prostředek shoduje, považuje se za použitelný a má stav **Kompatibilní** nebo **Nekompatibilní**. Pokud je **typ** nebo **název** jedinou vlastností v definici, pak jsou všechny prostředky považovány za použitelné a jsou vyhodnoceny.

Procento dodržování předpisů je určeno vydělením **prostředků kompatibilního s** _a min. celkovým počtem prostředků_.
_Celkový počet prostředků_ je definován jako součet **kompatibilních**, **nekompatibilních**a **konfliktních** prostředků. Celková čísla dodržování předpisů jsou součtem různých prostředků, které jsou **kompatibilní** děleno součtem všech různých prostředků. Na obrázku níže je 20 různých prostředků, které jsou použitelné a pouze jeden je **nevyhovující**. Celkový soulad s prostředky je 95 % (19 z 20).

![Příklad dodržování zásad ze stránky Dodržování předpisů](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

Portál Azure představuje grafické prostředí vizualizace a pochopení stavu dodržování předpisů ve vašem prostředí. Na stránce **Zásady** nabízí možnost **Přehled** podrobnosti o dostupných oborech dodržování zásad i iniciativ. Spolu se stavem dodržování předpisů a počtem na přiřazení obsahuje graf zobrazující dodržování předpisů za posledních sedm dní. Stránka **Dodržování předpisů** obsahuje většinu stejných informací (s výjimkou grafu), ale poskytuje další možnosti filtrování a řazení.

![Příklad stránky dodržování zásad Azure](../media/getting-compliance-data/compliance-page.png)

Vzhledem k tomu, že zásadu nebo iniciativu lze přiřadit k různým oborům, obsahuje tabulka obor pro každé přiřazení a typ přiřazené definice. Počet nekompatibilní prostředky a nekompatibilní zásady pro každé přiřazení jsou také k dispozici. Kliknutím na zásadu nebo iniciativu v tabulce získáte podrobnější pohled na dodržování předpisů pro dané přiřazení.

![Příklad stránky Podrobnosti o dodržování zásad Azure](../media/getting-compliance-data/compliance-details.png)

Seznam zdrojů na kartě **Dodržování předpisů zdrojů** zobrazuje stav vyhodnocení existujících zdrojů pro aktuální přiřazení. Karta je výchozí na **Nekompatibilní**, ale může být filtrována.
Události (připojit, auditovat, odepřít, nasadit) aktivované požadavkem na vytvoření prostředku se zobrazí na kartě **Události.**

> [!NOTE]
> Pro zásady Modul AKS je zobrazený prostředek skupinou prostředků.

![Příklad událostí dodržování zásad Azure](../media/getting-compliance-data/compliance-events.png)

V případě prostředků [režimu zprostředkovatele prostředků](../concepts/definition-structure.md#resource-provider-modes) se na kartě **Dodržování předpisů o prostředku** otevře výběr prostředku nebo kliknutí pravým tlačítkem myši na řádek a výběr **možnosti Zobrazit podrobnosti o dodržování předpisů** podrobnosti o dodržování předpisů. Tato stránka také nabízí karty pro zobrazení zásad, které jsou přiřazeny k tomuto prostředku, událostem, událostem komponent a historii změn.

![Příklad podrobností o dodržování předpisů součástí zásad Azure](../media/getting-compliance-data/compliance-components.png)

Zpět na stránce dodržování předpisů o prostředku klikněte pravým tlačítkem myši na řádek události, o které chcete získat další podrobnosti, a vyberte **zobrazit protokoly aktivit**. Otevře se stránka protokolu aktivit a je předem filtrována do hledání zobrazujícího podrobnosti o přiřazení a událostech. Protokol aktivit poskytuje další kontext a informace o těchto událostech.

![Příklad protokolu aktivit dodržování zásad Azure](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Pochopte nedodržování předpisů

<a name="change-history-preview"></a>

Pokud je prostředky určeny jako **nekompatibilní**, existuje mnoho možných důvodů. Chcete-li zjistit důvod, proč prostředek není **kompatibilní** nebo najít změnu odpovědného, naleznete [v tématu Určení nedodržování předpisů](./determine-non-compliance.md).

## <a name="command-line"></a>Příkazový řádek

Stejné informace dostupné na portálu lze načíst pomocí rozhraní REST API (včetně [armclient),](https://github.com/projectkudu/ARMClient)Azure PowerShell a Azure CLI (preview).
Podrobné informace o rozhraní REST API najdete v tématu Azure [Policy Insights](/rest/api/policy-insights/) odkaz. Referenční stránky ROZHRANÍ REST API mají na každé operaci zelené tlačítko "Try It", které vám umožní vyzkoušet si to přímo v prohlížeči.

Použijte ARMClient nebo podobný nástroj pro zpracování ověřování v Azure pro příklady rozhraní REST API.

### <a name="summarize-results"></a>Shrnout výsledky

Pomocí rozhraní REST API lze souhrn provádět pomocí kontejneru, definice nebo přiřazení. Tady je příklad shrnutí na úrovni předplatného pomocí [souhrnu](/rest/api/policy-insights/policystates/summarizeforsubscription)služby Azure Policy Insight pro předplatné :

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Výstup shrnuje odběr. V ukázkovém výstupu níže jsou souhrnné dodržování předpisů pod **hodnota.results.nonCompliantResources** a **value.results.nonCompliantPolicies**. Tento požadavek obsahuje další podrobnosti, včetně každého přiřazení, které tvoří nekompatibilní čísla a informace o definici pro každé přiřazení. Každý objekt zásad v hierarchii poskytuje **queryResultsUri,** který lze použít k získání dalších podrobností na této úrovni.

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

Ve výše uvedeném příkladu **value.policyAssignments.policyDefinitions.results.queryResultsUri** poskytuje ukázku uri pro všechny nekompatibilní prostředky pro konkrétní definici zásad. Při pohledu na **hodnotu $filter** IsCompliant je rovna (eq) na false, PolicyAssignmentId je určen pro definici zásady a potom PolicyDefinitionId sám. Důvod pro zahrnutí PolicyAssignmentId ve filtru je, protože PolicyDefinitionId může existovat v několika přiřazení zásad nebo iniciativy s různými obory. Zadáním Id PolicyAssignmentId a PolicyDefinitionId, můžeme být explicitní ve výsledcích, které hledáme. Dříve pro PolicyStates jsme použili **nejnovější**, který automaticky nastaví **od** a **do** časového období za posledních 24 hodin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Příklad odpovědi níže byl oříznut na jeden nekompatibilní prostředek pro stručnost. Podrobná odpověď má několik částí dat o zdroji, zásadách nebo iniciativě a přiřazení. Všimněte si, že můžete také zobrazit, jaké parametry přiřazení byly předány definici zásady.

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

Při vytvoření nebo aktualizaci prostředku je vygenerován výsledek vyhodnocení zásad. Výsledky se nazývají _události zásad_. Pomocí následujícího identifikátoru Uri můžete zobrazit nedávné události zásad přidružené k předplatnému.

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

Další informace o dotazování na události zásad najdete v článku odkaz [na události zásad Azure.](/rest/api/policy-insights/policyevents)

### <a name="azure-powershell"></a>Azure PowerShell

Modul Azure PowerShell pro zásady Azure je dostupný v Galerii PowerShellu jako [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Pomocí PowerShellGet, můžete nainstalovat `Install-Module -Name Az.PolicyInsights` modul pomocí (ujistěte se, že máte nainstalovaný nejnovější [Azure PowerShell):](/powershell/azure/install-az-ps)

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

Příklad: Získání souhrnu stavu pro nejvyšší přiřazenou zásadu s nejvyšším počtem nekompatibilních prostředků.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Příklad: Získání záznamu stavu pro naposledy vyhodnocován prostředek (výchozí je podle časového razítka v sestupném pořadí).

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

Příklad: Získání podrobností o všech nekompatibilních prostředků chprostředků virtuální sítě.

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

Příklad: Získání událostí souvisejících s nekompatibilními prostředky virtuální sítě, ke kterým došlo po určitém datu.

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

**PrincipalOid** pole lze získat konkrétního uživatele s rutinou `Get-AzADUser`Prostředí Azure PowerShell . Nahraďte **{principalOid}** odpovědí, kterou dostanete z předchozího příkladu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Pokud máte [pracovní prostor Log Analytics](../../../log-analytics/log-analytics-overview.md) z `AzureActivity` [řešení Analýzy protokolů aktivit](../../../azure-monitor/platform/activity-log-collect.md) vázané na vaše předplatné, můžete také zobrazit výsledky `AzureActivity` nedodržování předpisů z cyklu hodnocení pomocí jednoduchých dotazů Kusto a tabulky. S podrobnostmi v protokolech Azure Monitor, výstrahy můžete nakonfigurovat tak, aby sledovat nedodržování předpisů.


![Dodržování zásad Azure pomocí protokolů Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Další kroky

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Pochopit, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).