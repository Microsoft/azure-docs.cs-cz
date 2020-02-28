---
title: Shromažďovat a analyzovat zprávy syslog v Azure Monitor | Microsoft Docs
description: Syslog je protokol protokolování událostí, které jsou společné pro Linux. Tento článek popisuje, jak nakonfigurovat shromažďování zpráv syslog v Log Analytics a podrobnosti o záznamech, které vytvoří.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670487"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Zdroje dat syslog v Azure Monitor
Syslog je protokol protokolování událostí, které jsou společné pro Linux. Aplikace odešle zprávy, které mohou být uložené na místním počítači nebo doručí do kolekcí Syslog. Pokud je nainstalován agent Log Analytics pro Linux, nakonfiguruje místní démon syslog, aby předal zprávy agentovi. Agent potom zprávu pošle Azure Monitor, kde se vytvoří odpovídající záznam.  

> [!NOTE]
> Azure Monitor podporuje shromažďování zpráv odeslaných pomocí rsyslog nebo syslog-ng, kde rsyslog je výchozí démon. Démon procesu syslog výchozí verze 5 Red Hat Enterprise Linux, CentOS a Oracle Linux verze (sysklog) není podporována pro shromažďování událostí protokolu syslog. Aby bylo možné shromažďovat data syslog z této verze těchto distribucí, je třeba nainstalovat [démona rsyslog](http://rsyslog.com) a nakonfigurovat tak, aby nahradila sysklog.
>
>

![Shromažďování Syslogu](media/data-sources-syslog/overview.png)

Kolekce syslog podporuje následující zařízení:

* párů
* uživatel
* pošta
* proces
* auth
* syslog
* port
* diskuz
* uucp
* cron
* authpriv
* ftp
* local0-local7

Pro jakékoli jiné zařízení [nakonfigurujte zdroj dat vlastních protokolů](data-sources-custom-logs.md) v Azure monitor.
 
## <a name="configuring-syslog"></a>Konfigurace Syslog
Agent Log Analytics pro Linux bude shromažďovat jenom události se zařízeními a závažnostmi, které jsou zadané v jeho konfiguraci. Syslog můžete nakonfigurovat prostřednictvím webu Azure portal nebo Správa konfiguračních souborů na agenty Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurovat Syslog na webu Azure Portal
Nakonfigurujte syslog z [nabídky data v části Upřesnit nastavení](agent-data-sources.md#configuring-data-sources). Tato konfigurace se doručí do konfiguračního souboru na každého agenta pro Linux.

Nové zařízení můžete přidat tak, že nejprve vyberete možnost **použít níže konfiguraci na moje počítače** a potom zadáte název a kliknete **+** . Pro každé zařízení nebudou se shromažďovat pouze zprávy s vybranou závažnosti.  Zaškrtněte závažnosti pro konkrétní zařízení, která chcete shromažďovat. Nelze zadat žádná další kritéria filtru zpráv.

![Konfigurovat Syslog](media/data-sources-syslog/configure.png)

Standardně jsou všechny změny konfigurace automaticky nahrány do všech agentů. Pokud chcete protokol syslog nakonfigurovat ručně u každého agenta pro Linux, zrušte jeho zrušení zaškrtněte v části *Konfigurace na moje počítače*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurace Syslog na agenta pro Linux
Když [je agent Log Analytics nainstalovaný v klientském počítači se systémem Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), nainstaluje výchozí konfigurační soubor syslog, který definuje zařízení a závažnost shromažďovaných zpráv. Můžete upravit tento soubor a změňte konfiguraci. Konfigurační soubor se liší v závislosti na démon procesu Syslog, který se klient nainstaloval.

> [!NOTE]
> Pokud upravíte konfiguraci syslogu, je nutné restartovat démon procesu syslog, aby se změny projevily.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurační soubor pro rsyslog je umístěný na adrese **/etc/rsyslog.d/95-omsagent.conf**. Níže se zobrazují výchozí obsah. To shromažďuje syslog zpráv odeslaných z místního agenta pro všechna zařízení s úrovní varování nebo vyšší.

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

Zařízení můžete odebrat odstraněním jeho oddílu konfiguračního souboru. Můžete omezit závažnosti, které se shromažďují pro konkrétní zařízení tak, že upravíte položka tohoto zařízení. Například pro omezení zařízení uživatele na zprávy s závažnost chyby nebo vyšší upravíte tento řádek v souboru konfigurace následujícím:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog-ng
Konfigurační soubor protokolu syslog-ng je umístění na adrese **/etc/syslog-ng/syslog-ng.conf**.  Níže se zobrazují výchozí obsah. To shromažďuje syslog zpráv odeslaných z místního agenta pro všechna zařízení a všechny závažnosti.   

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

Zařízení můžete odebrat odstraněním jeho oddílu konfiguračního souboru. Můžete omezit závažnosti, které se shromažďují pro konkrétní zařízení tak, že odeberete ze seznamu.  Například pokud chcete omezit zařízení uživatele na právě upozornění a kritické zprávy, upravíte tohoto oddílu konfiguračního souboru takto:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Shromažďování dat z další porty Syslog
Agent Log Analytics naslouchá zprávám syslog v místním klientovi na portu 25224.  Při instalaci agenta je výchozí konfigurace syslog použít a najdete v následujícím umístění:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Číslo portu můžete změnit tak, že vytvoříte dva konfigurační soubory: konfigurační soubor FluentD a soubor rsyslog nebo syslog-ng v závislosti na tom jste si nainstalovali démon procesu Syslog.  

* Soubor s přípravnou konfigurací by měl být nový soubor umístěný v umístění: `/etc/opt/microsoft/omsagent/conf/omsagent.d` a nahradit hodnotu v položce **port** vlastním číslem portu.

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

* V případě rsyslog byste měli vytvořit nový konfigurační soubor umístěný v umístění: `/etc/rsyslog.d/` a nahradit hodnotu% SYSLOG_PORT% vlastním číslem portu.  

    > [!NOTE]
    > Pokud tuto hodnotu upravíte v konfiguračním souboru `95-omsagent.conf`, bude přepsána, když Agent použije výchozí konfiguraci.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Konfigurace syslog-ng by se měla upravit zkopírováním ukázkové konfigurace uvedené níže a přidáním vlastního upraveného nastavení na konec konfiguračního souboru syslog-ng. conf, který se nachází v `/etc/syslog-ng/`. Nepoužívejte **výchozí** popisek **% WORKSPACE_ID% _oms** ani **% WORKSPACE_ID_OMS**, definujte vlastní popisek, který bude lépe odlišit vaše změny.  

    > [!NOTE]
    > Pokud změníte výchozí hodnoty v konfiguračním souboru, budou přepsány při agent použije výchozí konfiguraci.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Po dokončení změn je nutné restartovat protokol syslog a službu Log Analytics agenta, aby se změny konfigurace projevily.   

## <a name="syslog-record-properties"></a>Vlastnosti záznamu Syslog
Záznamy syslog mají typ **SYSLOG** a mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Computer |Počítač, který událost byla shromážděna z. |
| Zařízení |Definuje části systému, který vygeneroval zprávu. |
| HostIP |IP adresa systému posílání zprávy. |
| název hostitele |Název systému posílání zprávy. |
| Úroveň závažnosti |Úroveň závažnosti události. |
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

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](../../azure-monitor/log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.
* Použijte [vlastní pole](../../azure-monitor/platform/custom-fields.md) k analýze dat ze záznamů syslog do jednotlivých polí.
* [Nakonfigurujte agenty Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) pro shromažďování dalších typů dat.
