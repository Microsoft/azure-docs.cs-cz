---
title: Shromažďovat data shromážděná v Azure Monitor | Microsoft Docs
description: Shromažďováno je open source démon pro Linux, který pravidelně shromažďuje data z aplikací a informací na úrovni systému.  Tento článek poskytuje informace o shromažďování dat z Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/27/2018
ms.openlocfilehash: 4bf58a7e446cb13366a230a35c83e6bf0acaa09a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932525"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Shromažďovat data shromážděná v agentech Linux v Azure Monitor
[Shromažďováno](https://collectd.org/) je open source démon pro Linux, který pravidelně shromažďuje metriky výkonu z aplikací a informací na úrovni systému. Příklady aplikací zahrnují prostředí Java Virtual Machine (JVM), MySQL server a Nginx. Tento článek poskytuje informace o shromažďování údajů o výkonu ze sběru Azure Monitor.

Úplný seznam dostupných modulů plug-in najdete v [tabulce modulů plug-in](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Shromážděný přehled](media/data-sources-collectd/overview.png)

Následující shromážděná konfigurace je součástí agenta Log Analytics pro Linux, aby mohla směrovat shromážděná data do agenta Log Analytics pro Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Kromě toho, pokud použijete verze shromážděné před 5,5, použijte místo toho následující konfiguraci.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Shromážděná konfigurace používá výchozí modul plug-in`write_http` k odesílání dat metrik výkonu přes port 26000 pro Log Analytics agenta pro Linux. 

> [!NOTE]
> Tento port můžete v případě potřeby nakonfigurovat na vlastní port definovaný.

Agent Log Analytics pro Linux také naslouchá na portu 26000 pro shromážděné metriky a pak je převede na Azure Monitor metriky schématu. Následuje příklad `collectd.conf`Log Analytics agenta pro konfiguraci Linux.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> Shromažďováno ve výchozím nastavení je nastaveno na hodnotu číst hodnoty v [intervalu](https://collectd.org/wiki/index.php/Interval)10 sekund. Vzhledem k tomu, že se má přímý vliv na objem dat odesílaných do Azure Monitor protokolů, možná budete muset tento interval vyladit v rámci shromážděné konfigurace, aby bylo dobré vyvážit rovnováhu mezi požadavky monitorování a přidruženými náklady a využitím pro Azure Monitor protokoly.

## <a name="versions-supported"></a>Podporované verze
- Azure Monitor aktuálně podporuje shromážděnou verzi 4,8 a vyšší.
- Pro kolekci shromažďování metrik se vyžaduje agent Log Analytics pro Linux v 1.1.0-217 nebo vyšší.


## <a name="configuration"></a>Konfigurace
Níže jsou uvedené základní kroky ke konfiguraci shromažďování shromážděných dat v Azure Monitor.

1. Proveďte konfiguraci shromážděnou pro posílání dat do agenta Log Analytics pro Linux pomocí modulu plug-in write_http.  
2. Nakonfigurujte agenta Log Analytics pro Linux, aby naslouchal pro shromážděná data na příslušném portu.
3. Restartujte shromažďování a Log Analytics agenta pro Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurace shromážděná na dopředná data 

1. Aby bylo možné směrovat shromážděná data do agenta Log Analytics pro Linux, `oms.conf` je nutné přidat do adresáře konfigurace ke shromáždění. Cíl tohoto souboru závisí na distribuce Linux vašeho počítače.

    Pokud je váš adresář shromážděné konfigurace umístěný v/etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Pokud je váš adresář shromážděné konfigurace umístěný v/etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Pro shromážděné verze před 5,5 bude nutné upravit značky v `oms.conf`, jak je uvedeno výše.
    >

2. Zkopírujte shromážděný. conf do konfiguračního adresáře omsagent požadovaného pracovního prostoru.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Pomocí následujících příkazů restartujte shromáždit a Log Analytics agenta pro Linux.

    Služba sudo se shromáždila restartování sudo restartování/opt/Microsoft/omsagent/bin/service_control

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Shromážděné metriky pro převod schématu Azure Monitor
Aby bylo možné udržovat známý model mezi metrikami infrastruktury již shromážděnými nástrojem Log Analytics Agent pro Linux a nové metriky shromážděné pomocí následujícího mapování schématu, je použito:

| Pole shromážděné metriky | Azure Monitor pole |
|:--|:--|
| `host` | Počítač |
| `plugin` | Žádné |
| `plugin_instance` | Název instance<br>Pokud má plugin_instance *hodnotu null* , pak klikněte na InstanceName = " *_Total*". |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Pokud má type_instance *hodnotu null* , pak CounterName =**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | Žádné |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení. 
* Použijte [vlastní pole](custom-fields.md) k analýze dat ze záznamů syslog do jednotlivých polí.
