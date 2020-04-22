---
title: Nasazení propředávání protokolu pro připojení dat CeF k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Zjistěte, jak nasadit agenta pro připojení dat CeF k Azure Sentinelu.
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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731646"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Krok 1: Nasazení předávání protokolu


V tomto kroku určíte a nakonfigurujete počítač s Linuxem, který přepošle protokoly z vašeho řešení zabezpečení do pracovního prostoru Azure Sentinelu. Tento počítač může být fyzický nebo virtuální počítač ve vašem místním prostředí, virtuální počítač Azure nebo virtuální počítač v jiném cloudu. Pomocí poskytnutého odkazu spustíte skript na určeném počítači, který provádí následující úlohy:
- Nainstaluje agenta Log Analytics pro Linux (označovaný také jako agent OMS) a nakonfiguruje ho pro následující účely:
    - naslouchání zpráv CEF od vestavěného daemonu Linux Syslog na portu TCP 25226
    - bezpečné odesílání zpráv přes TLS do pracovního prostoru Azure Sentinelu, kde jsou analyzovány a obohaceny

- Konfiguruje vestavěného daemona Linux Syslog (rsyslog.d/syslog-ng) pro následující účely:
    - naslouchání zpráv Syslog z vašich bezpečnostních řešení na portu TCP 514
    - předávání pouze zprávy, které identifikuje jako CEF agenta Log Analytics na localhost pomocí portu TCP 25226
 
## <a name="prerequisites"></a>Požadavky

- Musíte mít zvýšená oprávnění (sudo) na určeném počítači s Linuxem.
- Musíte mít python nainstalovaný na počítači s Linuxem.<br>Pomocí `python -version` příkazu zkontrolujte.
- Počítač s Linuxem nesmí být připojen k žádné pracovní prostory Azure před instalací agenta Log Analytics.

## <a name="run-the-deployment-script"></a>Spuštění skriptu nasazení
 
1. V navigační nabídce Azure Sentinel klikněte na **Datové konektory**. V seznamu konektorů klikněte na dlaždici **Společný formát událostí (CEF)** a potom na tlačítko **Otevřít stránku konektoru** vpravo dole. 

1. V **části 1.2 Nainstalujte kolektor CeF na počítač i- Linux**, zkopírujte odkaz uvedený v části Spustit následující skript pro instalaci a použití **kolektoru CeF**nebo z následujícího textu:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Během spuštění skriptu zkontrolujte, zda se nezobrazí žádné chybové nebo varovné zprávy.

Pokračujte [krokem 2: Nakonfigurujte bezpečnostní řešení tak, aby přesměrovává zprávy CeF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Skript nasazení vysvětlený

Následuje příkaz-by-command popis akcí skriptu nasazení.

Zvolte daemon syslog uvidíte příslušný popis.

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **Stažení a instalace agenta Log Analytics:**

    - Stáhne instalační skript pro agenta Linuxu Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Nainstaluje agenta analýzy protokolů.<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurace daemonu Syslog:**

    1. Otevře port 514 pro komunikaci TCP pomocí `/etc/rsyslog.conf`konfiguračního souboru syslogu .

    1. Nakonfiguruje daemon předávání zpráv nástroje Sdílení cen pro sdílení dat agentovi Log Analytics `security-config-omsagent.conf` na portu TCP 25226 vložením speciálního konfiguračního souboru do adresáře daemonu syslogu `/etc/rsyslog.d/`.

        Obsah souboru: `security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Restartování daemonu Syslog**

    `service rsyslog restart`

1. **Nastavení konfigurace agenta Log Analytics tak, aby naslouchala na portu 25226 a přesměrovává zprávy Nástroje pro obnovení služby Do Azure Sentinelu**

    1. Stáhne konfiguraci z úložiště GitHub agenta Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Restartuje agenta analýzy protokolů.<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **Stažení a instalace agenta Log Analytics:**

    - Stáhne instalační skript pro agenta Linuxu Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Nainstaluje agenta analýzy protokolů.<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurace daemonu Syslog:**

    1. Otevře port 514 pro komunikaci TCP pomocí `/etc/syslog-ng/syslog-ng.conf`konfiguračního souboru syslogu .

    1. Nakonfiguruje daemon předávání zpráv nástroje Sdílení cen pro sdílení dat agentovi Log Analytics `security-config-omsagent.conf` na portu TCP 25226 vložením speciálního konfiguračního souboru do adresáře daemonu syslogu `/etc/syslog-ng/conf.d/`.

        Obsah souboru: `security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Restartování daemonu Syslog**

    `service syslog-ng restart`

1. **Nastavení konfigurace agenta Log Analytics tak, aby naslouchala na portu 25226 a přesměrovává zprávy Nástroje pro obnovení služby Do Azure Sentinelu**

    1. Stáhne konfiguraci z úložiště GitHub agenta Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Restartuje agenta analýzy protokolů.<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak nasadit agenta Analýzy protokolů pro připojení zařízení CeF k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).

