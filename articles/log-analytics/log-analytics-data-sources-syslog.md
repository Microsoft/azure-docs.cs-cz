---
title: Shromažďovat a analyzovat zprávy Syslog v OMS Log Analytics | Dokumentace Microsoftu
description: Syslog je protokol protokolování událostí, které jsou společné pro Linux. Tento článek popisuje, jak nakonfigurovat kolekce zprávy Syslog v Log Analytics a podrobnosti o záznamy, které vytvářejí v úložišti OMS.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 3b1724853b4c874a1482a13c4de0ccb179f52f98
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041099"
---
# <a name="syslog-data-sources-in-log-analytics"></a>Zdroje dat protokolu Syslog v Log Analytics
Syslog je protokol protokolování událostí, které jsou společné pro Linux.  Aplikace odešle zprávy, které mohou být uložené na místním počítači nebo doručí do kolekcí Syslog.  Při instalaci agenta OMS pro Linux, konfiguruje místní démona Syslog na předávání zpráv k agentovi.  Agent pak odešle zprávu do Log Analytics, ve kterém se vytvoří odpovídající záznam v úložišti OMS.  

> [!NOTE]
> Log Analytics podporuje sadu zpráv odeslaných rsyslog nebo syslog-ng, kde je rsyslog démona výchozí. Démon procesu syslog výchozí verze 5 Red Hat Enterprise Linux, CentOS a Oracle Linux verze (sysklog) není podporována pro shromažďování událostí protokolu syslog. Pro shromažďování syslogu z této verze těchto distribuce [používá proces démona rsyslogu](http://rsyslog.com) by měla být nainstalovaná a nakonfigurovaná pro nahrazení sysklog.
>
>

![Shromažďování Syslogu](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Konfigurace Syslog
Agenta OMS pro Linux bude shromažďovat pouze události s zařízení a závažnosti, které jsou uvedeny v jeho konfiguraci.  Syslog můžete nakonfigurovat prostřednictvím webu Azure portal nebo Správa konfiguračních souborů na agenty Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurovat Syslog na webu Azure Portal
Konfigurovat Syslog z [dat nabídky v Log Analytics Upřesnit nastavení](log-analytics-data-sources.md#configuring-data-sources).  Tato konfigurace se doručí do konfiguračního souboru na každého agenta pro Linux.

Můžete přidat nové zařízení tak, že zadáte jeho název a kliknete **+**.  Pro každé zařízení nebudou se shromažďovat pouze zprávy s vybranou závažnosti.  Zaškrtněte závažnosti pro konkrétní zařízení, která chcete shromažďovat.  Nelze zadat žádná další kritéria filtru zpráv.

![Konfigurovat Syslog](media/log-analytics-data-sources-syslog/configure.png)

Standardně jsou všechny změny konfigurace automaticky nahrány do všech agentů.  Pokud chcete ručně konfigurovat Syslog na každého agenta pro Linux, poté zrušte zaškrtnutí políčka *použít uvedenou konfiguraci u mých Linuxových počítačů*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurace Syslog na agenta pro Linux
Když [instalaci agenta OMS v klientském počítači Linux](log-analytics-linux-agents.md), nainstaluje výchozí konfigurační soubor procesu syslog, který definuje zařízení a závažnost zpráv, které byly shromážděny.  Můžete upravit tento soubor a změňte konfiguraci.  Konfigurační soubor se liší v závislosti na démon procesu Syslog, který se klient nainstaloval.

> [!NOTE]
> Pokud upravíte konfiguraci syslogu, je nutné restartovat démon procesu syslog, aby se změny projevily.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurační soubor pro rsyslog nachází ve **/etc/rsyslog.d/95-omsagent.conf**.  Níže se zobrazují výchozí obsah.  To shromažďuje syslog zpráv odeslaných z místního agenta pro všechna zařízení s úrovní varování nebo vyšší.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Zařízení můžete odebrat odstraněním jeho oddílu konfiguračního souboru.  Můžete omezit závažnosti, které se shromažďují pro konkrétní zařízení tak, že upravíte položka tohoto zařízení.  Například pro omezení zařízení uživatele na zprávy s závažnost chyby nebo vyšší upravíte tento řádek v souboru konfigurace následujícím:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog-ng
Konfigurační soubor pro syslog-ng je umístění, od **/etc/syslog-ng/syslog-ng.conf**.  Níže se zobrazují výchozí obsah.  To shromažďuje syslog zpráv odeslaných z místního agenta pro všechna zařízení a všechny závažnosti.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Zařízení můžete odebrat odstraněním jeho oddílu konfiguračního souboru.  Můžete omezit závažnosti, které se shromažďují pro konkrétní zařízení tak, že odeberete ze seznamu.  Například pokud chcete omezit zařízení uživatele na právě upozornění a kritické zprávy, upravíte tohoto oddílu konfiguračního souboru takto:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Shromažďování dat z další porty Syslog
Agenta OMS přijímá zprávy Syslog na portu 25224 místního klienta.  Při instalaci agenta je výchozí konfigurace syslog použít a najdete v následujícím umístění:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Číslo portu můžete změnit tak, že vytvoříte dva konfigurační soubory: konfigurační soubor FluentD a soubor rsyslog nebo syslog-ng v závislosti na tom jste si nainstalovali démon procesu Syslog.  

* Konfigurační soubor FluentD by měl být nový soubor umístěný v: `/etc/opt/microsoft/omsagent/conf/omsagent.d` a nahraďte hodnoty v **port** položka se vaše vlastní číslo portu.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Pro rsyslog, měli vytvořit nový konfigurační soubor umístěný v: `/etc/rsyslog.d/` a nahraďte hodnotu % SYSLOG_PORT vaše vlastní číslo portu.  

    > [!NOTE]
    > Pokud upravíte tuto hodnotu v konfiguračním souboru `95-omsagent.conf`, budou přepsány, pokud agent použije výchozí konfiguraci.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Konfigurace syslog-ng by měl být upraven tak, že zkopírujete Ukázková konfigurace je uvedeno níže, a přidáte vlastní upravené nastavení za účelem syslog ng.conf konfigurační soubor umístěný ve `/etc/syslog-ng/`.  Proveďte **není** použít výchozí popisek **% WORKSPACE_ID % _oms** nebo **% WORKSPACE_ID_OMS**, definovat vlastní popisek, rozeznali provedené změny.  

    > [!NOTE]
    > Pokud změníte výchozí hodnoty v konfiguračním souboru, budou přepsány při agent použije výchozí konfiguraci.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Po dokončení změny, Syslogu a agenta OMS služba potřebuje restartovat, aby zajistěte, aby že změny konfigurace projevily.   

## <a name="syslog-record-properties"></a>Vlastnosti záznamu Syslog
Záznamy Syslogu mají typ **Syslog** a mít vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Počítač, který událost byla shromážděna z. |
| Zařízení |Definuje části systému, který vygeneroval zprávu. |
| HostIP |IP adresa systému posílání zprávy. |
| název hostitele |Název systému posílání zprávy. |
| SeverityLevel |Úroveň závažnosti události. |
| SyslogMessage |Text zprávy. |
| ProcessID |ID procesu, který se vygeneroval zprávu. |
| čas události |Datum a čas, která byla vygenerována událost. |

## <a name="log-queries-with-syslog-records"></a>Protokol dotazů s záznamy Syslogu
Následující tabulka obsahuje příklady různých dotazů na protokoly, které načítají záznamy Syslogu.

| Dotaz | Popis |
|:--- |:--- |
| Syslog |Všechny Syslogy. |
| Syslog &#124; kde SeverityLevel == "Chyba" |Všechny záznamy Syslogu s závažnost chyby. |
| Syslog &#124; summarize AggregatedValue = count() podle počítače |Počet Syslog záznamy podle počítače. |
| Syslog &#124; summarize AggregatedValue = count() podle zařízení |Počet Syslog záznamy podle zařízení. |

## <a name="next-steps"></a>Další postup
* Další informace o [prohledávání protokolů](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.
* Použití [vlastní pole](log-analytics-custom-fields.md) analyzovat data ze záznamů protokolu syslog do jednotlivých polí.
* [Konfigurace agentů systému Linux](log-analytics-linux-agents.md) ke shromažďování dalších typů dat.
