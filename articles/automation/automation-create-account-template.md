---
title: Vytvoření účtu Automation pomocí šablony Azure Resource Manager | Microsoft Docs
description: V tomto článku se dozvíte, jak pomocí šablony Azure Resource Manager vytvořit účet Azure Automation.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 10b53da1ef277d7a3d6ab7c850192ce8f08ded68
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712668"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>Vytvoření účtu Automation pomocí šablony Azure Resource Manager

Pomocí [Azure Resource Manager šablon](../azure-resource-manager/templates/template-syntax.md) můžete vytvořit účet Azure Automation ve skupině prostředků. Tento článek poskytuje ukázkovou šablonu, která:

* Automatizuje vytváření Azure Monitorho pracovního prostoru Log Analytics.
* Automatizuje vytváření Azure Automation účtu.
* Propojí účet Automation s pracovním prostorem Log Analytics.

Šablona neautomatizuje povolování virtuálních počítačů Azure nebo mimo Azure. 

>[!NOTE]
>Vytvoření účtu Automation spustit jako není podporované, když používáte šablonu Azure Resource Manager. Pokud chcete vytvořit účet Spustit jako ručně z portálu nebo pomocí PowerShellu, přečtěte si téma [Správa účtů spustit jako](manage-runas-account.md).

## <a name="api-versions"></a>Verze rozhraní API

Následující tabulka uvádí verzi rozhraní API pro prostředky použité v tomto příkladu.

| Prostředek | Typ prostředku | Verze rozhraní API |
|:---|:---|:---|
| Pracovní prostor | pracovní prostory | 2017-03-15 – Preview |
| Účet Automation | automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>Před použitím šablony

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek Azure PowerShell AZ Module. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure. S prostředím PowerShell používá nasazení rutina [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít spuštěnou verzi 2.1.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Pomocí Azure CLI toto nasazení používá příkaz [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Šablona JSON je nakonfigurovaná tak, aby vás vyzvala k těmto akcím:

* Název pracovního prostoru
* Oblast, ve které se má pracovní prostor vytvořit.
* Název účtu Automation.
* Oblast, ve které se má účet vytvořit.

Následující parametry v šabloně jsou nastaveny s výchozí hodnotou pro Log Analytics pracovní prostor:

* Výchozí hodnota *SKU* je cenová úroveň na GB vydaná v cenovém modelu z dubna 2018.
* *Doba uchovávání dat* je nastavena na 30 dní.
* *capacityReservationLevel* má výchozí hodnotu 100 GB.

>[!WARNING]
>Pokud chcete vytvořit nebo nakonfigurovat pracovní prostor Log Analytics v předplatném, které se zavedlo do cenového modelu z dubna 2018, bude jediná platná Log Analytics cenová úroveň *PerGB2018*.
>

Šablona JSON určuje výchozí hodnotu pro ostatní parametry, které by pravděpodobně byly použity jako standardní konfigurace ve vašem prostředí. Šablonu můžete uložit v účtu služby Azure Storage pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Pokud Azure Automation a Azure Monitor začínáte, je důležité, abyste porozuměli následujícím podrobnostem o konfiguraci. Můžou vám zabránit chybám při pokusu o vytvoření, konfiguraci a používání pracovního prostoru Log Analytics propojeného s vaším novým účtem Automation. 

* Přečtěte si [Další podrobnosti](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) , abyste plně pochopili možnosti konfigurace pracovního prostoru, jako je režim řízení přístupu, cenová úroveň, uchování a úroveň rezervace kapacity.

* Zkontrolujte [mapování pracovních prostorů](how-to/region-mappings.md) a určete podporované oblasti v inline nebo v souboru parametrů. Pro propojení Log Analyticsho pracovního prostoru a účtu Automation v rámci předplatného se podporují jenom některé oblasti.

* Pokud jste s protokoly Azure Monitor a ještě nemáte nasazený pracovní prostor, měli byste si projít pokyny k [návrhu pracovního prostoru](../azure-monitor/platform/design-logs-deployment.md). Pomůže vám se dozvědět o řízení přístupu a porozumět strategiích implementace návrhu, které doporučujeme pro vaši organizaci.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
            }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
        "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
       ]
      }
     ]
    }
    ```

2. Upravte šablonu tak, aby splňovala vaše požadavky. Místo předání parametrů jako vložené hodnoty zvažte vytvoření [souboru parametrů správce prostředků](../azure-resource-manager/templates/parameter-files.md) .

3. Uložte tento soubor jako deployAzAutomationAccttemplate. JSON do místní složky.

4. Jste připraveni k nasazení této šablony. Můžete použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI. Po zobrazení výzvy k zadání pracovního prostoru a názvu účtu Automation zadejte globálně jedinečný název ve všech vašich předplatných Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Dokončení nasazení může trvat několik minut. V takovém případě se zobrazí zpráva podobná následující, která obsahuje výsledek.

    ![Příklad výsledku po dokončení nasazení](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření runbooku v PowerShellu](automation-first-runbook-textual-powershell.md)
* [Vytvoření Runbooku pracovního postupu PowerShellu](automation-first-runbook-textual.md)
* [Vytvoření runbooku v Pythonu](automation-first-runbook-textual-python2.md)