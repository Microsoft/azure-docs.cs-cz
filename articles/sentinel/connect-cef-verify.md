---
title: Ověření připojení k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Ověřte připojení vašeho řešení zabezpečení a ujistěte se, že zprávy CeF jsou předávány do Azure Sentinelu.
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731820"
---
# <a name="step-3-validate-connectivity"></a>KROK 3: Ověření připojení

Po nasazení propředávání protokolu (v kroku 1) a nakonfigurován řešení zabezpečení tak, aby odesílat zprávy CEF (v kroku 2), postupujte podle těchto pokynů k ověření připojení mezi řešením zabezpečení a Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- Musíte mít zvýšená oprávnění (sudo) v počítači pro předávání protokolů.

- V počítači pro předávání protokolů musíte mít nainstalovaný Python.<br>
Pomocí `python –version` příkazu zkontrolujte.

## <a name="how-to-validate-connectivity"></a>Jak ověřit připojení

1. V navigační nabídce Azure Sentinel otevřete **protokoly**. Spusťte dotaz pomocí schématu **CommonSecurityLog** a zjistěte, zda přijímáte protokoly z vašeho řešení zabezpečení.<br>
Uvědomte si, že může trvat přibližně 20 minut, dokud se vaše protokoly začnou zobrazovat v **Log Analytics**. 

1. Pokud nevidíte žádné výsledky z dotazu, ověřte, že události jsou generovány z vašeho řešení zabezpečení, nebo zkuste některé generovat a ověřte, zda jsou předávány do počítače pro předávání Syslog, který jste určili. 

1. Spusťte následující skript na prodlanejždíprotokolprotokolu a zkontrolujte připojení mezi řešením zabezpečení, předávacím protokolem a Azure Sentinelem. Tento skript zkontroluje, že daemon naslouchá na správných portech, že předávání je správně nakonfigurováno a že nic neblokuje komunikaci mezi daemonem a agentem Log Analytics. Odesílá také falešné zprávy "TestCommonEventFormat" pro kontrolu připojení od konce do konce. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Vysvětlení ověřovacího skriptu

Ověřovací skript provádí následující kontroly:

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. Zkontroluje, zda je soubor<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existuje a je platný.

1. Zkontroluje, zda soubor obsahuje následující text:

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

1. Zkontroluje, zda v počítači nejsou nějaká vylepšení zabezpečení, která mohou blokovat síťový provoz (například bránu firewall hostitele).

1. Zkontroluje, zda je daemon syslog (rsyslog) správně nakonfigurován tak, aby odesílá zprávy, které identifikuje jako nástroj Provozit (pomocí regulárního výrazu) agentovi Log Analytics na portu TCP 25226:

    - Konfigurační soubor:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Zkontroluje, zda daemon syslogu přijímá data na portu 514.

1. Zkontroluje, zda jsou navázána potřebná připojení: tcp 514 pro příjem dat, tcp 25226 pro interní komunikaci mezi demonem syslogu a agentem Log Analytics.

1. Odešle data MOCK na port 514 na localhost. Tato data by měla být pozorovatelná v pracovním prostoru Azure Sentinel spuštěním následujícího dotazu:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Zkontroluje, zda je soubor<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existuje a je platný.

1. Zkontroluje, zda soubor obsahuje následující text:

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

1. Zkontroluje, zda v počítači nejsou nějaká vylepšení zabezpečení, která mohou blokovat síťový provoz (například bránu firewall hostitele).

1. Zkontroluje, zda je daemon syslog (syslog-ng) správně nakonfigurován tak, aby odesílá zprávy, které identifikuje jako Nástroj pro cef (pomocí regulárního výrazu) agentovi Analýzy protokolů na portu TCP 25226:

    - Konfigurační soubor:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Zkontroluje, zda daemon syslogu přijímá data na portu 514.

1. Zkontroluje, zda jsou navázána potřebná připojení: tcp 514 pro příjem dat, tcp 25226 pro interní komunikaci mezi demonem syslogu a agentem Log Analytics.

1. Odešle data MOCK na port 514 na localhost. Tato data by měla být pozorovatelná v pracovním prostoru Azure Sentinel spuštěním následujícího dotazu:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit zařízení CEF k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

