---
title: Shromažďování & analýzu protokolů zdrojů
description: Zjistěte, jak odesílat protokoly o prostředcích a data událostí ze skupin kontejnerů v instanci kontejnerů Azure do protokolů Azure Monitoru
ms.topic: article
ms.date: 01/08/2020
ms.author: danlep
ms.openlocfilehash: 304e98fff386911b878877d2f03d489d0eef5dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770539"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Protokolování skupin y kontejnerů a instancí pomocí protokolů Azure Monitoru

Pracovní prostory Log Analytics poskytují centralizované umístění pro ukládání a dotazování dat protokolu nejen z prostředků Azure, ale také místních prostředků a prostředků v jiných cloudech. Instance kontejnerů Azure zahrnuje integrovanou podporu pro odesílání protokolů a dat událostí do protokolů Azure Monitoru.

Chcete-li odeslat data protokolu skupiny kontejnerů a událostí do protokolů Azure Monitor, zadejte existující ID pracovního prostoru analýzy protokolů a klíč pracovního prostoru při vytváření skupiny kontejnerů. Následující části popisují, jak vytvořit skupinu kontejnerů s povolenou protokolováním a jak dotazovat protokoly.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> V současné době můžete odesílat pouze data událostí z instancí kontejneru Linuxu do log analytics.

## <a name="prerequisites"></a>Požadavky

Abyste mohli povolit protokolování ve vašich instancích kontejnerů, potřebujete následující:

* [Pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [Rozhraní Azure CLI](/cli/azure/install-azure-cli) (nebo službu [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Získání přihlašovacích údajů služby Log Analytics

Služba Azure Container Instances potřebuje oprávnění k odesílání dat do pracovního prostoru služby Log Analytics. Abyste mohli udělit toto oprávnění a povolit protokolování, musíte při vytváření skupiny kontejnerů zadat ID pracovního prostoru služby Log Analytics a jeden z jeho klíčů (primární nebo sekundární).

Chcete-li získat ID pracovního prostoru analýzy protokolu a primární klíč:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Nastavení**vyberte **Upřesnit nastavení.**
1. Vyberte **připojené zdroje** > **windows servery** (nebo **Linux servery**--ID a klíče jsou stejné pro oba)
1. Poznamenejte si:
   * **ID PRACOVNÍHO PROSTORU**
   * **PRIMÁRNÍ KLÍČ**

## <a name="create-container-group"></a>Vytvoření skupiny kontejnerů

Teď, když máte ID pracovního prostoru analýzy protokolů a primární klíč, jste připraveni vytvořit skupinu kontejnerů s povoleným protokolováním.

Následující příklady ukazují dva způsoby, jak vytvořit skupinu kontejnerů, která se skládá z jednoho [plynulého][fluentd] kontejneru: Azure CLI a Azure CLI se šablonou YAML. Kontejner fluentd vytváří několik řádků výstupu ve své výchozí konfiguraci. Tento výstup se odesílá do pracovního prostoru služby Log Analytics, takže se dobře hodí pro ukázku zobrazení a dotazování protokolů.

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

Dále proveďte následující příkaz k nasazení skupiny kontejnerů. Nahraďte `myResourceGroup` skupinou prostředků v předplatném (nebo nejprve vytvořte skupinu prostředků s názvem myResourceGroup):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Krátce po spuštění příkazu byste měli dostat odpověď z Azure s podrobnostmi o nasazení.

## <a name="view-logs"></a>Zobrazení protokolů

Po nasazení skupiny kontejnerů může trvat několik minut (až 10), než se první položky protokolu objeví na webu Azure Portal. Zobrazení protokolů skupiny kontejnerů `ContainerInstanceLog_CL` v tabulce:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Obecné**vyberte **Protokoly.**  
1. Zadejte následující dotaz:`ContainerInstanceLog_CL | limit 50`
1. Vybrat **spustit**

Měli byste vidět několik výsledků zobrazených dotazem. Pokud se zpočátku nezobrazují žádné výsledky, počkejte několik minut a pak vyberte tlačítko **Spustit** a dotaz znovu spusťte. Ve výchozím nastavení jsou položky protokolu zobrazeny ve formátu **tabulky.** Pak můžete rozbalením řádku zobrazit obsah příslušné položky protokolu.

![Výsledky hledání v protokolu na webu Azure Portal][log-search-01]

## <a name="view-events"></a>Zobrazení událostí

Můžete také zobrazit události pro instance kontejneru na webu Azure Portal. Události zahrnují čas vytvoření instance a její spuštění. Zobrazení dat událostí v `ContainerEvent_CL` tabulce:

1. Na webu Azure Portal přejděte do svého pracovního prostoru služby Log Analytics.
1. V části **Obecné**vyberte **Protokoly.**  
1. Zadejte následující dotaz:`ContainerEvent_CL | limit 50`
1. Vybrat **spustit**

Měli byste vidět několik výsledků zobrazených dotazem. Pokud se zpočátku nezobrazují žádné výsledky, počkejte několik minut a pak vyberte tlačítko **Spustit** a dotaz znovu spusťte. Ve výchozím nastavení jsou položky zobrazeny ve formátu **tabulky.** Potom můžete rozbalit řádek a zobrazit obsah jednotlivé položky.

![Výsledky hledání událostí na webu Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Dotazování protokolů kontejnerů

Protokoly Azure Monitor obsahuje rozsáhlý [dotazovací jazyk][query_lang] pro vytahování informací z potenciálně tisíce řádků výstupu protokolu.

Základní struktura dotazu je zdrojová tabulka (v tomto článku `ContainerInstanceLog_CL` nebo `ContainerEvent_CL`) následovaná řadou operátorů oddělených znakem kanálu (`|`). Zřetězením více operátorů můžete zúžit výsledky a provádět pokročilé funkce.

Chcete-li zobrazit ukázkové výsledky dotazu, vložte následující dotaz do textového pole dotazu a vyberte tlačítko **Spustit** pro spuštění dotazu. Tento dotaz zobrazí všechny položky protokolu, jejichž pole Message (Zpráva) obsahuje slovo warn (upozornění):

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

Další informace o dotazování protokolů a konfiguraci výstrah v protokolech Azure Monitoru najdete v tématu:

* [Principy hledání protokolů v protokolech Azure Monitor](../log-analytics/log-analytics-log-search.md)
* [Sjednocená upozornění ve službě Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorování procesoru a paměti kontejneru

Informace o monitorování prostředků procesoru a paměti instance kontejneru najdete v tomto tématu:

* [Monitorování prostředků kontejneru ve službě Azure Container Instances](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create