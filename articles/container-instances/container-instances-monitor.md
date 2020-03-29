---
title: Monitorování instancí kontejnerů
description: Jak sledovat spotřebu výpočetních prostředků, jako je procesor a paměť pomocí kontejnerů v azure container instances.
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: b4a66254c18d7e01b6d56e64e6b62721b620d499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250034"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorování prostředků kontejneru ve službě Azure Container Instances

[Azure Monitor][azure-monitoring] poskytuje přehled o výpočetních prostředků používaných instancemi kontejnerů. Tato data o využití prostředků vám pomohou určit nejlepší nastavení prostředků pro skupiny kontejnerů. Azure Monitor také poskytuje metriky, které sledují aktivitu sítě ve vašich instancích kontejneru.

Tento dokument podrobnosti shromažďování metriky Azure Monitor pro instance kontejnerů pomocí portálu Azure a Azure CLI.

> [!IMPORTANT]
> Metriky Azure Monitoru v instanci kontejnerů Azure jsou aktuálně ve verzi preview a platí určitá [omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení náhledu

V tuto chvíli metriky Azure Monitor jsou k dispozici pouze pro kontejnery Linuxu.

## <a name="available-metrics"></a>Dostupné metriky

Azure Monitor poskytuje následující [metriky pro instance kontejnerů Azure][supported-metrics]. Tyto metriky jsou k dispozici pro skupinu kontejnerů a jednotlivé kontejnery.

* **Využití procesoru** - měřeno v **milijádrích**. Jeden milicore je 1/1000th jádra CPU, takže 500 milicores (nebo 500 m) představuje 50% využití jádra CPU. Agregované jako **průměrné využití** ve všech jádrech.

* **Využití paměti** - agregované jako **průměrné bajty**.

* **Počet přijatých síťových bajtů za sekundu** a **počet přenesených síťových bajtů za sekundu** – agregováno jako **průměrný počet bajtů za sekundu**. 

## <a name="get-metrics---azure-portal"></a>Získání metrik – Azure Portal

Data služby Azure Monitor budou k dispozici na webu Azure Portal po vytvoření skupiny kontejnerů. Metriky pro skupinu kontejnerů najdete na stránce **Přehled** pro skupinu kontejnerů. Zde si můžete prohlédnout předem vytvořené grafy pro každou z dostupných metrik.

![Dvojitý graf][dual-chart]

Ve skupině kontejnerů, která obsahuje více kontejnerů, použijte [dimenzi][monitor-dimension] k prezentaci metrik podle kontejneru. Graf s metrikami jednotlivých kontejnerů vytvoříte následovně:

1. Na stránce **Přehled** vyberte jeden z grafů metrik, například **PROCESOR**. 
1. Vyberte tlačítko **Použít rozdělení** a vyberte **Název kontejneru**.

![Dimenze][dimension]

## <a name="get-metrics---azure-cli"></a>Získání metrik – Azure CLI

Metriky pro instance kontejneru lze také shromáždit pomocí azure cli. Nejprve pomocí následujícího příkazu získejte ID skupiny kontejnerů. Část `<resource-group>` nahraďte názvem skupiny prostředků a část `<container-group>` nahraďte názvem skupiny kontejnerů.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Pro získání metriky využití **procesoru** použijte následující příkaz.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Změňte hodnotu `--metric` parametru v příkazu a získejte další [podporované metriky][supported-metrics]. Pomocí následujícího příkazu můžete například získat metriky využití **paměti.** 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Pro skupinu s více `containerName` kontejnery lze dimenzi přidat k vrácení metriky na kontejner.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Další kroky

Další informace o monitorování Azure najdete v [přehledu monitorování Azure][azure-monitoring].

Zjistěte, jak vytvořit [upozornění na metriky,][metric-alert] abyste dostali upozornění, když metrika pro instance kontejnerů Azure překročí prahovou hodnotu.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
