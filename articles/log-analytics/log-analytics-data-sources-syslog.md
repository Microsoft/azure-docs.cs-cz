---
title: Shromažďovat a analyzovat zprávy Syslog v OMS Log Analytics | Microsoft Docs
description: Syslog je protokol protokolování událostí, které je běžné Linux. Tento článek popisuje, jak nakonfigurovat kolekce zprávy Syslog v analýzy protokolů a podrobnosti záznamů, které vytvoří v úložišti OMS.
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
ms.author: magoedte;bwren
ms.component: na
ms.openlocfilehash: 1e7806e802f9b1dda16a9d5d477783663d03d416
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131782"
---
# <a name="syslog-data-sources-in-log-analytics"></a>Syslog zdroje dat v analýzy protokolů
Syslog je protokol protokolování událostí, které je běžné Linux.  Aplikace bude odesílat zprávy, které mohou být uloženy v místním počítači nebo doručit do kolekce Syslog.  Pokud je nainstalován Agent OMS pro Linux, nakonfiguruje místní démon procesu Syslog předávání zpráv do agenta.  Agent pak odešle zprávu k analýze protokolů, které se vytvoří odpovídající záznam v úložišti OMS.  

> [!NOTE]
> Analýzy protokolů podporuje kolekce zprávy odeslané rsyslog nebo syslog ng, kde je rsyslog démon výchozí. Démon procesu syslog výchozí na verze 5 Red Hat Enterprise Linux a CentOS, Oracle Linux verze (sysklog) není podporována pro shromažďování událostí syslog. Ke shromažďování dat syslog z této verze těchto distribuce [rsyslog démon](http://rsyslog.com) musí být nainstalovaná a nakonfigurovaná nahradit sysklog.
>
>

![Kolekce Syslog](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Konfigurace procesu Syslog
OMS agenta pro Linux bude pouze shromažďovat události se zařízením a závažnosti, které jsou určené v konfiguraci.  Můžete nakonfigurovat Syslog prostřednictvím portálu Azure nebo Správa konfiguračních souborů na agenty systému Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurovat Syslog na portálu Azure
Konfigurace Syslog z [nabídce Data v nastavení pokročilé analýzy protokolů](log-analytics-data-sources.md#configuring-data-sources).  Tato konfigurace se doručí do konfiguračního souboru na každého agenta systému Linux.

Můžete přidat nové zařízení zadáním v jeho název a kliknutím na tlačítko **+**.  Pro každé zařízení budou shromažďovány pouze zprávy s vybranou závažnosti.  Zkontrolujte závažnosti pro konkrétní zařízení, které chcete shromažďovat.  Nelze poskytnout žádná další kritéria filtru zpráv.

![Konfigurace procesu Syslog](media/log-analytics-data-sources-syslog/configure.png)

Ve výchozím nastavení všechny změny konfigurace automaticky odesílají na všechny agenty.  Pokud chcete ručně nakonfigurovat Syslog na každého agenta systému Linux, poté zrušte zaškrtnutí políčka *použít dole uvedenou konfiguraci u mých Linuxových počítačů*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurace Syslog na agenta systému Linux
Když [Linux klienta je nainstalován OMS agent](log-analytics-linux-agents.md), nainstaluje výchozí syslog konfiguračního souboru, který definuje zařízením a závažnost zpráv, které byly shromážděny.  Můžete upravit tento soubor a změňte konfiguraci.  Konfigurační soubor se liší v závislosti na tom, který se klient nainstaloval démon procesu Syslog.

> [!NOTE]
> Pokud chcete upravit konfiguraci syslog, je nutné restartovat démon procesu syslog změny se projeví.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurační soubor pro rsyslog se nachází v **/etc/rsyslog.d/95-omsagent.conf**.  Níže jsou uvedeny výchozí obsah.  To shromažďuje syslog zpráv odeslaných z místního agenta pro všechna zařízení s úrovní varování nebo vyšší.

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

Budovy můžete odebrat odstraněním příslušném oddílu konfiguračního souboru.  Můžete omezit závažnosti, které se shromažďují pro konkrétní zařízení úpravou položky tohoto zařízení.  Chcete-li omezit uživatele zařízení pro zprávy o závažnosti chyby nebo vyšší je by upravit daného řádku konfiguračního souboru pro následující:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog ng
Konfigurační soubor pro syslog ng je umístění v **/etc/syslog-ng/syslog-ng.conf**.  Níže jsou uvedeny výchozí obsah.  To shromažďuje syslog zpráv odeslaných z místního agenta pro všechna zařízení a všechny závažnosti.   

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

Budovy můžete odebrat odstraněním příslušném oddílu konfiguračního souboru.  Můžete omezit závažnosti, které se shromažďují pro konkrétní zařízení jejich vyjmutím ze seznamu.  Chcete-li omezit uživatele zařízení pro právě upozornění a kritickou zprávy, je by upravit tento oddíl konfiguračního souboru pro následující:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Shromažďování dat z další porty Syslog
Zprávy Syslog v místním klientovi na portu 25224 naslouchá OMS agent.  Pokud je nainstalován agent nástroje, použít výchozí konfigurace syslog a najít v následujícím umístění:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Číslo portu můžete změnit tak, že vytvoříte dvě konfigurační soubory: soubor konfigurace FluentD a soubor rsyslog nebo syslog ng v závislosti na instalaci démon procesu Syslog.  

* Soubor konfigurace FluentD by měl být nový soubor umístěný ve: `/etc/opt/microsoft/omsagent/conf/omsagent.d` a nahraďte hodnotu v **port** položka se vaše vlastní číslo portu.

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

* Pro rsyslog, měli vytvořit nový soubor konfigurace umístěný v: `/etc/rsyslog.d/` a nahraďte hodnotu % SYSLOG_PORT % vaše vlastní číslo portu.  

    > [!NOTE]
    > Pokud změníte tuto hodnotu v konfiguračním souboru `95-omsagent.conf`, pokud agent použije výchozí konfiguraci bude přepsán.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Konfigurace syslog ng by měl být upraven tak, že zkopírujete následující příklad konfigurace a přidání vlastní změny nastavení na konec syslog ng.conf konfigurační soubor umístěný ve `/etc/syslog-ng/`.  Proveďte **není** použít výchozí štítek **% WORKSPACE_ID % _oms** nebo **% WORKSPACE_ID_OMS**, definovat vlastní popisek k lepšímu odlišení změny.  

    > [!NOTE]
    > Pokud změníte výchozí hodnoty v konfiguračním souboru, budou při agent použije výchozí konfigurace přepsány.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Po dokončení změny, Syslog a OMS agent služba musí restartovat, aby se že projevily změny konfigurace.   

## <a name="syslog-record-properties"></a>Vlastnosti záznamu Syslog
Zaznamenává Syslog mít typ **Syslog** a mít vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Počítač, který událost nebyla shromážděna z. |
| Zařízení |Definuje část systému, který vygeneroval zprávu. |
| HostIP |IP adresa systému odeslání zprávy. |
| název hostitele |Název systému odeslání zprávy. |
| Úroveň závažnosti |Úroveň závažnosti události. |
| SyslogMessage |Text zprávy. |
| ProcessID |ID procesu, který vygeneroval zprávu. |
| EventTime |Datum a čas, která byla vygenerována událost. |

## <a name="log-queries-with-syslog-records"></a>Dotazy protokolu se záznamy Syslog
Následující tabulka obsahuje různé příklady dotazů protokolu, která načítají záznamy Syslog.

| Dotaz | Popis |
|:--- |:--- |
| Syslog |Všechny systémové protokoly. |
| Syslog &#124; kde úroveň závažnosti == "error. |Všechny záznamy Syslog s závažnosti chyby. |
| Syslog &#124; shrnout AggregatedValue = count() počítačem. |Počet Syslog záznamy počítačem. |
| Syslog &#124; shrnout AggregatedValue = count() podle zařízení |Počet Syslog záznamy podle budovy. |

## <a name="next-steps"></a>Další postup
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení.
* Použití [vlastní pole](log-analytics-custom-fields.md) k analýze dat z syslog záznamů do jednotlivých polí.
* [Konfigurace agentů Linux](log-analytics-linux-agents.md) ke shromažďování dalších typů dat.
