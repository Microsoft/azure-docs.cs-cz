---
title: Vytvoření pracovního prostoru Analýzy protokolů pomocí Azure PowerShellu| Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics, který umožní řešení pro správu a shromažďování dat z cloudových a místních prostředí pomocí Azure PowerShellu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: a2765aaf36aa5f7e541e0ee7fb3178246d2cca5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659894"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>Vytvoření pracovního prostoru Analýzy protokolů s Azure PowerShellem

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. Tento rychlý start ukazuje, jak použít modul Azure PowerShell k nasazení pracovního prostoru Log Analytics v Azure Monitoru. Pracovní prostor Log Analytics je jedinečné prostředí pro data protokolu Azure Monitor. Každý pracovní prostor má své vlastní úložiště dat a konfiguraci a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládali data v určitém pracovním prostoru. Pracovní prostor Log Analytics vyžadujete, pokud máte v úmyslu shromažďovat data z následujících zdrojů:

* Prostředky Azure ve vašem předplatném  
* Místní počítače monitorované správcem operací system center  
* Kolekce zařízení z nástroje Configuration Manager  
* Diagnostika nebo protokolování dat z úložiště Azure  
 
Další zdroje, jako jsou virtuální počítače Azure a virtuální počítače s Windows nebo Linux ve vašem prostředí, najdete v následujících tématech:

* [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md)
* [Shromažďování dat z hybridního počítače s Linuxem](../learn/quick-collect-linux-computer.md)
* [Shromažďování dat z hybridního počítače se systémem Windows](quick-collect-windows-computer.md)

Pokud nemáte předplatné Azure, vytvořte [si bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Prostředí PowerShell místně, tento kurz vyžaduje modul Azure PowerShell Az. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Vytvořte pracovní prostor s [new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Následující příklad vytvoří pracovní prostor v umístění *eastus* pomocí šablony Správce prostředků z místního počítače. Šablona JSON je nakonfigurována tak, aby vás pouze vyzvala k zadání názvu pracovního prostoru a určuje výchozí hodnotu pro ostatní parametry, které by pravděpodobně byly použity jako standardní konfigurace ve vašem prostředí. 

Informace o podporovaných oblastech najdete [v tématu oblasti Log Analytics je k dispozici v](https://azure.microsoft.com/regions/services/) a vyhledejte Azure Monitor z pole Hledat **produkt.** 

Následující parametry nastavují výchozí hodnotu:

* umístění - výchozí nastavení na východní USA
* sku – výchozí hodnota nové cenové úrovně Per-GB vydané v cenovém modelu z dubna 2018

>[!WARNING]
>Pokud vytváříte nebo konfigurujete pracovní prostor Log Analytics v předplatném, které se přihlásilo k novému cenovému modelu z dubna 2018, je jedinou platnou cenovou úrovní Log Analytics **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Vytvoření šablony nasazení

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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
            "apiVersion": "2015-11-01-preview",
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

2. Upravte šablonu tak, aby vyhovovala vašim požadavkům. Projděte si odkaz na [šablonu microsoft.operationalinsights/pracovní prostory,](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) abyste zjistili, jaké vlastnosti a hodnoty jsou podporovány. 
3. Uložte tento soubor jako **deploylaworkspacetemplate.json** do místní složky.   
4. Jste připraveni k nasazení této šablony. Ze složky obsahující šablonu použijte následující příkazy. Až se zobrazí výzva k zadání názvu pracovního prostoru, zadejte název, který je globálně jedinečný ve všech předplatných Azure.

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:

![Příklad výsledku po dokončení nasazení](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>Další kroky
Teď, když máte k dispozici pracovní prostor, můžete nakonfigurovat kolekci monitorování telemetrie, spustit vyhledávání protokolů k analýze těchto dat a přidat řešení pro správu poskytovat další data a analytické přehledy.  

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo Azure storage, přečtěte si najdete [v tématu Shromažďování protokolů služeb Azure a metrik pro použití v Azure Monitoru](../platform/collect-azure-metrics-logs.md).  
* Přidejte [nástroj Operations Manager system center jako zdroj dat](../platform/om-agents.md) pro shromažďování dat od agentů, kteří hlásí vaši skupinu pro správu nástroje Operations Manager, a uložte je do pracovního prostoru Analýzy protokolů.  
* Připojte [nástroj Configuration Manager](../platform/collect-sccm.md) k importu počítačů, které jsou členy kolekcí v hierarchii.  
* Projděte si [dostupná řešení monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.
