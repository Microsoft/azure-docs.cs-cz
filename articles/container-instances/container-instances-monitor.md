---
title: Monitorování kontejnerů ve službě Azure Container Instances
description: Podrobnosti o způsobu monitorování využití výpočetních prostředků, jako je procesor a paměť, kontejnery ve službě Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: marsma
ms.openlocfilehash: 6107aee0a4d7497e90186e0ea4bda6cd2712820d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979603"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorování prostředků kontejneru ve službě Azure Container Instances

Služba Azure Monitor poskytuje přehled o výpočetních prostředcích používaných instancemi kontejnerů. Použijte Azure Monitor ke sledování využití procesoru a paměti skupiny kontejnerů a jednotlivé kontejnery. Tato data o využití prostředků vám u skupin kontejnerů pomohou určit nejlepší nastavení pro procesor a paměť.

Tento dokument podrobně popisuje shromažďování využití procesoru a paměti u instancí kontejnerů pomocí jak webu Azure Portal, tak i rozhraní Azure CLI.

> [!IMPORTANT]
> Aktuálně jsou metriky využití prostředků dostupné pouze u linuxových kontejnerů.
>

## <a name="available-metrics"></a>Dostupné metriky

Služba Azure Monitor poskytuje u služby Azure Container Instances metriky o využití **procesoru** i **paměti**. Obě metriky jsou k dispozici pro skupinu kontejnerů i jednotlivé kontejnery.

Metriky využití procesoru se uvádějí v jednotkách **millicore**. Jeden millicore je tisícina jádra procesoru, takže 500 jednotek millicore (nebo 500 m) představuje 50% využití jádra procesoru.

Metriky využití paměti se uvádějí v **bajtech**.

## <a name="get-metrics---azure-portal"></a>Získání metrik – Azure Portal

Data služby Azure Monitor budou k dispozici na webu Azure Portal po vytvoření skupiny kontejnerů. Pokud si chcete zobrazit metriky skupiny kontejnerů, vyberte nejprve skupinu prostředků a potom danou skupinu kontejnerů. Níže si můžete prohlédnout předem vytvořené grafy pro využití procesoru i paměti.

![Dvojitý graf][dual-chart]

Pokud máte skupinu kontejnerů, která obsahuje více kontejnerů, použijte k prezentování metrik jednotlivých kontejnerů [dimenzi][monitor-dimension]. Graf s metrikami jednotlivých kontejnerů vytvoříte následovně:

1. Z levé navigační nabídky vyberte **Sledovat**.
2. Vyberte skupinu kontejnerů a metriku (procesor nebo paměť).
3. Vyberte zelené tlačítko dimenze a potom vyberte **Název kontejneru**.

![Dimenze][dimension]

## <a name="get-metrics---azure-cli"></a>Získání metrik – Azure CLI

Využití procesoru a paměti instancí kontejnerů je také možné shromáždit pomocí služby Azure CLI. Nejprve pomocí následujícího příkazu získejte ID skupiny kontejnerů. Část `<resource-group>` nahraďte názvem skupiny prostředků a část `<container-group>` nahraďte názvem skupiny kontejnerů.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Pro získání metriky využití **procesoru** použijte následující příkaz.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

A pro získání metriky využití **paměti** použijte tento příkaz.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

U skupiny více kontejnerů je možné přidat dimenzi `containerName` a získat tato data pro jednotlivé kontejnery.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Další kroky

Další informace o monitorování Azure najdete v [přehledu monitorování Azure][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../monitoring/monitoring-data-collection.md#multi-dimensional-metrics
