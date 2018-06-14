---
title: Vytvoření souboru sady management řešení v Azure | Microsoft Docs
description: Řešení pro správu poskytuje scénářů zabalené správy, které zákazníci mohou přidat do prostředí Azure.  Tento článek poskytuje podrobné informace o tom, jak můžete vytvořit řešení správy, který se má použít ve svém vlastním prostředí nebo k dispozici pro vaše zákazníky.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d896fb7c5ffed5c0fe338c2d2f1ef864aacd6f79
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28929044"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Vytvoření souboru sady management řešení v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytvoření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Žádné schéma popsané níže se mohou změnit.  

Řešení pro správu v Azure jsou implementované jako [šablony Resource Manageru](../azure-resource-manager/resource-manager-template-walkthrough.md).  Hlavní úloha naučit vytvářet řešení pro správu je učení postup [vytvořit šablonu](../azure-resource-manager/resource-group-authoring-templates.md).  Tento článek obsahuje jedinečné Podrobnosti šablony použité pro řešení a jak nakonfigurovat prostředky typické řešení.


## <a name="tools"></a>Nástroje

Libovolného textového editoru můžete pracovat se soubory řešení, ale doporučujeme využívat funkce uvedené v sadě Visual Studio nebo Visual Studio Code, jak je popsáno v následujících článcích.

- [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Práce s šablony Azure Resource Manageru v sadě Visual Studio kódu](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>Struktura
Základní struktura soubor řešení správy je stejná jako [šablony Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md#template-format), což je následujícím způsobem.  Každý z níže uvedených částech popisuje elementy nejvyšší úrovně a jejich obsah v řešení.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../azure-resource-manager/resource-group-authoring-templates.md#parameters) jsou hodnoty, které požadujete od uživatele při instalaci řešení pro správu.  Jsou standardní parametry, které budou mít všechna řešení a podle potřeby můžete přidat další parametry pro vaše konkrétní řešení.  Jak budou uživatelé zadali hodnoty parametrů při instalaci řešení bude záviset na konkrétní parametr a jak se instaluje řešení.

Když uživatel nainstaluje řešení pro správu prostřednictvím [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) nebo [šablony Azure QuickStart](operations-management-suite-solutions.md#finding-and-installing-management-solutions) se výzva k výběru [pracovní prostor analýzy protokolů a automatizace účet](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account).  Ty se používají k naplnění hodnoty jednotlivých standardní parametry.  Uživatel nebude vyzván k přímo zadat hodnoty pro standardní parametry, ale bude vyzván k zadání hodnot pro žádné další parametry.


Když uživatel nainstaluje řešení [jinou metodu](operations-management-suite-solutions.md#finding-and-installing-management-solutions), musí zadat hodnotu pro všechny standardní parametry a všech dalších parametrů.

Ukázka parametrů jsou uvedeny níže.  

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
| type |Datový typ pro parametr. Vstupní ovládací prvek zobrazí pro uživatele, závisí na typu dat.<br><br>BOOL – rozevíracího seznamu<br>String – textové pole<br>int – textové pole<br>SecureString - pole pro heslo<br> |
| category |Volitelné kategorie pro parametr.  Parametry ve stejné kategorii jsou seskupeny dohromady. |
| Ovládací prvek |Další funkce pro parametry řetězce.<br><br>Zobrazí se datum a čas - datum a čas řízení.<br>identifikátor GUID – hodnota identifikátoru Guid je generován automaticky, a parametr nezobrazuje. |
| description |Volitelný popis pro parametr.  Zobrazí v bublinách informace vedle parametru. |

### <a name="standard-parameters"></a>Standardní parametry
Následující tabulka uvádí standardní parametry pro všechna řešení pro správu.  Tyto hodnoty jsou naplněny pro uživatele místo dotaz na ně při řešení z Azure Marketplace nebo šablony rychlý start.  Uživatel musí poskytnout hodnoty pro ně, pokud řešení se instaluje s jinou metodu.

> [!NOTE]
> Uživatelské rozhraní v Azure Marketplace a šablony rychlý start očekává názvy parametrů v tabulce.  Pokud používáte jiný parametr názvy pak uživatele vyzváni k jejich a nebude se automaticky vyplní.
>
>

| Parametr | Typ | Popis |
|:--- |:--- |:--- |
| accountName |řetězec |Název účtu Azure Automation. |
| pricingTier |řetězec |Cenová úroveň pracovní prostor analýzy protokolů a účet Azure Automation. |
| regionId |řetězec |Oblast účet Azure Automation. |
| solutionName |řetězec |Název řešení.  Pokud nasazujete řešení prostřednictvím šablony rychlý start, pak byste měli definovat název řešení solutionName jako parametr, můžete definovat místo nutnosti uživateli zadat jeden řetězec. |
| workspaceName |řetězec |Název pracovního prostoru analýzy protokolů |
| workspaceRegionId |řetězec |Oblast pracovního prostoru analýzy protokolů. |


Následuje strukturu standardní parametry, které můžete zkopírovat a vložit do souboru řešení.  

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


Odkazujete na hodnoty parametrů v další prvky řešení se syntaxí **parametry (název parametru)**.  Například pokud chcete získat přístup k název pracovního prostoru, použijte **parameters('workspaceName')**

## <a name="variables"></a>Proměnné
[Proměnné](../azure-resource-manager/resource-group-authoring-templates.md#variables) jsou hodnoty, které budete používat ve zbývající části řešení pro správu.  Tyto hodnoty nejsou zveřejněné má uživatel instalující řešení.  Ty jsou určené Autor poskytnout na jednom místě, kde můžete spravovat hodnoty, které se dají použít více než jednou v celé řešení. Byste měli umístit všechny hodnoty specifické pro vaše řešení v proměnné a pevné kódování v **prostředky** elementu.  To usnadňuje kód srozumitelnější a umožňuje snadno změnit tyto hodnoty v novějších verzích.

Následuje příklad **proměnné** element s typické parametry použité v řešení.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Odkazujete na hodnoty proměnné prostřednictvím řešení se syntaxí **proměnné ('název proměnné')**.  Například pokud chcete přístup k proměnné Název řešení SolutionName, použijte **variables('SolutionName')**.

Můžete také definovat komplexní proměnné tohoto několik sad hodnot.  Tyto jsou zvláště užitečné při řešení pro správu, které definujete více vlastností pro různé typy prostředků.  Například může změnit strukturu proměnné řešení uvedené výše takto.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

V takovém případě je odkazovat na hodnoty proměnné prostřednictvím řešení se syntaxí **variables('variable name').property**.  Například pokud chcete přístup k proměnné Název řešení, použijte **variables('Solution'). Název**.

## <a name="resources"></a>Zdroje a prostředky
[Prostředky](../azure-resource-manager/resource-group-authoring-templates.md#resources) definovat různé prostředky, které nainstaluje a nakonfiguruje vaše řešení pro správu.  To bude největší a těch nejsložitějších část šablony.  Můžete získat strukturu a úplný popis elementů prostředků v [šablon pro tvorbu Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Různé prostředky, které se obvykle definují, jsou popsané v další články v této dokumentaci. 


### <a name="dependencies"></a>Závislosti
**DependsOn** určuje element [závislostí](../azure-resource-manager/resource-group-define-dependencies.md) na jiný prostředek.  Při instalaci řešení prostředku se nevytvoří, dokud všechny jeho závislé součásti byly vytvořeny.  Například může být vaše řešení [spuštění sady runbook](operations-management-suite-solutions-resources-automation.md#runbooks) při instalaci pomocí [úlohy prostředků](operations-management-suite-solutions-resources-automation.md#automation-jobs).  Prostředek úlohy by být závislý na prostředku sady runbook, abyste měli jistotu, že je sada runbook vytvořena předtím, než se vytvoří úloha.

### <a name="log-analytics-workspace-and-automation-account"></a>Pracovní prostor analýzy protokolů a účet Automation.
Vyžaduje řešení pro správu [pracovní prostor analýzy protokolů](../log-analytics/log-analytics-manage-access.md) tak, aby obsahovala zobrazení a [účet Automation](../automation/automation-security-overview.md#automation-account-overview) tak, aby obsahovala sady runbook a související prostředky.  Musí mít k dispozici před prostředky v řešení jsou vytvořeny a nesmí být definována v řešení sám sebe.  Uživatel bude [zadejte prostoru a účet](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account) při jejich nasazování svého řešení, ale jako autor byste měli zvážit následující body.


## <a name="solution-resource"></a>Řešení prostředků
Každé řešení vyžaduje záznam prostředků v **prostředky** element, který definuje řešení sám sebe.  To bude mít typ **Microsoft.OperationsManagement/solutions** a mít následující strukturu. To zahrnuje [standardní parametry](#parameters) a [proměnné](#variables) , jsou obvykle používány k definování vlastností řešení.


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
Musí mít prostředek řešení [závislostí](../azure-resource-manager/resource-group-define-dependencies.md) na každý jiný prostředek v řešení vzhledem k tomu, že potřebují existovat, aby bylo možné vytvořit řešení.  To uděláte tak, že přidáte položku pro každý zdroj v **dependsOn** elementu.

### <a name="properties"></a>Vlastnosti
Řešení prostředek má vlastnosti v následující tabulce.  To zahrnuje prostředky odkazuje a obsažený v řešení, která definuje, jak se spravuje prostředku po instalaci řešení.  Všechny prostředky v řešení by měl být uveden buď **referencedResources** nebo **containedResources** vlastnost.

| Vlastnost | Popis |
|:--- |:--- |
| workspaceResourceId |ID pracovního prostoru analýzy protokolů ve formě  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<název pracovního prostoru\>*. |
| referencedResources |Seznam prostředků v řešení, které by se neměly odebírat, když dojde k odebrání řešení. |
| containedResources |Seznam prostředků v řešení, které byste měli odebrat, když dojde k odebrání řešení. |

V předchozím příkladu je řešení s sady runbook, plán a zobrazení.  Plán a sady runbook jsou *odkazované* v **vlastnosti** element tak nejsou odebrány při odebrání řešení.  Zobrazení je *obsažené* proto je odebrána, když dojde k odebrání řešení.

### <a name="plan"></a>Plánování
**Plán** entity řešení prostředku má vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| jméno |Název řešení. |
| verze |Verze řešení, počítáno od autora. |
| produkt |Jedinečný řetězec k identifikaci řešení. |
| Vydavatele |Vydavatel řešení. |



## <a name="sample"></a>Ukázka
Můžete zobrazit ukázky soubory řešení s prostředek řešení v následujících umístěních.

- [Prostředky služby Automation](operations-management-suite-solutions-resources-automation.md#sample)
- [Hledání a výstraha prostředky](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Další postup
* [Přidat uložená hledání a výstrahy](operations-management-suite-solutions-resources-searches-alerts.md) do řešení pro správu.
* [Přidání zobrazení](operations-management-suite-solutions-resources-views.md) do řešení pro správu.
* [Přidat sady runbook a dalším prostředkům Automation](operations-management-suite-solutions-resources-automation.md) do řešení pro správu.
* Další podrobnosti o [šablon pro tvorbu Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).
* Hledání [šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates) ukázky různých šablonách Resource Manager.
