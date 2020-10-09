---
title: Ukázky šablon Správce prostředků pro Azure Monitor
description: Nasazení a konfigurace funkcí Azure Monitor pomocí šablon Správce prostředků
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: ab869fc8577d4d1934be96404ded5a2051237bbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922724"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Ukázky šablon Správce prostředků pro Azure Monitor

Azure Monitor se dá nasadit a nakonfigurovat ve velkém měřítku pomocí [Azure Resource Manager šablony](../../azure-resource-manager/templates/template-syntax.md). Následující články poskytují ukázkové šablony pro různé funkce Azure Monitor. Tyto ukázky lze upravit pro vaše konkrétní požadavky a nasadit pomocí libovolné standardní metody nasazení Správce prostředků šablon. 

## <a name="deploying-the-sample-templates"></a>Nasazení ukázkových šablon
Základní kroky pro použití ukázek:

1. Zkopírujte šablonu a uložte ji jako soubor JSON.
2. Upravte parametry vašeho prostředí a uložte ho jako soubor JSON.
4. Nasaďte šablonu pomocí [libovolné metody nasazení pro správce prostředků šablon](../../azure-resource-manager/templates/deploy-powershell.md). 

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

- [Agenti](resource-manager-agent.md) – nasaďte a nakonfigurujte agenta Log Analytics a diagnostické rozšíření.
- Výstrahy
  - [Pravidla upozornění protokolů](resource-manager-alerts-log.md) – výstrahy z log dotazů a protokolu aktivit Azure
  - [Pravidla upozornění metrik](resource-manager-alerts-metric.md) – výstrahy z metriky s využitím různých druhů logiky.
- [Application Insights](resource-manager-app-resource.md)
- [Nastavení diagnostiky](resource-manager-diagnostic-settings.md) – umožňuje vytvořit nastavení diagnostiky pro přeposílání protokolů a metrik z různých typů prostředků.
- [Dotazy protokolu](resource-manager-log-queries.md) – vytvoření uložených dotazů protokolu v pracovním prostoru Log Analytics.
- [Log Analytics pracovní prostor](resource-manager-workspace.md) – vytvořit pracovní prostor Log Analytics a nakonfigurovat kolekci různých zdrojů dat od agenta Log Analytics.
- [Sešity](resource-manager-workbooks.md) – Vytvářejte sešity.
- [Azure monitor pro kontejnery](resource-manager-container-insights.md) – zprovoznění Azure monitor clusterů pro kontejnery.
- [Azure monitor pro virtuální počítače](resource-manager-vminsights.md) – zprovoznění virtuálních počítačů k Azure monitor pro virtuální počítače.



## <a name="next-steps"></a>Další kroky

- Další informace o [šablonách správce prostředků](../../azure-resource-manager/templates/overview.md)