---
title: Získání protokolů pro řešení potíží s datovými službami s podporou ARC Azure
description: Naučte se, jak získat soubory protokolu z řadiče dat a vyřešit problémy s datovými službami s podporou ARC Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93234035"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Získání protokolů pro řešení potíží s datovými službami s podporou ARC Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Předpoklady

Než budete pokračovat, budete potřebovat:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Další informace najdete v tématu [instalace klientských nástrojů pro nasazení a správu datových služeb Azure ARC](./install-client-tools.md).
* Účet správce pro přihlášení k řadiči dat s povoleným ARC Azure.

## <a name="get-log-files"></a>Získat soubory protokolu

Pro účely řešení potíží můžete získat protokoly služeb napříč všemi lusky nebo určitými lusky. Jedním ze způsobů je použití standardních Kubernetes nástrojů, jako je `kubectl logs` příkaz. V tomto článku budete používat [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] nástroj, který usnadňuje získání všech protokolů najednou.

1. Přihlaste se k řadiči dat pomocí účtu správce.

   ```console
   azdata login
   ```

2. Spusťte následující příkaz pro výpis těchto protokolů:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Například:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Řadič dat vytvoří soubory protokolu v aktuálním pracovním adresáři v podadresáři s názvem `logs` . 

## <a name="options"></a>Možnosti

`azdata arc dc debug copy-logs`Příkaz poskytuje následující možnosti pro správu výstupu:

* Výstup souborů protokolu do jiného adresáře pomocí `--target-folder` parametru.
* Zkomprimujte soubory vynecháním `--skip-compress` parametru.
* Vynechejte Trigger a přidejte výpisy paměti `--exclude-dumps` . Tuto metodu nedoporučujeme, pokud podpora Microsoftu nepožadoval výpisy paměti. Získání výpisu paměti vyžaduje, aby se nastavení řadiče dat `allowDumps` `true` při vytváření řadiče dat nastavilo.
* Filtr pro shromažďování protokolů jenom pro konkrétní pod ( `--pod` ) nebo kontejner ( `--container` ) podle názvu.
* Vyfiltrujte shromažďování protokolů pro konkrétní vlastní prostředek předáním `--resource-kind` parametrů a `--resource-name` . `resource-kind`Hodnota parametru by měla být jeden z názvů vlastních definic prostředků. Tyto názvy můžete načíst pomocí příkazu `kubectl get customresourcedefinition` .

Pomocí těchto parametrů můžete nahradit `<parameters>` v následujícím příkladu: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Například:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Následující hierarchie složek je příklad. Organizuje se podle názvu pod, potom kontejner a pak podle hierarchie adresářů v rámci kontejneru.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Další kroky

[azdata ARC – protokoly ladění řadiče domény](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
