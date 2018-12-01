---
title: Vytvoření pracovního prostoru Log Analytics pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics umožňuje správu řešení a shromažďování dat z vašich cloudových a místních prostředích pomocí Azure Powershellu.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: bfff98a282622cfa5e847910530c2db4ddf9e2e0
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722781"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Vytvořit pracovní prostor Log Analytics pomocí Azure Powershellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak použít modul Azure PowerShell k nasazení pracovního prostoru Log Analytics v Azure, což je jedinečné prostředí s vlastním úložištěm dat, zdroje dat a řešení.  Podle kroků popsaných v tomto článku jsou požadovány, pokud máte v úmyslu na shromažďování dat z těchto zdrojů:

* Prostředky ve vašem předplatném Azure  
* Místní služba System Center Operations Manager monitorovat počítače  
* Kolekce zařízení ze System Center Configuration Manager  
* Diagnostika nebo protokolování dat z úložiště Azure  
 
U jiných zdrojů, jako jsou virtuální počítače Azure a Windows nebo virtuální počítače s Linuxem ve vašem prostředí naleznete v následujících tématech:

* [Shromažďování dat z virtuálních počítačů Azure](log-analytics-quick-collect-azurevm.md)
* [Shromažďování dat z počítače s Linuxem hybridní](log-analytics-quick-collect-linux-computer.md)
* [Shromažďování dat z počítače Windows hybridní](log-analytics-quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte v tomto kurzu použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Vytvoření pracovního prostoru s [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Následující příklad vytvoří pracovní prostor s názvem *TestWorkspace* ve skupině prostředků *Lab* v *eastus* pomocí šablony Resource Manageru z vašeho místního umístění počítač. Šablona JSON je nakonfigurován pouze s výzvou k zadání názvu pracovního prostoru a určí výchozí hodnotu pro parametry, které se pravděpodobně použije jako standardní konfigurace ve vašem prostředí. 

Následující parametry nastavení výchozí hodnoty:

* umístění – výchozí hodnota je USA – východ
* Skladová položka – výchozí hodnota je novou cenovou úroveň Per GB vydáno v dubnu 2018 cenový model

>[!WARNING]
>Pokud vytváříte nebo konfigurace pracovního prostoru Log Analytics v rámci předplatného, který je zapojen do nové platný od dubna 2018 cenový model, platné pouze v Log Analytics cenová úroveň je **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Vytvoření a nasazení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
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
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Upravte šablonu podle svých požadavků.  Kontrola [Microsoft.OperationalInsights/workspaces šablony](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referenční dokumentace se dozvíte, jaké vlastnosti a hodnoty jsou podporovány. 
3. Uložte soubor jako **deploylaworkspacetemplate.json** do místní složky.   
4. Jste připraveni k nasazení této šablony. Ze složky obsahující šablonu použijte následující příkazy:

    ```powershell
        New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, který obsahuje výsledek:

![Příklad výsledků po dokončení nasazení](media/log-analytics-quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Další postup
Teď, když máte k dispozici pracovní prostor, konfigurace shromažďování dat monitorování telemetrických dat a spustit prohledávání protokolů pro analýzu těchto dat přidat řešení správy, které poskytují další data a analytické přehledy.  

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí diagnostiky Azure nebo do úložiště Azure, najdete v článku [metriky pro použití v Log Analytics a Azure shromažďovat protokoly služby](../azure-monitor/platform/collect-azure-metrics-logs.md).  
* Přidat [System Center Operations Manager jako zdroj dat](log-analytics-om-agents.md) shromažďovat data z agenty posílající sestavy skupině pro správu nástroje Operations Manager a uloží je v pracovním prostoru Log Analytics.  
* Připojit [nástroje Configuration Manager](log-analytics-sccm.md) importovat počítače, které jsou členy kolekce v hierarchii.  
* Zkontrolujte [řešení pro správu](../azure-monitor/insights/solutions.md) k dispozici a jak přidat nebo odebrat některé řešení z pracovního prostoru.
