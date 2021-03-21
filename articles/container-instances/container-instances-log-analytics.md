---
title: Shromažďovat & analyzovat protokoly prostředků
description: Přečtěte si, jak odesílat protokoly prostředků a data událostí ze skupin kontejnerů v Azure Container Instances do protokolů Azure Monitor
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: cfdcd1cc8e36a118c4e3c4435eaa002e4d3b1b93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579329"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Protokolování skupiny kontejnerů a instance s protokoly Azure Monitor

Log Analytics pracovní prostory poskytují centralizované umístění pro ukládání a dotazování dat protokolu nejen z prostředků Azure, ale i místních prostředků a prostředků v jiných cloudech. Azure Container Instances obsahují integrovanou podporu pro posílání protokolů a dat událostí do protokolů Azure Monitor.

Chcete-li odeslat data protokolu a událostí skupiny kontejnerů do Azure Monitor protokolů, zadejte existující Log Analytics ID pracovního prostoru a klíč pracovního prostoru při konfiguraci skupiny kontejnerů. 

Následující části popisují, jak vytvořit skupinu kontejnerů s povoleným protokolováním a jak se dotazovat na protokoly. Chcete-li povolit protokolování, můžete také [aktualizovat skupinu kontejnerů](container-instances-update.md) pomocí ID pracovního prostoru a klíče pracovního prostoru.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> V současné době můžete odesílat data událostí z instancí kontejnerů Linux pouze do Log Analytics.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli povolit protokolování ve vašich instancích kontejnerů, potřebujete následující:

* [Pracovní prostor Log Analytics](../azure-monitor/logs/quick-create-workspace.md)
* [Rozhraní Azure CLI](/cli/azure/install-azure-cli) (nebo službu [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Získání přihlašovacích údajů služby Log Analytics

Služba Azure Container Instances potřebuje oprávnění k odesílání dat do pracovního prostoru služby Log Analytics. Abyste mohli udělit toto oprávnění a povolit protokolování, musíte při vytváření skupiny kontejnerů zadat ID pracovního prostoru služby Log Analytics a jeden z jeho klíčů (primární nebo sekundární).

Získání ID a primárního klíče pracovního prostoru Log Analytics:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Nastavení** vyberte **Správa agentů** .
1. Poznamenejte si:
   * **ID pracovního prostoru**
   * **Primární klíč**

## <a name="create-container-group"></a>Vytvoření skupiny kontejnerů

Teď, když máte ID pracovního prostoru Log Analytics a primární klíč, jste připraveni vytvořit skupinu kontejnerů s podporou protokolování.

Následující příklady znázorňují dva způsoby, jak vytvořit skupinu kontejnerů, která se skládá [z jednoho][fluentd] mikrokontejneru: Azure CLI a Azure CLI se šablonou YAML. V mikrokontejneru se vytvoří několik řádků výstupu ve své výchozí konfiguraci. Tento výstup se odesílá do pracovního prostoru služby Log Analytics, takže se dobře hodí pro ukázku zobrazení a dotazování protokolů.

### <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

Pokud chcete k nasazení využít Azure CLI, zadejte v příkazu [az container create][az-container-create] parametry `--log-analytics-workspace` a `--log-analytics-workspace-key`. Tyto dvě hodnoty pracovního prostoru nahraďte hodnotami, které jste získali v předchozím kroku (a aktualizujte název skupiny prostředků). Teprve potom spusťte následující příkaz.

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
apiVersion: 2019-12-01
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

## <a name="view-logs"></a>Zobrazení protokolů

Po nasazení skupiny kontejnerů může trvat několik minut (až 10), než se první položky protokolu objeví na webu Azure Portal. 

Chcete-li zobrazit protokoly skupiny kontejnerů v `ContainerInstanceLog_CL` tabulce:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Obecné** vyberte **protokoly** .  
1. Zadejte následující dotaz: `ContainerInstanceLog_CL | limit 50`
1. Vyberte **Spustit** .

Měl by se zobrazit několik výsledků zobrazených dotazem. Pokud nevidíte žádné výsledky, počkejte pár minut a pak znovu spusťte dotaz kliknutím na tlačítko **Spustit** . Ve výchozím nastavení se položky protokolu zobrazují ve formátu **tabulky** . Pak můžete rozbalením řádku zobrazit obsah příslušné položky protokolu.

![Výsledky hledání v protokolu na webu Azure Portal][log-search-01]

## <a name="view-events"></a>Zobrazení událostí

Události pro instance kontejnerů můžete zobrazit také v Azure Portal. Události zahrnují čas vytvoření instance a její spuštění. Zobrazení dat událostí v `ContainerEvent_CL` tabulce:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Obecné** vyberte **protokoly** .  
1. Zadejte následující dotaz: `ContainerEvent_CL | limit 50`
1. Vyberte **Spustit** .

Měl by se zobrazit několik výsledků zobrazených dotazem. Pokud nevidíte žádné výsledky, počkejte pár minut a pak znovu spusťte dotaz kliknutím na tlačítko **Spustit** . Ve výchozím nastavení se položky zobrazují ve formátu **tabulky** . Potom můžete rozbalit řádek a zobrazit obsah jednotlivé položky.

![Výsledky hledání událostí v Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Dotazování protokolů kontejnerů

Protokoly Azure Monitor obsahují obsáhlý [dotazovací jazyk][query_lang] pro přijímání informací z potenciálně tisíců výstupů protokolu.

Základní strukturou dotazu je zdrojová tabulka (v tomto článku), po které `ContainerInstanceLog_CL` `ContainerEvent_CL` následuje řada operátorů oddělených znakem svislé čáry ( `|` ). Zřetězením více operátorů můžete zúžit výsledky a provádět pokročilé funkce.

Chcete-li zobrazit příklady výsledků dotazu, vložte následující dotaz do textového pole dotazu a vyberte tlačítko **Spustit** pro spuštění dotazu. Tento dotaz zobrazí všechny položky protokolu, jejichž pole Message (Zpráva) obsahuje slovo warn (upozornění):

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Podporované jsou i složitější dotazy. Tento dotaz například zobrazí jenom položky protokolu pro skupinu kontejnerů mycontainergroup001 vygenerované během poslední hodiny:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Další kroky

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Další informace o dotazování protokolů a konfiguraci výstrah v Azure Monitorch protokolech najdete v tématech:

* [Principy prohledávání protokolů v Azure Monitorch protokolech](../azure-monitor/logs/log-query-overview.md)
* [Sjednocená upozornění ve službě Azure Monitor](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorování procesoru a paměti kontejneru

Informace o monitorování prostředků procesoru a paměti instance kontejneru najdete v tomto tématu:

* [Monitorování prostředků kontejneru ve službě Azure Container Instances](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create