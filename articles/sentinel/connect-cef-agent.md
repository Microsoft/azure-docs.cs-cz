---
title: Nasazení služby pro překládání protokolů pro připojení dat CEF ke službě Azure Sentinel | Microsoft Docs
description: Naučte se, jak nasadit agenta pro připojení dat CEF k Sentinel Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: 8261856598a155e97f90ea350cedcd4c10e6893c
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747302"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Krok 1: nasazení serveru pro překládání protokolů


V tomto kroku určíte a nakonfigurujete počítač se systémem Linux, který přepošle protokoly z řešení zabezpečení do vašeho pracovního prostoru služby Azure Sentinel. Tento počítač může být fyzickým nebo virtuálním počítačem v místním prostředí, VIRTUÁLNÍm počítači Azure nebo VIRTUÁLNÍm počítači v jiném cloudu. Pomocí zadaného odkazu spustíte skript na určeném počítači, který provede následující úlohy:

- Nainstaluje agenta Log Analytics pro Linux (označuje se také jako agent OMS) a nakonfiguruje ho pro následující účely:
    - poslouchání zpráv CEF z integrovaného démona systému Linux syslog na portu TCP 25226
    - zabezpečená posílání zpráv přes protokol TLS do vašeho pracovního prostoru Sentinel Azure, kde se analyzují a obohaceny

- Konfiguruje integrovaný démon procesu Linux syslog (rsyslog. d/syslog-ng) pro následující účely:
    - naslouchání zpráv syslog z řešení zabezpečení na portu TCP 514
    - předávání pouze zpráv, které identifikuje jako CEF agenta Log Analytics na localhost pomocí portu TCP 25226
 
## <a name="prerequisites"></a>Požadavky

- Na určeném počítači se systémem Linux musíte mít zvýšená oprávnění (sudo).

- Na počítači se systémem Linux musí být nainstalován **python 2,7** nebo **3** .<br>Použijte `python -version` příkaz pro kontrolu.

- Před instalací agenta Log Analytics nesmí být počítač se systémem Linux připojen k žádnému pracovnímu prostoru Azure.

- Počítač se systémem Linux musí mít minimálně **4 jádra procesoru a 8 GB paměti RAM**.

    > [!NOTE]
    > - Jeden počítač pro přeposílání protokolů využívající démon **rsyslog** má podporovanou kapacitu **až 8500 událostí za sekundu (EPS)** .

- V určitém okamžiku tohoto procesu možná budete potřebovat ID pracovního prostoru a primární klíč pracovního prostoru. Můžete je najít v prostředku pracovního prostoru v části **Správa agentů**.

## <a name="run-the-deployment-script"></a>Spuštění zaváděcího skriptu
 
1. V navigační nabídce Azure Sentinel klikněte na **datové konektory**. V seznamu konektorů klikněte na dlaždici **CEF (Common Event Format)** a pak na tlačítko **otevřít konektor** na pravé straně. 

