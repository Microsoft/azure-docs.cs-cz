---
title: Řešení potíží s agentem Azure Log Analytics Linux | Dokumentace Microsoftu
description: Popište příznaky, příčiny a řešení nejběžnějších problémů s agentem Log Analytics pro Linux v Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: b7036b305b4b1041fced3be68024be29d49a4990
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086867"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Řešení potíží pomocí agenta Log Analytics pro Linux 

Tento článek poskytuje nápovědu k řešení chyb, se kterými se můžete setkat s agentem Log Analytics pro Linux v Azure Monitor a navrhuje možná řešení k jejich řešení.

Pokud žádný z těchto kroků pro vás nejvhodnější, jsou také k dispozici následující kanály podpory:

* Zákazníci s výhodami Premier Support můžou otevřít žádost o podporu pomocí [Premier](https://premier.microsoft.com/).
* Zákazníci se smlouvou o podpoře Azure můžou na [Azure Portal](https://manage.windowsazure.com/?getsupport=true)otevřít žádost o podporu.
* Diagnostikujte problémy OMI pomocí [Průvodce odstraňováním potíží OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Navštivte stránku Log Analytics zpětnou vazbu pro kontrolu odeslaných nápadů a chyb [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) nebo zasouboru nového.  

## <a name="important-log-locations-and-log-collector-tool"></a>Umístění protokolu důležité a nástroj Kolektoru protokolů

 Soubor | Cesta
 ---- | -----
 Agenta log Analytics pro soubor protokolu systému Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Soubor protokolu log Analytics agenta konfigurace | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Doporučujeme použít nástroj pro shromažďování protokolů k načtení důležité protokoly pro řešení potíží nebo před odesláním problém na Githubu. Můžete si přečíst další informace o nástroji a o tom, jak ho spustit [tady](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Důležité konfigurační soubory

 Kategorie | Umístění souboru
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` nebo `/etc/rsyslog.conf` nebo `/etc/rsyslog.d/95-omsagent.conf`
 Výkon, Nagios, Zabbix výstup Log Analytics a obecné agenta | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Další konfigurace | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Pokud je kolekce nakonfigurovaná z [nabídky data Log Analytics Upřesnit nastavení](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) v Azure Portal pro váš pracovní prostor, upraví se konfigurační soubory pro čítače výkonu a syslog se přepíše. Chcete-li zakázat konfiguraci pro všechny agenty, zakažte shromažďování z Log Analytics **Upřesnit nastavení** nebo pro jednoho agenta spusťte následující příkaz:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Kódy chyb instalace

| Kód chyby | Význam |
| --- | --- |
| NOT_DEFINED | Protože nejsou nainstalované potřebné závislosti, nenainstalují se modul plug-in auoms démona auditd | Instalace se nezdařila, auoms nainstalovat balíček auditd. |
| 2 | Neplatná možnost sadě prostředí k dispozici. Spustit `sudo sh ./omsagent-*.universal*.sh --help` pro použití |
| 3 | Žádná možnost není k dispozici pro prostředí sady. Spusťte `sudo sh ./omsagent-*.universal*.sh --help` pro použití. |
| 4 | Neplatný typ balíčku nebo neplatné nastavení proxy serveru; balíčky omsagent-*ot*. sh lze instalovat pouze na systémy na bázi ot./min. a balíčky omsagent-*deb*. sh lze instalovat pouze v systémech založených na Debian. Doporučuje se použít univerzální instalační program z [nejnovější verze](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Přečtěte si také téma ověření nastavení proxy serveru. |
| 5 | Prostředí sady je nutné provést jako uživatel root nebo se Chyba 403 vrátil během registrace. Spusťte příkaz pomocí `sudo`. |
| 6 | Během připojování se vrátila Neplatná architektura balíčku nebo došlo k chybě 200. balíčky omsagent-*x64.sh lze instalovat pouze v systémech 64 a balíčky omsagent-* x86.sh lze instalovat pouze na 32 systémy. Stáhněte si správný balíček pro vaši architekturu z [nejnovější verze](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Instalace balíčku OMS se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 19 | Instalace balíčku OMI se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 20 | Instalace balíčku SCX se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 21 | Instalace zprostředkovatele sady se nezdařila. Prohlédněte si výstup příkazu kořenové selhání. |
| 22 | Instalace balíčku jako součást balíčku se nezdařila. Prohlédněte si výstup příkazu kořenové selhání |
| 23 | Balíček SCX nebo OMI už nainstalovaná. K instalaci sady prostředí použijte `--upgrade` místo `--install`. |
| 30 | Interní sada došlo k chybě. Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 55 | Nepodporovaná verze OpenSSL nebo se nemůže připojit k Azure Monitor nebo bázi dpkg je uzamčený nebo chybí ve složeném programu. |
| 61 | Chybějící ctypes knihovnu Pythonu. Nainstalujte Python ctypes knihovny nebo balíčku (python-ctypes). |
| 62 | Chybějící program tar, cíl instalace. |
| 63 | Chybějící sed program, instalace sed. |
| 64 | Chybějící program curl, instalace curl. |
| 65 | Chybějící gpg program, instalace gpg. |

## <a name="onboarding-error-codes"></a>Kódy chyb registrace

| Kód chyby | Význam |
| --- | --- |
| 2 | Neplatná možnost omsadmin skriptu předávají. Spusťte `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` pro použití. |
| 3 | Neplatná konfigurace skriptu omsadmin předávají. Spusťte `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` pro použití. |
| 4 | Neplatná adresa proxy omsadmin skriptu předávají. Ověřte proxy server a Prohlédněte si [dokumentaci k používání proxy serveru http](log-analytics-agent.md#network-firewall-requirements). |
| 5 | z Azure Monitor byla přijata chyba HTTP 403. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 6 | Od Azure Monitor přijata chyba HTTP, která není 200. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 7 | Nepovedlo se připojit k Azure Monitor. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 8 | Chyba připojení k pracovnímu prostoru Log Analytics. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 30 | Vnitřní chyba skriptu. Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 31 | ID chyby generování agenta. Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 32 | Chyba při generování certifikátů. Zobrazit celý výstup skriptu omsadmin podrobnosti. |
| 33 | Chyba při generování metaconfiguration pro omsconfig. Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 34 | Generování skriptu Metaconfiguration není k dispozici. Opakujte registraci pomocí `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Povolit protokolování ladění
### <a name="oms-output-plugin-debug"></a>Ladění modulu plug-in výstup OMS
 FluentD se pro úrovně přihlašování specifické pro modul plug-in vám umožňuje určit úrovně jiný protokol pro vstupy a výstupy. Pokud chcete pro výstup OMS zadat jinou úroveň protokolu, upravte obecnou konfiguraci agenta na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 V modulu plug-in OMS Output před koncem konfiguračního souboru změňte vlastnost `log_level` z `info` na `debug`:

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

Protokolování ladění umožňuje zobrazit dávková odeslání pro Azure Monitor oddělená typem, počet datových položek a dobu trvání odeslání:

*Příklad protokolu s povoleným laděním:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Podrobný výstup
Místo používání výstupního modulu plug-in OMS můžete také výstupní datové položky přímo do `stdout`, které jsou viditelné v souboru protokolu Log Analytics agenta pro Linux.

V Log Analytics obecný konfigurační soubor agenta v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`přidejte před každý řádek `#` výstupní modul plug-in OMS.

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

Pod výstupním modulem plug-in odkomentujte následující oddíl odebráním `#` před každým řádkem:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problém: nejde se připojit přes proxy server k Azure Monitor

### <a name="probable-causes"></a>Možných příčin
* Proxy zadaný během registrace byl nesprávný
* V datovém centru nejsou na seznamu povolených koncových bodů služby Azure Monitor a Azure Automation. 

### <a name="resolution"></a>Řešení
1. Azure Monitor Reonboard pomocí Log Analytics agenta pro Linux pomocí následujícího příkazu s povolenou možností `-v`. Umožňuje podrobný výstup agenta připojujícího se prostřednictvím proxy serveru k Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Projděte si část [nastavení proxy serveru](agent-manage.md#update-proxy-settings) , abyste ověřili, že jste správně nakonfigurovali agenta pro komunikaci prostřednictvím proxy server.    
* Dvakrát ověřte, zda jsou na seznamu povolených koncových bodů Azure Monitor následující:

    |Prostředek agenta| Porty | Směr |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.oms.opinsights.azure.com | Port 443| Příchozí a odchozí |  
    |*.blob.core.windows.net | Port 443| Příchozí a odchozí |  

    Pokud plánujete použít Azure Automation Hybrid Runbook Worker k připojení a registraci ve službě Automation pro použití sad Runbook nebo řešení správy ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v tématu [Konfigurace sítě pro Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

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


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problém: vidíte omiagent s využitím 100% CPU

### <a name="probable-causes"></a>Možných příčin
Regrese v NSS-PEM Package [v 1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) způsobila závažný problém s výkonem, takže jsme se seznámili s hodně v distribucích RedHat/CentOS 7. x. Další informace o tomto problému najdete v následující dokumentaci: Chyba [1667121 výkon regrese v libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

K chybám souvisejícím s výkonem nedojde po celou dobu a jejich reprodukování je velmi obtížné. Pokud se setkáte s tímto problémem s omiagent, měli byste použít skript omiHighCPUDiagnostics.sh, který bude shromažďovat trasování zásobníku omiagent, pokud překročení určité prahové hodnoty.

1. Stáhnout skript <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Spustit diagnostiku po dobu 24 hodin s 30% prahovou hodnotou procesoru <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Zásobník volání bude v souboru omiagent_trace dumpingové, pokud si všimnete mnoho volání funkcí NSS a, postupujte podle následujících kroků řešení.

### <a name="resolution-step-by-step"></a>Řešení (krok za krokem)

1. Upgradujte balíček NSS-PEM na verzi [v 1.0.3-5. el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Pokud NSS-PEM není k dispozici pro upgrade (většinou probíhá na CentOS), pak downgrade na 7.29.0-46. Pokud omylem spustíte "Yumu Update" (aktualizace), bude se tato možnost aktualizovat na 7.29.0-51 a k problému dojde znovu. <br/>
`sudo yum downgrade curl libcurl`

3. Restartovat OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problém: Se nezobrazují žádná data na webu Azure Portal

### <a name="probable-causes"></a>Možných příčin

- Připojování k Azure Monitor se nezdařilo.
- Připojení k Azure Monitor je blokované.
- Agenta log Analytics pro Linux data se zálohují.

### <a name="resolution"></a>Řešení
1. Ověřte, jestli Azure Monitor Registrace proběhla úspěšně, kontrolou, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard pomocí instrukcí pro `omsadmin.sh` příkazového řádku
3. Pokud používáte proxy server, podívejte se na postup řešení proxy server jste zadali dříve.
4. V některých případech když agenta Log Analytics pro Linux nemůže komunikovat se službou, data v agentovi je ve frontě velikost úplné vyrovnávací paměti, což je 50 MB. Agenta byste měli restartovat spuštěním následujícího příkazu: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Tento problém je vyřešen 1.1.0-28 verzi agenta a novější.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problém: Se nezobrazují v předaných zprávách Syslog 

### <a name="probable-causes"></a>Možných příčin
* Konfigurace pro Linux server nepovoluje kolekce odeslané zařízení a/nebo jeho úrovně
* Na Linux server není správně přeposílají Syslog
* Počet zpráv, které jsou předávány dál za sekundu, které jsou příliš velký pro základní konfigurace agenta Log Analytics pro Linux pro zpracování

### <a name="resolution"></a>Řešení
* Ověřte, že konfigurace v pracovním prostoru Log Analytics pro Syslog má všem zařízením a úrovních protokolování správné. Přečtěte si téma [Konfigurace kolekce syslog v Azure Portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ověřte, že se předané zprávy budou zobrazovat v nativním procesu pro zasílání zpráv syslog (`rsyslog``syslog-ng`).
* Zkontrolujte nastavení brány firewall na serveru Syslog a ujistěte se, že nejsou blokovány zprávy
* Simulace zprávy syslog pro Log Analytics pomocí příkazu `logger`
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problém: Dostáváte Errno adresa už používá v souboru protokolu omsagent
Pokud se zobrazí `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` v protokolu omsagent. log.

### <a name="probable-causes"></a>Možných příčin
Tato chyba označuje, že rozšíření Linux Diagnostic (LAD) nainstalovaná souběžně rozšíření virtuálního počítače s Linuxem Log Analytics a používá stejný port pro shromažďování dat protokolu syslog, omsagent.

### <a name="resolution"></a>Řešení
1. Jako uživatel root spusťte následující příkazy (Všimněte si, že je příkladem 25224 a je možné, že ve vašem prostředí se zobrazí jiné číslo portu používané LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Pak je potřeba upravit správný konfigurační soubor `rsyslogd` nebo `syslog_ng` a změnit konfiguraci související s LAD pro zápis na port 25229.

2. Pokud je virtuální počítač spuštěný `rsyslogd`, soubor, který se má upravit, je: `/etc/rsyslog.d/95-omsagent.conf` (pokud existuje, jinak `/etc/rsyslog`). Pokud je virtuální počítač spuštěný `syslog_ng`, soubor, který se má upravit, je: `/etc/syslog-ng/syslog-ng.conf`.
3. Restartujte omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Restartujte službu syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problém: Nemůžete odinstalovat omsagent pomocí možnosti mazání

### <a name="probable-causes"></a>Možných příčin

* Je nainstalovaná rozšíření Linux Diagnostic
* Diagnostické rozšíření Linuxu se instaluje a odinstaluje, ale stále zobrazí chyba o omsagent mdsd používána a nelze ji odebrat.

### <a name="resolution"></a>Řešení
1. Odinstalace rozšíření Linux Diagnostic (LAD).
2. Odeberte soubory diagnostických rozšíření pro Linux z počítače, pokud jsou k dispozici v následujícím umístění: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` a `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problém: Nemůžete vidět data žádná data Nagios 

### <a name="probable-causes"></a>Možných příčin
* Omsagent uživatel nemá oprávnění ke čtení ze souboru protokolu Nagios
* Zdroj Nagios a filtru nebyly neokomentovaném textu ze souboru omsagent.conf

### <a name="resolution"></a>Řešení
1. Pomocí následujících [pokynů](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)přidejte uživatele omsagent ke čtení ze souboru Nagios.
2. V obecném konfiguračním souboru Log Analytics agenta pro Linux v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`se ujistěte, **že je Nagios zdroj i filtr** odkomentovat.

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
* Připojování k Azure Monitor se nezdařilo.
* Připojení k Azure Monitor je blokované.
* Virtuální počítač byl restartován.
* OMI balíčku byl ručně upgradovat na novější verzi ve srovnání s co jste nainstalovali agenta Log Analytics pro linuxového balíčku
* V souboru protokolu `omsconfig.log` *nebyla nalezena chyba třídy* v protokolech prostředků DSC.
* Agenta log Analytics pro data se zálohují.
* *Aktuální konfigurace protokolu DSC neexistuje. Spusťte příkaz Start-DscConfiguration s parametrem-Path pro určení konfiguračního souboru a nejprve vytvořte aktuální konfiguraci.* v souboru protokolu `omsconfig.log` neexistují žádné zprávy protokolu týkající se `PerformRequiredConfigurationChecks` operací.

### <a name="resolution"></a>Řešení
1. Nainstalujte všechny závislosti, jako jsou démona auditd balíčku.
2. Ověřte, jestli se připojování k Azure Monitor úspěšné, kontrolou, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Pokud tomu tak není, reonboard pomocí [instrukcí](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)příkazového řádku omsadmin.sh.
4. Pokud používáte proxy server, zkontrolujte proxy server výše uvedených kroků pro řešení potíží.
5. U některých systémů Azure distribuce démon server OMI omid nespustí po restartování virtuálního počítače. Výsledkem bude nezobrazila se auditování, sledování změn ve nebo UpdateManagement řešení související data. Alternativním řešením je ruční spuštění serveru OMI spuštěním `sudo /opt/omi/bin/service_control restart`.
6. Po balíček OMI je ručně upgradovat na novější verzi, musí ručně restartovat agenta Log Analytics, aby fungovaly. Tento krok je nutný pro některé distribuce, kde OMI server se nespustí automaticky po dojde k upgradu. Spusťte `sudo /opt/omi/bin/service_control restart` pro restartování OMI.
7. Pokud se v omsconfig. log zobrazuje chyba *třídy* prostředků DSC, spusťte `sudo /opt/omi/bin/service_control restart`.
8. V některých případech platí, že když agent Log Analytics pro Linux nemůže komunikovat s Azure Monitor, data v agentovi se zálohují do plné velikosti vyrovnávací paměti: 50 MB. Agenta byste měli restartovat spuštěním následujícího příkazu `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Tento problém je vyřešen 1.1.0-28 verze agenta nebo novější
    >

* Pokud soubor protokolu `omsconfig.log` neindikuje, že se v systému pravidelně spouští `PerformRequiredConfigurationChecks` operace, může se jednat o problém s úlohou nebo službou cron. Ujistěte se, že v `/etc/cron.d/OMSConsistencyInvoker`existuje úloha cron. Pokud je potřeba spuštěním následujících příkazů vytvořte úlohu cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Také se ujistěte, zda že je spuštěna služba cron. K ověření stavu této služby můžete použít `service cron status` s Debian, Ubuntu, SUSE nebo `service crond status` s RHEL, CentOS Oracle Linux. Pokud služba neexistuje, můžete nainstalovat binární soubory a spustit službu následujícím způsobem:

    **Ubuntu/Debian**

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
**Background:** `omsconfig` je agent pro konfiguraci Log Analytics agenta pro Linux, který každých pět minut vyhledává novou konfiguraci na straně portálu. Tato konfigurace se následně použije na agenta Log Analytics pro Linux konfigurační soubory umístěné na /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* V některých případech nemusí být schopen komunikovat se službou konfigurace portálu, což vede k nejnovější konfiguraci nejsou nyní použity agenta Log Analytics pro konfiguraci agenta pro Linux.
  1. Ověřte, zda je nainstalován agent `omsconfig` spuštěním `dpkg --list omsconfig` nebo `rpm -qi omsconfig`.  Pokud nainstalovaná není, znovu nainstalujte nejnovější verzi agenta Log Analytics pro Linux.

  2. Ověřte, zda agent `omsconfig` může komunikovat s Azure Monitor spuštěním následujícího příkazu `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. Tento příkaz vrátí konfiguraci, že daný agent přijímá ze služby, včetně nastavení Syslog, čítače výkonu systému Linux a vlastní protokoly. Pokud tento příkaz neproběhne úspěšně, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Tento příkaz vynutí, aby měl agent omsconfig komunikaci s Azure Monitor a načíst nejnovější konfiguraci.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problém: Se nezobrazují žádné vlastní data protokolu 

### <a name="probable-causes"></a>Možných příčin
* Připojování k Azure Monitor se nezdařilo.
* Nastavení **použít následující konfiguraci pro servery Linux** nebylo vybráno.
* omsconfig nebyl neexistoval nejnovější konfiguraci vlastního protokolu ze služby.
* Agent Log Analytics pro Linux `omsagent` uživatele nemůže získat přístup k vlastnímu protokolu z důvodu oprávnění nebo nenalezených.  Mohou se zobrazit následující chyby:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Známý problém s opravených agenta Log Analytics pro Linux verze 1.1.0-217 konflikt časování

### <a name="resolution"></a>Řešení
1. Ověření připojení k Azure Monitor bylo úspěšné, když zkontrolujete, jestli tento soubor existuje: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Pokud ne, buď:  

  1. Reonboard pomocí [instrukcí](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)příkazového řádku omsadmin.sh.
  2. V části **Upřesnit nastavení** v Azure Portal zajistěte, aby bylo povolené nastavení **použít následující konfiguraci na servery se systémem Linux** .  

2. Ověřte, zda agent `omsconfig` může komunikovat s Azure Monitor spuštěním následujícího příkazu `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  Tento příkaz vrátí konfiguraci, že daný agent přijímá ze služby, včetně nastavení Syslog, čítače výkonu systému Linux a vlastní protokoly. Pokud tento příkaz neproběhne úspěšně, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Tento příkaz vynutí, aby měl agent omsconfig komunikaci s Azure Monitor a načíst nejnovější konfiguraci.

**Pozadí:** Místo agenta Log Analytics pro Linux spuštěný jako privilegovaný uživatel `root`se agent spouští jako `omsagent`ý uživatel. Ve většině případů musí udělit explicitní oprávnění tohoto uživatele za určité soubory pro čtení. Chcete-li udělit oprávnění uživateli `omsagent`, spusťte následující příkazy:

1. Přidat uživatele `omsagent` do konkrétní skupiny `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Udělte k požadovanému souboru univerzální přístup pro čtení `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Existuje známý problém s časování pomocí agenta Log Analytics pro Linux verze starší než 1.1.0-217. Po aktualizaci na nejnovějšího agenta spusťte následující příkaz, který získá nejnovější verzi výstupního modulu plug-in `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problém: Pokoušíte se reonboard na nový pracovní prostor
Při pokusu o reonboard agenta na nový pracovní prostor, je potřeba vyčištění před reonboarding konfigurace agenta Log Analytics. Pokud chcete vyčistit starou konfiguraci z agenta, spusťte sadu prostředků prostředí pomocí `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Nebo

```
sudo sh ./onboard_agent.sh --purge
```

Po použití možnosti `--purge` můžete pokračovat v reonboard.

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Rozšíření agenta log Analytics na portálu Azure portal je označené stavu selhání: zřizování se nepovedlo

### <a name="probable-causes"></a>Možných příčin
* Agenta log Analytics se odebral z operačního systému
* Služba agenta log Analytics je mimo provoz, zakázané nebo není nakonfigurováno

### <a name="resolution"></a>Řešení 
Proveďte následující kroky, chcete-li opravit tento problém.
1. Odeberte rozšíření z webu Azure portal.
2. Nainstalujte agenta podle [pokynů](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Restartujte agenta spuštěním následujícího příkazu: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Počkejte několik minut a stav zřizování se změní na **zřizování bylo úspěšné**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problém: Upgrade agenta Log Analytics na vyžádání

### <a name="probable-causes"></a>Možných příčin

Balíčky agenta Log Analytics na hostiteli jsou zastaralé.

### <a name="resolution"></a>Řešení 
Proveďte následující kroky, chcete-li opravit tento problém.

1. Podívejte se na nejnovější verzi na [stránce](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Stáhněte si instalační skript (1.4.2-124 jako příklad verze):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Upgradujte balíčky spuštěním `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
