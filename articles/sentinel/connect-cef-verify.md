---
title: Ověřit připojení ke službě Azure Sentinel | Microsoft Docs
description: Ověřte připojení k řešení zabezpečení, abyste měli jistotu, že se zprávy CEF předávají do Azure Sentinel.
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
ms.openlocfilehash: f9fb1c917a0719cb9d250b997329d3415b5872eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98747470"
---
# <a name="step-3-validate-connectivity"></a>Krok 3: ověření připojení

Po nasazení služby pro přeposílání protokolů (v kroku 1) a konfiguraci řešení zabezpečení pro posílání zpráv CEF (v kroku 2) postupujte podle těchto pokynů, abyste ověřili konektivitu mezi vaším řešením zabezpečení a službou Azure Sentinel. 

## <a name="prerequisites"></a>Předpoklady

- V počítači pro překládání protokolů musíte mít zvýšená oprávnění (sudo).

- V počítači pro přeposílání protokolů musíte mít nainstalovaný **python 2,7** nebo **3** .<br>
Použijte `python –version` příkaz pro kontrolu.

- V určitém okamžiku tohoto procesu možná budete potřebovat ID pracovního prostoru a primární klíč pracovního prostoru. Můžete je najít v prostředku pracovního prostoru v části **Správa agentů**.

## <a name="how-to-validate-connectivity"></a>Jak ověřit připojení

1. V nabídce navigace v Azure Sentinel otevřete **protokoly**. Spusťte dotaz pomocí schématu **CommonSecurityLog** a zjistěte, jestli přijímáte protokoly z řešení zabezpečení.<br>
Počítejte s tím, že může trvat přibližně 20 minut, než se vaše protokoly začnou zobrazovat v **Log Analytics**. 

1. Pokud nevidíte žádné výsledky z dotazu, ověřte, že se události generují z řešení zabezpečení, nebo zkuste některé z nich vygenerovat a ověřte, že jsou předávány do počítače pro přeposílání syslog, který jste určili. 

1. Spusťte následující skript na serveru pro přeposílání protokolů (použití ID pracovního prostoru místo zástupného symbolu), abyste zkontrolovali možnosti připojení mezi řešením zabezpečení, serverem pro přeposílání protokolů a službou Azure Sentinel. Tento skript kontroluje, že démon naslouchá na správných portech, že předávání je správně nakonfigurované a že nic neblokuje komunikaci mezi démonem a agentem Log Analytics. Také pošle zprávy typu "TestCommonEventFormat" ke kontrole komplexního připojení. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Může se vám zobrazit zpráva s přímým spuštěním příkazu pro opravu problému s **mapováním pole *počítač***. Podrobnosti najdete [v popisu v ověřovacím skriptu](#mapping-command) .

    - Může se vám zobrazit zpráva s přímým spuštěním příkazu pro opravu problému s **analýzou protokolů brány firewall Cisco ASA**. Podrobnosti najdete [v popisu v ověřovacím skriptu](#parsing-command) .

## <a name="validation-script-explained"></a>Vysvětlený skript pro ověření

Ověřovací skript provede následující kontroly:

# <a name="rsyslog-daemon"></a>[démon rsyslog](#tab/rsyslog)

1. Kontroluje, zda soubor<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existuje a je platný.

1. Kontroluje, zda soubor obsahuje následující text:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Kontroluje, jestli je analýza událostí firewallu Cisco ASA nakonfigurovaná podle očekávání, pomocí následujícího příkazu: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Pokud dojde k potížím s analýzou, skript vytvoří chybovou zprávu, která vás přesměruje na **Ruční spuštění následujícího příkazu** (místo zástupného symbolu použije ID pracovního prostoru). Příkaz zajistí správné analýzy a restart agenta.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Kontroluje, zda je pole *počítač* ve zdroji syslog správně mapováno v agentovi Log Analytics pomocí následujícího příkazu: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Pokud dojde k potížím s mapováním, skript vytvoří chybovou zprávu s přímým přístupem k **ručnímu spuštění následujícího příkazu** (místo zástupného symbolu použije ID pracovního prostoru). Příkaz ověří správné mapování a restartuje agenta.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Kontroluje, jestli jsou na počítači nějaká vylepšení zabezpečení, která by mohla blokovat síťový provoz (například hostitelskou bránu firewall).

1. Kontroluje, zda je démon procesu Syslog (rsyslog) správně nakonfigurován pro odesílání zpráv (které identifikuje jako CEF) na agenta Log Analytics na portu TCP 25226:

    - Konfigurační soubor: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Restartuje démon procesu Syslog a agenta Log Analytics:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Kontroluje, zda jsou navázána potřebná připojení: TCP 514 pro příjem dat, TCP 25226 pro interní komunikaci mezi démonem syslog a agentem Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Kontroluje, zda démon procesu Syslog přijímá data na portu 514 a že agent přijímá data na portu 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Odesílá z místního hostitele data na portu 514. Tato data by se měla pozorovat v pracovním prostoru Azure Sentinel spuštěním následujícího dotazu:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[démon procesu syslog-ng](#tab/syslogng)

1. Kontroluje, zda soubor<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existuje a je platný.

1. Kontroluje, zda soubor obsahuje následující text:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Kontroluje, jestli je analýza událostí firewallu Cisco ASA nakonfigurovaná podle očekávání, pomocí následujícího příkazu: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Pokud dojde k potížím s analýzou, skript vytvoří chybovou zprávu, která vás přesměruje na **Ruční spuštění následujícího příkazu** (místo zástupného symbolu použije ID pracovního prostoru). Příkaz zajistí správné analýzy a restart agenta.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Kontroluje, zda je pole *počítač* ve zdroji syslog správně mapováno v agentovi Log Analytics pomocí následujícího příkazu: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Pokud dojde k potížím s mapováním, skript vytvoří chybovou zprávu s přímým přístupem k **ručnímu spuštění následujícího příkazu** (místo zástupného symbolu použije ID pracovního prostoru). Příkaz ověří správné mapování a restartuje agenta.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Kontroluje, jestli jsou na počítači nějaká vylepšení zabezpečení, která by mohla blokovat síťový provoz (například hostitelskou bránu firewall).

1. Kontroluje, zda je démon procesu Syslog (syslog-ng) správně nakonfigurován tak, aby odesílal zprávy, které identifikuje jako CEF (pomocí regulárního výrazu) na agentovi Log Analytics na portu TCP 25226:

    - Konfigurační soubor: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Restartuje démon procesu Syslog a agenta Log Analytics:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Kontroluje, zda jsou navázána potřebná připojení: TCP 514 pro příjem dat, TCP 25226 pro interní komunikaci mezi démonem syslog a agentem Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Kontroluje, zda démon procesu Syslog přijímá data na portu 514 a že agent přijímá data na portu 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Odesílá z místního hostitele data na portu 514. Tato data by se měla pozorovat v pracovním prostoru Azure Sentinel spuštěním následujícího dotazu:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
