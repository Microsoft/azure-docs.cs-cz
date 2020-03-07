---
title: Získat data dodržování zásad
description: Azure hodnocení zásad a efekty určení dodržování předpisů. Přečtěte si, jak získat podrobnosti o dodržování předpisů pro vaše prostředky Azure.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384426"
---
# <a name="get-compliance-data-of-azure-resources"></a>Získání dat o dodržování předpisů u prostředků Azure

Jednou z největších výhod Azure Policy je přehled a ovládací prvky, které poskytuje nad prostředky v rámci předplatného nebo [skupiny pro správu](../../management-groups/overview.md) předplatných. Tento ovládací prvek lze uplatnit v mnoha různými způsoby, jako je například brání prostředky vytváří v nesprávném umístění vynucovat použití značky běžné a jednotné, nebo auditování stávající prostředky pro odpovídající konfigurace a nastavení. Ve všech případech jsou data generována Azure Policy, která vám umožní pochopit stav dodržování předpisů vašeho prostředí.

Přístup k informacím o dodržování předpisů, generovaných zásady a přiřazení iniciativ několika způsoby:

- Použití [Azure Portal](#portal)
- Pomocí skriptování na [příkazovém řádku](#command-line)

Před zobrazením metody k vytvoření sestavy dodržování předpisů, Podívejme se na při aktualizaci informací o dodržování předpisů a četnost a události, které aktivují cyklu hodnocení.

> [!WARNING]
> Pokud je stav dodržování předpisů hlášen jako **neregistrované**, ověřte, zda je poskytovatel prostředků **Microsoft. PolicyInsights** zaregistrován a zda má uživatel příslušná oprávnění řízení přístupu na základě role (RBAC), jak je popsáno v části [RBAC v Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Vyhodnocení aktivační události

Výsledky dokončeného cyklu vyhodnocení jsou k dispozici v `Microsoft.PolicyInsights` poskytovatel prostředků prostřednictvím operací `PolicyStates` a `PolicyEvents`. Další informace o operacích REST API Azure Policy Insights najdete v tématu [Azure Policy Insights](/rest/api/policy-insights/).

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

- `{YourRG}` – nahraďte názvem vaší skupiny prostředků.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

Kontrola podporuje vyhodnocení prostředků v rámci předplatného nebo skupiny prostředků. Spusťte kontrolu pomocí příkazu REST API **post** pomocí následujících struktur identifikátorů URI:

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

`{ResourceContainerGUID}` se staticky vygeneroval pro požadovaný rozsah. Pokud obor již běží kontroly na vyžádání, není spuštěná novou kontrolu. Místo toho se nové žádosti dodávají pro stav stejný identifikátor URI `{ResourceContainerGUID}` **umístění** . Příkaz REST API **Get** do identifikátoru URI pro **umístění** vrací **202 přijatý** , zatímco probíhá vyhodnocení. Po dokončení kontroly vyhodnocení vrátí stav **200 OK** . Text dokončení kontroly je odpověď ve formátu JSON se stavem:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Jak funguje dodržování předpisů

V přiřazení je prostředek **nekompatibilní** , pokud nedodržuje pravidla zásad nebo iniciativ.
Následující tabulka ukazuje, jak různé zásady účinky pracovat s vyhodnocením podmínek pro výsledný stav dodržování předpisů:

| Stav prostředku | Účinek | Vyhodnocení zásad | Stav dodržování předpisů |
| --- | --- | --- | --- |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Neodpovídající |
| Existuje | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Odpovídající |
| Nová | Audit, AuditIfNotExist\* | True | Neodpovídající |
| Nová | Audit, AuditIfNotExist\* | False | Odpovídající |

\* Účinky Append, DeployIfNotExist a AuditIfNotExist vyžadují, aby byl příkaz IF nastaven na TRUE.
Tyto účinky také vyžadují, aby existovala podmínka, která musí nabývat hodnoty FALSE, aby byla zásada vyhodnocena jako Nevyhovující předpisům. Pokud má hodnotu TRUE, aktivuje podmínka IF vyhodnocení podmínky existence pro související prostředky.

Předpokládejme například, že máte skupinu prostředků – ContsoRG, některé účty úložiště (zvýrazněné červeně), které jsou vystaveny veřejné sítě.

![Účty úložiště, které jsou zveřejněné na veřejných sítích](../media/getting-compliance-data/resource-group01.png)

V tomto příkladu potřebujete dávejte pozor na bezpečnostní rizika. Teď, když vytvoříte přiřazení zásady, se vyhodnotí pro všechny účty úložiště ve skupině prostředků ContosoRG. Audituje tři účty úložiště, které nedodržují předpisy, proto mění stavy na **nevyhovující předpisům.**

![Auditovat účty úložiště nedodržující předpisy](../media/getting-compliance-data/resource-group03.png)

Kromě **kompatibilních** a **nekompatibilních**zásad a prostředků mají tři další stavy:

- **Konflikt**: Existují dvě nebo více zásad s konfliktními pravidly. Například dvě zásady připojení stejnou značku s různými hodnotami.
- **Nezahájeno**: cyklus vyhodnocení se nespustil pro zásady nebo prostředek.
- **Neregistrováno**: poskytovatel prostředků Azure Policy nebyl zaregistrován nebo účet přihlášený nemá oprávnění ke čtení dat dodržování předpisů.

Azure Policy používá pole **typ** a **název** v definici k určení, jestli se jedná o shodu prostředku. Pokud se prostředek shoduje, je považován za platný a má stav buď **kompatibilní** , nebo **nekompatibilní**. Pokud je v definici jediná vlastnost **Type** nebo **Name** , pak jsou vyhodnoceny všechny prostředky, které jsou považovány za použitelné a jsou vyhodnocovány.

Procento dodržování předpisů je určeno vydělením **odpovídajících** prostředků _celkovými prostředky_.
_Celkem prostředků_ je definováno jako součet **kompatibilních**, **nekompatibilních**a **konfliktních** prostředků. Celková čísla dodržování předpisů jsou součtem různých prostředků, **které jsou v souladu s** hodnotou součet všech různých prostředků. Na následujícím obrázku je více než 20 různých prostředků, které jsou k dispozici, a pouze jeden z nich **nedodržuje předpisy**. Celkové dodržování předpisů prostředkem je 95 % (19 z 20).

![Příklad dodržování zásad na stránce dodržování předpisů](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

Na webu Azure portal prezentuje grafické prostředí vizualizace a pochopení stavu dodržování předpisů ve vašem prostředí. Možnost **Přehled** na stránce **zásady** poskytuje podrobnosti pro dostupné obory kompatibility obou zásad i iniciativ. Stav dodržování předpisů a počet na přiřazení obsahuje graf zobrazující dodržování předpisů za posledních sedm dní. Stránka **dodržování předpisů** obsahuje mnoho stejných informací (s výjimkou grafu), ale nabízí další možnosti filtrování a řazení.

![Příklad stránky dodržování předpisů Azure Policy](../media/getting-compliance-data/compliance-page.png)

Protože zásady nebo iniciativa lze přiřadit k různým oborům, tabulka obsahuje obor pro každé přiřazení a typ definice, která byla přiřazena. Jsou k dispozici také počet nekompatibilních prostředků a nekompatibilní zásady pro každé přiřazení. Kliknutím na zásady nebo iniciativa v tabulce poskytuje hlubší pohled na dodržování předpisů pro tento konkrétní přiřazení.

![Příklad stránky s podrobnostmi o kompatibilitě Azure Policy](../media/getting-compliance-data/compliance-details.png)

Seznam prostředků na kartě **Kompatibilita prostředků** zobrazuje stav vyhodnocení existujících prostředků pro aktuální přiřazení. Karta je standardně **nekompatibilní**, lze ji však filtrovat.
Události (připojit, audit, odepřít, nasadit) aktivované žádostí o vytvoření prostředku se zobrazí na kartě **události** .

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

Výstup obsahuje souhrn předplatného. V následujícím příkladu výstupu jsou shrnuty dodržování předpisů v **hodnotě Value. Results. nonCompliantResources** a **Value. Results. nonCompliantPolicies**. Tento požadavek poskytuje další podrobnosti, včetně každé přiřazení, která se skládá z čísel nedodržují předpisy a informace o definici pro každé přiřazení. Každý objekt zásad v hierarchii poskytuje **queryResultsUri** , který se dá použít k získání dalších podrobností na této úrovni.

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

V předchozím příkladu **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** poskytuje ukázkový identifikátor URI pro všechny prostředky, které nedodržují předpisy pro konkrétní definici zásad. Při zobrazení hodnoty **$Filter** se hodnota PolicyAssignmentId rovná (EQ) na false, pro definici zásady se zadá a pak se PolicyDefinitionId sám. Důvod pro zahrnutí PolicyAssignmentId ve filtru je vzhledem k tomu, PolicyDefinitionId může existovat několik zásad nebo přiřazení iniciativ s různými obory. Zadáním PolicyAssignmentId a PolicyDefinitionId můžeme být definován v požadovaných výsledků hledáme. Dříve jsme pro PolicyStates používali **nejnovější**, což automaticky nastaví časové období **z** a **do** za posledních 24 hodin.

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

Pokud se prostředek vytvoří nebo aktualizuje, se vygeneruje výsledek vyhodnocení zásad. Výsledky se nazývají _události zásad_. Chcete-li zobrazit poslední události zásad, které jsou přidružené k předplatnému pomocí následující identifikátor Uri.

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

### <a name="azure-powershell"></a>Azure Powershell

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