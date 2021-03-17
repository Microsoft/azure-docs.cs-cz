---
title: Monitorovat instance kontejnerů
description: Jak monitorovat spotřebu výpočetních prostředků, jako je CPU a paměť, v kontejnerech v Azure Container Instances.
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: ae9725ffe66bebbed26745c311b2ada07d5d2c00
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589290"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorování prostředků kontejneru ve službě Azure Container Instances

[Azure monitor][azure-monitoring] poskytuje přehled o výpočetních prostředcích používaných instancemi kontejnerů. Tato data o využití prostředků pomáhají určit nejlepší nastavení prostředků pro skupiny kontejnerů. Azure Monitor také poskytuje metriky, které sledují síťové aktivity ve vašich kontejnerových instancích.

Tento dokument popisuje shromažďování Azure Monitor metrik pro instance kontejnerů pomocí Azure Portal a Azure CLI.

> [!IMPORTANT]
> Metriky Azure Monitor v Azure Container Instances jsou momentálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení verze Preview

V současné době Azure Monitor metriky dostupné jenom pro kontejnery Linux.

## <a name="available-metrics"></a>Dostupné metriky

Azure Monitor poskytuje následující [metriky pro Azure Container Instances][supported-metrics]. Tyto metriky jsou k dispozici pro skupinu kontejnerů a jednotlivé kontejnery. Ve výchozím nastavení jsou metriky agregované jako průměry.

- **Využití CPU** měřené v **millicores**. 
  - Jedna millicore je 1/1000th jádra procesoru, takže 500 millicores představuje využití 0,5 CPU core.
- **Využití paměti** v bajtech
- **Počet přijatých bajtů sítě** za sekundu
- **Bajty přenášené přes síť** za sekundu 

## <a name="get-metrics---azure-portal"></a>Získání metrik – Azure Portal

Data služby Azure Monitor budou k dispozici na webu Azure Portal po vytvoření skupiny kontejnerů. Chcete-li zobrazit metriky pro skupinu kontejnerů, přejděte na stránku **Přehled** pro skupinu kontejnerů. Tady vidíte předem vytvořené grafy pro každou z dostupných metrik.

![Dvojitý graf][dual-chart]

Ve skupině kontejnerů, která obsahuje více kontejnerů, použijte [dimenzi][monitor-dimension] pro zobrazení metrik podle kontejneru. Graf s metrikami jednotlivých kontejnerů vytvoříte následovně:

1. Na stránce **Přehled** vyberte jeden z grafů metrik, například **CPU**. 
1. Vyberte tlačítko **použít rozdělení** a vyberte **název kontejneru**.

![Snímek obrazovky ukazuje metriku pro instanci kontejneru s vybraným dělením a názvem kontejneru.][dimension]

## <a name="get-metrics---azure-cli"></a>Získání metrik – Azure CLI

Metriky pro instance kontejnerů se dají shromažďovat taky pomocí Azure CLI. Nejprve pomocí následujícího příkazu získejte ID skupiny kontejnerů. Část `<resource-group>` nahraďte názvem skupiny prostředků a část `<container-group>` nahraďte názvem skupiny kontejnerů.


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
2020-12-17 23:34:00  CPU Usage
. . .
2020-12-18 00:25:00  CPU Usage
2020-12-18 00:26:00  CPU Usage  0.4
2020-12-18 00:27:00  CPU Usage  0.0
```

Změňte hodnotu `--metric` parametru v příkazu, aby se získaly další [podporované metriky][supported-metrics]. Pomocí následujícího příkazu můžete například získat metriky využití **paměti** . 

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

Pro skupinu s více kontejnery se `containerName` dá dimenze přidat pro návratovou metriku na kontejner.

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

Naučte se vytvářet [Upozornění na metriky][metric-alert] a dostávat upozornění, když metrika Azure Container Instances překračuje prahovou hodnotu.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/alerts/alerts-metric.md
[monitor-dimension]: ../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups
