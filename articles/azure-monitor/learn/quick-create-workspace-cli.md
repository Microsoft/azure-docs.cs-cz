---
title: Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI | Microsoft Docs
description: Naučte se, jak vytvořit pracovní prostor Log Analytics, který umožňuje řešení pro správu a shromažďování dat z vašeho cloudového a místního prostředí pomocí Azure CLI.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.openlocfilehash: 18bfc99ded6e3e9171fbb20fbf329700817829de
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895002"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI 2,0

Azure CLI 2.0 slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure CLI 2,0 nasadit v Azure Monitor Log Analytics pracovní prostor. Log Analytics pracovní prostor je jedinečné prostředí pro Azure Monitor data protokolu. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru. Pokud máte v úmyslu shromažďovat data z následujících zdrojů, potřebujete Log Analytics pracovní prostor:

* Prostředky Azure ve vašem předplatném  
* Místní počítače monitorované nástrojem System Center Operations Manager  
* Kolekce zařízení z System Center Configuration Manager  
* Diagnostika nebo protokolování dat z úložiště Azure  
 
Další zdroje, jako jsou virtuální počítače Azure a virtuální počítače s Windows nebo Linux ve vašem prostředí, najdete v následujících tématech:

* [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md)
* [Shromažďování dat z hybridního počítače se systémem Linux](../learn/quick-collect-linux-computer.md)
* [Shromažďování dat z hybridního počítače s Windows](quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Vytvořte pracovní prostor pomocí [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Následující příklad vytvoří pracovní prostor v umístění *eastus* pomocí šablony Správce prostředků z místního počítače. Šablona JSON je nakonfigurovaná tak, aby se zobrazila výzva k zadání názvu pracovního prostoru, a určuje výchozí hodnotu pro ostatní parametry, které by se ve vašem prostředí pravděpodobně používaly jako standardní konfigurace. Případně můžete šablonu uložit v účtu služby Azure Storage pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) .

Informace o podporovaných oblastech najdete v tématu [oblasti Log Analytics jsou k dispozici v](https://azure.microsoft.com/regions/services/) a vyhledejte Azure monitor z **hledání pole produkt** . 

Výchozí hodnotu nastavíte pomocí následujících parametrů:

* umístění – výchozí nastavení Východní USA
* SKU – výchozí hodnota pro novou cenovou úroveň za GB vydanou v cenovém modelu z dubna 2018

>[!WARNING]
>Pokud vytváříte nebo konfigurujete pracovní prostor Log Analytics v předplatném, které se přihlásilo k novému cenovému modelu od dubna 2018, bude jediná platná Log Analytics cenová úroveň **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Vytvoření a nasazení šablony

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

2. Upravte šablonu tak, aby splňovala vaše požadavky. Informace o podporovaných vlastnostech a hodnotách najdete v referenčních informacích k [šabloně Microsoft. OperationalInsights/Workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) . 
3. Uložte tento soubor jako **deploylaworkspacetemplate. JSON** do místní složky.   
4. Jste připraveni k nasazení této šablony. Použijte následující příkazy ze složky obsahující šablonu. Po zobrazení výzvy k zadání názvu pracovního prostoru zadejte název, který je globálně jedinečný v rámci všech předplatných Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:

![Příklad výsledku po dokončení nasazení](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Další kroky
Teď, když máte dostupný pracovní prostor, můžete nakonfigurovat shromažďování telemetrie monitorování, spustit prohledávání protokolů a tato data analyzovat a přidat řešení pro správu, která poskytují další data a analytické přehledy.  

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo úložiště Azure, přečtěte si téma [shromáždění protokolů služby Azure a metrik pro použití v Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Přidejte [System Center Operations Manager jako zdroj dat](../platform/om-agents.md) , abyste mohli shromažďovat data z agentů, kteří hlásí skupinu pro správu Operations Manager, a uložit je do pracovního prostoru Log Analytics.  
* Připojte [Configuration Manager](../platform/collect-sccm.md) k importu počítačů, které jsou členy kolekcí v hierarchii.  
* Projděte si dostupná [řešení pro monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.
