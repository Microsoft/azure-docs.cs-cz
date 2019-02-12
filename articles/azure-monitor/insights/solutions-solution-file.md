---
title: Vytváření souboru řešení správy v Azure | Dokumentace Microsoftu
description: Řešení pro správu poskytují scénáře zabalené správy, které zákazníci můžete přidat do svého prostředí Azure.  Tento článek obsahuje podrobné informace o tom, jak můžete vytvořit řešení pro správu pro použití ve vašem prostředí nebo k dispozici zákazníkům.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c84aedbdb306bbd7432c3c0b611e6e58243624e2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999364"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Vytváření souboru řešení správy v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Žádné schéma je popsáno níže se může změnit.  

Řešení pro správu v Azure jsou implementovány jako [šablon Resource Manageru](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).  Učení hlavního úkolu v tom, jak vytvářet řešení pro správu jak [vytvářet šablony](../../azure-resource-manager/resource-group-authoring-templates.md).  Tento článek obsahuje jedinečné podrobnosti šablon použitých pro řešení a konfigurace prostředků typické řešení.


## <a name="tools"></a>Nástroje

Můžete použít libovolný textový editor pro práci se soubory řešení, ale doporučujeme využívá funkce, které jsou součástí sady Visual Studio nebo Visual Studio Code, jak je popsáno v následujících článcích.

