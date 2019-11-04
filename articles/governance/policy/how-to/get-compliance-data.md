---
title: Získat data dodržování zásad
description: Azure Policy hodnocení a účinky určují dodržování předpisů. Přečtěte si, jak získat podrobnosti o dodržování předpisů.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: bd65fcf6ebff931fbb408ca8337a37d355221dfe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480258"
---
# <a name="get-compliance-data-of-azure-resources"></a>Získání dat o dodržování předpisů u prostředků Azure

Jednou z největších výhod Azure Policy je přehled a ovládací prvky, které poskytuje nad prostředky v rámci předplatného nebo [skupiny pro správu](../../management-groups/overview.md) předplatných. Tento ovládací prvek lze uplatnit mnoha různými způsoby, například zabránit vytváření prostředků v nesprávném umístění, vynucování společného a konzistentního použití značek nebo auditování existujících prostředků pro příslušné konfigurace a nastavení. Ve všech případech jsou data generována Azure Policy, která vám umožní pochopit stav dodržování předpisů vašeho prostředí.

Existuje několik způsobů, jak získat přístup k informacím o dodržování předpisů generovanými pomocí zásad a přiřazení iniciativ:

- Použití [Azure Portal](#portal)
- Pomocí skriptování na [příkazovém řádku](#command-line)

Než začnete se zaměřením na dodržování předpisů, Podívejme se na to, kdy se aktualizují informace o dodržování předpisů a četnost a události, které aktivují zkušební cyklus.

> [!WARNING]
> Pokud je stav dodržování předpisů hlášen jako **neregistrované**, ověřte, zda je poskytovatel prostředků **Microsoft. PolicyInsights** zaregistrován a zda má uživatel příslušná oprávnění řízení přístupu na základě role (RBAC), jak je popsáno v části [RBAC v. Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Aktivační události vyhodnocení

Výsledky dokončeného cyklu vyhodnocení jsou k dispozici v `Microsoft.PolicyInsights` poskytovatel prostředků prostřednictvím operací `PolicyStates` a `PolicyEvents`. Další informace o operacích REST API Azure Policy Insights najdete v tématu [Azure Policy Insights](/rest/api/policy-insights/).

K vyhodnocení přiřazených zásad a iniciativ dojde v důsledku různých událostí:

- Zásada nebo iniciativa je nově přiřazena k oboru. Použití přiřazení pro definovaný obor trvá přibližně 30 minut. Jakmile se použije, cyklus hodnocení začne u prostředků v tomto rozsahu s nově přiřazenou zásadou nebo iniciativou a v závislosti na účincích používaných zásadami nebo iniciativou jsou prostředky označené jako vyhovující nebo nevyhovující. Velký počet zásad nebo iniciativ vyhodnocených pro velký rozsah prostředků může chvíli trvat. V takovém případě neexistuje předem definované očekávání, kdy se cyklus vyhodnocení dokončí. Po dokončení budou aktualizované výsledky dodržování předpisů k dispozici na portálu a sadách SDK.

- Zásada nebo iniciativa, která je již přiřazena k oboru, je aktualizována. Cyklus hodnocení a časování pro tento scénář je stejný jako u nového přiřazení k oboru.

- Prostředek se nasadí do oboru s přiřazením prostřednictvím Správce prostředků, REST, Azure CLI nebo Azure PowerShell. V tomto scénáři se na portálu budou k dispozici informace o vlivu události (připojení, audit, zamítnutí, nasazení) a odpovídajících informací o stavu pro jednotlivé prostředky na portálu a sady SDK o 15 minutách. Tato událost nezpůsobí vyhodnocení dalších prostředků.

- Standardní cyklus hodnocení dodržování předpisů. Každých 24 hodin se přiřazení automaticky přehodnotí. Velké zásady nebo podněty mnoha prostředků můžou nějakou dobu trvat, takže není k dispozici předem definovaná Očekávaná doba, po které se cyklus vyhodnocení dokončí. Po dokončení budou aktualizované výsledky dodržování předpisů k dispozici na portálu a sadách SDK.

- Poskytovatel prostředků [Konfigurace hosta](../concepts/guest-configuration.md) se aktualizuje s podrobnostmi o dodržování předpisů spravovaným prostředkem.

- Kontrola na vyžádání

### <a name="on-demand-evaluation-scan"></a>Kontrola vyhodnocení na vyžádání

Kontrolu vyhodnocení předplatného nebo skupiny prostředků lze spustit voláním REST API. Tato kontrola je asynchronní proces. V takovém případě koncový bod REST pro zahájení kontroly nečeká na odpověď, dokud se kontrola nedokončí. Místo toho poskytuje identifikátor URI pro dotaz na stav vyžádaného vyhodnocení.

Každý identifikátor URI v REST API používá proměnné, které je potřeba nahradit vašimi vlastními hodnotami:

- `{YourRG}` – nahraďte názvem vaší skupiny prostředků.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

Kontrola podporuje vyhodnocení prostředků v rámci předplatného nebo ve skupině prostředků. Spusťte kontrolu pomocí příkazu REST API **post** pomocí následujících struktur identifikátorů URI:

- Předplatné

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Skupina prostředků

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Volání vrátí stav **přijato 202** . Zahrnuté v hlavičce odpovědi je vlastnost **umístění** s následujícím formátem:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` se staticky vygeneroval pro požadovaný rozsah. Pokud je v oboru již spuštěno prohledávání na vyžádání, nová kontrola není spuštěna. Místo toho se nové žádosti dodávají pro stav stejný identifikátor URI `{ResourceContainerGUID}` **umístění** . Příkaz REST API **Get** do identifikátoru URI pro **umístění** vrací **202 přijatý** , zatímco probíhá vyhodnocení. Po dokončení kontroly vyhodnocení vrátí stav **200 OK** . Tělo dokončeného prohledávání je odpověď JSON se stavem:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Jak dodržování předpisů funguje

V přiřazení je prostředek **nekompatibilní** , pokud nedodržuje pravidla zásad nebo iniciativ.
Následující tabulka ukazuje, jak různé účinky zásad fungují s hodnocením podmínek pro výsledný stav dodržování předpisů:

| Stav prostředku | Efekt | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Pravda | Neodpovídající |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Nepravda | V souladu s předpisy |
| Novinka | Audit, AuditIfNotExist\* | Pravda | Neodpovídající |
| Novinka | Audit, AuditIfNotExist\* | Nepravda | V souladu s předpisy |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Předpokládejme například, že máte skupinu prostředků – ContsoRG s některými účty úložiště (zvýrazněné červeně), které jsou vystaveny veřejným sítím.

![Účty úložiště zveřejněné pro veřejné sítě](../media/getting-compliance-data/resource-group01.png)

V tomto příkladu je třeba přistupují opatrně rizika zabezpečení. Teď, když jste vytvořili přiřazení zásady, se vyhodnotí pro všechny účty úložiště ve skupině prostředků ContosoRG. Audituje tři účty úložiště, které nedodržují předpisy, proto mění stavy na **nevyhovující předpisům.**

![Auditované účty úložiště, které nedodržují předpisy](../media/getting-compliance-data/resource-group03.png)

Kromě **kompatibilních** a **nekompatibilních**zásad a prostředků mají tři další stavy:

- **Konflikt**: Existují dvě nebo více zásad s konfliktními pravidly. Například dvě zásady připojují stejnou značku s různými hodnotami.
- **Nezahájeno**: cyklus vyhodnocení se nespustil pro zásady nebo prostředek.
- **Neregistrováno**: poskytovatel prostředků Azure Policy nebyl zaregistrován nebo účet přihlášený nemá oprávnění ke čtení dat dodržování předpisů.

Azure Policy používá pole **typ** a **název** v definici k určení, jestli se jedná o shodu prostředku. Pokud se prostředek shoduje, je považován za platný a má stav buď **kompatibilní** , nebo **nekompatibilní**. Pokud je v definici jediná vlastnost **Type** nebo **Name** , pak jsou vyhodnoceny všechny prostředky, které jsou považovány za použitelné a jsou vyhodnocovány.

Procento dodržování předpisů je určeno vydělením **odpovídajících** prostředků _celkovými prostředky_.
_Celkem prostředků_ je definováno jako součet **kompatibilních**, **nekompatibilních**a **konfliktních** prostředků. Celková čísla dodržování předpisů jsou součtem různých prostředků, **které jsou v souladu s** hodnotou součet všech různých prostředků. Na následujícím obrázku je více než 20 různých prostředků, které jsou k dispozici, a pouze jeden z nich **nedodržuje předpisy**. Celkové dodržování předpisů prostředků je 95% (19 z 20).

![Příklad dodržování zásad na stránce dodržování předpisů](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

Azure Portal prezentuje grafické prostředí pro vizualizaci a porozumění stavu dodržování předpisů ve vašem prostředí. Možnost **Přehled** na stránce **zásady** poskytuje podrobnosti pro dostupné obory kompatibility obou zásad i iniciativ. Spolu se stavem a počtem dodržování předpisů na přiřazení obsahuje graf, který zobrazuje dodržování předpisů během posledních sedmi dnů. Stránka **dodržování předpisů** obsahuje mnoho stejných informací (s výjimkou grafu), ale nabízí další možnosti filtrování a řazení.

![Příklad stránky dodržování předpisů Azure Policy](../media/getting-compliance-data/compliance-page.png)

Vzhledem k tomu, že může být zásada nebo iniciativa přiřazena k různým oborům, tabulka zahrnuje rozsah pro každé přiřazení a typ definice, která byla přiřazena. K dispozici je také počet neodpovídajících prostředků a nevyhovujících zásad pro každé přiřazení. Kliknutím na zásadu nebo iniciativu v tabulce získáte hlubší přehled o dodržování předpisů pro příslušné přiřazení.

![Příklad stránky s podrobnostmi o kompatibilitě Azure Policy](../media/getting-compliance-data/compliance-details.png)

Seznam prostředků na kartě **Kompatibilita prostředků** zobrazuje stav vyhodnocení existujících prostředků pro aktuální přiřazení. Karta je standardně **nekompatibilní**, lze ji však filtrovat.
Události (připojit, audit, odepřít, nasadit) aktivované žádostí o vytvoření prostředku se zobrazí na kartě **události** .

> [!NOTE]
> Pro zásady stroje AKS je zobrazený prostředek skupina prostředků.

![Příklad událostí dodržování předpisů Azure Policy](../media/getting-compliance-data/compliance-events.png)

V případě prostředků [režimu poskytovatele prostředků](../concepts/definition-structure.md#resource-provider-modes) na kartě **Kompatibilita prostředků** vyberte prostředek, klikněte na něj pravým tlačítkem myši a vyberte **Zobrazit podrobnosti** o dodržování předpisů. otevře se podrobnosti o kompatibilitě komponent. Tato stránka také nabízí karty k zobrazení zásad, které jsou přiřazeny k tomuto prostředku, událostem, událostem komponenty a historii změn.

![Příklad podrobností o kompatibilitě komponent Azure Policy](../media/getting-compliance-data/compliance-components.png)

Zpět na stránce kompatibilita prostředků klikněte pravým tlačítkem na řádek události, pro kterou chcete získat další podrobnosti, a vyberte **Zobrazit protokoly aktivit**. Otevře se stránka protokolu aktivit a je předem filtrována na hledání, které zobrazuje podrobnosti o přiřazení a událostech. Protokol aktivit poskytuje další kontext a informace o těchto událostech.

![Příklad protokolu aktivit Azure Policy dodržování předpisů](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Pochopení nedodržování předpisů

<a name="change-history-preview"></a>

Pokud je zjištěno, že prostředky nejsou **kompatibilní**, existuje mnoho možných důvodů. Informace o tom, proč prostředek **nedodržuje předpisy** nebo najít odpovědné změny, najdete v tématu [určení nedodržení předpisů](./determine-non-compliance.md).

## <a name="command-line"></a>Příkazový řádek

Stejné informace, které jsou k dispozici na portálu, lze načíst pomocí REST API (včetně [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell a Azure CLI (Preview).
Úplné podrobnosti o REST API najdete v referenčních informacích k [Azure Policy Insights](/rest/api/policy-insights/) . Referenční stránky REST API mají zeleně stisknuté tlačítko vyzkoušet u každé operace, která vám umožní vyzkoušet si ho přímo v prohlížeči.

Použijte ARMClient nebo podobný nástroj pro zpracování ověřování v Azure pro REST API příklady.

### <a name="summarize-results"></a>Shrnutí výsledků

Díky REST API je souhrn možné provádět pomocí kontejneru, definice nebo přiřazení. Tady je příklad sumarizace na úrovni předplatného, který používá Shrnutí Azure Policyho přehledu [pro předplatné](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Výstup shrnuje předplatné. V následujícím příkladu výstupu jsou shrnuty dodržování předpisů v **hodnotě Value. Results. nonCompliantResources** a **Value. Results. nonCompliantPolicies**. Tato žádost obsahuje další podrobnosti, včetně každého přiřazení, které tvoří nekompatibilní čísla, a informace o definici pro každé přiřazení. Každý objekt zásad v hierarchii poskytuje **queryResultsUri** , který se dá použít k získání dalších podrobností na této úrovni.

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

V předchozím příkladu **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** poskytuje ukázkový identifikátor URI pro všechny prostředky, které nedodržují předpisy pro konkrétní definici zásad. Při zobrazení hodnoty **$Filter** se hodnota PolicyAssignmentId rovná (EQ) na false, pro definici zásady se zadá a pak se PolicyDefinitionId sám. Důvodem zahrnutí PolicyAssignmentId do filtru je, že PolicyDefinitionId může existovat v několika zásadách nebo přiřazeních iniciativ s různými obory. Když zadáte jak PolicyAssignmentId, tak PolicyDefinitionId, můžeme být ve výsledcích hledání explicitní. Dříve jsme pro PolicyStates používali **nejnovější**, což automaticky nastaví časové období **z** a **do** za posledních 24 hodin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Ukázková odezva níže byla oříznuta na jeden nekompatibilní prostředek pro zkrácení. Podrobná odpověď má několik částí dat týkajících se prostředku, zásady nebo iniciativy a přiřazení. Všimněte si, že můžete také zjistit, jaké parametry přiřazení byly předány do definice zásady.

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

Při vytvoření nebo aktualizaci prostředku se vygeneruje výsledek vyhodnocení zásad. Výsledky se nazývají _události zásad_. K zobrazení posledních událostí zásad přidružených k předplatnému použijte následující identifikátor URI.

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
Pomocí PowerShellGet můžete nainstalovat modul pomocí `Install-Module -Name Az.PolicyInsights` (Ujistěte se, že máte nainstalovanou nejnovější [Azure PowerShell](/powershell/azure/install-az-ps) ):

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

Příklad: získání souhrnu stavu pro nejvyšší přiřazenou zásadu s největším počtem nevyhovujících prostředků.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Příklad: získání záznamu stavu pro naposledy vyhodnocený prostředek (výchozí je podle časového razítka v sestupném pořadí).

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

Příklad: získávání podrobností pro všechny prostředky virtuálních síťových prostředků, které nedodržují předpisy.

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

Příklad: získávání událostí souvisejících s neodpovídajícími prostředky virtuální sítě, k nimž došlo po určitém datu.

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

Pole **PrincipalOid** lze použít k získání konkrétního uživatele s rutinou Azure PowerShell `Get-AzADUser`. Nahraďte **{principalOid}** odpovědí, kterou jste dostali z předchozího příkladu.

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