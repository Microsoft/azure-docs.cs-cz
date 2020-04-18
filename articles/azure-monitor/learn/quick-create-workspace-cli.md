---
title: Vytvoření pracovního prostoru Analýzy protokolů pomocí azure cli | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics, který umožní řešení pro správu a shromažďování dat z cloudových a místních prostředí pomocí azure cli.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 0e91bc9c994a48b335c3ccb7373a9f4f5dc6d1e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605091"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Vytvoření pracovního prostoru Analýzy protokolů pomocí Azure CLI 2.0

Azure CLI 2.0 slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento rychlý start ukazuje, jak pomocí Azure CLI 2.0 nasadit pracovní prostor Analýzy protokolů v Azure Monitoru. Pracovní prostor Log Analytics je jedinečné prostředí pro data protokolu Azure Monitor. Každý pracovní prostor má své vlastní úložiště dat a konfiguraci a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládali data v určitém pracovním prostoru. Pracovní prostor Log Analytics vyžadujete, pokud máte v úmyslu shromažďovat data z následujících zdrojů:

* Prostředky Azure ve vašem předplatném  
* Místní počítače monitorované správcem operací system center  
* Kolekce zařízení z nástroje Configuration Manager  
* Diagnostika nebo protokolování dat z úložiště Azure  

Další zdroje, jako jsou virtuální počítače Azure a virtuální počítače s Windows nebo Linux ve vašem prostředí, najdete v následujících tématech:

* [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md)
* [Shromažďování dat z hybridního počítače s Linuxem](../learn/quick-collect-linux-computer.md)
* [Shromažďování dat z hybridního počítače se systémem Windows](quick-collect-windows-computer.md)

Pokud nemáte předplatné Azure, vytvořte [si bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Vytvořte pracovní prostor s [vytvořením nasazení skupiny az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Následující příklad vytvoří pracovní prostor v umístění *eastus* pomocí šablony Správce prostředků z místního počítače. Šablona JSON je nakonfigurována tak, aby vás pouze vyzvala k zadání názvu pracovního prostoru a určuje výchozí hodnotu pro ostatní parametry, které by pravděpodobně byly použity jako standardní konfigurace ve vašem prostředí. Nebo můžete šablonu uložit do účtu úložiště Azure pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v [tématu Nasazení prostředků pomocí šablon Správce prostředků a azure cli.](../../azure-resource-manager/templates/deploy-cli.md)

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

2. Upravte šablonu tak, aby vyhovovala vašim požadavkům. Projděte si odkaz na [šablonu microsoft.operationalinsights/pracovní prostory,](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) abyste zjistili, jaké vlastnosti a hodnoty jsou podporovány.
3. Uložte tento soubor jako **deploylaworkspacetemplate.json** do místní složky.   
4. Jste připraveni k nasazení této šablony. Ze složky obsahující šablonu použijte následující příkazy. Až se zobrazí výzva k zadání názvu pracovního prostoru, zadejte název, který je globálně jedinečný ve všech předplatných Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:

![Příklad výsledku po dokončení nasazení](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Další kroky
Teď, když máte k dispozici pracovní prostor, můžete nakonfigurovat kolekci monitorování telemetrie, spustit vyhledávání protokolů k analýze těchto dat a přidat řešení pro správu poskytovat další data a analytické přehledy.  

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo Azure storage, přečtěte si najdete [v tématu Shromažďování protokolů služeb Azure a metrik pro použití v Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Přidejte [nástroj Operations Manager system center jako zdroj dat](../platform/om-agents.md) pro shromažďování dat od agentů, kteří hlásí vaši skupinu pro správu nástroje Operations Manager, a uložte je do pracovního prostoru Analýzy protokolů.  
* Připojte [nástroj Configuration Manager](../platform/collect-sccm.md) k importu počítačů, které jsou členy kolekcí v hierarchii.  
* Projděte si [dostupná řešení monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.
