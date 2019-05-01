---
title: Monitorování kontejnerů ve službě Azure Container Instances
description: Jak monitorovat využití výpočetních prostředků, jako je procesor a paměť a kontejnerů ve službě Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 7b46ea0518038eeb908591b8438acc2a9095242c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570893"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorování prostředků kontejneru ve službě Azure Container Instances

[Azure Monitor] [ azure-monitoring] poskytuje podrobné informace o výpočetních prostředků použitých ve vašich instancí kontejnerů. Tato data použití prostředků vám pomůže určit nejlepší nastavení prostředků pro vaše skupiny kontejnerů. Platforma Azure Monitor také poskytuje metriky, které sledují aktivitu sítě ve vaší službě container instances.

Tento dokument podrobně popisuje shromažďování metrik Azure monitoru pro container instances pomocí webu Azure portal a rozhraní příkazového řádku Azure.

> [!IMPORTANT]
> Azure Monitor metriky ve službě Azure Container Instances jsou aktuálně ve verzi preview a některé [platí omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení verze Preview

V tuto chvíli Azure Monitor metriky jsou k dispozici pouze pro kontejnery Linuxu.

## <a name="available-metrics"></a>Dostupné metriky

Platforma Azure Monitor poskytuje následující [metriky pro Azure Container Instances][supported-metrics]. Tyto metriky jsou k dispozici pro skupinu kontejnerů a jednotlivých kontejnerů.

* **Využití procesoru** – měřenou v **jednotkách millicore**. Jeden millicore je 1/1000th jádra procesoru, takže 500 jednotkách millicore (nebo 500 milionů) představuje 50 % využití Procesorové jádro. Agregovat jako **průměrné využití** ze všech jader.

* **Využití paměti** – agregované jako **průměrný počet bajtů**.

* **Síťové bajty přijatých za sekundu** a **sítě bajtů odeslaných za sekundu** – agregované jako **průměrný počet bajtů za sekundu**. 

## <a name="get-metrics---azure-portal"></a>Získání metrik – Azure Portal

Data služby Azure Monitor budou k dispozici na webu Azure Portal po vytvoření skupiny kontejnerů. Pokud chcete zobrazit metriky pro skupinu kontejnerů, přejděte na **přehled** stránce pro skupinu kontejnerů. Tady vidíte předem vytvořené grafy pro každý z dostupných metrik.

![Dvojitý graf][dual-chart]

Ve skupině kontejnerů, která obsahuje více kontejnerů, použijte [dimenze] [ monitor-dimension] zobrazíte metriky v kontejneru. Graf s metrikami jednotlivých kontejnerů vytvoříte následovně:

1. V **přehled** stránky, vyberte jednu z metrik grafů, jako je například **procesoru**. 
1. Vyberte **použít rozdělení** tlačítko a vyberte **název kontejneru**.

![Dimenze][dimension]

## <a name="get-metrics---azure-cli"></a>Získání metrik – Azure CLI

Metriky pro container instances můžete shromáždit také pomocí rozhraní příkazového řádku Azure. Nejprve pomocí následujícího příkazu získejte ID skupiny kontejnerů. Část `<resource-group>` nahraďte názvem skupiny prostředků a část `<container-group>` nahraďte názvem skupiny kontejnerů.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Pro získání metriky využití **procesoru** použijte následující příkaz.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

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

Změňte hodnotu `--metric` parametr příkazu pro získání dalších [podporované metriky][supported-metrics]. Například použijte následující příkaz k získání **paměti** metriky využití. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

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

Pro skupiny více kontejnerů `containerName` dimenze mohou být přidány do vrátit metriky na kontejner.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

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

## <a name="next-steps"></a>Další postup

Další informace o monitorování Azure najdete v [přehledu monitorování Azure][azure-monitoring].

Zjistěte, jak vytvořit [upozornění na metriku] [ metric-alert] máme vás upozornit, když se překročí mezní hodnotu metriky pro Azure Container Instances.

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
