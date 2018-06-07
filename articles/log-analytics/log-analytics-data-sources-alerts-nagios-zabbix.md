---
title: Shromažďovat výstrahy Nagios a Zabbix v OMS Log Analytics | Microsoft Docs
description: Nagios a Zabbix jsou nástroje pro sledování s otevřeným zdrojem. Můžete shromáždit výstrahy z těchto nástrojů do analýzy protokolů, chcete-li analyzovat, společně s výstrahy z jiných zdrojů.  Tento článek popisuje postup konfigurace agenta OMS pro Linux ke shromažďování výstrah z těchto systémů.
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
ms.date: 04/13/2018
ms.author: magoedte
ms.openlocfilehash: a34a4be75488aca46fe232331e4bac3e0ac414b0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637765"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>Shromažďovat výstrahy z Nagios a Zabbix v analýzy protokolů z OMS agenta pro Linux 
[Nagios](https://www.nagios.org/) a [Zabbix](http://www.zabbix.com/) jsou nástroje pro sledování s otevřeným zdrojem. Výstrahy můžete shromáždit z těchto nástrojů do analýzy protokolů, aby bylo možné analyzovat, spolu s [výstrahy z jiných zdrojů](log-analytics-alerts.md).  Tento článek popisuje postup konfigurace agenta OMS pro Linux ke shromažďování výstrah z těchto systémů.
 
## <a name="prerequisites"></a>Požadavky
Agent OMS pro Linux podporuje shromažďování výstrahy z Nagios verzi 4.2.x a Zabbix až verze 2.x.

## <a name="configure-alert-collection"></a>Konfigurace shromažďování výstrah

### <a name="configuring-nagios-alert-collection"></a>Konfigurace shromažďování výstrah Nagios
Shromažďovat výstrahy, proveďte následující kroky na serveru Nagios.

1. Udělit **omsagent** přístup pro čtení do souboru protokolu Nagios `/var/log/nagios/nagios.log`. Za předpokladu, že soubor nagios.log je vlastníkem skupiny `nagios`, můžete přidat uživatele **omsagent** k **nagios** skupiny. 

    sudo "usermod" - a -G nagios omsagent

2.  Upravte konfigurační soubor v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Ujistěte se, že následující položky jsou existuje a není komentáři se:  

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

3. Restartujte démon omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Konfigurace shromažďování výstrah Zabbix
Shromažďovat výstrahy ze serveru Zabbix, budete muset zadat uživatele a heslo v *text vymažte, pokud*.  Když není ideální, doporučujeme vytvoření Zabbix uživatele s oprávněními jen pro čtení k zachycení příslušné výstrahy.

Ke shromažďování výstrah na serveru Nagios, proveďte následující kroky.

1. Upravte konfigurační soubor v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Zkontrolujte, zda že následující položky jsou existuje a není komentáři limitu.  Změňte hodnoty pro vaše prostředí Zabbix uživatelské jméno a heslo.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Restartujte démon omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Výstrahy záznamů
Výstrahy záznamy můžete načíst z Nagios a Zabbix pomocí [protokolu hledání](log-analytics-log-searches.md) v analýzy protokolů.

### <a name="nagios-alert-records"></a>Výstraha Nagios záznamů

Výstrahy mají záznamy shromažďují Nagios **typ** z **výstrahy** a **SourceSystem** z **Nagios**.  V následující tabulce mají vlastnosti.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*Výstrahy* |
| SourceSystem |*Nagios* |
| AlertName |Název výstrahy. |
| AlertDescription | Popis výstrahy. |
| AlertState | Stav služby nebo hostitele.<br><br>OK<br>UPOZORNĚNÍ<br>NAHORU<br>DOLŮ |
| Název hostitele | Název hostitele, který vytvořili výstrahu. |
| PriorityNumber | Úroveň priority výstrahy. |
| StateType | Typ stav výstrahy.<br><br>Konfigurace SOFT - problém, který nebyl opakovaným zkontrolováním.<br>PEVNÝ - problém, který byl znovu zkontrolují zadaného počtu opakování.  |
| TimeGenerated |Datum a čas vytvoření výstrahy. |


### <a name="zabbix-alert-records"></a>Výstrahy záznamy Zabbix
Výstrahy mají záznamy shromažďují Zabbix **typ** z **výstrahy** a **SourceSystem** z **Zabbix**.  V následující tabulce mají vlastnosti.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*Výstrahy* |
| SourceSystem |*Zabbix* |
| AlertName | Název výstrahy. |
| AlertPriority | Závažnost výstrahy.<br><br>není klasifikovaný<br>Informace<br>upozornění<br>průměr<br>Vysoká<br>po havárii  |
| AlertState | Stav výstrahy.<br><br>0 – stav je aktuální.<br>1 - stav není znám.  |
| AlertTypeNumber | Určuje, zda výstraha může vygenerovat více událostí problém.<br><br>0 – stav je aktuální.<br>1 - stav není znám.    |
| Komentáře | Další poznámky pro výstrahu. |
| Název hostitele | Název hostitele, který vytvořili výstrahu. |
| PriorityNumber | Hodnota označující závažnost výstrahy.<br><br>0 – nezařazených<br>1 - informace<br>2 – upozornění<br>3 – průměr<br>4 – vysoká<br>5 - po havárii |
| TimeGenerated |Datum a čas vytvoření výstrahy. |
| TimeLastModified |Datum a čas, kdy byl naposledy změněn stav výstrahy. |


## <a name="next-steps"></a>Další postup
* Další informace o [výstrahy](log-analytics-alerts.md) v analýzy protokolů.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení. 
