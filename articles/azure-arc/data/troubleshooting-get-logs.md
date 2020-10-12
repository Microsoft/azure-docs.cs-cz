---
title: Získání protokolů pro řešení potíží s povoleným řadičem dat Azure ARC
description: Získejte protokoly služby pro řešení potíží s povoleným řadičem dat Azure ARC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936045"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Získání protokolů datových služeb s podporou ARC Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Požadavky

K načtení protokolů datových služeb s podporou ARC Azure budete potřebovat nástroj Azure Data CLI. [Pokyny k instalaci](./install-client-tools.md)

Musíte být schopni se přihlásit ke službě kontroleru datových služeb s podporou ARC Azure jako správce.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Získání protokolů datových služeb s podporou ARC Azure

Pro účely řešení potíží můžete získat protokoly datových služeb s podporou ARC Azure ve všech luskech nebo v jednotlivých luskech.  To můžete provést pomocí standardních Kubernetes nástrojů, jako je například `kubectl logs` příkaz nebo v tomto článku, budete používat nástroj Azure Data CLI, který usnadňuje získání všech protokolů najednou.

Nejprve se ujistěte, že jste se přihlásili k řadiči dat.

```console
azdata login
```

Pak spuštěním následujícího příkazu vypíšete protokoly:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Soubory protokolu se v aktuálním pracovním adresáři vytvoří ve výchozím nastavení v podadresáři s názvem Logs.  Soubory protokolů můžete výstupovat do jiného adresáře pomocí `--target-folder` parametru.

Můžete zvolit komprimaci souborů vynecháním `--skip-compress` parametru.

Můžete aktivovat a zahrnout výpisy paměti tím, že vynecháte `--exclude-dumps` , ale to se nedoporučuje, pokud podpora Microsoftu nepožadoval výpisy paměti.  Pořizování výpisu paměti vyžaduje, aby se nastavení kontroleru dat `allowDumps` nastavilo na `true` čas vytvoření řadiče dat.

Volitelně můžete zvolit, že se má filtrovat protokol pro shromažďování protokolů jenom pro konkrétní pod ( `--pod` ) nebo kontejner ( `--container` ) podle názvu.

Můžete také filtrovat a shromažďovat protokoly pro konkrétní vlastní prostředek předáním `--resource-kind` `--resource-name` parametr a.  `resource-kind`Hodnota parametru by měla být jeden z názvů vlastních definic prostředků, které mohou být načteny příkazem `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Příklad hierarchie složek  Všimněte si, že hierarchie složek je uspořádána podle názvu pod názvem a pak podle kontejneru a pak podle hierarchie adresářů v rámci kontejneru.

```console
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