---
title: Protokolování instanci kontejneru s protokoly Azure monitoru
description: Zjistěte, jak odeslat protokoly z Azure container instances protokoly Azure monitoru.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: cab0bc4d2d0491c70a1d2f11f3a5d5d831ade6cf
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722635"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Protokolování instanci kontejneru s protokoly Azure monitoru

Pracovní prostory log Analytics poskytuje centralizovaného umístění pro ukládání a dotazování dat protokolu z nejen prostředků Azure, ale také místní prostředky a prostředky v jiných cloudech. Služba Azure Container Instances zahrnuje integrovanou podporu pro odesílání dat na protokoly Azure monitoru.

Odeslat data instance kontejneru na protokoly Azure monitoru, musíte zadat klíč ID a pracovnímu prostoru pracovní prostor Log Analytics, při vytváření skupiny kontejnerů. Následující oddíly popisují vytvoření skupiny kontejnerů s povoleným protokolováním a dotazování protokolů.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli povolit protokolování ve vašich instancích kontejnerů, potřebujete následující:

* [Pracovní prostor služby Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [Rozhraní Azure CLI](/cli/azure/install-azure-cli) (nebo službu [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Získání přihlašovacích údajů služby Log Analytics

Služba Azure Container Instances potřebuje oprávnění k odesílání dat do pracovního prostoru služby Log Analytics. Abyste mohli udělit toto oprávnění a povolit protokolování, musíte při vytváření skupiny kontejnerů zadat ID pracovního prostoru služby Log Analytics a jeden z jeho klíčů (primární nebo sekundární).

Postup získání ID pracovního prostoru log analytics a primární klíč:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **nastavení**vyberte **upřesňující nastavení**
1. Vyberte **Připojené zdroje** > **Servery Windows** (nebo **Linuxové servery** – ID a klíče jsou pro obojí stejné).
1. Poznamenejte si:
   * **ID PRACOVNÍHO PROSTORU**
   * **PRIMÁRNÍ KLÍČ**

## <a name="create-container-group"></a>Vytvoření skupiny kontejnerů

Teď, když máte ID pracovního prostoru log analytics a primární klíč, jste připraveni vytvořit skupinu povoleno protokolování kontejneru.

Následující příklady znázorňují dva způsoby, jak vytvořit skupinu kontejnerů pomocí jediného [fluentd][fluentd] kontejneru: Azure CLI a rozhraní příkazového řádku Azure s využitím šablony YAML. Kontejner fluentd ve výchozí konfiguraci produkuje několik řádků výstupu. Tento výstup se odesílá do pracovního prostoru služby Log Analytics, takže se dobře hodí pro ukázku zobrazení a dotazování protokolů.

### <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

Chcete-li nasadit pomocí rozhraní příkazového řádku Azure, zadejte `--log-analytics-workspace` a `--log-analytics-workspace-key` parametry v [vytvořit az container][az-container-create] příkaz. Tyto dvě hodnoty pracovního prostoru nahraďte hodnotami, které jste získali v předchozím kroku (a aktualizujte název skupiny prostředků). Teprve potom spusťte následující příkaz.

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

V dalším kroku spusťte následující příkaz pro nasazení skupiny kontejnerů. Nahraďte `myResourceGroup` u prostředku skupiny v rámci vašeho předplatného (nebo nejdřív vytvořte skupinu prostředků s názvem "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Krátce po spuštění příkazu byste měli dostat odpověď z Azure s podrobnostmi o nasazení.

## <a name="view-logs-in-azure-monitor-logs"></a>Zobrazení protokolů v protokoly Azure monitoru

Po nasazení skupiny kontejnerů může trvat několik minut (až 10), než se první položky protokolu objeví na webu Azure Portal. Chcete-li zobrazit protokoly pro skupinu kontejnerů:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Obecné**vyberte **protokoly**  
1. Zadejte následující dotaz: `search *`
1. Vyberte **spuštění**

Mělo by se zobrazit několik výsledků dotazu `search *`. Pokud nejprve se nezobrazí žádné výsledky, počkejte několik minut a pak vyberte **spustit** tlačítko a spusťte dotaz znovu. Ve výchozím nastavení, se zobrazí položky protokolu v **tabulky** formátu. Pak můžete rozbalením řádku zobrazit obsah příslušné položky protokolu.

![Výsledky hledání v protokolu na webu Azure Portal][log-search-01]

## <a name="query-container-logs"></a>Dotazování protokolů kontejnerů

Protokoly Azure monitoru obsahuje rozsáhlý [dotazovací jazyk][query_lang] pro načítání informací z potenciálně tisíce řádků výstupu protokolu.

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

Další informace o dotazování protokolů a konfigurace upozornění protokolů Azure Monitor naleznete v tématu:

* [Principy prohledávání protokolů v protokoly Azure monitoru](../log-analytics/log-analytics-log-search.md)
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