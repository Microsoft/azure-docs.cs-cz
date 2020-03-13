---
title: Vytváření souboru řešení pro správu v Azure | Microsoft Docs
description: Řešení pro správu poskytují sbalené scénáře správy, které můžou zákazníci přidat do prostředí Azure.  Tento článek poskytuje podrobné informace o tom, jak můžete vytvářet řešení pro správu, která se mají používat ve vašem vlastním prostředí nebo která budou k dispozici pro vaše zákazníky.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275175"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Vytvoření souboru řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytváření řešení pro správu v Azure, která jsou momentálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.  

Řešení pro správu v Azure se implementují jako [šablony Správce prostředků](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).  Hlavním úkolem při učení, jak vytvářet řešení pro správu, je učení, jak vytvořit [šablonu](../../azure-resource-manager/templates/template-syntax.md).  Tento článek poskytuje jedinečné podrobnosti o šablonách používaných pro řešení a konfiguraci typických prostředků řešení.


## <a name="tools"></a>Nástroje

Můžete použít libovolný textový editor pro práci se soubory řešení, ale doporučujeme využívat funkce poskytované v aplikaci Visual Studio nebo Visual Studio Code, jak je popsáno v následujících článcích.

- [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Práce se šablonami Azure Resource Manager v Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktura
Základní struktura souboru řešení pro správu je shodná s [Správce prostředků šablonou](../../azure-resource-manager/templates/template-syntax.md#template-format), což je následující.  Všechny níže uvedené části popisují prvky nejvyšší úrovně a jejich obsah v řešení.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../../azure-resource-manager/templates/template-syntax.md#parameters) jsou hodnoty, které od uživatele požadujete při instalaci řešení pro správu.  Existují standardní parametry, které budou mít všechna řešení, a můžete přidat další parametry podle požadavků vašeho konkrétního řešení.  Způsob, jakým budou uživatelé zadávat hodnoty parametrů při instalaci vašeho řešení, budou záviset na konkrétním parametru a způsobu instalace řešení.

Když uživatel [nainstaluje vaše řešení pro správu](solutions.md#install-a-monitoring-solution) prostřednictvím Azure Marketplace nebo šablon Azure pro rychlý Start, zobrazí se výzva k výběru [pracovního prostoru Log Analytics a účtu Automation](solutions.md#log-analytics-workspace-and-automation-account).  Používají se k naplnění hodnot všech standardních parametrů.  Uživateli se nezobrazí výzva k zadání hodnot standardních parametrů, ale zobrazí se jim výzva k zadání hodnot pro všechny další parametry.


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
| typ |Datový typ pro parametr Ovládací prvek vstupu zobrazený pro uživatele závisí na datovém typu.<br><br>logická hodnota – rozevírací seznam<br>řetězec – textové pole<br>int – textové pole<br>SecureString – pole hesla<br> |
| category |Volitelná kategorie pro parametr  Parametry ve stejné kategorii jsou seskupeny dohromady. |
| control |Další funkce pro řetězcové parametry.<br><br>zobrazí se ovládací prvek DateTime-DateTime.<br>identifikátor GUID – hodnota GUID je vygenerována automaticky a parametr není zobrazen. |
| description |Volitelný popis parametru  Zobrazuje se v informační bublině vedle parametru. |

### <a name="standard-parameters"></a>Standardní parametry
V následující tabulce jsou uvedeny standardní parametry pro všechna řešení pro správu.  Tyto hodnoty se naplní pro uživatele, a to místo na dotazování, když se vaše řešení nainstaluje ze šablon Azure Marketplace nebo rychlý Start.  Uživatel musí zadat hodnoty, pokud je řešení nainstalováno s jinou metodou.

> [!NOTE]
> Uživatelské rozhraní v šablonách Azure Marketplace a rychlé zprovoznění očekává názvy parametrů v tabulce.  Pokud použijete jiné názvy parametrů, zobrazí se uživateli výzva k zadání a nebudou automaticky vyplněny.
>
>

| Parametr | Typ | Popis |
|:--- |:--- |:--- |
| accountName |string |Azure Automation název účtu. |
| pricingTier |string |Cenová úroveň obou Log Analytics pracovní prostor a účet Azure Automation. |
| regionId |string |Oblast účtu Azure Automation. |
| solutionName |string |Název řešení.  Pokud nasazujete své řešení prostřednictvím šablon pro rychlý Start, pak byste měli definovat parametr pro řešení jako parametr, abyste mohli definovat řetězec místo toho, aby uživatel mohl zadat jeden. |
| workspaceName |string |Log Analytics název pracovního prostoru. |
| workspaceRegionId |string |Oblast pracovního prostoru Log Analytics. |


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


Odkazujete na hodnoty parametrů v jiných prvcích řešení pomocí **parametrů syntaxe (název parametru)** .  Například pro přístup k názvu pracovního prostoru byste použili **parametry ("pracovní prostor")** .

## <a name="variables"></a>Proměnné
[Proměnné](../../azure-resource-manager/templates/template-syntax.md#variables) jsou hodnoty, které použijete ve zbývající části řešení pro správu.  Tyto hodnoty nejsou zveřejněny uživateli, který řešení nainstaloval.  Jsou určeny k poskytnutí jediného místa, kde mohou spravovat hodnoty, které mohou být používány několikrát v celém řešení. Měli byste do proměnných umístit jakékoli hodnoty specifické pro vaše řešení, a to na rozdíl od jejich hardwarového kódování v elementu **Resources** .  Díky tomu je kód čitelnější a umožňuje snadno změnit tyto hodnoty v pozdějších verzích.

Následuje příklad prvku **proměnné** s typickými parametry použitými v řešeních.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Pomocí **proměnných syntaxe (název proměnné ')** můžete odkazovat na proměnné hodnoty prostřednictvím řešení.  Například pro přístup k proměnné řešení byste použili **proměnné (' Solution ')** .

Můžete také definovat komplexní proměnné, které mají více sad hodnot.  Tyto informace jsou zvláště užitečné v řešeních pro správu, kde definujete více vlastností pro různé typy prostředků.  Můžete například změnit strukturu proměnných řešení zobrazených výše na následující.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

V takovém případě odkazujete na proměnné hodnoty prostřednictvím řešení pomocí **proměnných syntaxe (' název proměnné '). Property**.  Například pro přístup k proměnné názvu řešení byste měli použít **proměnné (Solution). Název**.

## <a name="resources"></a>Prostředky
[Prostředky](../../azure-resource-manager/templates/template-syntax.md#resources) definují různé prostředky, které vaše řešení pro správu budou instalovat a konfigurovat.  Toto bude největší a nejsložitější část šablony.  Můžete získat strukturu a úplný popis prvků prostředků při [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md#resources).  Různé prostředky, které obvykle definujete, jsou podrobně popsané v dalších článcích v této dokumentaci. 


### <a name="dependencies"></a>Závislosti
Element **dependsOn** Určuje [závislost](../../azure-resource-manager/templates/define-resource-dependency.md) na jiném prostředku.  Po instalaci řešení není prostředek vytvořen, dokud nebudou vytvořeny všechny jeho závislosti.  Řešení může například [Spustit sadu Runbook](solutions-resources-automation.md#runbooks) při instalaci pomocí [prostředku úlohy](solutions-resources-automation.md#automation-jobs).  Prostředek úlohy by byl závislý na prostředku Runbooku, aby se zajistilo, že se Runbook vytvoří před vytvořením úlohy.

### <a name="log-analytics-workspace-and-automation-account"></a>Pracovní prostor log Analytics a účet služby Automation
Řešení pro správu vyžadují, aby [Log Analytics pracovní prostor](../../azure-monitor/platform/manage-access.md) obsahovat zobrazení a [účet Automation](../../automation/automation-security-overview.md#automation-account-overview) , které obsahují Runbooky a související prostředky.  Tyto musí být k dispozici před vytvořením prostředků v řešení a neměly by být definovány v samotném řešení.  Uživatel zadá [pracovní prostor a účet](solutions.md#log-analytics-workspace-and-automation-account) při nasazení vašeho řešení, ale jako autor byste měli zvážit následující body.


## <a name="solution-resource"></a>Prostředek řešení
Každé řešení vyžaduje záznam prostředku v elementu **Resources** , který definuje samotné řešení.  Tato akce bude mít typ **Microsoft. OperationsManagement/Solutions** a má následující strukturu. To zahrnuje [Standardní parametry](#parameters) a [proměnné](#variables) , které se obvykle používají k definování vlastností řešení.


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
Prostředek řešení musí mít [závislost](../../azure-resource-manager/templates/define-resource-dependency.md) na všech ostatních zdrojích v řešení, protože musí existovat, aby bylo možné řešení vytvořit.  To provedete tak, že přidáte položku pro každý prostředek v elementu **dependsOn** .

### <a name="properties"></a>Vlastnosti
Prostředek řešení obsahuje vlastnosti v následující tabulce.  To zahrnuje prostředky odkazované a obsažené v řešení, které definují, jak je prostředek spravován po instalaci řešení.  Každý prostředek v řešení by měl být uveden buď v **referencedResources** , nebo v vlastnosti **containedResources** .

| Vlastnost | Popis |
|:--- |:--- |
| workspaceResourceId |ID pracovního prostoru Log Analytics ve formuláři *\<ID skupiny prostředků >/providers/Microsoft.OperationalInsights/workspaces/\<název pracovního prostoru\>* . |
| referencedResources |Seznam prostředků v řešení, které by neměly být odebrány po odebrání řešení. |
| containedResources |Seznam prostředků v řešení, které by měly být odebrány po odebrání řešení. |

Výše uvedený příklad je pro řešení se sadou Runbook, plánem a zobrazením.  Plán a sada Runbook jsou *odkazovány* v elementu **Properties** , takže nejsou odstraněny při odebrání řešení.  Zobrazení je *obsaženo* , takže je odebráno při odebrání řešení.

### <a name="plan"></a>Plánování
Entita **plánu** prostředku řešení má vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| name |Název řešení. |
| Verze nástroje |Verze řešení určená autorem. |
| product |Jedinečný řetězec pro identifikaci řešení |
| publisher |Vydavatel řešení. |



## <a name="next-steps"></a>Další kroky
* [Přidejte uložená hledání a výstrahy](solutions-resources-searches-alerts.md) do řešení pro správu.
* [Přidejte zobrazení](solutions-resources-views.md) do řešení pro správu.
* [Přidejte Runbooky a další prostředky služby Automation](solutions-resources-automation.md) do řešení pro správu.
* Seznamte se s podrobnostmi o [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md).
* Vyhledejte v [šablonách rychlého startu Azure](https://azure.microsoft.com/documentation/templates) ukázky různých šablon Správce prostředků.
