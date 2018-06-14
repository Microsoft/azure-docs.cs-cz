---
title: Shromažďování dat z CollectD v OMS Log Analytics | Microsoft Docs
description: CollectD je démon Linux s otevřeným zdrojem, který pravidelně shromažďuje data z aplikace a informace o úrovni systému.  Tento článek obsahuje informace o shromažďování dat z CollectD v analýzy protokolů.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855205"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Shromažďování dat z CollectD na agentech Linux v analýzy protokolů
[CollectD](https://collectd.org/) je démon Linux s otevřeným zdrojem, který pravidelně shromažďuje metriky výkonu z aplikace a informace o úrovni systému. Příklad aplikace patří Java Virtual Machine (JVM), MySQL Server a Nginx. Tento článek obsahuje informace o shromažďování dat výkonu z CollectD v analýzy protokolů.

Úplný seznam dostupných modulů plug-in najdete na [tabulky modulů plug-in](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Přehled CollectD](media/log-analytics-data-sources-collectd/overview.png)

Následující konfigurace CollectD je součástí agenta OMS pro Linux tak, aby data trasy CollectD OMS agenta pro Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Kromě toho pokud se používá verzích collectD před 5.5 místo toho použijte následující konfiguraci.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Konfigurace CollectD používá výchozí`write_http` modulu plug-in k odesílání dat metriky výkonu přes port 26000 do OMS agenta pro Linux. 

> [!NOTE]
> Tento port lze nakonfigurovat na vlastní port, v případě potřeby.

OMS agenta pro Linux také naslouchá na portu 26000 CollectD metrik a převede je na OMS schématu metriky. Tady je OMS agenta pro Linux konfiguraci `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Podporované verze
- Aktuálně podporuje CollectD verze 4.8 analýzy protokolů a vyšší.
- Je vyžadována pro kolekci metriky CollectD OMS agenta pro Linux v1.1.0-217 nebo vyšší.


## <a name="configuration"></a>Konfigurace
Následují základní kroky konfigurace sběru dat CollectD v analýzy protokolů.

1. Nakonfigurujte CollectD k odesílání dat do OMS agenta pro Linux pomocí modulu plug-in write_http.  
2. Konfigurace agenta OMS pro Linux naslouchat CollectD data na příslušný port.
3. Restartujte CollectD a OMS agenta pro Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurace CollectD k předávání dat 

1. K datům CollectD trasy a OMS agenta pro Linux `oms.conf` musí být přidán do adresáře společnosti CollectD konfigurace. Cílem tohoto souboru závisí na distro Linux počítače.

    Pokud konfigurace adresáře CollectD nachází ve /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Pokud konfigurace adresáře CollectD nachází ve /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Pro verze CollectD před 5.5 budete muset upravit značky v `oms.conf` jako v příkladu nahoře.
    >

2. Zkopírujte collectd.conf požadované prostoru omsagent konfiguračního adresáře.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Restartujte CollectD a OMS agenta pro Linux pomocí následujících příkazů.

    sudo služby collectd restartování sudo /opt/microsoft/omsagent/bin/service_control restartování

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>CollectD metrik převod schématu analýzy protokolů
Chcete-li zachovat, že známý model mezi infrastruktura metriky již shromážděné agentem OMS pro Linux a nové metriky shromažďují CollectD následující schéma mapování se používá:

| Metrika CollectD pole | Pole analýzy protokolů |
|:--|:--|
| hostitele | Počítač |
| Modul plug-in | Žádný |
| plugin_instance | Název instance<br>Pokud **plugin_instance** je *null* pak InstanceName = "*_celkem*" |
| type | Název objektu |
| type_instance | Název_čítače<br>Pokud **type_instance** je *null* pak CounterName =**prázdné** |
| [dsnames] | Název_čítače |
| dstypes | Žádný |
| [] – hodnoty | Přepočtené |

## <a name="next-steps"></a>Další kroky
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení. 
* Použití [vlastní pole](log-analytics-custom-fields.md) k analýze dat z syslog záznamů do jednotlivých polí.

