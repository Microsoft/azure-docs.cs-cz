---
title: Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI | Microsoft Docs
description: Naučte se, jak vytvořit pracovní prostor Log Analytics, který umožňuje řešení pro správu a shromažďování dat z vašeho cloudového a místního prostředí pomocí Azure CLI.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 8e1fc816e32e563161e1604bdcd7a7006353e4ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047379"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI 2,0

Azure CLI 2.0 slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure CLI 2,0 nasadit v Azure Monitor Log Analytics pracovní prostor. Log Analytics pracovní prostor je jedinečné prostředí pro Azure Monitor data protokolu. Každý pracovní prostor má své vlastní úložiště a konfiguraci dat a zdroje dat a řešení jsou nakonfigurovány tak, aby ukládaly data do konkrétního pracovního prostoru. Pokud máte v úmyslu shromažďovat data z následujících zdrojů, potřebujete Log Analytics pracovní prostor:

* Prostředky Azure ve vašem předplatném  
* Místní počítače monitorované nástrojem System Center Operations Manager  
* Kolekce zařízení z Configuration Manager  
* Diagnostika nebo protokolování dat z úložiště Azure  

Další zdroje, jako jsou virtuální počítače Azure a virtuální počítače s Windows nebo Linux ve vašem prostředí, najdete v následujících tématech:

* [Shromažďování dat z virtuálních počítačů Azure](../vm/quick-collect-azurevm.md)
* [Shromažďování dat z hybridního počítače se systémem Linux](../vm/quick-collect-linux-computer.md)
* [Shromažďování dat z hybridního počítače s Windows](../vm/quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.30 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Vytvořte pracovní prostor pomocí [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create). Následující příklad vytvoří pracovní prostor v umístění *eastus* pomocí šablony Správce prostředků z místního počítače. Šablona JSON je nakonfigurovaná tak, aby se zobrazila výzva k zadání názvu pracovního prostoru, a určuje výchozí hodnotu pro ostatní parametry, které by se ve vašem prostředí pravděpodobně používaly jako standardní konfigurace. Případně můžete šablonu uložit v účtu služby Azure Storage pro sdílený přístup ve vaší organizaci. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) .

Informace o podporovaných oblastech najdete v tématu [oblasti Log Analytics jsou k dispozici v](https://azure.microsoft.com/regions/services/) a vyhledejte Azure monitor z **hledání pole produkt** .

Výchozí hodnotu nastavíte pomocí následujících parametrů:

* umístění – výchozí nastavení Východní USA
* SKU – výchozí hodnota pro novou cenovou úroveň za GB vydanou v cenovém modelu z dubna 2018

>[!WARNING]
>Pokud vytváříte nebo konfigurujete pracovní prostor Log Analytics v předplatném, které se přihlásilo k novému cenovému modelu od dubna 2018, bude jediná platná Log Analytics cenová úroveň **PerGB2018**.
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

2. Upravte šablonu tak, aby splňovala vaše požadavky. Informace o podporovaných vlastnostech a hodnotách najdete v referenčních informacích k [šabloně Microsoft. OperationalInsights/Workspaces](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) .
3. Uložte tento soubor jako **deploylaworkspacetemplate.js** do místní složky.   
4. Jste připraveni k nasazení této šablony. Použijte následující příkazy ze složky obsahující šablonu. Po zobrazení výzvy k zadání názvu pracovního prostoru zadejte název, který je globálně jedinečný v rámci všech předplatných Azure.

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která obsahuje výsledek:

![Příklad výsledku po dokončení nasazení](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Poradce při potížích
Když vytvoříte pracovní prostor, který byl odstraněn za posledních 14 dní a ve [stavu "obnovitelného odstranění](../logs/delete-workspace.md#soft-delete-behavior)", může mít operace v závislosti na konfiguraci pracovního prostoru jiný výsledek:
1. Pokud zadáte stejný název pracovního prostoru, skupinu prostředků, předplatné a oblast jako v odstraněném pracovním prostoru, váš pracovní prostor se obnoví včetně dat, konfigurace a připojených agentů.
2. Pokud použijete stejný název pracovního prostoru, ale v jiné skupině prostředků, předplatném nebo oblasti dojde k chybě, zobrazí se chyba název pracovního prostoru *"pracovní prostor" Name "není jedinečný nebo je v* *konfliktu*. Chcete-li přepsat obnovitelné odstranění a trvale odstranit pracovní prostor a vytvořit nový pracovní prostor se stejným názvem, postupujte podle následujících kroků a obnovte nejprve pracovní prostor a proveďte trvalé odstranění:
   * [Obnovení](../logs/delete-workspace.md#recover-workspace) pracovního prostoru
   * [Trvalé odstranění](../logs/delete-workspace.md#permanent-workspace-delete) pracovního prostoru
   * Vytvoří nový pracovní prostor s použitím stejného názvu pracovního prostoru.

## <a name="next-steps"></a>Další kroky
Teď, když máte dostupný pracovní prostor, můžete nakonfigurovat shromažďování telemetrie monitorování, spustit prohledávání protokolů a tato data analyzovat a přidat řešení pro správu, která poskytují další data a analytické přehledy.  

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí Azure Diagnostics nebo úložiště Azure, přečtěte si téma [shromáždění protokolů služby Azure a metrik pro použití v Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace).  
* Přidejte [System Center Operations Manager jako zdroj dat](../agents/om-agents.md) , abyste mohli shromažďovat data z agentů, kteří hlásí skupinu pro správu Operations Manager, a uložit je do pracovního prostoru Log Analytics.  
* Připojte [Configuration Manager](../logs/collect-sccm.md) k importu počítačů, které jsou členy kolekcí v hierarchii.  
* Projděte si dostupná [řešení pro monitorování](../insights/solutions.md) a postup přidání nebo odebrání řešení z pracovního prostoru.

