---
title: Shromažďovat data shromážděná v OMS Log Analytics | Dokumentace Microsoftu
description: Shromážděná je linuxového démona otevřít zdroj, který pravidelně shromažďuje data z aplikací a informace na úrovni systému.  Tento článek obsahuje informace o shromažďování dat z shromážděná v Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: eb053ef8fc66ff9d71a9576b71eb4edfcd688638
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041286"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Shromažďovat data shromážděná na agentech pro Linux ve službě Log Analytics
[Shromážděná](https://collectd.org/) je linuxového démona otevřít zdroj, který pravidelně shromažďuje metriky výkonu z aplikace a informace na úrovni systému. Příklad aplikace obsahují Java Virtual Machine (JVM), MySQL Server a Nginxu. Tento článek obsahuje informace o shromažďování dat výkonu z shromážděná v Log Analytics.

Úplný seznam dostupných modulů plug-in najdete v [tabulky moduly plug-in](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Přehled shromážděná](media/log-analytics-data-sources-collectd/overview.png)

Následující konfigurace shromážděná je součástí agenta OMS pro Linux na shromážděná data trasy pro agenta OMS pro Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Kromě toho pokud se používá verzích shromážděná před 5.5 místo toho použijte následující konfiguraci.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Konfigurace shromážděná používá výchozí`write_http` modul plug-in, který odešlete data metrik výkonu přes port 26000 do agenta OMS pro Linux. 

> [!NOTE]
> V případě potřeby lze nastavit na vlastní port tohoto portu.

Agenta OMS pro Linux také naslouchá na portu 26000 shromážděná metriky a převede je do OMS schématu metrik. Tady je agenta OMS pro Linux konfigurace `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Verze podporováno
- Log Analytics v současné době podporuje shromážděná verze 4,8 a vyšší.
- Agenta OMS pro Linux v1.1.0-217 nebo novější je vyžadováno pro shromažďování metrik shromážděná.


## <a name="configuration"></a>Konfigurace
Tady jsou základní postup pro konfiguraci kolekce shromážděná data v Log Analytics.

1. Nakonfigurujte shromážděná k odesílání dat do agenta OMS pro Linux s využitím modulu plug-in write_http.  
2. Konfigurace agenta OMS pro Linux pro naslouchání shromážděná data na příslušný port.
3. Restartujte shromážděná a agenta OMS pro Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurace shromážděná k předávání dat 

1. Na data shromážděná trasy pro agenta OMS pro Linux `oms.conf` musí být přidán do vaší shromážděná konfigurační adresář. Cílem tohoto souboru závisí na distribuce Linuxu vašeho počítače.

    Pokud je ve /etc/collectd.d/ adresáři shromážděná config:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Pokud je ve /etc/collectd/collectd.conf.d/ adresáři shromážděná config:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Pro verze shromážděná před 5.5 budete muset upravit značky v `oms.conf` jak je znázorněno výše.
    >

2. Zkopírujte collectd.conf do požadovaného pracovního prostoru omsagent konfigurační adresář.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Pomocí následujících příkazů restartujte shromážděná a agenta OMS pro Linux.

    sudo služby shromážděná restartování sudo /opt/microsoft/omsagent/bin/service_control restartování

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Shromážděná metriky k převodu schématu Log Analytics
Údržba známým modelem mezi metriky infrastruktury již shromážděné agentem OMS pro Linux a nové metriky shromážděné shromážděná následující schéma mapování se používá:

| Metrika shromážděná pole | Log Analytics pole |
|:--|:--|
| hostitel | Počítač |
| Modul plug-in | Žádný |
| plugin_instance | Název instance<br>Pokud **plugin_instance** je *null* pak InstanceName = "*_celkem*" |
| type | Název objektu |
| type_instance | Hodnota counterName<br>Pokud **type_instance** je *null* pak CounterName =**prázdné** |
| [] dsnames | Hodnota counterName |
| dstypes | Žádný |
| hodnoty] | CounterValue |

## <a name="next-steps"></a>Další postup
* Další informace o [prohledávání protokolů](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 
* Použití [vlastní pole](log-analytics-custom-fields.md) analyzovat data ze záznamů protokolu syslog do jednotlivých polí.

