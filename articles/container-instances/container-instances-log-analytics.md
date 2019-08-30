---
title: Protokolování instance kontejneru s protokoly Azure Monitor
description: Naučte se, jak odesílat protokoly z Azure Container Instances, abyste Azure Monitor protokoly.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: 9b57775040251312c8afbff5983a52ae9d14e6c6
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172497"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Protokolování instance kontejneru s protokoly Azure Monitor

Pracovní prostory Log Analytics poskytují centralizované umístění pro ukládání a dotazování dat protokolu z nejenom prostředků Azure, ale i místních prostředků a prostředků v jiných cloudech. Azure Container Instances obsahují integrovanou podporu pro posílání dat do protokolů Azure Monitor.

Chcete-li odeslat data instance kontejneru do protokolů Azure Monitor, je nutné při vytváření skupiny kontejnerů zadat Log Analytics ID a klíč pracovního prostoru. Následující oddíly popisují vytvoření skupiny kontejnerů s povoleným protokolováním a dotazování protokolů.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli povolit protokolování ve vašich instancích kontejnerů, potřebujete následující:

* [Pracovní prostor služby Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [Rozhraní Azure CLI](/cli/azure/install-azure-cli) (nebo službu [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Získání přihlašovacích údajů služby Log Analytics

Služba Azure Container Instances potřebuje oprávnění k odesílání dat do pracovního prostoru služby Log Analytics. Abyste mohli udělit toto oprávnění a povolit protokolování, musíte při vytváření skupiny kontejnerů zadat ID pracovního prostoru služby Log Analytics a jeden z jeho klíčů (primární nebo sekundární).

Získání ID a primárního klíče pracovního prostoru Log Analytics:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Nastavení**vyberte **Upřesnit nastavení** .
1. Vyberte **Připojené zdroje** > **Servery Windows** (nebo **Linuxové servery** – ID a klíče jsou pro obojí stejné).
1. Poznamenejte si:
   * **ID PRACOVNÍHO PROSTORU**
   * **PRIMÁRNÍ KLÍČ**

## <a name="create-container-group"></a>Vytvoření skupiny kontejnerů

Teď, když máte ID pracovního prostoru Log Analytics a primární klíč, jste připraveni vytvořit skupinu kontejnerů s podporou protokolování.

Následující příklady znázorňují dva způsoby, jak vytvořit skupinu kontejnerů s jedním [][fluentd] mikrokontejnerem: Rozhraní příkazového řádku Azure a Azure CLI se šablonou YAML. Kontejner fluentd ve výchozí konfiguraci produkuje několik řádků výstupu. Tento výstup se odesílá do pracovního prostoru služby Log Analytics, takže se dobře hodí pro ukázku zobrazení a dotazování protokolů.

### <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

Pokud ho chcete nasadit pomocí Azure CLI, zadejte `--log-analytics-workspace` parametry `--log-analytics-workspace-key` a v příkazu [AZ Container Create][az-container-create] . Tyto dvě hodnoty pracovního prostoru nahraďte hodnotami, které jste získali v předchozím kroku (a aktualizujte název skupiny prostředků). Teprve potom spusťte následující příkaz.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Nasazení pomocí YAML

Tuto metodu použijte, pokud dáváte přednost nasazení skupiny kontejnerů pomocí souboru YAML. Následující soubor YAML definuje skupinu kontejnerů s jediným kontejnerem. Zkopírujte tento soubor YAML do nového souboru a potom nahraďte `LOG_ANALYTICS_WORKSPACE_ID` a `LOG_ANALYTICS_WORKSPACE_KEY` hodnotami, které jste získali v předchozím kroku. Soubor uložte pod názvem **deploy-aci.yaml**.

```yaml
apiVersion: 2018-10-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Potom spuštěním následujícího příkazu nasaďte skupinu kontejnerů. Nahraďte `myResourceGroup` skupinou prostředků ve vašem předplatném (nebo nejdřív vytvořte skupinu prostředků s názvem "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Krátce po spuštění příkazu byste měli dostat odpověď z Azure s podrobnostmi o nasazení.

## <a name="view-logs-in-azure-monitor-logs"></a>Zobrazit protokoly v protokolu Azure Monitor

Po nasazení skupiny kontejnerů může trvat několik minut (až 10), než se první položky protokolu objeví na webu Azure Portal. Zobrazení protokolů skupiny kontejnerů:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Obecné**vyberte **protokoly** .  
1. Zadejte následující dotaz:`search *`
1. Vyberte **Spustit** .

Mělo by se zobrazit několik výsledků dotazu `search *`. Pokud nevidíte žádné výsledky, počkejte pár minut a pak znovu spusťte dotaz kliknutím na tlačítko **Spustit** . Ve výchozím nastavení se položky protokolu zobrazují ve formátu **tabulky** . Pak můžete rozbalením řádku zobrazit obsah příslušné položky protokolu.

![Výsledky hledání v protokolu na webu Azure Portal][log-search-01]

## <a name="query-container-logs"></a>Dotazování protokolů kontejnerů

Protokoly Azure Monitor obsahují obsáhlý [dotazovací jazyk][query_lang] pro přijímání informací z potenciálně tisíců výstupů protokolu.

Agent protokolování služby Azure Container Instances odesílá položky do tabulky `ContainerInstanceLog_CL` v pracovním prostoru služby Log Analytics. Základní strukturu dotazu představuje zdrojová tabulka (`ContainerInstanceLog_CL`) následovaná řadou operátorů oddělených znakem svislé čáry (`|`). Zřetězením více operátorů můžete zúžit výsledky a provádět pokročilé funkce.

Pokud se chcete podívat na výsledky ukázkového dotazu, vložte následující dotaz do textového pole dotazu (pod odkazem Zobrazit převaděč starší verze jazyka) a spusťte dotaz tlačítkem **SPUSTIT**. Tento dotaz zobrazí všechny položky protokolu, jejichž pole Message (Zpráva) obsahuje slovo warn (upozornění):

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Podporované jsou i složitější dotazy. Tento dotaz například zobrazí jenom položky protokolu pro skupinu kontejnerů mycontainergroup001 vygenerované během poslední hodiny:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Další postup

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Další informace o dotazování protokolů a konfiguraci výstrah v Azure Monitorch protokolech najdete v tématech:

* [Principy prohledávání protokolů v Azure Monitorch protokolech](../log-analytics/log-analytics-log-search.md)
* [Sjednocená upozornění ve službě Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorování procesoru a paměti kontejneru

Informace o monitorování prostředků procesoru a paměti instance kontejneru najdete v tomto tématu:

* [Monitorování prostředků kontejneru ve službě Azure Container Instances](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create