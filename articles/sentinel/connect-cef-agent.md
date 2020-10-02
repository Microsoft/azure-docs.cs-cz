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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: a54dfa0f2b072d30cac605937a1b623ef9d4051d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631490"
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
- Je nutné, aby byl v počítači se systémem Linux nainstalován Python.<br>Použijte `python -version` příkaz pro kontrolu.
- Před instalací agenta Log Analytics nesmí být počítač se systémem Linux připojen k žádnému pracovnímu prostoru Azure.

## <a name="run-the-deployment-script"></a>Spuštění zaváděcího skriptu
 
1. V navigační nabídce Azure Sentinel klikněte na **datové konektory**. V seznamu konektorů klikněte na dlaždici **CEF (Common Event Format)** a pak na tlačítko **otevřít konektor** na pravé straně. 

1. V části **1,2 instalace kolekce CEF na počítači se systémem Linux**zkopírujte odkaz, který je k dispozici v části **spuštění následujícího skriptu pro instalaci a použití kolekce CEF**, nebo z následujícího textu:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Když je skript spuštěný, zkontrolujte, že nezískáváte žádné chybové zprávy ani upozornění.

> [!NOTE]
> **Použití stejného počítače pro přeposílání prostých zpráv syslog *a* CEF**
>
> Pokud máte v úmyslu použít tento počítač pro překládání protokolů k přeposílání [zpráv SYSLOG](connect-syslog.md) a také CEF, abyste se vyhnuli duplicitě událostí v tabulkách syslog a CommonSecurityLog:
>
> 1. Na každém zdrojovém počítači, který odesílá protokoly do služby pro přeposílání ve formátu CEF, je nutné upravit konfigurační soubor syslog a odebrat tak zařízení, která se používají k odesílání zpráv CEF. Zařízení, která jsou odesílána v CEF, nebudou také odesílána ve službě syslog. Podrobné pokyny k tomu, jak to udělat, najdete v tématu [Konfigurace protokolu syslog v agentovi Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Pokud chcete zakázat synchronizaci agenta s konfigurací syslog v Azure Sentinel, musíte na těchto počítačích spustit následující příkaz. Tím se zajistí, že se změna konfigurace, kterou jste provedli v předchozím kroku, nepřepíše.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Pokračujte [krokem 2: konfigurace řešení zabezpečení pro přeposílání zpráv CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Vysvětlení skriptu nasazení

Následuje popis akcí skriptu nasazení v následujícím příkazu.

Zvolením démona syslog zobrazíte příslušný popis.

# <a name="rsyslog-daemon"></a>[démon rsyslog](#tab/rsyslog)

1. **Stažení a instalace agenta Log Analytics:**

    - Stáhne instalační skript pro agenta Log Analytics (OMS) Linux.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Nainstaluje agenta Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Nastavení konfigurace agenta Log Analytics k naslouchání na portu 25226 a přeposílání zpráv CEF do služby Azure Sentinel:**

    - Stáhne konfiguraci z úložiště GitHub agenta Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
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

    - Zajišťuje, aby pole *počítač* ve zdroji syslog bylo správně namapováno v agentovi Log Analytics spuštěním tohoto příkazu a restartováním agenta.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[démon procesu syslog-ng](#tab/syslogng)

1. **Stažení a instalace agenta Log Analytics:**

    - Stáhne instalační skript pro agenta Log Analytics (OMS) Linux.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Nainstaluje agenta Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Nastavení konfigurace agenta Log Analytics k naslouchání na portu 25226 a přeposílání zpráv CEF do služby Azure Sentinel:**

    - Stáhne konfiguraci z úložiště GitHub agenta Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfiguruje se démon procesu Syslog:**

    - Otevře port 514 pro komunikaci TCP pomocí konfiguračního souboru syslog `/etc/syslog-ng/syslog-ng.conf` .

    - Nakonfiguruje démona k přeposílání zpráv CEF na agenta Log Analytics na portu TCP 25226 vložením speciálního konfiguračního souboru `security-config-omsagent.conf` do adresáře démona syslog `/etc/syslog-ng/conf.d/` .

        Obsah `security-config-omsagent.conf` souboru:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
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

    - Zajišťuje, aby pole *počítač* ve zdroji syslog bylo správně namapováno v agentovi Log Analytics spuštěním tohoto příkazu a restartováním agenta.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak nasadit agenta Log Analytics pro připojení zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).

