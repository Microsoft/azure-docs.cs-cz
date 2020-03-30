---
title: Shromažďování dat ze služby CollectD ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: CollectD je open source linuxový daemon, který pravidelně shromažďuje data z aplikací a informací na úrovni systému.  Tento článek obsahuje informace o shromažďování dat ze služby CollectD v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: b8c09d4ac5d0856eb0d448a1cabd9adc567850c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670606"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Shromažďování dat ze služby CollectD na linuxových agentech ve službě Azure Monitor
[CollectD](https://collectd.org/) je open source linuxový daemon, který pravidelně shromažďuje metriky výkonu z aplikací a informací o úrovni systému. Mezi příklady aplikací patří Java Virtual Machine (JVM), MySQL Server a Nginx. Tento článek obsahuje informace o shromažďování dat o výkonu ze služby CollectD v Azure Monitoru.

Úplný seznam dostupných pluginů lze nalézt v [tabulce pluginů](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Přehled Sbírat](media/data-sources-collectd/overview.png)

Následující konfigurace CollectD je součástí agenta Log Analytics pro Linux směrovat shromažďovaná data agentovi Log Analytics pro Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Navíc pokud používáte verze collectD před 5.5 použít následující konfiguraci místo.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Konfigurace CollectD používá`write_http` výchozí modul plug-in k odesílání dat metrik výkonu přes port 26000 agentovi Log Analytics pro Linux. 

> [!NOTE]
> Tento port lze v případě potřeby nakonfigurovat na vlastní definovaný port.

Agent Log Analytics pro Linux také naslouchá na portu 26000 pro metriky CollectD a pak je převede na metriky schématu Azure Monitor. Následuje agent Log Analytics pro `collectd.conf`konfiguraci Linuxu .

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> Ve výchozím nastavení je ve výchozím nastavení nastaveno na čtení hodnot v [intervalu](https://collectd.org/wiki/index.php/Interval)10 sekund . Vzhledem k tomu, že to přímo ovlivňuje objem dat odeslaných do protokolů monitorování Azure, budete muset vyladit tento interval v rámci konfigurace CollectD, abyste získali dobrou rovnováhu mezi požadavky na monitorování a souvisejícími náklady a využitím protokolů monitorování Azure.

## <a name="versions-supported"></a>Podporované verze
- Azure Monitor aktuálně podporuje collectd verze 4.8 a vyšší.
- Agent Log Analytics pro Linux v1.1.0-217 nebo vyšší je vyžadován pro shromažďování metriky kolekce.


## <a name="configuration"></a>Konfigurace
Následují základní kroky ke konfiguraci shromažďování dat v Azure Monitoru.

1. Nakonfigurujte službu CollectD tak, aby odesílala data agentovi Log Analytics pro Linux pomocí write_http pluginu.  
2. Nakonfigurujte agenta Log Analytics pro Linux tak, aby naslouchal shromažďovaná data na příslušném portu.
3. Restartujte agenta CollectD a Log Analytics pro Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurace funkce CollectD k předávání dat 

1. Chcete-li směrovat shromažďovaná data `oms.conf` agentovi Log Analytics pro Linux, je třeba přidat do konfiguračního adresáře CollectD. Cíl tohoto souboru závisí na distribuci Linuxu vašeho počítače.

    Pokud je adresář collectd config umístěn v souboru /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Pokud je adresář collectd config umístěn v aplikaci /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >U verzí CollectD před verzí 5.5 budete `oms.conf` muset značky upravit, jak je uvedeno výše.
    >

2. Zkopírujte soubor collectd.conf do konfiguračního adresáře omsagent v požadovaném pracovním prostoru.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Restartujte agenta CollectD a Log Analytics pro Linux pomocí následujících příkazů.

    Služba sudo shromáždila restart sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Shromažďované metriky do převodu schématu Azure Monitoru
Chcete-li udržovat známý model mezi metrikami infrastruktury, které již byly shromážděny agentem Log Analytics pro Linux, a novými metrikami shromážděnými službou CollectD se používá následující mapování schématu:

| Pole Shromažďovaná metrika | Pole Azure Monitor |
|:--|:--|
| `host` | Počítač |
| `plugin` | Žádný |
| `plugin_instance` | Název instance<br>Pokud **je plugin_instance** *null,* pak InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | Countername<br>Pokud **je type_instance** *null,* pak CounterName=**prázdné** |
| `dsnames[]` | Countername |
| `dstypes` | Žádný |
| `values[]` | Hodnota čítače |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení. 
* Vlastní [pole](custom-fields.md) slouží k rozdělení dat ze záznamů syslogu do jednotlivých polí.