- [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Práce s šablonami Azure Resource Manageru ve Visual Studio Code](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktura
Základní struktura souboru řešení správy je stejné jako [šablony Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md#template-format), což je následujícím způsobem.  Každé z níže uvedených částech najdete popisuje nejvyšší úrovně prvky a jejich obsah v řešení.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) jsou hodnoty, které požadujete od uživatele při instalaci řešení pro správu.  Existuje standardní parametry, které budou mít všechna řešení, a podle potřeby můžete přidat další parametry pro konkrétní řešení.  Jak se uživatelé zadáním hodnot parametrů při instalaci řešení bude záviset na konkrétních parametrů a jak se instaluje řešení.

Když se uživatel [nainstaluje řešení pro správu](solutions.md#install-a-monitoring-solution) prostřednictvím šablon Azure Marketplace nebo Azure pro rychlý start, zobrazí se výzva k výběru [pracovní prostor Log Analytics a účet Automation](solutions.md#log-analytics-workspace-and-automation-account).  Ty se používají k naplnění hodnoty každého standardní parametry.  Uživatel není vyzván, aby přímo zadat hodnoty pro standardní parametry, ale zobrazí se výzva k zadání hodnoty pro žádné další parametry.


Parametr vzorku je uveden níže.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Následující tabulka popisuje atributy parametr.

| Atribut | Popis |
|:--- |:--- |
| type |Datový typ pro parametr. Vstupní ovládací prvek zobrazuje pro uživatele závisí na datovém typu.<br><br>BOOL – rozevíracího seznamu<br>String – textové pole<br>int – textové pole<br>SecureString - pole pro heslo<br> |
| category |Volitelné kategorie pro parametr.  Parametry ve stejné kategorii jsou seskupené dohromady. |
| Ovládací prvek |Další funkce pro parametry řetězce.<br><br>Zobrazí se datum a čas – ovládací prvek datum a čas.<br>identifikátor GUID - hodnota identifikátoru Guid je generován automaticky, a parametr nezobrazuje. |
| description |Volitelný popis pro parametr.  Zobrazí bublinu informace vedle parametru. |

### <a name="standard-parameters"></a>Standardní parametry
Následující tabulka uvádí standardní parametry pro všechna řešení pro správu.  Tyto hodnoty se vyplní pro uživatele místo vás vyzve k zadání jejich při instalaci řešení z Azure Marketplace nebo šablon rychlého startu.  Uživatel musí zadat jejich hodnoty v případě, že je řešení nainstalováno s jinou metodu.

> [!NOTE]
> Uživatelské rozhraní v Azure Marketplace a šablony pro rychlý start očekává názvy parametrů v tabulce.  Pokud používáte různé názvy parametrů poté uživateli zobrazí výzva pro ně a nesmí být vyplněné automaticky.
>
>

| Parametr | Type | Popis |
|:--- |:--- |:--- |
| accountName |řetězec |Název účtu Azure Automation. |
| pricingTier |řetězec |Cenová úroveň pracovního prostoru Log Analytics a účet Azure Automation. |
| regionId |řetězec |Oblast účtu Azure Automation. |
| solutionName |řetězec |Název řešení.  Pokud provádíte nasazení vašeho řešení prostřednictvím šablon rychlý start, pak byste měli definovat solutionName jako parametr, můžete definovat místo toho by uživatel musel zadejte jeden řetězec. |
| workspaceName |řetězec |Název pracovního prostoru log Analytics. |
| workspaceRegionId |řetězec |Oblasti pracovního prostoru Log Analytics. |


Tady je struktura standardní parametry, které můžete zkopírovat a vložit do souboru řešení.  

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


Získat hodnoty parametrů v dalších prvků řešení se syntaxí **parametry (název parametru)**.  Například pro přístup k názvu pracovního prostoru, můžete využít **parameters('workspaceName')**

## <a name="variables"></a>Proměnné
[Proměnné](../../azure-resource-manager/resource-group-authoring-templates.md#variables) jsou hodnoty, které budete používat ve zbytku řešení pro správu.  Tyto hodnoty nejsou zveřejněné uživatel, který instaluje řešení.  Jsou určeny Autor poskytnout na jednom místě, kde můžete spravovat hodnoty, které lze použít více než jednou v rámci řešení. Měli byste umístit všechny hodnoty konkrétní do svého řešení v proměnných na rozdíl od pevného kódování v **prostředky** elementu.  To usnadňuje kód lépe čitelný a vám umožní snadno změnit tyto hodnoty v pozdějších verzích.

Tady je příklad **proměnné** element s typické parametrů použitých v řešení.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Odkazovat na proměnné hodnoty v řešení se syntaxí **proměnných ("Proměnná")**.  Například pro přístup k proměnné SolutionName, byste použili **variables('SolutionName')**.

Můžete také definovat proměnné komplexní této více sad hodnot.  Tyto jsou zvláště užitečné při řešení pro správu, kde jsou definování více vlastností pro jednotlivé typy materiálů.  Například může změnit strukturu proměnné řešení uvedené výše následující.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

V tomto případě odkazujete na hodnoty proměnných v řešení se syntaxí **variables('variable name').property**.  Například pro přístup k proměnné Název řešení, můžete využít **variables('Solution'). Název**.

## <a name="resources"></a>Zdroje a prostředky
[Prostředky](../../azure-resource-manager/resource-group-authoring-templates.md#resources) definovat různé prostředky, které budou nainstalovány a konfigurovány řešení pro správu.  Bude jím největší a nejsložitější část šablony.  Můžete získat strukturu a úplný popis prvků prostředku v [šablon pro vytváření Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md#resources).  Různé prostředky, které se obvykle definují je podrobně popsaný v jiných článcích v této dokumentaci. 


### <a name="dependencies"></a>Závislosti
**DependsOn** určuje element [závislost](../../azure-resource-manager/resource-group-define-dependencies.md) na jiný prostředek.  Při instalaci řešení není vytvořen prostředek, dokud všechny jeho závislosti vytvořili.  Například může být vaše řešení [spuštění sady runbook](solutions-resources-automation.md#runbooks) po instalaci pomocí [úlohy prostředků](solutions-resources-automation.md#automation-jobs).  Zdroj úlohy by být závislý na prostředku sady runbook, abyste měli jistotu, že sada runbook je vytvořená před vytvořením úlohy.

### <a name="log-analytics-workspace-and-automation-account"></a>Pracovní prostor log Analytics a účet služby Automation
Vyžadovat řešení pro správu [pracovní prostor Log Analytics](../../azure-monitor/platform/manage-access.md) tak, aby obsahovala zobrazení a [účtu Automation](../../automation/automation-security-overview.md#automation-account-overview) tak, aby obsahovala sady runbook a související prostředky.  Toto musí být k dispozici prostředky v řešení jsou vytvořeny a neměl by být definován ve vlastním řešením.  Uživatel uvidí [určit pracovní prostor a účet](solutions.md#log-analytics-workspace-and-automation-account) po jejich nasazení vašeho řešení, ale jako autor, měli byste zvážit následující body.


## <a name="solution-resource"></a>Řešení prostředků
Každé řešení vyžaduje zadání prostředků v **prostředky** element, který definuje vlastním řešením.  To bude mít typ **Microsoft.OperationsManagement/solutions** a mají následující strukturu. Jedná se o [standardní parametry](#parameters) a [proměnné](#variables) , která se obvykle používají k definování vlastností řešení.


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
Prostředek řešení musí mít [závislost](../../azure-resource-manager/resource-group-define-dependencies.md) na každých dalších prostředků v řešení, protože musí existovat před vytvořením řešení.  Provedete to přidáním položky pro každý zdroj v **dependsOn** elementu.

### <a name="properties"></a>Vlastnosti
Prostředek řešení má vlastnosti v následující tabulce.  To zahrnuje prostředky odkazovat a obsažen v řešení, která definuje, jak se spravují prostředek po instalaci řešení.  Každý prostředek v řešení by měly být uvedeny v buď **referencedResources** nebo **containedResources** vlastnost.

| Vlastnost | Popis |
|:--- |:--- |
| workspaceResourceId |ID pracovního prostoru Log Analytics ve formě  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<název pracovního prostoru\>*. |
| referencedResources |Seznam prostředků v řešení, které by se neměly odebírat při odebrání řešení. |
| containedResources |Seznam prostředků v řešení, které by měly být odstraněny při odebrání řešení. |

Výše uvedený příklad je pro řešení s sady runbook, plánu a zobrazení.  Plán a sady runbook jsou *odkazované* v **vlastnosti** element tak, že nejsou odebrány při odebrání řešení.  Zobrazení je *obsažené* , odebere se při odebrání řešení.

### <a name="plan"></a>Plánování
**Plán** entity prostředků řešení má vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| jméno |Název řešení. |
| version |Verze řešení podle autora. |
| produkt |Jedinečný řetězec k identifikaci řešení. |
| vydavatele |Vydavatel řešení. |



## <a name="sample"></a>Ukázka
Můžete si prohlédnout ukázky soubory řešení, s prostředkem řešení v následujících umístěních.

- [Prostředky služby Automation](solutions-resources-automation.md#sample)
- [Hledání a výstrahy prostředky](solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Další postup
* [Přidat uložené výsledky hledání a výstrahy](solutions-resources-searches-alerts.md) do řešení pro správu.
* [Přidání zobrazení](solutions-resources-views.md) do řešení pro správu.
* [Přidání runbooků a další prostředky služby Automation](solutions-resources-automation.md) do řešení pro správu.
* Další podrobnosti o [šablon pro vytváření Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md).
* Hledání [šablony pro rychlý start Azure](https://azure.microsoft.com/documentation/templates) ukázky různých šablon Resource Manageru.
