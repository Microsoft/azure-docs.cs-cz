---
title: Protokolování instancí kontejnerů s využitím Azure Log Analytics
description: Naučte se, jak se dá odesílat výstup kontejnerů (STDOUT a STDERR) do služby Azure Log Analytics.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: 5d3ba4ac4a86b6051ea5dafbff8c038c9198108c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279547"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Protokolování instancí kontejnerů s využitím Azure Log Analytics

Pracovní prostory služby Log Analytics poskytují centralizované umístění pro ukládání a dotazování dat protokolů nejen z prostředků Azure, ale i z místních prostředků a prostředků v jiných cloudech. Služba Azure Container Instances zahrnuje integrovanou podporu pro odesílání dat do služby Log Analytics.

Pokud chcete odesílat data z instancí kontejnerů do služby Log Analytics, musíte vytvořit skupinu kontejnerů pomocí rozhraní Azure CLI (nebo služby Cloud Shell) a soubor YAML. Následující oddíly popisují vytvoření skupiny kontejnerů s povoleným protokolováním a dotazování protokolů.

## <a name="prerequisites"></a>Požadavky

Abyste mohli povolit protokolování ve vašich instancích kontejnerů, potřebujete následující:

* [Pracovní prostor služby Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)
* [Rozhraní Azure CLI](/cli/azure/install-azure-cli) (nebo službu [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Získání přihlašovacích údajů služby Log Analytics

Služba Azure Container Instances potřebuje oprávnění k odesílání dat do pracovního prostoru služby Log Analytics. Abyste mohli udělit toto oprávnění a povolit protokolování, musíte při vytváření skupiny kontejnerů zadat ID pracovního prostoru služby Log Analytics a jeden z jeho klíčů (primární nebo sekundární).

Získání ID a primárního klíče pracovního prostoru služby Log Analytics:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **NASTAVENÍ** vyberte **Upřesňující nastavení**.
1. Vyberte **Připojené zdroje** > **Servery Windows** (nebo **Linuxové servery** – ID a klíče jsou pro obojí stejné).
1. Poznamenejte si:
   * **ID PRACOVNÍHO PROSTORU**
   * **PRIMÁRNÍ KLÍČ**

## <a name="create-container-group"></a>Vytvoření skupiny kontejnerů

Jakmile máte ID a primární klíč pracovního prostoru služby Log Analytics, jste připravení vytvořit skupinu kontejnerů s povoleným protokolováním.

Následující příklady ukazují dva způsoby vytvoření skupiny kontejnerů s využitím jednoho kontejneru [fluentd][fluentd]: Azure CLI a Azure CLI se šablonou YAML. Kontejner fluentd ve výchozí konfiguraci produkuje několik řádků výstupu. Tento výstup se odesílá do pracovního prostoru služby Log Analytics, takže se dobře hodí pro ukázku zobrazení a dotazování protokolů.

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
apiVersion: 2018-06-01
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

Potom spusťte následující příkaz, který nasadí skupinu kontejnerů. Parametr `myResourceGroup` nahraďte názvem skupiny prostředků ve vašem předplatném (nebo napřed vytvořte skupinu prostředků s názvem myResourceGroup):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Krátce po spuštění příkazu byste měli dostat odpověď z Azure s podrobnostmi o nasazení.

## <a name="view-logs-in-log-analytics"></a>Zobrazení protokolů ve službě Log Analytics

Po nasazení skupiny kontejnerů může trvat několik minut (až 10), než se první položky protokolu objeví na webu Azure Portal. Když budete chtít zobrazit protokoly skupiny kontejnerů, otevřete pracovní prostor služby Log Analytics a pak:

1. V přehledu **Pracovní prostor OMS** vyberte **Prohledávání protokolu**. Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.  
1. V části **Další dotazy k vyzkoušení** vyberte odkaz **Všechna shromážděná data**.

Mělo by se zobrazit několik výsledků dotazu `search *`. Pokud se při prvním pokusu nezobrazí žádné výsledky, počkejte několik minut a pak spusťte dotaz znovu tlačítkem **SPUSTIT**. Ve výchozím nastavení se položky protokolu zobrazí v zobrazení Seznam. Pokud chcete položky protokolu zobrazit ve zhuštěnějším formátu, vyberte možnost **Tabulka**. Pak můžete rozbalením řádku zobrazit obsah příslušné položky protokolu.

![Výsledky hledání v protokolu na webu Azure Portal][log-search-01]

## <a name="query-container-logs"></a>Dotazování protokolů kontejnerů

Služba Log Analytics zahrnuje rozsáhlý [dotazovací jazyk][query_lang] pro extrahování informací z potenciálně tisíců řádků výstupů v protokolech.

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

## <a name="next-steps"></a>Další kroky

### <a name="log-analytics"></a>Log Analytics

Další informace o dotazování protokolů a konfiguraci upozornění ve službě Azure Log Analytics najdete v těchto tématech:

* [Principy hledání v protokolu ve službě Log Analytics](../log-analytics/log-analytics-queries.md)
* [Sjednocená upozornění ve službě Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

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