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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 07a6b84569fe0356267440e38b31ac738b2659d6
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260827"
---
# <a name="step-3-validate-connectivity"></a>Krok 3: ověření připojení

Po nasazení služby pro přeposílání protokolů (v kroku 1) a konfiguraci řešení zabezpečení pro posílání zpráv CEF (v kroku 2) postupujte podle těchto pokynů, abyste ověřili konektivitu mezi vaším řešením zabezpečení a službou Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- V počítači pro překládání protokolů musíte mít zvýšená oprávnění (sudo).

- V počítači se systémem pro překládání protokolů musíte mít nainstalovaný Python.<br>
Použijte `python –version` příkaz pro kontrolu.

## <a name="how-to-validate-connectivity"></a>Jak ověřit připojení

1. V nabídce navigace v Azure Sentinel otevřete **protokoly**. Spusťte dotaz pomocí schématu **CommonSecurityLog** a zjistěte, jestli přijímáte protokoly z řešení zabezpečení.<br>
Počítejte s tím, že může trvat přibližně 20 minut, než se vaše protokoly začnou zobrazovat v **Log Analytics**. 

1. Pokud nevidíte žádné výsledky z dotazu, ověřte, že se události generují z řešení zabezpečení, nebo zkuste některé z nich vygenerovat a ověřte, že jsou předávány do počítače pro přeposílání syslog, který jste určili. 

1. Spusťte následující skript na serveru pro přeposílání protokolů, abyste zkontrolovali konektivitu mezi řešením zabezpečení, serverem pro přeposílání protokolů a službou Azure Sentinel. Tento skript kontroluje, že démon naslouchá na správných portech, že předávání je správně nakonfigurované a že nic neblokuje komunikaci mezi démonem a agentem Log Analytics. Také pošle zprávy typu "TestCommonEventFormat" ke kontrole komplexního připojení. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Vysvětlený skript pro ověření

Ověřovací skript provede následující kontroly:

# <a name="rsyslog-daemon"></a>[démon rsyslog](#tab/rsyslog)

1. Kontroluje, zda soubor<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existuje a je platný.

1. Kontroluje, zda soubor obsahuje následující text:

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

1. Kontroluje, jestli jsou na počítači nějaká vylepšení zabezpečení, která by mohla blokovat síťový provoz (například hostitelskou bránu firewall).

1. Kontroluje, zda je démon procesu Syslog (rsyslog) správně nakonfigurován tak, aby odesílal zprávy, které identifikuje jako CEF (pomocí regulárního výrazu) k agentovi Log Analytics na portu TCP 25226:

    - Konfigurační soubor:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF"|"ASA"
            *.* @@127.0.0.1:25226

1. Kontroluje, zda démon procesu Syslog přijímá data na portu 514.

1. Kontroluje, zda jsou navázána potřebná připojení: TCP 514 pro příjem dat, TCP 25226 pro interní komunikaci mezi démonem syslog a agentem Log Analytics.

1. Odesílá z místního hostitele data na portu 514. Tato data by se měla pozorovat v pracovním prostoru Azure Sentinel spuštěním následujícího dotazu:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[démon procesu syslog-ng](#tab/syslogng)

1. Kontroluje, zda soubor<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    existuje a je platný.

1. Kontroluje, zda soubor obsahuje následující text:

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

1. Kontroluje, jestli jsou na počítači nějaká vylepšení zabezpečení, která by mohla blokovat síťový provoz (například hostitelskou bránu firewall).

1. Kontroluje, zda je démon procesu Syslog (syslog-ng) správně nakonfigurován tak, aby odesílal zprávy, které identifikuje jako CEF (pomocí regulárního výrazu) na agentovi Log Analytics na portu TCP 25226:

    - Konfigurační soubor:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Kontroluje, zda démon procesu Syslog přijímá data na portu 514.

1. Kontroluje, zda jsou navázána potřebná připojení: TCP 514 pro příjem dat, TCP 25226 pro interní komunikaci mezi démonem syslog a agentem Log Analytics.

1. Odesílá z místního hostitele data na portu 514. Tato data by se měla pozorovat v pracovním prostoru Azure Sentinel spuštěním následujícího dotazu:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit zařízení CEF ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

