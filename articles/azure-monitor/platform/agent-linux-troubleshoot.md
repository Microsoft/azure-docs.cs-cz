---
title: Řešení potíží s agentem Azure Log Analytics Linux | Dokumentace Microsoftu
description: Popište příznaky, příčiny a řešení běžných problémů pomocí agenta Log Analytics pro Linux.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 8da42ee6db4b9ec76fa97e94a77076ed347e2952
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080670"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Řešení potíží pomocí agenta Log Analytics pro Linux 

Tento článek poskytuje nápovědu řešení potíží s chybami setkat s agenta Log Analytics pro Linux a navrhne řešení k jejich řešení.

Pokud žádný z těchto kroků pro vás nejvhodnější, jsou také k dispozici následující kanály podpory:

* Výhody v oblasti podpory pro zákazníky v rámci podpory Premier můžou otevřít žádost o podporu s [Premier](https://premier.microsoft.com/).
* Zákazníci s smlouvy o podpoře Azure můžou otevřít žádost o podporu [na webu Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Diagnostikujte problémy OMI pomocí [Průvodce odstraňováním potíží OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Soubor [problém Githubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Na stránce Log Analytics zpětnou vazbu ke kontrole odeslané nápady a chyby [ http://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) nebo soubor nový.  

## <a name="important-log-locations-and-log-collector-tool"></a>Umístění protokolu důležité a nástroj Kolektoru protokolů

 File | Cesta
 ---- | -----
 Agenta log Analytics pro soubor protokolu systému Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log `
 Soubor protokolu log Analytics agenta konfigurace | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Doporučujeme použít nástroj pro shromažďování protokolů k načtení důležité protokoly pro řešení potíží nebo před odesláním problém na Githubu. Můžete si přečíst informace o nástroji a jak ji spustit [tady](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Důležité konfigurační soubory

 Kategorie | Umístění souboru
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` nebo `/etc/rsyslog.conf` nebo `/etc/rsyslog.d/95-omsagent.conf`
 Výkon, Nagios, Zabbix výstup Log Analytics a obecné agenta | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Další konfigurace | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Úprava konfiguračních souborů pro čítače výkonu a Syslog je přepsán, pokud kolekce je nakonfigurována z [nabídky dat Log Analytics Upřesnit nastavení](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) na webu Azure Portal pro váš pracovní prostor. Pokud chcete zakázat konfiguraci pro všechny agenty, zakažte shromažďování od Log Analytics **Upřesnit nastavení** nebo jednom agentovi, spusťte následující příkaz:  
> `sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable`

## <a name="installation-error-codes"></a>Kódy chyb instalace

| Kód chyby | Význam |
| --- | --- |
| NOT_DEFINED | Protože nejsou nainstalované potřebné závislosti, nenainstalují se modul plug-in auoms démona auditd | Instalace se nezdařila, auoms nainstalovat balíček auditd. |
| 2 | Neplatná možnost sadě prostředí k dispozici. Spustit `sudo sh ./omsagent-*.universal*.sh --help` za využití |
| 3 | Žádná možnost není k dispozici pro prostředí sady. Spustit `sudo sh ./omsagent-*.universal*.sh --help` za využití. |
| 4 | Neplatný balíček typu nebo neplatné nastavení proxy serveru; omsagent -*ot. / min*.sh balíčky lze nainstalovat pouze na systémy založené na ot. / min a omsagent -*deb*.sh balíčky můžete nainstalovat jenom v systémech založených na Debian. To se doporučuje použít univerzální instalačního programu z [nejnovější vydaná verze](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Také [zkontrolujte](#issue:-unable-to-connect-through-proxy-to-log-analytics) ověřit vaše nastavení proxy serveru. |
| 5 | Prostředí sady je nutné provést jako uživatel root nebo se Chyba 403 vrátil během registrace. Spuštění příkazu s `sudo`. |
| 6 | Neplatný balíček architektury nebo Chyba 200 chyba vrátil během připojování; omsagent -*x64.sh balíčky lze nainstalovat pouze v 64bitových systémech složku a omsagent*x86.sh balíčky můžete nainstalovat jenom na 32bitových systémech. Stáhněte si správný balíček architektury z [nejnovější vydaná verze](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Instalace balíčku OMS se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 19 | Instalace balíčku OMI se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 20 | Instalace balíčku SCX se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 21 | Instalace zprostředkovatele sady se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 22 | Instalace balíčku jako součást balíčku se nezdařila. Prohlédněte si výstup příkazu kořenové selhání |
| 23 | Balíček SCX nebo OMI už nainstalovaná. Použití `--upgrade` místo `--install` k instalaci sady prostředí. |
| 30 | Interní sada došlo k chybě. Soubor [problém Githubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 55 | Nepodporovaná openssl verze nemůže připojit ke službě Log Analytics nebo dpkg je uzamčené nebo chybějící curl programu. |
| 61 | Chybějící ctypes knihovnu Pythonu. Nainstalujte Python ctypes knihovny nebo balíčku (python-ctypes). |
| 62 | Chybějící program tar, cíl instalace. |
| 63 | Chybějící sed program, instalace sed. |
| 64 | Chybějící program curl, instalace curl. |
| 65 | Chybějící gpg program, instalace gpg. |

## <a name="onboarding-error-codes"></a>Kódy chyb registrace

| Kód chyby | Význam |
| --- | --- |
| 2 | Neplatná možnost omsadmin skriptu předávají. Spustit `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` za využití. |
| 3 | Neplatná konfigurace skriptu omsadmin předávají. Spustit `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` za využití. |
| 4 | Neplatná adresa proxy omsadmin skriptu předávají. Ověřit proxy serveru a v tématu naše [dokumentace pro používání proxy serveru HTTP](log-analytics-agent.md#network-firewall-requirements). |
| 5 | Chyba 403 HTTP přijatých ze služby Log Analytics. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 6 | Než 200 HTTP došlo k chybě: ze služby Log Analytics. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 7 | Nelze se připojit ke službě Log Analytics. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 8 | Chyba připojení k pracovnímu prostoru Log Analytics. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 30 | Vnitřní chyba skriptu. Soubor [problém Githubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 31 | ID chyby generování agenta. Soubor [problém Githubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 32 | Chyba při generování certifikátů. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 33 | Chyba při generování metaconfiguration pro omsconfig. Soubor [problém Githubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 34 | Generování skriptu Metaconfiguration není k dispozici. Opakujte registraci s `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Povolit protokolování ladění
### <a name="oms-output-plugin-debug"></a>Ladění modulu plug-in výstup OMS
 FluentD se pro úrovně přihlašování specifické pro modul plug-in vám umožňuje určit úrovně jiný protokol pro vstupy a výstupy. K určení úrovně jiný protokol pro výstup OMS, upravte konfiguraci obecné agenta na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 V modulu plug-in výstup OMS, do konce konfigurační soubor změnit `log_level` vlastnost z `info` k `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Protokolování ladění vám umožní vidět dávkové nahrávání ve službě Log Analytics oddělené typ, počet datových položek a čas potřebný k odeslání:

*Protokol ladění povoleno příkladu:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Podrobný výstup
Namísto použití modulu plug-in výstup OMS můžete také výstup datových položek přímo na `stdout`, který se zobrazí v agenta Log Analytics pro soubor protokolu systému Linux.

V konfiguračním souboru obecné agenta Log Analytics na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, Odkomentujte OMS výstup modul plug-in tak, že přidáte `#` před každý řádek:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Následující modul plug-in výstup, zrušte komentář u následující části tak, že odeberete `#` před každý řádek:

```
<match **>
  type stdout
</match>
```

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problém: Nelze se připojit přes proxy do Log Analytics

### <a name="probable-causes"></a>Možných příčin
* Proxy zadaný během registrace byl nesprávný
* Log Analytics a koncové body služby automatizace Azure nejsou na seznamu povolených ve vašem datovém centru 

### <a name="resolution"></a>Řešení
1. Reonboard do služby Log Analytics pomocí agenta Log Analytics pro Linux pomocí následujícího příkazu s parametrem `-v` povolena. To umožňuje podrobný výstup agenta připojeným přes proxy do služby Log Analytics. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Projděte si část [aktualizovat nastavení proxy serveru](agent-manage.md#update-proxy-settings) k ověření aplikace správně nakonfigurována, aby mohli agenti komunikovat přes proxy server.    
* Pečlivě zkontrolujte, že následující koncové body Log Analytics jsou povolené:

    |Prostředek agenta| Porty | Směr |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.oms.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.blob.core.windows.net | Port 443| Příchozí a odchozí |  
    |*.azure-automation.net | Port 443| Příchozí a odchozí | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problém: Zobrazí Chyba 403 při pokusu o připojení

### <a name="probable-causes"></a>Možných příčin
* Datum a čas není na serveru s Linuxem 
* ID pracovního prostoru a klíč pracovního prostoru použít nejsou správné

### <a name="resolution"></a>Řešení

1. Zkontrolujte čas na serveru Linux s datem příkazu. Pokud je doba +/-15 minut od aktuálního času, registrace se nezdaří. Pokud chcete správné to aktualizovat datum a časové pásmo serveru Linux. 
2. Ověřte, že máte nainstalovanou nejnovější verzi agenta Log Analytics pro Linux.  Nejnovější verze nyní oznámí, že jste Pokud časového posunu je příčinou selhání připojení.
3. Reonboard pomocí správné ID pracovního prostoru a klíč pracovního prostoru, postupujte podle pokynů instalaci výše v tomto článku.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problém: Se zobrazí chyba 500 a 404 v souboru protokolu hned po registraci
Jde o známý problém, ke které dojde při prvním uložení dat s Linuxem do pracovního prostoru Log Analytics. Dat odeslaných nebo že služba prostředí to neovlivní.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problém: Se nezobrazují žádná data na webu Azure Portal

### <a name="probable-causes"></a>Možných příčin

- Připojování ke službě Log Analytics se nezdařilo
- Připojení ke službě Log Analytics je blokován.
- Agenta log Analytics pro Linux data se zálohují.

### <a name="resolution"></a>Řešení
1. Zkontrolujte, jestli registrace služby Log Analytics byla úspěšná kontrolou, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Pomocí Reonboard `omsadmin.sh` pokyny příkazového řádku
3. Pokud používáte proxy server, podívejte se na postup řešení proxy server jste zadali dříve.
4. V některých případech když agenta Log Analytics pro Linux nemůže komunikovat se službou, data v agentovi je ve frontě velikost úplné vyrovnávací paměti, což je 50 MB. Agent by měl být restartován spuštěním následujícího příkazu: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Tento problém je vyřešen 1.1.0-28 verzi agenta a novější.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problém: Se nezobrazují v předaných zprávách Syslog 

### <a name="probable-causes"></a>Možných příčin
* Konfigurace pro Linux server nepovoluje kolekce odeslané zařízení a/nebo jeho úrovně
* Na Linux server není správně přeposílají Syslog
* Počet zpráv, které jsou předávány dál za sekundu, které jsou příliš velký pro základní konfigurace agenta Log Analytics pro Linux pro zpracování

### <a name="resolution"></a>Řešení
* Ověřte, že konfigurace v pracovním prostoru Log Analytics pro Syslog má všem zařízením a úrovních protokolování správné. Kontrola [Konfigurovat shromažďování Syslogu na webu Azure Portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ověřte nativní syslog zasílání zpráv procesy démon (`rsyslog`, `syslog-ng`) se dostávají v předaných zprávách
* Zkontrolujte nastavení brány firewall na serveru Syslog a ujistěte se, že nejsou blokovány zprávy
* Simulace zprávy Syslog do Log Analytics pomocí `logger` příkaz
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problém: Dostáváte Errno adresa už používá v souboru protokolu omsagent
Pokud se zobrazí `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` v omsagent.log.

### <a name="probable-causes"></a>Možných příčin
Tato chyba označuje, že rozšíření Linux Diagnostic (LAD) nainstalovaná souběžně rozšíření virtuálního počítače s Linuxem Log Analytics a používá stejný port pro shromažďování dat protokolu syslog, omsagent.

### <a name="resolution"></a>Řešení
1. Jako uživatel root spusťte následující příkazy (Všimněte si, že je příkladem 25224 a je možné, že ve vašem prostředí se zobrazí jiné číslo portu používané LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Bude potřeba upravit správné `rsyslogd` nebo `syslog_ng` konfigurační soubor a změňte konfigurace související s LAD pro zápis do portů 25229.

2. Pokud je virtuální počítač spuštěný `rsyslogd`, je soubor, který má být změněn: `/etc/rsyslog.d/95-omsagent.conf` (pokud existuje, jinak `/etc/rsyslog`). Pokud je virtuální počítač spuštěný `syslog_ng`, je soubor, který má být změněn: `/etc/syslog-ng/syslog-ng.conf`.
3. Restartujte omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Restartujte službu syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problém: Nemůžete odinstalovat omsagent pomocí možnosti mazání

### <a name="probable-causes"></a>Možných příčin

* Je nainstalovaná rozšíření Linux Diagnostic
* Diagnostické rozšíření Linuxu se instaluje a odinstaluje, ale stále zobrazí chyba o omsagent mdsd používána a nelze ji odebrat.

### <a name="resolution"></a>Řešení
1. Odinstalace rozšíření Linux Diagnostic (LAD).
2. Z počítače odeberte rozšíření Linux Diagnostic soubory, kdyby se nacházely v následujícím umístění: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` a `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problém: Nemůžete vidět data žádná data Nagios 

### <a name="probable-causes"></a>Možných příčin
* Omsagent uživatel nemá oprávnění ke čtení ze souboru protokolu Nagios
* Zdroj Nagios a filtru nebyly neokomentovaném textu ze souboru omsagent.conf

### <a name="resolution"></a>Řešení
1. Přidání uživatele omsagent ke čtení ze souboru Nagios pomocí těchto [pokyny](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. V Log Analytics agenta pro Linux obecné konfigurační soubor s `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, ujistěte se, že **obě** zdroj Nagios a filtr jsou neokomentovaném textu.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problém: Se nezobrazují žádná data pro Linux 

### <a name="probable-causes"></a>Možných příčin
* Připojování ke službě Log Analytics se nezdařilo
* Připojení ke službě Log Analytics je blokován.
* Virtuální počítač byl restartován.
* OMI balíčku byl ručně upgradovat na novější verzi ve srovnání s co jste nainstalovali agenta Log Analytics pro linuxového balíčku
* Protokoly prostředku DSC *nebyla nalezena třída* při `omsconfig.log` souboru protokolu
* Agenta log Analytics pro data se zálohují.
* Protokoly DSC *neexistuje aktuální konfigurace. Spusťte příkaz Start-DscConfiguration s - parametr cesty a zadejte konfigurační soubor nejprve vytvořte aktuální konfiguraci.* v `omsconfig.log` souboru protokolu, ale žádná zpráva protokolu existuje o `PerformRequiredConfigurationChecks` operace.

### <a name="resolution"></a>Řešení
1. Nainstalujte všechny závislosti, jako jsou démona auditd balíčku.
2. Zkontrolujte, jestli připojování ke službě Log Analytics byla úspěšná kontrolou, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Pokud ne, byl reonboard pomocí příkazového řádku omsadmin.sh [pokyny](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
4. Pokud používáte proxy server, zkontrolujte proxy server výše uvedených kroků pro řešení potíží.
5. U některých systémů Azure distribuce démon server OMI omid nespustí po restartování virtuálního počítače. Výsledkem bude nezobrazila se auditování, sledování změn ve nebo UpdateManagement řešení související data. Alternativním řešením je omi serveru spustit ručně spuštěním `sudo /opt/omi/bin/service_control restart`.
6. Po balíček OMI je ručně upgradovat na novější verzi, musí ručně restartovat agenta Log Analytics, aby fungovaly. Tento krok je nutný pro některé distribuce, kde OMI server se nespustí automaticky po dojde k upgradu. Spustit `sudo /opt/omi/bin/service_control restart` restartovat OMI.
7. Pokud se zobrazí prostředků DSC *nebyla nalezena třída* při omsconfig.log spustit `sudo /opt/omi/bin/service_control restart`.
8. V některých případech, když agenta Log Analytics pro Linux nemůže komunikovat s služby Log Analytics v agentovi zálohovaných dat. k úplné vyrovnávací paměť: 50 MB. Spuštěním následujícího příkazu byste měli restartovat agenta `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Tento problém je vyřešen 1.1.0-28 verze agenta nebo novější
    >

* Pokud `omsconfig.log` souboru protokolu, který neznamená `PerformRequiredConfigurationChecks` operace jsou pravidelně spuštěné v systému, pravděpodobně došlo k potížím s úlohy cron nebo služby. Ujistěte se, že úlohy cron existuje v rámci `/etc/cron.d/OMSConsistencyInvoker`. Pokud je potřeba spuštěním následujících příkazů vytvořte úlohu cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Také se ujistěte, zda že je spuštěna služba cron. Můžete použít `service cron status` s Debian, Ubuntu, SUSE, nebo `service crond status` s RHEL, CentOS, Oracle Linux a zkontrolujte stav této služby. Pokud služba neexistuje, můžete nainstalovat binární soubory a spustit službu následujícím způsobem:

    **Ubuntu nebo Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problém: Při konfiguraci kolekce z portálu pro čítače výkonu Syslog nebo Linux, nejsou tato nastavení použita

### <a name="probable-causes"></a>Možných příčin
* Agenta Log Analytics pro Linux ještě nenačítají nejnovější konfiguraci.
* Nebyly použity změny nastavení na portálu

### <a name="resolution"></a>Řešení
**Na pozadí:** `omsconfig` je agenta Log Analytics pro linuxového agenta konfigurace, která hledá nové konfigurace na straně portálu každých pět minut. Tato konfigurace se následně použije na agenta Log Analytics pro Linux konfigurační soubory umístěné na /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* V některých případech nemusí být schopen komunikovat se službou konfigurace portálu, což vede k nejnovější konfiguraci nejsou nyní použity agenta Log Analytics pro konfiguraci agenta pro Linux.
  1. Zkontrolujte, že `omsconfig` instalaci agenta spuštěním `dpkg --list omsconfig` nebo `rpm -qi omsconfig`.  Pokud nainstalovaná není, znovu nainstalujte nejnovější verzi agenta Log Analytics pro Linux.

  2. Zkontrolujte, že `omsconfig` agent může komunikovat se službou Log Analytics spuštěním následujícího příkazu `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. Tento příkaz vrátí konfiguraci, že daný agent přijímá ze služby, včetně nastavení Syslog, čítače výkonu systému Linux a vlastní protokoly. Pokud tento příkaz selže, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Tento příkaz vynutí omsconfig agenta pro komunikaci služby Log Analytics a načíst nejnovější konfiguraci.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problém: Se nezobrazují žádné vlastní data protokolu 

### <a name="probable-causes"></a>Možných příčin
* Připojování ke službě Log Analytics se nezdařilo.
* Nastavení **Moje servery s Linuxem použijte následující konfiguraci** nebyla vybrána.
* omsconfig nebyl neexistoval nejnovější konfiguraci vlastního protokolu ze služby.
* Agenta log Analytics pro Linux uživatele `omsagent` nemá přístup k vlastní protokol problémy s oprávněními nebo nebyl nalezen.  Mohou se zobrazit následující chyby:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Známý problém s opravených agenta Log Analytics pro Linux verze 1.1.0-217 konflikt časování

### <a name="resolution"></a>Řešení
1. Připojení ke službě Log Analytics proběhlo úspěšně kontrolou, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Pokud ne, buď:  

  1. Pomocí příkazového řádku omsadmin.sh Reonboard [pokyny](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. V části **Upřesnit nastavení** na webu Azure Portal, ujistěte se, že nastavení **Moje servery s Linuxem použijte následující konfiguraci** je povolená.  

2. Zkontrolujte, že `omsconfig` agent může komunikovat se službou Log Analytics spuštěním následujícího příkazu `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  Tento příkaz vrátí konfiguraci, že daný agent přijímá ze služby, včetně nastavení Syslog, čítače výkonu systému Linux a vlastní protokoly. Pokud tento příkaz selže, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Tento příkaz vynutí omsconfig agenta pro komunikaci služby Log Analytics a načíst nejnovější konfiguraci.

**Na pozadí:** místo agenta Log Analytics pro Linux spuštěný jako uživatel - `root`, je spuštěn agent jako `omsagent` uživatele. Ve většině případů musí udělit explicitní oprávnění tohoto uživatele za určité soubory pro čtení. Udělení oprávnění ke `omsagent` uživatele, spusťte následující příkazy:

1. Přidat `omsagent` uživatele do konkrétní skupiny `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Udělte univerzální oprávnění ke čtení pro požadovaný soubor `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Existuje známý problém s časování pomocí agenta Log Analytics pro Linux verze starší než 1.1.0-217. Po aktualizaci na nejnovější verzi agenta, spusťte následující příkaz získat nejnovější verzi modulu plug-in výstup `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problém: Pokoušíte se reonboard na nový pracovní prostor
Při pokusu o reonboard agenta na nový pracovní prostor, je potřeba vyčištění před reonboarding konfigurace agenta Log Analytics. Chcete-li vyčistit staré konfigurace z agenta, spusťte sady prostředí s `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Nebo

```
sudo sh ./onboard_agent.sh --purge
```

Můžete dál reonboard po použití `--purge` možnost

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Rozšíření agenta log Analytics na portálu Azure portal je označené stavu selhání: zřizování se nepovedlo

### <a name="probable-causes"></a>Možných příčin
* Agenta log Analytics se odebral z operačního systému
* Služba agenta log Analytics je mimo provoz, zakázané nebo není nakonfigurováno

### <a name="resolution"></a>Řešení 
Proveďte následující kroky, chcete-li opravit tento problém.
1. Odeberte rozšíření z webu Azure portal.
2. Nainstalujte agenta po [pokyny](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Restartujte agenta spuštěním následujícího příkazu: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Počkejte pár minut a změní stav zřizování **Zřizování proběhlo úspěšně**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problém: Upgrade agenta Log Analytics na vyžádání

### <a name="probable-causes"></a>Možných příčin

Balíčky agenta Log Analytics na hostiteli jsou zastaralé.

### <a name="resolution"></a>Řešení 
Proveďte následující kroky, chcete-li opravit tento problém.

1. Zkontrolujte nejnovější verzi [stránky](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Stáhněte si instalační skript (1.4.2-124 jako příklad verze):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Balíčky s upgradem spuštěním `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