1. V části **1,2 instalace kolekce CEF na počítači se systémem Linux** zkopírujte odkaz, který je k dispozici v části **spuštění následujícího skriptu pro instalaci a použití kolekce CEF**, nebo z následujícího textu (místo zástupných symbolů použijte ID pracovního prostoru a primární klíč):

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```

1. Když je skript spuštěný, zkontrolujte, že nezískáváte žádné chybové zprávy ani upozornění.
    - Může se vám zobrazit zpráva s přímým spuštěním příkazu pro opravu problému s mapováním pole *počítač* . Podrobnosti najdete v tématu věnovaném [vysvětlení ve skriptu nasazení](#mapping-command) .

1. Pokračujte [krokem 2: konfigurace řešení zabezpečení pro přeposílání zpráv CEF](connect-cef-solution-config.md).


> [!NOTE]
> **Použití stejného počítače pro přeposílání prostých zpráv syslog *a* CEF**
>
> Pokud máte v úmyslu použít tento počítač pro překládání protokolů k přeposílání [zpráv SYSLOG](connect-syslog.md) a také CEF, abyste se vyhnuli duplicitě událostí v tabulkách syslog a CommonSecurityLog:
>
> 1. Na každém zdrojovém počítači, který odesílá protokoly do služby pro přeposílání ve formátu CEF, je nutné upravit konfigurační soubor syslog a odebrat tak zařízení, která se používají k odesílání zpráv CEF. Zařízení, která jsou odesílána v CEF, nebudou také odesílána ve službě syslog. Podrobné pokyny k tomu, jak to udělat, najdete v tématu [Konfigurace protokolu syslog v agentovi Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Pokud chcete zakázat synchronizaci agenta s konfigurací syslog v Azure Sentinel, musíte na těchto počítačích spustit následující příkaz. Tím se zajistí, že se změna konfigurace, kterou jste provedli v předchozím kroku, nepřepíše.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

> [!NOTE]
> **Změna zdroje pole TimeGenerated**
>
> - Ve výchozím nastavení agent Log Analytics naplní pole *TimeGenerated* ve schématu časem, kdy agent přijal událost z démona syslog. Proto se ve službě Azure Sentinel nezaznamená čas vygenerování události ve zdrojovém systému.
>
> - Můžete ale spustit následující příkaz, který bude stahovat a spouštět `TimeGenerated.py` skript. Tento skript nakonfiguruje agenta Log Analytics k naplnění pole *TimeGenerated* pomocí původního času události v jeho zdrojovém systému místo času, kdy ho agent přijal.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="deployment-script-explained"></a>Vysvětlení skriptu nasazení

Následuje popis akcí skriptu nasazení v následujícím příkazu.

Zvolením démona syslog zobrazíte příslušný popis.

# <a name="rsyslog-daemon"></a>[démon rsyslog](#tab/rsyslog)

1. **Stažení a instalace agenta Log Analytics:**

    - Stáhne instalační skript pro agenta Log Analytics (OMS) Linux.

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Nainstaluje agenta Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Nastavení konfigurace agenta Log Analytics k naslouchání na portu 25226 a přeposílání zpráv CEF do služby Azure Sentinel:**

    - Stáhne konfiguraci z úložiště GitHub agenta Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfiguruje se démon procesu Syslog:**

    - Otevře port 514 pro komunikaci TCP pomocí konfiguračního souboru syslog `/etc/rsyslog.conf` .

    - Nakonfiguruje démona k přeposílání zpráv CEF na agenta Log Analytics na portu TCP 25226 vložením speciálního konfiguračního souboru `security-config-omsagent.conf` do adresáře démona syslog `/etc/rsyslog.d/` .

        Obsah `security-config-omsagent.conf` souboru:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Restartování procesu démona syslog a agenta Log Analytics:**

    - Restartuje démon rsyslog.
    
        ```bash
        service rsyslog restart
        ```

    - Restartuje agenta Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Ověřování mapování pole *počítače* podle očekávání:**

    - Zajišťuje, aby pole *počítač* ve zdroji syslog bylo správně namapováno v agentu Log Analytics pomocí následujícího příkazu: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Pokud dojde k potížím s mapováním, skript vytvoří chybovou zprávu s přímým přístupem k **ručnímu spuštění následujícího příkazu** (místo zástupného symbolu použije ID pracovního prostoru). Příkaz ověří správné mapování a restartuje agenta.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[démon procesu syslog-ng](#tab/syslogng)

1. **Stažení a instalace agenta Log Analytics:**

    - Stáhne instalační skript pro agenta Log Analytics (OMS) Linux.

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Nainstaluje agenta Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Nastavení konfigurace agenta Log Analytics k naslouchání na portu 25226 a přeposílání zpráv CEF do služby Azure Sentinel:**

    - Stáhne konfiguraci z úložiště GitHub agenta Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfiguruje se démon procesu Syslog:**

    - Otevře port 514 pro komunikaci TCP pomocí konfiguračního souboru syslog `/etc/syslog-ng/syslog-ng.conf` .

    - Nakonfiguruje démona k přeposílání zpráv CEF na agenta Log Analytics na portu TCP 25226 vložením speciálního konfiguračního souboru `security-config-omsagent.conf` do adresáře démona syslog `/etc/syslog-ng/conf.d/` .

        Obsah `security-config-omsagent.conf` souboru:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Restartování procesu démona syslog a agenta Log Analytics:**

    - Restartuje démon syslog-ng.
    
        ```bash
        service syslog-ng restart
        ```

    - Restartuje agenta Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Ověřování mapování pole *počítače* podle očekávání:**

    - Zajišťuje, aby pole *počítač* ve zdroji syslog bylo správně namapováno v agentu Log Analytics pomocí následujícího příkazu: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Pokud dojde k potížím s mapováním, skript vytvoří chybovou zprávu s přímým přístupem k **ručnímu spuštění následujícího příkazu** (místo zástupného symbolu použije ID pracovního prostoru). Příkaz ověří správné mapování a restartuje agenta.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak nasadit agenta Log Analytics pro připojení zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).
