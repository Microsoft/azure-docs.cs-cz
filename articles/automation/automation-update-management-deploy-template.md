---
title: Použití šablon Azure Resource Manager k založení správy aktualizací | Dokumenty společnosti Microsoft
description: K nasazení řešení Azure Automation Update Management můžete použít šablonu Azure Resource Manageru.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925797"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Řešení pro správu aktualizací pomocí šablony Azure Resource Manager

Pomocí [šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) můžete povolit řešení Azure Automation Update Management ve vaší skupině prostředků. Tento článek obsahuje ukázkovou šablonu, která automatizuje následující:

* Vytvoření pracovního prostoru Azure Monitor Log Analytics.
* Vytvoření účtu Azure Automation.
* Propojí účet Automation s pracovním prostorem Log Analytics, pokud již není propojen.
* Na palubě řešení Azure Automation Update Management

Šablona neautomatizuje připojení jednoho nebo více virtuálních počítačů Azure nebo než Azure.

Pokud už máte v předplatném nasazený pracovní prostor Analýzy protokolů a automatizační účet v podporované oblasti, nejsou propojené a v pracovním prostoru ještě není nasazené řešení správy aktualizací, pomocí této šablony se úspěšně vytvoří propojení a nasadí řešení správy aktualizací. 

## <a name="api-versions"></a>Verze rozhraní API

V následující tabulce je uvedena verze rozhraní API pro prostředky použité v tomto příkladu.

| Prostředek | Typ prostředku | Verze rozhraní API |
|:---|:---|:---|
| Pracovní prostor | pracovní prostory | 2017-03-15-náhled |
| Účet Automation | automation | 2015-10-31 | 
| Řešení | Řešení | 2015-11-01-náhled |

## <a name="before-using-the-template"></a>Před použitím šablony

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento článek vyžaduje modul Azure PowerShell Az. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure. S Azure PowerShell, nasazení používá [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.1.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Pomocí příkazového [příkazového příkazového příkazu](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)Azure používá toto nasazení vytvoření nasazení skupiny AZ . 

Šablona JSON je nakonfigurována tak, aby vás vyzvala k zadání:

* Název pracovního prostoru
* Oblast, ve které chcete vytvořit pracovní prostor
* Název účtu automatizace
* Oblast, ve které chcete vytvořit účet v

Šablona JSON určuje výchozí hodnotu pro ostatní parametry, které by pravděpodobně byly použity jako standardní konfigurace ve vašem prostředí. Šablonu můžete uložit do účtu úložiště Azure pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v [tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Následující parametry v šabloně jsou nastaveny s výchozí hodnotou pro pracovní prostor Log Analytics:

* sku – výchozí hodnota nové cenové úrovně Per-GB vydané v cenovém modelu z dubna 2018
* uchovávání dat - výchozí hodnota na třicet dní

>[!WARNING]
>Pokud vytváříte nebo konfigurujete pracovní prostor Log Analytics v předplatném, které se přihlásilo k novému cenovému modelu z dubna 2018, je jedinou platnou cenovou úrovní Log Analytics **PerGB2018**.
>

>[!NOTE]
>Před použitím této šablony zkontrolujte [další podrobnosti,](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) abyste plně pochopili možnosti konfigurace pracovního prostoru, jako je režim řízení přístupu, cenová úroveň, uchovávání informací a úroveň rezervace kapacity. Pokud jste novým protokoly Azure Monitor a ještě jste nenasadili pracovní prostor, měli byste si projít pokyny k [návrhu pracovního prostoru,](../azure-monitor/platform/design-logs-deployment.md) abyste se dozvěděli o řízení přístupu a pochopili strategie implementace návrhu, které doporučujeme pro vaši organizaci.

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

2. Upravte šablonu tak, aby vyhovovala vašim požadavkům.

3. Uložte tento soubor jako deployUMSolutiontemplate.json do místní složky.

4. Jste připraveni k nasazení této šablony. Můžete použít buď PowerShell nebo Azure CLI. Až se zobrazí výzva k zadání názvu účtu pracovního prostoru a automatizace, zadejte název, který je globálně jedinečný ve všech předplatných Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:

    ![Příklad výsledku po dokončení nasazení](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Další kroky

Teď, když máte nasazené řešení správy aktualizací, můžete povolit virtuální ms pro správu, zkontrolovat hodnocení aktualizací a nasadit aktualizace, aby byly v souladu s předpisy.

- Z vašeho [účtu Azure Automation](automation-onboard-solutions-from-automation-account.md) pro jeden nebo více počítačů Azure a ručně pro počítače než Azure.

- Pro jeden virtuální počítač Azure ze stránky virtuálního počítače na webu Azure Portal. Tento scénář je k dispozici pro [virtuální počítače s Linuxem](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) a [Windows.](../virtual-machines/windows/tutorial-config-management.md#enable-update-management)

- Pro [více virtuálních počítačů Azure](manage-update-multi.md) jejich výběrem ze stránky Virtuální **počítače** na webu Azure Portal. 