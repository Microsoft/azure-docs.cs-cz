---
title: Shromažďovat výstrahy Nagios a Zabbix ve službě Azure Monitor | Dokumentace Microsoftu
description: Nagios a Zabbix jsou open source nástroje pro monitorování. Můžete shromažďovat výstrahy z těchto nástrojů do služby Azure Monitor, aby bylo možné analyzovat taky výstrahy z jiných zdrojů.  Tento článek popisuje, jak nakonfigurovat agenta Log Analytics pro Linux ke shromažďování výstrah z těchto systémů.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: ac8e214df6b6990e2b27b5897350c85e0a944e0c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997953"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Shromažďovat výstrahy z řešení Nagios a Zabbix ve službě Azure Monitor z agenta Log Analytics pro Linux 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) a [Zabbix](http://www.zabbix.com/) jsou open source nástroje pro monitorování. Můžete shromažďovat výstrahy z těchto nástrojů do služby Azure Monitor, abyste mohli analyzovat data protokolů z jiných zdrojů.  Tento článek popisuje, jak nakonfigurovat agenta Log Analytics pro Linux ke shromažďování výstrah z těchto systémů.


> [!NOTE]
> [Upozornění vytvořená službou Azure Monitor](alerts-overview.md) jsou uloženy odděleně od dat protokolu a není přístupný z dotazů na protokoly.

 
## <a name="prerequisites"></a>Požadavky
Agenta Log Analytics pro Linux podporuje shromažďování údajů o výstrahy Nagios verzi 4.2.x a Zabbix verzi 2.x.

## <a name="configure-alert-collection"></a>Konfigurace shromažďování dat výstrah

### <a name="configuring-nagios-alert-collection"></a>Konfigurace shromažďování výstrah Nagios
Shromažďovat výstrahy, na serveru Nagios proveďte následující kroky.

1. Udělit uživateli **omsagent** přístup pro čtení do souboru protokolu Nagios `/var/log/nagios/nagios.log`. Za předpokladu, že soubor nagios.log je vlastněná touto skupinou `nagios`, je-li přidat uživatele **omsagent** k **nagios** skupiny. 

    sudo usermod - a -G nagios omsagent

2.  Upravte konfigurační soubor na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Ověřte, že následující položky jsou k dispozici a není komentářem navýšení kapacity:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Restartujte démona omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Konfigurace shromažďování výstrah Zabbix
Shromažďovat výstrahy z Zabbix serverem, budete muset zadat uživatele a heslo v *předáváním nešifrovaného textu*.  Když to není ideální, doporučujeme vytvořit Zabbix uživatele s oprávněními jen pro čtení k zachycení relevantní alarmy.

Shromažďovat výstrahy Nagios serveru, proveďte následující kroky.

1. Upravte konfigurační soubor na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Ujistěte se, že následující položky jsou k dispozici a není komentářem navýšení kapacity.  Změňte na hodnoty pro vaše prostředí Zabbix uživatelské jméno a heslo.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Restartujte démona omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Záznamy upozornění
Záznamy upozornění můžete načíst z řešení Nagios a Zabbix pomocí [protokolu dotazy](../log-query/log-query-overview.md) ve službě Azure Monitor.

### <a name="nagios-alert-records"></a>Výstrahy Nagios záznamů

Výstrahy mají záznamy shromážděné Nagios **typ** z **výstrah** a **SourceSystem** z **Nagios**.  V následující tabulce mají vlastnosti.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*Upozornění* |
| SourceSystem |*Nagios* |
| AlertName |Název výstrahy. |
| AlertDescription | Popis výstrahy. |
| AlertState | Stav hostitele nebo službu.<br><br>OK<br>UPOZORNĚNÍ<br>NAHORU<br>DOLŮ |
| název hostitele | Název hostitele, která upozornění vytvořila. |
| PriorityNumber | Úroveň priority výstrahy. |
| StateType | Typ stav výstrahy.<br><br>Konfigurace SOFT - problém, který nebyl znovu zkontrolují.<br>PEVNÉ – problém, který se znovu zkontrolují zadaného počtu opakování.  |
| TimeGenerated |Datum a čas, který byla výstraha vytvořena. |


### <a name="zabbix-alert-records"></a>Záznamy upozornění Zabbix
Výstrahy mají záznamy shromážděné Zabbix **typ** z **výstrah** a **SourceSystem** z **Zabbix**.  V následující tabulce mají vlastnosti.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*Upozornění* |
| SourceSystem |*Zabbix* |
| AlertName | Název výstrahy. |
| AlertPriority | Závažnost výstrahy.<br><br>není klasifikovaný<br>Informace<br>upozornění<br>průměr<br>Vysoká<br>po havárii  |
| AlertState | Stav výstrahy.<br><br>0 – stav je aktuální.<br>1 - stav není znám.  |
| AlertTypeNumber | Určuje, zda výstraha může vygenerovat několik událostí problém.<br><br>0 – stav je aktuální.<br>1 - stav není znám.    |
| Komentáře | Další komentáře pro výstrahu. |
| název hostitele | Název hostitele, která upozornění vytvořila. |
| PriorityNumber | Hodnota označuje závažnost výstrahy.<br><br>0 – nezařazených<br>1 – informace<br>2 – upozornění<br>3 – průměr<br>4 – vysoká<br>5 - po havárii |
| TimeGenerated |Datum a čas, který byla výstraha vytvořena. |
| TimeLastModified |Datum a čas, který byl naposled změněn stav výstrahy. |


## <a name="next-steps"></a>Další postup
* Další informace o [výstrahy](alerts-overview.md) ve službě Azure Monitor.
* Další informace o [protokolu dotazy](../log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 