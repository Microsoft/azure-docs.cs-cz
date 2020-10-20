---
title: Povolit Update Management pomocí šablony Azure Resource Manager | Microsoft Docs
description: V tomto článku se dozvíte, jak používat šablonu Azure Resource Manager k povolení Update Management.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/18/2020
ms.openlocfilehash: eac7999db61bd883dd1148e8b866b811a4f3c851
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222383"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Povolení Update Managementu s využitím šablony Azure Resource Manageru

K povolení funkce Update Management Azure Automation ve vaší skupině prostředků můžete použít [šablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) . Tento článek poskytuje ukázkovou šablonu, která automatizuje následující:

* Automatizuje vytváření Azure Monitorho pracovního prostoru Log Analytics.
* Automatizuje vytváření Azure Automation účtu.
* Propojí účet Automation s pracovním prostorem Log Analytics.
* Přidá do účtu ukázkové Runbooky pro automatizaci.
* Povolí funkci Update Management.

Šablona neautomatizuje povolení Update Management na jednom nebo několika virtuálních počítačích Azure nebo mimo Azure.

Pokud už máte pracovní prostor Log Analytics a účet Automation je nasazený v podporované oblasti v rámci vašeho předplatného, nejsou propojené. Pomocí této šablony se úspěšně vytvoří odkaz a nasadí se Update Management.

>[!NOTE]
>Vytvoření účtu Automation spustit jako není podporované, když používáte šablonu ARM. Pokud chcete vytvořit účet Spustit jako ručně z portálu nebo pomocí PowerShellu, přečtěte si téma [Správa účtů spustit jako](../manage-runas-account.md).

Po dokončení těchto kroků je potřeba [nakonfigurovat nastavení diagnostiky](../automation-manage-send-joblogs-log-analytics.md) pro váš účet Automation, aby se odesílaly datové proudy úloh Runbooku do propojeného Log Analytics pracovního prostoru.

## <a name="api-versions"></a>Verze rozhraní API

Následující tabulka uvádí verzi rozhraní API pro prostředky použité v tomto příkladu.

| Prostředek | Typ prostředku | Verze rozhraní API |
|:---|:---|:---|
| [Pracovní prostor](/azure/templates/microsoft.operationalinsights/workspaces) | pracovní prostory | 2020-03-01 – Preview |
| [Účet služby Automation](/azure/templates/microsoft.automation/automationaccounts) | automatizace | 2020-01-13 – Preview |
| [Propojené služby v pracovním prostoru](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | pracovní prostory | 2020-03-01 – Preview |
| [Řešení](/azure/templates/microsoft.operationsmanagement/solutions) | Řešení | 2015-11-01 – Preview |

## <a name="before-using-the-template"></a>Před použitím šablony

Šablona JSON je nakonfigurovaná tak, aby vás vyzvala k těmto akcím:

* Název pracovního prostoru
* Oblast, ve které se má pracovní prostor vytvořit.
* Název účtu Automation.
* Oblast, ve které se má účet služby Automation vytvořit.

Následující parametry v šabloně jsou nastaveny s výchozí hodnotou pro Log Analytics pracovní prostor:

* Výchozí hodnota *SKU* je cenová úroveň na GB vydaná v cenovém modelu z dubna 2018.
* *Doba uchovávání dat* je nastavena na 30 dní.

>[!WARNING]
>Pokud chcete vytvořit nebo nakonfigurovat pracovní prostor Log Analytics v předplatném, které se zavedlo do cenového modelu z dubna 2018, bude jediná platná Log Analytics cenová úroveň *PerGB2018*.
>

Šablona JSON určuje výchozí hodnotu pro ostatní parametry, které by pravděpodobně byly použity jako standardní konfigurace ve vašem prostředí. Šablonu můžete uložit v účtu služby Azure Storage pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

Pokud Azure Automation a Azure Monitor začínáte, je důležité, abyste porozuměli následujícím podrobnostem o konfiguraci. Můžou vám zabránit chybám při pokusu o vytvoření, konfiguraci a používání pracovního prostoru Log Analytics propojeného s vaším novým účtem Automation.

* Přečtěte si [Další podrobnosti](../../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace) , abyste plně pochopili možnosti konfigurace pracovního prostoru, jako je režim řízení přístupu, cenová úroveň, uchování a úroveň rezervace kapacity.

* Zkontrolujte [mapování pracovních prostorů](../how-to/region-mappings.md) a určete podporované oblasti v inline nebo v souboru parametrů. Pro propojení Log Analyticsho pracovního prostoru a účtu Automation v rámci předplatného se podporují jenom některé oblasti.

* Pokud jste s protokoly Azure Monitor a ještě nemáte nasazený pracovní prostor, měli byste si projít pokyny k [návrhu pracovního prostoru](../../azure-monitor/platform/design-logs-deployment.md). Pomůže vám se dozvědět o řízení přístupu a porozumět strategiích implementace návrhu, které doporučujeme pro vaši organizaci.

## <a name="deploy-template"></a>Nasazení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
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
                    "description": "Specifies the location in which to create the Automation account."
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
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
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
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
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
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Upravte šablonu tak, aby splňovala vaše požadavky. Místo předání parametrů jako vložené hodnoty zvažte vytvoření [souboru parametrů správce prostředků](../../azure-resource-manager/templates/parameter-files.md) .

3. Uložte tento soubor do místní složky jako **deployUMSolutiontemplate.jsna**.

4. Jste připraveni k nasazení této šablony. Můžete použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI. Po zobrazení výzvy k zadání pracovního prostoru a názvu účtu Automation zadejte název, který bude globálně jedinečný v rámci všech předplatných Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:

    ![Příklad výsledku po dokončení nasazení](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

2. V Azure Portal otevřete účet Automation, který jste vytvořili.

3. V levém podokně vyberte **Runbooky**. Na stránce sady **Runbook** jsou uvedené tři výukové Runbooky vytvořené s účtem Automation.

    ![Výukové Runbooky vytvořené pomocí účtu Automation](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. V levém podokně vyberte **propojený pracovní prostor**. Na stránce **propojený pracovní prostor** se zobrazuje Log Analytics pracovní prostor, který jste zadali dříve, propojený s vaším účtem Automation.

    ![Účet služby Automation propojený s pracovním prostorem Log Analytics](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. V levém podokně vyberte **Správa aktualizací**. Na stránce **Správa aktualizací** se zobrazí stránka posouzení bez jakýchkoli informací v důsledku pouhého povolení a počítače nejsou konfigurovány pro správu.

    ![Update Management zobrazení posouzení funkcí](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, odstraňte řešení **aktualizace** v pracovním prostoru Log Analytics, odpojte účet Automation s pracovním prostorem a pak odstraňte účet služby Automation a pracovní prostor.

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro vaše virtuální](manage-updates-for-vm.md)počítače.

* Pokud už nechcete používat Update Management a chcete ho odebrat, přečtěte si pokyny v části [odebrání Update Management funkce](remove-feature.md).

* Postup odstranění virtuálních počítačů z Update Management najdete v tématu [Odebrání virtuálních počítačů z Update Management](remove-vms.md).