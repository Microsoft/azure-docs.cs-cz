---
title: Shromažďovat data shromážděná v Azure Monitor | Microsoft Docs
description: Shromažďováno je open source démon pro Linux, který pravidelně shromažďuje data z aplikací a informací na úrovni systému.  Tento článek poskytuje informace o shromažďování dat z Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: a0efeaa3df0ecc69fa29dcb2cbb50874c4ab486a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610578"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Shromažďovat data shromážděná v agentech Linux v Azure Monitor
[Shromažďováno](https://collectd.org/) je open source démon pro Linux, který pravidelně shromažďuje metriky výkonu z aplikací a informací na úrovni systému. Příklady aplikací zahrnují prostředí Java Virtual Machine (JVM), MySQL server a Nginx. Tento článek poskytuje informace o shromažďování údajů o výkonu ze sběru Azure Monitor.

Úplný seznam dostupných modulů plug-in najdete v [tabulce modulů plug-in](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Shromážděný přehled](media/data-sources-collectd/overview.png)

Následující shromážděná konfigurace je součástí agenta Log Analytics pro Linux, aby mohla směrovat shromážděná data do agenta Log Analytics pro Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

Kromě toho, pokud použijete verze shromážděné před 5,5, použijte místo toho následující konfiguraci.

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

Shromážděná konfigurace používá výchozí `write_http` modul plug-in k odesílání dat metrik výkonu přes port 26000 pro Log Analytics agenta pro Linux. 

> [!NOTE]
> Tento port můžete v případě potřeby nakonfigurovat na vlastní port definovaný.

Agent Log Analytics pro Linux také naslouchá na portu 26000 pro shromážděné metriky a pak je převede na Azure Monitor metriky schématu. Následuje Log Analytics agenta pro konfiguraci systému Linux  `collectd.conf` .

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

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

1. Aby bylo možné směrovat shromážděná data do agenta Log Analytics pro Linux, je `oms.conf` nutné přidat do adresáře konfigurace ke shromáždění. Cíl tohoto souboru závisí na distribuce Linux vašeho počítače.

    Pokud je váš adresář shromážděné konfigurace umístěný v/etc/collectd.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    Pokud je váš adresář shromážděné konfigurace umístěný v/etc/collectd/collectd.conf.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >Pro shromážděné verze před 5,5 bude nutné upravit značky v `oms.conf` , jak je uvedeno výše.
    >

2. Zkopírujte shromážděný. conf do konfiguračního adresáře omsagent požadovaného pracovního prostoru.

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. Pomocí následujících příkazů restartujte shromáždit a Log Analytics agenta pro Linux.

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Shromážděné metriky pro převod schématu Azure Monitor
Aby bylo možné udržovat známý model mezi metrikami infrastruktury již shromážděnými nástrojem Log Analytics Agent pro Linux a nové metriky shromážděné pomocí následujícího mapování schématu, je použito:

| Pole shromážděné metriky | Azure Monitor pole |
|:--|:--|
| `host` | Počítač |
| `plugin` | Žádné |
| `plugin_instance` | Název instance<br>Pokud **plugin_instance** má *hodnotu null* , pak je hodnota InstanceName = "*_Total*". |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Pokud **type_instance** má *hodnotu null* , CounterName =**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | Žádné |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení. 
* Použijte [vlastní pole](../platform/custom-fields.md) k analýze dat ze záznamů syslog do jednotlivých polí.
