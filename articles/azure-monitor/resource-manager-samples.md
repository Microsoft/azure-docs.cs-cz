---
title: Ukázky šablon Správce prostředků pro Azure Monitor
description: Nasazení a konfigurace funkcí Azure Monitor pomocí šablon Správce prostředků
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 9218886ded7827d4b7a1e2413f1470ee5cd1563d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733954"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Ukázky šablon Správce prostředků pro Azure Monitor

Azure Monitor se dá nasadit a nakonfigurovat ve velkém měřítku pomocí [Azure Resource Manager šablony](../azure-resource-manager/templates/template-syntax.md). Následující články poskytují ukázkové šablony pro různé funkce Azure Monitor. Tyto ukázky lze upravit pro vaše konkrétní požadavky a nasadit pomocí libovolné standardní metody nasazení Správce prostředků šablon. 

## <a name="deploying-the-sample-templates"></a>Nasazení ukázkových šablon
Základní kroky pro použití ukázek:

1. Zkopírujte šablonu a uložte ji jako soubor JSON.
2. Upravte parametry vašeho prostředí a uložte ho jako soubor JSON.
4. Nasaďte šablonu pomocí [libovolné metody nasazení pro správce prostředků šablon](../azure-resource-manager/templates/deploy-powershell.md). 

Pomocí následujících příkazů můžete například nasadit šablonu a soubor parametrů do skupiny prostředků pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Seznam vzorových šablon

- [Agenti](agents/resource-manager-agent.md) – nasaďte a nakonfigurujte agenta Log Analytics a diagnostické rozšíření.
- Výstrahy
  - [Pravidla upozornění protokolů](alerts/resource-manager-alerts-log.md) – výstrahy z log dotazů a protokolu aktivit Azure
  - [Pravidla upozornění metrik](alerts/resource-manager-alerts-metric.md) – výstrahy z metriky s využitím různých druhů logiky.
- [Application Insights](app/resource-manager-app-resource.md)
- [Nastavení diagnostiky](essentials/resource-manager-diagnostic-settings.md) – umožňuje vytvořit nastavení diagnostiky pro přeposílání protokolů a metrik z různých typů prostředků.
- [Dotazy protokolu](logs/resource-manager-log-queries.md) – vytvoření uložených dotazů protokolu v pracovním prostoru Log Analytics.
- [Log Analytics pracovní prostor](logs/resource-manager-workspace.md) – vytvořit pracovní prostor Log Analytics a nakonfigurovat kolekci různých zdrojů dat od agenta Log Analytics.
- [Sešity](visualize/resource-manager-workbooks.md) – Vytvářejte sešity.
- [Container Insights](containers/resource-manager-container-insights.md) – zprovoznění clusterů do kontejneru Insights.
- [Azure monitor pro virtuální počítače](vm/resource-manager-vminsights.md) – zprovoznění virtuálních počítačů k Azure monitor pro virtuální počítače.



## <a name="next-steps"></a>Další kroky

- Další informace o [šablonách správce prostředků](../azure-resource-manager/templates/overview.md)