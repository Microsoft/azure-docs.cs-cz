---
title: Vytvoření souboru řešení pro správu v Azure | Dokumenty společnosti Microsoft
description: Řešení pro správu poskytují zabalené scénáře správy, které mohou zákazníci přidat do svého prostředí Azure.  Tento článek obsahuje podrobnosti o tom, jak můžete vytvořit řešení pro správu, která budou použita ve vašem vlastním prostředí nebo zpřístupněna zákazníkům.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275175"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Vytvoření souboru řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Jakékoli schéma popsané níže se může změnit.  

Řešení pro správu v Azure se implementují jako [šablony Správce prostředků](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).  Hlavním úkolem při učení, jak vytvářet řešení pro správu, je naučit se [vytvářet šablonu](../../azure-resource-manager/templates/template-syntax.md).  Tento článek obsahuje jedinečné podrobnosti o šablonách používaných pro řešení a jak konfigurovat typické prostředky řešení.


## <a name="tools"></a>Nástroje

Můžete použít libovolný textový editor pro práci se soubory řešení, ale doporučujeme využít funkce poskytované v sadě Visual Studio nebo Visual Studio Kód, jak je popsáno v následujících článcích.

- [Vytvoření a nasazení skupiny prostředků Azure pomocí sady Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Práce s šablonami Azure Resource Manageru ve Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktura
Základní struktura souboru řešení pro správu je stejná jako [šablona Správce prostředků](../../azure-resource-manager/templates/template-syntax.md#template-format), která je následující.  Každá z níže uvedených částí popisuje prvky nejvyšší úrovně a jejich obsah v řešení.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../../azure-resource-manager/templates/template-syntax.md#parameters) jsou hodnoty, které požadujete od uživatele při instalaci řešení pro správu.  Existují standardní parametry, které budou mít všechna řešení, a můžete přidat další parametry podle potřeby pro konkrétní řešení.  Způsob, jakým uživatelé při instalaci řešení zadají hodnoty parametrů, bude záviset na konkrétním parametru a způsobu instalace řešení.

Když uživatel [nainstaluje vaše řešení pro správu](solutions.md#install-a-monitoring-solution) prostřednictvím šablon Azure Marketplace nebo Azure QuickStart, zobrazí se výzva k výběru [pracovního prostoru Analýzy protokolů a účtu Automation](solutions.md#log-analytics-workspace-and-automation-account).  Ty se používají k naplnění hodnot každého standardního parametru.  Uživatel není vyzván k přímému zadání hodnot pro standardní parametry, ale jsou vyzváni k zadání hodnot pro všechny další parametry.


Ukázkový parametr je uveden níže.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Následující tabulka popisuje atributy parametru.

| Atribut | Popis |
|:--- |:--- |
| type |Datový typ parametru. Vstupní ovládací prvek zobrazený pro uživatele závisí na typu dat.<br><br>bool - Rozbalovací box<br>řetězec - textové pole<br>int - Textové pole<br>securestring - heslo pole<br> |
| category |Volitelná kategorie parametru.  Parametry ve stejné kategorii jsou seskupeny. |
| – ovládací prvek |Další funkce pro parametry řetězce.<br><br>datetime - Datetime ovládací prvek je zobrazen.<br>guid - Guid hodnota je automaticky generována a parametr se nezobrazí. |
| description |Volitelný popis parametru.  Zobrazí se v informační bublině vedle parametru. |

### <a name="standard-parameters"></a>Standardní parametry
V následující tabulce jsou uvedeny standardní parametry pro všechna řešení pro správu.  Tyto hodnoty jsou vyplněny pro uživatele namísto výzvy pro ně při instalaci vašeho řešení z Azure Marketplace nebo úvodní ho.  Uživatel musí zadat hodnoty pro ně, pokud je řešení nainstalováno jinou metodou.

> [!NOTE]
> Uživatelské rozhraní v šablonách Azure Marketplace a Quickstart očekává názvy parametrů v tabulce.  Pokud použijete různé názvy parametrů, bude uživatel vyzván k jejich zobrazení a nebudou automaticky naplněny.
>
>

| Parametr | Typ | Popis |
|:--- |:--- |:--- |
| accountName |řetězec |Název účtu Azure Automation. |
| pricingTier |řetězec |Cenová úroveň pracovního prostoru Log Analytics i účtu Azure Automation. |
| regionId |řetězec |Oblast účtu Azure Automation. |
| název_řešení |řetězec |Název řešení.  Pokud nasazujete řešení prostřednictvím šablon rychlého startu, měli byste definovat solutionName jako parametr, abyste mohli definovat řetězec, který vyžaduje, aby uživatel zadaný. |
| název_pracovního_ |řetězec |Název pracovního prostoru Analýzy protokolů. |
| workspaceRegionId |řetězec |Oblast pracovního prostoru Log Analytics. |


Následuje struktura standardních parametrů, které můžete zkopírovat a vložit do souboru řešení.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Odkazujete na hodnoty parametrů v jiných prvcích řešení se **syntaktickými parametry ("název parametru")**.  Chcete-li například získat přístup k názvu pracovního prostoru, použijte **parametry ('workspaceName')**

## <a name="variables"></a>Proměnné
[Proměnné](../../azure-resource-manager/templates/template-syntax.md#variables) jsou hodnoty, které budete používat ve zbytku řešení pro správu.  Tyto hodnoty nejsou vystaveny uživateli instalaci řešení.  Jsou určeny k poskytnutí autora s jedním umístěním, kde mohou spravovat hodnoty, které mohou být použity vícekrát v celém řešení. Měli byste umístit všechny hodnoty specifické pro vaše řešení v proměnných na rozdíl od pevné kódování je v elementu **prostředků.**  Díky kódu čitelnější a umožňuje snadno změnit tyto hodnoty v novějších verzích.

Následuje příklad prvku **proměnných** s typickými parametry používanými v řešeních.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Odkazujete na hodnoty proměnných prostřednictvím řešení s proměnnými syntaxe **('název proměnné')**.  Například pro přístup k proměnné SolutionName byste **použili proměnné('SolutionName')**.

Můžete také definovat složité proměnné, které více sad hodnot.  To jsou užitečné zejména v řešení pro správu, kde definujete více vlastností pro různé typy prostředků.  Můžete například restrukturalizovat výše uvedené proměnné řešení na následující.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

V tomto případě odkazujete na hodnoty proměnných prostřednictvím řešení se **syntaxí proměnných('název proměnné').vlastnost**.  Například pro přístup k proměnné Název řešení byste **použili proměnné ('Solution'). Název**.

## <a name="resources"></a>Prostředky
[Prostředky](../../azure-resource-manager/templates/template-syntax.md#resources) definují různé prostředky, které bude vaše řešení pro správu instalovat a konfigurovat.  Bude to největší a nejsložitější část šablony.  Strukturu a úplný popis prvků prostředků můžete získat ve [vytváření šablon Azure Resource Manager .](../../azure-resource-manager/templates/template-syntax.md#resources)  Různé prostředky, které obvykle definujete, jsou podrobně popsány v jiných článcích v této dokumentaci. 


### <a name="dependencies"></a>Závislosti
Element **dependsOn** určuje [závislost](../../azure-resource-manager/templates/define-resource-dependency.md) na jiném prostředku.  Při instalaci řešení prostředek není vytvořen, dokud nebyly vytvořeny všechny jeho závislosti.  Vaše řešení může například [spustit runbook,](solutions-resources-automation.md#runbooks) když je nainstalován pomocí [prostředku úlohy](solutions-resources-automation.md#automation-jobs).  Zdroj úlohy by byl závislý na prostředku runbooku, aby se ujistil, že je runbook vytvořen před vytvořením úlohy.

### <a name="log-analytics-workspace-and-automation-account"></a>Účet pracovního prostoru Log Analytics a automatizačního účtu
Řešení pro správu vyžadují, aby [pracovní prostor Log Analytics](../../azure-monitor/platform/manage-access.md) obsahoval zobrazení a účet [Automation,](../../automation/automation-security-overview.md#automation-account-overview) který obsahuje sady Runbook a související prostředky.  Ty musí být k dispozici před vytvořením prostředků v řešení a neměly by být definovány v samotném řešení.  Uživatel při nasazení vašeho řešení [určí pracovní prostor a účet,](solutions.md#log-analytics-workspace-and-automation-account) ale jako autor byste měli zvážit následující body.


## <a name="solution-resource"></a>Prostředek řešení
Každé řešení vyžaduje položku prostředků v elementu **prostředků,** který definuje samotné řešení.  To bude mít typ **Microsoft.OperationsManagement/solutions** a mají následující strukturu. To zahrnuje [standardní parametry](#parameters) a [proměnné,](#variables) které se obvykle používají k definování vlastností řešení.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Závislosti
Prostředek řešení musí mít [závislost](../../azure-resource-manager/templates/define-resource-dependency.md) na všech ostatních prostředků v řešení, protože musí existovat před vytvořením řešení.  To provést přidáním položky pro každý prostředek v **dependsOn** element.

### <a name="properties"></a>Vlastnosti
Prostředek řešení má vlastnosti v následující tabulce.  To zahrnuje prostředky odkazované a obsažené v řešení, které definuje, jak je prostředek spravován po instalaci řešení.  Každý prostředek v řešení by měl být uveden v **vlastnosti referencedResources** nebo **containedResources.**

| Vlastnost | Popis |
|:--- |:--- |
| workspaceResourceId |ID pracovního prostoru Log Analytics ve formuláři * \<ID skupiny prostředků>/zprostředkovatelů/Microsoft.OperationalInsights/workspace/\<Název\>pracovního prostoru*. |
| odkazovanéZdroje |Seznam prostředků v řešení, které by neměly být odebrány při odebrání řešení. |
| containedZdroje |Seznam prostředků v řešení, které by měly být odebrány při odebrání řešení. |

Výše uvedený příklad je pro řešení s runbook, plán a zobrazení.  Plán a runbook jsou *odkazovány* v elementu **vlastnosti,** takže nejsou odebrány při odebrání řešení.  Zobrazení je *obsaženo* tak, že je odebráno při odebrání řešení.

### <a name="plan"></a>Plánování
Entita **plánu** prostředku řešení má vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| jméno |Název řešení. |
| version |Verze řešení stanovená autorem. |
| product |Jedinečný řetězec k identifikaci řešení. |
| vydavatel |Vydavatel řešení. |



## <a name="next-steps"></a>Další kroky
* [Přidejte uložená hledání a výstrahy](solutions-resources-searches-alerts.md) do svého řešení pro správu.
* [Přidejte zobrazení](solutions-resources-views.md) do řešení správy.
* [Přidejte sady Runbook a další prostředky automatizace](solutions-resources-automation.md) do svého řešení pro správu.
* Přečtěte si podrobnosti o [vytváření šablon Azure Resource Manager .](../../azure-resource-manager/templates/template-syntax.md)
* Prohledejte [šablony Azure QuickStart](https://azure.microsoft.com/documentation/templates) pro ukázky různých šablon Správce prostředků.
