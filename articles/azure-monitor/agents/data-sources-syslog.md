---
title: Shromáždění zdrojů dat syslog pomocí agenta Log Analytics v Azure Monitor
description: Syslog je protokol protokolování událostí, který je společný pro Linux. Tento článek popisuje, jak nakonfigurovat shromažďování zpráv syslog v Log Analytics a podrobnosti o záznamech, které vytvoří.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: e82e74f4cd325444221bbd2e1c060b7cd2f5c6c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036723"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>Shromáždění zdrojů dat syslog pomocí agenta Log Analytics
Syslog je protokol protokolování událostí, který je společný pro Linux. Aplikace budou odesílat zprávy, které mohou být uloženy v místním počítači nebo doručeny do kolekce syslog. Pokud je nainstalován agent Log Analytics pro Linux, nakonfiguruje místní démon syslog, aby předal zprávy agentovi. Agent potom zprávu pošle Azure Monitor, kde se vytvoří odpovídající záznam.  

> [!IMPORTANT]
> Tento článek popisuje shromažďování událostí protokolu syslog pomocí [agenta Log Analytics](./log-analytics-agent.md) , který je jedním z agentů používaných Azure monitor. Jiní agenti shromažďují různá data a nakonfigurují se jinak. Seznam dostupných agentů a data, která mohou shromažďovat, najdete v tématu [Přehled agentů Azure monitor](../agents/agents-overview.md) .

