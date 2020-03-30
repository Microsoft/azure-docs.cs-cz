---
title: Shromažďování a analýza zpráv Syslog v Azure Monitoru | Dokumenty společnosti Microsoft
description: Syslog je protokol protokolování událostí, který je společný pro Linux. Tento článek popisuje, jak nakonfigurovat kolekci zpráv Syslog v Log Analytics a podrobnosti o záznamy, které vytvářejí.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274720"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Zdroje dat Syslogu ve službě Azure Monitor
Syslog je protokol protokolování událostí, který je společný pro Linux. Aplikace budou odesílat zprávy, které mohou být uloženy v místním počítači nebo doručeny do kolektoru Syslog. Když je nainstalován agent Log Analytics pro Linux, nakonfiguruje místního daemona Syslogu tak, aby přesměrovává zprávy agentovi. Agent pak odešle zprávu azure monitoru, kde je vytvořen odpovídající záznam.  

> [!NOTE]
> Azure Monitor podporuje shromažďování zpráv odeslaných rsyslog nebo syslog-ng, kde rsyslog je výchozí daemon. Výchozí daemon syslogu ve verzi 5 verze Red Hat Enterprise Linux, CentOS a Oracle Linux verze (sysklog) není podporován pro shromažďování událostí syslogu. Chcete-li shromažďovat data syslogu z této verze těchto distribucí, měl by být nainstalován a nakonfigurován [daemon rsyslog,](http://rsyslog.com) který nahradí sysklog.
>
>

![Kolekce Syslog](media/data-sources-syslog/overview.png)

S kolektorem Syslog jsou podporována následující zařízení:

* Kern
* uživatel
* pošta
* Daemon
* Auth
* syslog
* Lpr
* news
* Uucp
* Cron
* authpriv
* ftp
* local0-local7

Pro jakékoli jiné zařízení [nakonfigurujte vlastní protokoly zdroje dat](data-sources-custom-logs.md) v Azure Monitoru.
 
## <a name="configuring-syslog"></a>Konfigurace syslogu
Agent Log Analytics pro Linux bude shromažďovat pouze události s vybavením a závažnostmi, které jsou určeny v jeho konfiguraci. Syslog můžete nakonfigurovat prostřednictvím portálu Azure nebo správou konfiguračních souborů na agentech Linuxu.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurace syslogu na webu Azure Portal
Konfigurace protokolu Syslog z [nabídky Data v rozšířeném nastavení](agent-data-sources.md#configuring-data-sources). Tato konfigurace je dodávána do konfiguračního souboru u každého agenta Linuxu.

Můžete přidat nové zařízení nejprve výběrem **možnosti Použít pod konfigurací na mých počítačích** a pak zadejte jeho název a klepněte na tlačítko **+**. Pro každé zařízení budou shromažďovány pouze zprávy s vybranými závažnostmi.  Zkontrolujte závažnost i pro konkrétní zařízení, které chcete sbírat. Pro filtrování zpráv nelze zadat žádná další kritéria.

![Konfigurace syslogu](media/data-sources-syslog/configure.png)

Ve výchozím nastavení jsou všechny změny konfigurace automaticky odesílány všem agentům. Pokud chcete nakonfigurovat Syslog ručně na každém agentovi Linuxu, pak zrušit zaškrtnutí *políčka Použít níže konfigurace na mých počítačích*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurace agenta Syslog u Linuxu
Když [je agent Log Analytics nainstalován v klientovi Linuxu](../../azure-monitor/learn/quick-collect-linux-computer.md), nainstaluje výchozí konfigurační soubor syslogu, který definuje zařízení a závažnost zpráv, které jsou shromažďovány. Tento soubor můžete upravit a změnit konfiguraci. Konfigurační soubor se liší v závislosti na daemonu Syslog, který má klient nainstalován.

> [!NOTE]
> Pokud upravíte konfiguraci syslogu, je nutné restartovat daemon syslogu, aby se změny projevily.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurační soubor pro rsyslog je umístěn na **adrese /etc/rsyslog.d/95-omsagent.conf**. Jeho výchozí obsah je uveden níže. To shromažďuje zprávy syslog odeslané od místního agenta pro všechna zařízení s úrovní upozornění nebo vyšší.

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

Zařízení můžete odebrat odebráním jeho části konfiguračního souboru. Závažnosti, které jsou shromažďovány pro určité zařízení, můžete omezit úpravou vstupu tohoto zařízení. Chcete-li například omezit zařízení uživatele na zprávy se závažností chyby nebo vyšší, upravte tento řádek konfiguračního souboru na následující:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
Konfigurační soubor pro syslog-ng je umístění na **/etc/syslog-ng/syslog-ng.conf**.  Jeho výchozí obsah je uveden níže. To shromažďuje syslog zprávy odeslané od místního agenta pro všechna zařízení a všechny závažnosti.   

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

Zařízení můžete odebrat odebráním jeho části konfiguračního souboru. Můžete omezit závažnosti, které jsou shromažďovány pro konkrétní zařízení jejich odebráním ze seznamu.  Chcete-li například omezit zařízení uživatele pouze na výstrahy a kritické zprávy, upravte tuto část konfiguračního souboru na následující:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Shromažďování dat z dalších portů Syslog
Agent Log Analytics naslouchá zprávy Syslog na místním klientovi na portu 25224.  Po instalaci agenta je použita výchozí konfigurace syslogu a nalezena v následujícím umístění:

* Rsyslog:`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng:`/etc/syslog-ng/syslog-ng.conf`

Číslo portu můžete změnit vytvořením dvou konfiguračních souborů: konfiguračního souboru FluentD a souboru rsyslog-or-syslog-ng v závislosti na daemonu Syslog, který jste nainstalovali.  

* Konfigurační soubor FluentD by měl `/etc/opt/microsoft/omsagent/conf/omsagent.d` být nový soubor umístěný v: a nahradit hodnotu v položce **portu** vlastním číslem portu.

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

* V případě rsyslogu byste měli vytvořit `/etc/rsyslog.d/` nový konfigurační soubor umístěný v: a nahradit hodnotu %SYSLOG_PORT% vlastním číslem portu.  

    > [!NOTE]
    > Pokud změníte tuto hodnotu `95-omsagent.conf`v konfiguračním souboru , bude přepsána, když agent použije výchozí konfiguraci.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Konfigurace syslog-ng by měla být změněna zkopírováním níže uvedené ukázkové konfigurace a přidáním vlastního upraveného nastavení `/etc/syslog-ng/`na konec konfiguračního souboru syslog-ng.conf umístěného v . **Nepoužívejte** výchozí popisek **%WORKSPACE_ID%_oms** nebo **%WORKSPACE_ID_OMS**, definujte vlastní popisek, který pomůže odlišit změny.  

    > [!NOTE]
    > Pokud změníte výchozí hodnoty v konfiguračním souboru, budou přepsány, když agent použije výchozí konfiguraci.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Po dokončení změn je třeba restartovat službu agenta Syslog a Log Analytics, aby se změny konfigurace projevily.   

## <a name="syslog-record-properties"></a>Vlastnosti záznamu Syslogu
Záznamy syslogu mají typ **syslogu** a mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Počítač, ze kterého byla událost shromážděna. |
| Zařízení |Definuje část systému, která zprávu vygenerovala. |
| HostIP |IP adresa systému odesílajícího zprávu. |
| HostName |Název systému odesílajícího zprávu. |
| Úroveň závažnosti |Úroveň závažnosti události. |
| Zpráva SyslogMessage |Text zprávy. |
| ProcessID |ID procesu, který zprávu vygeneroval. |
| EventTime |Datum a čas, kdy byla událost vygenerována. |

## <a name="log-queries-with-syslog-records"></a>Protokolovat dotazy pomocí záznamů Syslogu
Následující tabulka obsahuje různé příklady dotazů protokolu, které načítají záznamy Syslog.

| Dotaz | Popis |
|:--- |:--- |
| Syslog |Všechny Syslogy. |
| Syslog &#124; kde SeverityLevel == "chyba" |Všechny záznamy Syslog u závažnosti chyby. |
| Syslog &#124; shrnout AggregatedValue = count() podle počítače |Počet záznamů Syslog podle počítače. |
| Syslog &#124; shrnout AggregatedValue = count() podle facility |Počet záznamů Syslog podle zařízení. |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](../../azure-monitor/log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení.
* Vlastní [pole](../../azure-monitor/platform/custom-fields.md) slouží k rozdělení dat ze záznamů syslogu do jednotlivých polí.
* [Nakonfigurujte linuxové agenty](../../azure-monitor/learn/quick-collect-linux-computer.md) tak, aby shromažďovali další typy dat.
