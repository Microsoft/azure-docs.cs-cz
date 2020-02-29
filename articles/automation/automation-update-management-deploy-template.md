---
title: Použití šablon Azure Resource Manager k zprovoznění Update Management | Microsoft Docs
description: K připojení Update Management řešení Azure Automation můžete použít šablonu Azure Resource Manager.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925797"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Připojení Update Management řešení pomocí šablony Azure Resource Manager

K povolení řešení Azure Automation Update Management ve vaší skupině prostředků můžete použít [šablony Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) . Tento článek poskytuje ukázkovou šablonu, která automatizuje následující:

* Vytváření pracovního prostoru Azure Monitor Log Analytics
* Vytvoření účtu Azure Automation.
* Propojí účet Automation s pracovním prostorem Log Analytics, pokud ještě není propojený.
* Připojení řešení Azure Automation Update Management

Šablona neautomatizuje připojování jednoho nebo více virtuálních počítačů Azure nebo mimo Azure.

Pokud už máte pracovní prostor Log Analytics a účet Automation je nasazený v podporované oblasti v rámci vašeho předplatného, nejsou propojené a pracovní prostor ještě nemá nasazené řešení Update Management a pomocí této šablony se úspěšně vytvořila. odkaz a nasadí řešení Update Management. 

## <a name="api-versions"></a>Verze rozhraní API

Následující tabulka uvádí verzi rozhraní API pro prostředky použité v tomto příkladu.

| Prostředek | Typ prostředku | Verze rozhraní API |
|:---|:---|:---|
| Pracovní prostor | pracovní prostory | 2017-03-15 – Preview |
| Účet Automation | automation | 2015-10-31 | 
| Řešení | Řešení | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Před použitím šablony

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek Azure PowerShell AZ Module. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure. Při použití Azure PowerShell nasazení používá [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.1.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Pomocí Azure CLI toto nasazení používá příkaz [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Šablona JSON je nakonfigurovaná tak, aby vás vyzvala k těmto akcím:

* Název pracovního prostoru
* Oblast, ve které se má vytvořit pracovní prostor
* Název účtu Automation
* Oblast, ve které se má účet vytvořit

Šablona JSON určuje výchozí hodnotu pro ostatní parametry, které by pravděpodobně byly použity jako standardní konfigurace ve vašem prostředí. Šablonu můžete uložit v účtu služby Azure Storage pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Následující parametry v šabloně jsou nastaveny s výchozí hodnotou pro Log Analytics pracovní prostor:

* Skladová položka – výchozí hodnota je novou cenovou úroveň Per GB vydáno v dubnu 2018 cenový model
* uchovávání dat – výchozí hodnota je 30 dní.

>[!WARNING]
>Pokud vytváříte nebo konfigurujete pracovní prostor Log Analytics v předplatném, které se přihlásilo k novému cenovému modelu od dubna 2018, bude jediná platná Log Analytics cenová úroveň **PerGB2018**.
>

>[!NOTE]
>Před použitím této šablony si přečtěte [Další podrobnosti](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) , abyste plně pochopili možnosti konfigurace pracovního prostoru, jako je režim řízení přístupu, cenová úroveň, uchování a úroveň rezervace kapacity. Pokud Azure Monitor protokoly a ještě nemáte nasazený pracovní prostor, měli byste si projít průvodce [návrhem pracovního prostoru](../azure-monitor/platform/design-logs-deployment.md) , kde najdete další informace o řízení přístupu a porozumění strategiích implementace návrhu, které doporučujeme pro vaši organizaci.

## <a name="deploy-template"></a>Nasazení šablony

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
        "pricingTier": {
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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
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
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
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
    ```

2. Upravte šablonu podle svých požadavků.

3. Uložte tento soubor jako deployUMSolutiontemplate. JSON do místní složky.

4. Jste připraveni k nasazení této šablony. Můžete použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI. Po zobrazení výzvy k zadání pracovního prostoru a názvu účtu Automation zadejte název, který bude globálně jedinečný v rámci všech předplatných Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, který obsahuje výsledek:

    ![Příklad výsledků po dokončení nasazení](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Další kroky

Teď, když máte nasazené řešení Update Management, můžete povolit virtuální počítače pro správu, zkontrolovat posouzení aktualizací a nasadit aktualizace, aby byly v souladu s předpisy.

- Z [účtu Azure Automation](automation-onboard-solutions-from-automation-account.md) pro jeden nebo více počítačů Azure a ručně pro počítače mimo Azure.

- Pro jeden virtuální počítač Azure ze stránky virtuálního počítače v Azure Portal. Tento scénář je k dispozici pro virtuální počítače se systémy [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) a [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

- Pro [více virtuálních počítačů Azure](manage-update-multi.md) je můžete vybrat ze stránky **virtuální počítače** v Azure Portal. 