> [!NOTE]
> Azure Monitor podporuje shromažďování zpráv odeslaných pomocí rsyslog nebo syslog-ng, kde rsyslog je výchozí démon. Výchozí démon procesu Syslog verze 5 Red Hat Enterprise Linux, CentOS a verze Oracle Linux (sysklog) není pro shromažďování událostí syslog podporován. Aby bylo možné shromažďovat data syslog z této verze těchto distribucí, je třeba nainstalovat [démona rsyslog](http://rsyslog.com) a nakonfigurovat tak, aby nahradila sysklog.
>
>

![Kolekce syslog](media/data-sources-syslog/overview.png)

Kolekce syslog podporuje následující zařízení:

* párů
* uživatel
* pošta
* proces
* auth
* syslog
* port
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

Pro jakékoli jiné zařízení [nakonfigurujte zdroj dat vlastních protokolů](data-sources-custom-logs.md) v Azure monitor.
 
## <a name="configuring-syslog"></a>Konfigurace protokolu syslog
Agent Log Analytics pro Linux bude shromažďovat jenom události se zařízeními a závažnostmi, které jsou zadané v jeho konfiguraci. Protokol syslog můžete nakonfigurovat prostřednictvím Azure Portal nebo správou konfiguračních souborů v agentech Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurace syslogu v Azure Portal
Nakonfigurujte syslog z [nabídky konfigurace agenta](../agents/agent-data-sources.md#configuring-data-sources) pro pracovní prostor Log Analytics. Tato konfigurace se doručuje do konfiguračního souboru každého agenta pro Linux.

Kliknutím na **Přidat zařízení** můžete přidat nové zařízení. Pro každé zařízení budou shromažďovány pouze zprávy s vybranými závažnostmi.  Ověřte závažnost konkrétního zařízení, které chcete shromáždit. Nemůžete zadat žádná další kritéria pro filtrování zpráv.

[![Konfigurace protokolu syslog](media/data-sources-syslog/configure.png)](media/data-sources-syslog/configure.png#lightbox)

Ve výchozím nastavení jsou všechny změny konfigurace automaticky vloženy do všech agentů. Pokud chcete protokol syslog nakonfigurovat ručně u každého agenta pro Linux, zrušte jeho zrušení zaškrtněte v části *Konfigurace na moje počítače*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurace protokolu syslog v agentovi Linux
Když [je agent Log Analytics nainstalovaný v klientském počítači se systémem Linux](../vm/quick-collect-linux-computer.md), nainstaluje výchozí konfigurační soubor syslog, který definuje zařízení a závažnost shromažďovaných zpráv. Úpravou tohoto souboru můžete změnit konfiguraci. Konfigurační soubor se liší v závislosti na procesu démona syslog, který klient nainstaloval.

> [!NOTE]
> Pokud upravíte konfiguraci syslogu, je nutné restartovat démona syslog, aby se změny projevily.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurační soubor pro rsyslog je umístěný na adrese **/etc/rsyslog.d/95-omsagent.conf**. Výchozí obsah je uveden níže. Tím se shromáždí zprávy syslog odeslané z místního agenta pro všechna zařízení s úrovní upozornění nebo vyšší.

```config
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
```

Zařízení můžete odebrat tak, že odeberete jeho část konfiguračního souboru. Závažnost, které se shromažďují pro konkrétní zařízení, můžete omezit změnou položky daného zařízení. Chcete-li například omezit uživatelská zařízení na zprávy se závažností chyby nebo vyšší, měli byste tento řádek konfiguračního souboru upravit následujícím způsobem:

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>syslog – ng
Konfigurační soubor protokolu syslog-ng je umístění na adrese **/etc/syslog-ng/syslog-ng.conf**.  Výchozí obsah je uveden níže. Tím se shromáždí zprávy syslog odeslané z místního agenta pro všechna zařízení a všechny závažnosti.   

```config
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
```

Zařízení můžete odebrat tak, že odeberete jeho část konfiguračního souboru. Můžete omezit závažnost, které jsou shromažďovány pro konkrétní zařízení odebráním z jeho seznamu.  Chcete-li například omezit uživatelská zařízení jenom na výstrahy a kritické zprávy, změňte tuto část konfiguračního souboru na následující:

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>Shromažďování dat z dalších portů syslog
Agent Log Analytics naslouchá zprávám syslog v místním klientovi na portu 25224.  Při instalaci agenta se použije výchozí konfigurace syslog, která se nachází v následujícím umístění:

* Rsyslog `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Číslo portu můžete změnit vytvořením dvou konfiguračních souborů: v závislosti na procesu démona syslog, který jste nainstalovali, můžete změnit soubor s mikroprotokolem a souborem rsyslog nebo syslog-ng.  

* V tomto souboru by měl být soubor s přístavou, který se nachází v umístění: `/etc/opt/microsoft/omsagent/conf/omsagent.d` a nahradit hodnotu v položce **port** vlastním číslem portu.

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* V případě rsyslog byste měli vytvořit nový konfigurační soubor v umístění: `/etc/rsyslog.d/` a nahradit hodnotu% SYSLOG_PORT% vlastním číslem portu.  

    > [!NOTE]
    > Pokud tuto hodnotu upravíte v konfiguračním souboru `95-omsagent.conf` , bude přepsána, když Agent použije výchozí konfiguraci.
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* Konfigurace syslog-ng by se měla upravit zkopírováním ukázkové konfigurace uvedené níže a přidáním vlastního upraveného nastavení na konec konfiguračního souboru syslog-ng. conf, který se nachází v `/etc/syslog-ng/` . Nepoužívejte **výchozí** popisek **% WORKSPACE_ID% _oms** ani **% WORKSPACE_ID_OMS**, definujte vlastní popisek, který bude lépe odlišit vaše změny.  

    > [!NOTE]
    > Pokud upravíte výchozí hodnoty v konfiguračním souboru, budou přepsány, když Agent použije výchozí konfiguraci.
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

Po dokončení změn je nutné restartovat protokol syslog a službu Log Analytics agenta, aby se změny konfigurace projevily.   

## <a name="syslog-record-properties"></a>Vlastnosti záznamu syslog
Záznamy syslog mají typ **SYSLOG** a mají vlastnosti v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| Počítač |Počítač, ze kterého byla událost shromážděna. |
| Vybavení |Definuje část systému, která vygenerovala zprávu. |
| HostIP |IP adresa systému, který posílá zprávu. |
| Název hostitele |Název systému odesílajícího zprávu. |
| SeverityLevel |Úroveň závažnosti události |
| SyslogMessage |Text zprávy |
| ProcessID |ID procesu, který vygeneroval zprávu |
| Čas události |Datum a čas, kdy byla událost vygenerována. |

## <a name="log-queries-with-syslog-records"></a>Dotazy protokolu se záznamy syslog
Následující tabulka uvádí různé příklady dotazů protokolu, které načítají záznamy syslog.

| Dotaz | Description |
|:--- |:--- |
| Syslog |Všechny Syslogy. |
| Syslog &#124;, kde SeverityLevel = = "Error" |Všechny záznamy syslog se závažností chyby. |
| Syslog &#124; souhrn AggregatedValue = Count () podle počítače |Počet záznamů syslog podle počítače |
| Syslog &#124; souhrn AggregatedValue = Count () podle zařízení |Počet záznamů syslog podle zařízení |

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](../logs/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.
* Použijte [vlastní pole](../logs/custom-fields.md) k analýze dat ze záznamů syslog do jednotlivých polí.
* [Nakonfigurujte agenty Linux](../vm/quick-collect-linux-computer.md) pro shromažďování dalších typů dat.