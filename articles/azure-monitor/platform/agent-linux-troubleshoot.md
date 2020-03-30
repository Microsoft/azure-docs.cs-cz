---
title: Poradce při potížích s agentem Azure Log Analytics Linux | Dokumenty společnosti Microsoft
description: Popište příznaky, příčiny a řešení nejčastějších problémů s agentem Log Analytics pro Linux v Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 24aa3462aef4f719e93d17389ff342084f6c7864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668753"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Řešení potíží s agentem Log Analytics pro Linux 

Tento článek poskytuje pomoc při řešení chyb, ke kterým může dojít s agentem Log Analytics pro Linux v Azure Monitoru a navrhuje možná řešení k jejich vyřešení.

Pokud žádný z těchto kroků nefunguje, jsou k dispozici také následující kanály podpory:

* Zákazníci s výhodami podpory Premier mohou otevřít žádost o podporu pomocí [služby Premier](https://premier.microsoft.com/).
* Zákazníci se smlouvami o podpoře Azure můžou otevřít žádost o podporu [na webu Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Diagnostikujte problémy OMI s [příručkou pro řešení potíží s OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Soubor [GitHub problém](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Navštivte stránku Senáčse Log Analytics a prohlédněte si odeslané nápady a chyby [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) nebo ponořte nové.  

## <a name="important-log-locations-and-log-collector-tool"></a>Důležitá umístění protokolů a nástroj kolekcí protokolů

 File | Cesta
 ---- | -----
 Agent log Analytics pro soubor protokolu Linuxu | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Soubor protokolu Analytics konfigurace agenta | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Doporučujeme použít náš nástroj pro sběr protokolů k načtení důležitých protokolů pro řešení potíží nebo před odesláním problému githubu. Můžete si přečíst více o nástroji a jak jej spustit [zde](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Důležité konfigurační soubory

 Kategorie | Umístění souboru
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf`nebo `/etc/rsyslog.conf` nebo`/etc/rsyslog.d/95-omsagent.conf`
 Výkon, Nagios, Zabbix, Log Analytics výstup a generální agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Další konfigurace | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Úpravy konfiguračních souborů pro čítače výkonu a Syslog se přepíše, pokud je kolekce nakonfigurována z [nabídky Data Log Analytics Advanced Settings](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) na portálu Azure pro váš pracovní prostor. Chcete-li zakázat konfiguraci pro všechny agenty, zakažte kolekci z **rozšířeného nastavení** analýzy protokolů nebo pro jednoho agenta spusťte následující:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Kódy chyb instalace

| Kód chyby | Význam |
| --- | --- |
| NOT_DEFINED | Vzhledem k tomu, že nejsou nainstalovány potřebné závislosti, nebude modul plug-in auoms auditovaný nainstalován | Instalace auoms se nezdařila, instalační balíček auditován. |
| 2 | Neplatná možnost poskytnutá do balíčku prostředí. Spustit `sudo sh ./omsagent-*.universal*.sh --help` pro použití |
| 3 | Pro balíček skořepiny není k dispozici žádná možnost. Spustit `sudo sh ./omsagent-*.universal*.sh --help` pro použití. |
| 4 | Neplatný typ balíčku NEBO neplatná nastavení proxy serveru; omsagent-*rpm*.sh balíčky mohou být instalovány pouze na rpm-založené systémy, a omsagent-*deb*.sh balíčky mohou být instalovány pouze na Debian-založené systémy. Doporučujeme použít univerzální instalační program z [nejnovější verze](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Zkontrolujte také ověření nastavení proxy serveru. |
| 5 | Balíček prostředí musí být proveden jako root NEBO během registrace byla vrácena chyba 403. Spusťte `sudo`příkaz pomocí aplikace . |
| 6 | Neplatná architektura balíčku nebo došlo k chybě 200 chyba vrácena během registrace; balíčky x64.sh mohou*být instalovány pouze na 64bitových systémech a balíčky x86.sh omsagent*lze instalovat pouze na 32bitové systémy. Stáhněte si správný balíček pro vaši architekturu z [nejnovější verze](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Instalace balíčku OMS se nezdařila. Prohlédněte si výstup příkazu pro selhání kořenového adresáře. |
| 19 | Instalace balíčku OMI se nezdařila. Prohlédněte si výstup příkazu pro selhání kořenového adresáře. |
| 20 | Instalace balíčku SCX se nezdařila. Prohlédněte si výstup příkazu pro selhání kořenového adresáře. |
| 21 | Instalace sad zprostředkovatele se nezdařila. Prohlédněte si výstup příkazu pro selhání kořenového adresáře. |
| 22 | Instalace balíčku se nezdařila. Prohlédněte si výstup příkazu pro selhání kořenového adresáře |
| 23 | Balíček SCX nebo OMI již nainstalován. Místo `--upgrade` instalace `--install` balíčku skořepiny použijte. |
| 30 | Vnitřní chyba svazku. Soubor [GitHub problém](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 55 | Nepodporovaná verze openssl or Nelze se připojit k Programu Azure Monitor nebo dpkg je uzamčen nebo chybí curl program. |
| 61 | Chybí knihovna ctypes pythonu. Nainstalujte knihovnu nebo balíček pythonctypů (python-ctypes). |
| 62 | Chybějící dehtový program, nainstalujte dehet. |
| 63 | Chybějící sed program, nainstalujte sed. |
| 64 | Chybějící curl program, nainstalujte curl. |
| 65 | Chybějící gpg program, nainstalujte gpg. |

## <a name="onboarding-error-codes"></a>Kódy chyb při zapisování

| Kód chyby | Význam |
| --- | --- |
| 2 | Pro skript omsadmin je k dispozici neplatná možnost. Spustit `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` pro použití. |
| 3 | Neplatná konfigurace poskytnutá skriptu omsadmin. Spustit `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` pro použití. |
| 4 | Pro skript omsadmin je k dispozici neplatný proxy server. Ověřte proxy server a podívejte se na naši [dokumentaci pro použití http proxy](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 CHYBA HTTP přijatá z Azure Monitoru. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 6 | Z Azure Monitoru byla přijata chyba HTTP, která nebyla 200. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 7 | Nelze se připojit k Azure Monitoru. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 8 | Při přihlašování do pracovního prostoru Log Analytics došlo k chybě. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 30 | Vnitřní chyba skriptu. Soubor [GitHub problém](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 31 | ID agenta pro generování chyb. Soubor [GitHub problém](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 32 | Při generování certifikátů došlo k chybě. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 33 | Při generování metakonfigurace pro omsconfig došlo k chybě. Soubor [GitHub problém](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 34 | Skript generování metakonfigurace není k dispozici. Opakujte zaškolení `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`pomocí . |

## <a name="enable-debug-logging"></a>Povolit protokolování ladění
### <a name="oms-output-plugin-debug"></a>Ladění výstupního pluginu OMS
 FluentD umožňuje úrovně protokolování specifické pro plugin, což umožňuje určit různé úrovně protokolu pro vstupy a výstupy. Chcete-li určit jinou úroveň protokolu pro výstup OMS, upravte konfiguraci obecného agenta na adrese `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 Ve výstupním pluginu OMS před koncem konfiguračního souboru změňte `log_level` vlastnost z na `info` `debug`:

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

Protokolování ladění umožňuje zobrazit dávkové nahrávání do Azure Monitoru oddělené podle typu, počtu datových položek a času odeslání:

*Příklad protokolu povoleného ladění:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Podrobný výstup
Namísto použití výstupního pluginu OMS můžete `stdout`také výstup datových položek přímo do , který je viditelný v agentovi Log Analytics pro soubor protokolu Linux.

V konfiguračním souboru `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`obecného agenta Log Analytics `#` na je třeba zakomentovat modul plug-in výstupu OMS přidáním a před každý řádek:

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

Pod výstupní plugin, odkomentujte následující `#` část odstraněním v přední části každého řádku:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problém: Nelze se připojit prostřednictvím proxy serveru k Azure Monitoru.

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Proxy server zadaný během registrace byl nesprávný.
* Koncové body Azure Monitor a Azure Automation Service nejsou ve vašem datovém centru naseznamu povolených 

### <a name="resolution"></a>Řešení
1. Reonboard na Azure Monitor s agentem Log Analytics pro `-v` Linux pomocí následujícího příkazu s povolenou možností. Umožňuje podrobný výstup agenta připojení prostřednictvím proxy serveru azure monitoru. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. V části [Aktualizace nastavení proxy serveru](agent-manage.md#update-proxy-settings) ověřte, zda jste agenta správně nakonfigurovali ke komunikaci prostřednictvím proxy serveru.    
* Zkontrolujte, zda jsou na seznamu povolených následujících koncových bodů Azure Monitoru:

    |Prostředek agenta| Porty | Směr |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Přístav 443| Příchozí a odchozí |  
    |*.oms.opinsights.azure.com | Přístav 443| Příchozí a odchozí |  
    |*.blob.core.windows.net | Přístav 443| Příchozí a odchozí |  

    Pokud plánujete použít pracovník hybridní sady Runbook Azure Automation pro připojení a registraci ke službě Automation a k použití runbooků nebo řešení pro správu ve vašem prostředí, musí mít přístup k číslu portu a adresám URL popsaným v [části Konfigurace sítě pro pracovníka hybridní sady Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problém: Při pokusu o napalubě se zobrazí chyba 403

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Datum a čas je na Linux Serveru nesprávné 
* Použité ID pracovního prostoru a použitý klíč pracovního prostoru nejsou správné

### <a name="resolution"></a>Řešení

1. Zkontrolujte čas na serveru Linux s datem příkazu. Pokud je čas +/- 15 minut od aktuálního času, pak onboarding selže. Chcete-li opravit tuto aktualizaci datum a / nebo časové pásmo serveru Linux. 
2. Ověřte, zda jste nainstalovali nejnovější verzi agenta Log Analytics pro Linux.  Nejnovější verze vás nyní upozorní, pokud zkosení času způsobuje selhání registrace.
3. Reonboard pomocí správné id pracovního prostoru a klíč pracovního prostoru podle pokynů k instalaci dříve v tomto článku.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problém: Zobrazí se chyba 500 a 404 v souboru protokolu hned po zapnutí
Toto je známý problém, ke kterému dochází při prvním nahrání dat Linuxu do pracovního prostoru Log Analytics. To nemá vliv na odesílané údaje nebo servisní prostředí.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problém: Vidíte omiagent pomocí 100% CPU

### <a name="probable-causes"></a>Pravděpodobné příčiny
Regrese v balíčku nss-pem [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) způsobila závažný problém s výkonem, který jsme viděli hodně v distribucích Redhat / Centos 7.x. Další informace o tomto problému naleznete v následující dokumentaci: Chyba [1667121 Regrese výkonu v libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Chyby související s výkonem se nedějí po celou dobu a je velmi obtížné je reprodukovat. Pokud narazíte na takový problém s omiagent, měli byste použít skript omiHighCPUDiagnostics.sh který bude shromažďovat stopy zásobníku omiagent při překročení určité prahové hodnoty.

1. Stáhněte si skript <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Spuštění diagnostiky po dobu 24 hodin s prahem procesoru 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstack bude uložen v omiagent_trace souboru, Pokud si všimnete mnoha volání funkce Curl a NSS, postupujte podle kroků řešení níže.

### <a name="resolution-step-by-step"></a>Rozlišení (krok za krokem)

1. Upgradujte balíček nss-pem na [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Pokud nss-pem není k dispozici pro upgrade (většinou se stane na Centos), pak downgrade curl na 7.29.0-46. Pokud omylem spustíte "yum update", pak curl bude upgradován na 7.29.0-51 a problém se stane znovu. <br/>
`sudo yum downgrade curl libcurl`

3. Restartovat OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problém: Na webu Azure Portal se nezobrazují žádná data.

### <a name="probable-causes"></a>Pravděpodobné příčiny

- Registrace do Azure Monitoru se nezdařila.
- Připojení k Azure Monitoru je blokované.
- Agent Log Analytics pro data Linuxu je zálohován

### <a name="resolution"></a>Řešení
1. Zkontrolujte, jestli bylo připojení azure monitoru úspěšné, a zkontrolujte, jestli existuje následující soubor:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard pomocí `omsadmin.sh` pokynů příkazového řádku
3. Pokud používáte proxy server, podívejte se na kroky řešení proxy uvedené dříve.
4. V některých případech, když agent Analýzy protokolů pro Linux nemůže komunikovat se službou, data na agenta jsou zařazena do fronty na plnou velikost vyrovnávací paměti, což je 50 MB. Agent by měl být restartován `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`spuštěním následujícího příkazu: . 

    >[!NOTE]
    >Tento problém je vyřešen v agentverzi verze 1.1.0-28 a novější.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problém: Nezobrazují se vám přeposílané zprávy Syslogu. 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Konfigurace použitá pro server Linux neumožňuje shromažďování odeslaných zařízení a/nebo úrovní protokolu.
* Syslog není správně předáván na server Linuxu
* Počet zpráv předávaných za sekundu je příliš velký pro základní konfiguraci agenta Log Analytics pro Linux pro zpracování

### <a name="resolution"></a>Řešení
* Ověřte konfiguraci v pracovním prostoru Log Analytics pro Syslog má všechna zařízení a správné úrovně protokolu. Kontrola [konfigurace kolekce Syslog na webu Azure Portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ověřte, zda nativní daemony`rsyslog` `syslog-ng`zasílání zpráv syslogu ( , ) mohou přijímat předané zprávy
* Zkontrolujte nastavení brány firewall na serveru Syslog a ujistěte se, že zprávy nejsou blokovány.
* Simulace zprávy Syslog do `logger` log Analytics pomocí příkazu
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problém: Přijímáte adresu Errno, která je již používána v souboru protokolu omsagent.
Pokud vidíte `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` v omsagent.log.

### <a name="probable-causes"></a>Pravděpodobné příčiny
Tato chyba označuje, že linuxové diagnostické rozšíření (LAD) je nainstalovánvedle log Analytics linuxové virtuální počítače rozšíření a používá stejný port pro shromažďování dat syslog jako omsagent.

### <a name="resolution"></a>Řešení
1. Jako root proveďte následující příkazy (všimněte si, že 25224 je příkladem a je možné, že ve vašem prostředí se zobrazí jiné číslo portu používané LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Potom je třeba upravit `rsyslogd` `syslog_ng` správný nebo konfigurační soubor a změnit konfiguraci související s ladem pro zápis na port 25229.

2. Pokud je virtuální `rsyslogd`svět spuštěn , soubor, `/etc/rsyslog.d/95-omsagent.conf` který má být `/etc/rsyslog`změněn, je: (pokud existuje, jinak ). Pokud je virtuální `syslog_ng`virtuální svět spuštěný , `/etc/syslog-ng/syslog-ng.conf`soubor, který má být změněn, je: .
3. Restartujte omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Restartujte službu syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problém: Nelze odinstalovat omsagent pomocí možnosti vyčistit

### <a name="probable-causes"></a>Pravděpodobné příčiny

* Je nainstalováno linuxové diagnostické rozšíření
* Linux Diagnostic Extension byl nainstalován a odinstalován, ale stále se zobrazí chyba omsagent používá mdsd a nelze je odebrat.

### <a name="resolution"></a>Řešení
1. Odinstalujte linuxové diagnostické rozšíření (LAD).
2. Odeberte soubory linuxové diagnostické přípony z počítače, pokud jsou přítomny v následujícím umístění: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` a `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problém: Nelze zobrazit data na nagios data 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Uživatel Omsagent nemá oprávnění ke čtení ze souboru protokolu Nagios
* Nagios zdroj a filtr nebyly uncommented ze souboru omsagent.conf

### <a name="resolution"></a>Řešení
1. Přidejte omsagent uživatele číst ze souboru Nagios podle následujících [pokynů](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. V agenta Log Analytics pro `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`linuxový obecný konfigurační soubor na na , ujistěte **se,** že zdroj Nagios a filtr jsou bez komentáře.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problém: Nevidíte žádná data Linuxu 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Registrace do Azure Monitoru se nezdařila.
* Připojení k Azure Monitoru je blokované.
* Virtuální počítač byl restartován
* Balíček OMI byl ručně upgradován na novější verzi ve srovnání s tím, co bylo nainstalováno agentem Log Analytics pro balíček Linux
* Třída protokolů prostředků DSC `omsconfig.log` *nebyla v* souboru protokolu nalezena chyba
* Agent log Analytics pro data je zálohován
* Protokoly DSC *Aktuální konfigurace neexistuje. Spusťte příkaz Start-DscConfiguration s parametrem -Path, abyste určili konfigurační soubor a nejprve vytvořili aktuální konfiguraci.* v `omsconfig.log` souboru protokolu, ale neexistuje `PerformRequiredConfigurationChecks` žádná zpráva protokolu o operacích.

### <a name="resolution"></a>Řešení
1. Nainstalujte všechny závislosti, jako je auditovaný balíček.
2. Zkontrolujte, jestli bylo přizapisování do Azure Monitoru úspěšné, a zkontrolujte, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Pokud tomu tak nebylo, reonboard pomocí omsadmin.sh [pokyny](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)příkazového řádku .
4. Pokud používáte proxy server, zkontrolujte výše uvedený postup řešení potíží s proxy serverem.
5. V některých distribučních systémech Azure omid OMI server daemon nespustí po restartování virtuálního počítače. Výsledkem budou nezoaškovat data související s auditem, ChangeTracking nebo UpdateManagement. Řešení je ručně spustit omi server `sudo /opt/omi/bin/service_control restart`spuštěním .
6. Po omi balíček je ručně upgradován na novější verzi, musí být ručně restartován pro agenta Log Analytics pokračovat v provozu. Tento krok je vyžadován pro některé distribuce, kde se omi server po upgradu automaticky nespustí. Spusťte `sudo /opt/omi/bin/service_control restart` restartování OMI.
7. Pokud se v souboru omsconfig.log zobrazí chyba `sudo /opt/omi/bin/service_control restart` *třídy* prostředků DSC, spusťte .
8. V některých případech, když agent Analýzy protokolů pro Linux nemůže mluvit s Azure Monitor, data na agenta se zálohuje na plnou velikost vyrovnávací paměti: 50 MB. Agent by měl být restartován `/opt/microsoft/omsagent/bin/service_control restart`spuštěním následujícího příkazu .

    >[!NOTE]
    >Tento problém je vyřešen v agentu verze 1.1.0-28 nebo novějším
    >

* Pokud `omsconfig.log` soubor protokolu neznamená, že `PerformRequiredConfigurationChecks` operace jsou v systému pravidelně spuštěny, může být problém s úlohou cron/službou. Ujistěte se, že `/etc/cron.d/OMSConsistencyInvoker`cron úloha existuje pod . V případě potřeby spusťte následující příkazy k vytvoření úlohy cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Také se ujistěte, že je spuštěna služba cron. Můžete použít `service cron status` s Debian, Ubuntu, `service crond status` SUSE, nebo s RHEL, CentOS, Oracle Linux zkontrolovat stav této služby. Pokud služba neexistuje, můžete nainstalovat binární soubory a spustit službu pomocí následujícího:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problém: Při konfiguraci kolekce z portálu pro čítače výkonu Syslog nebo Linux, nastavení nejsou použity

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Agent Log Analytics pro Linux nezachytil nejnovější konfiguraci
* Změněná nastavení na portálu nebyla použita.

### <a name="resolution"></a>Řešení
**Pozadí:** `omsconfig` je agent Log Analytics pro konfigurační agent Linuxu, který hledá novou konfiguraci na straně portálu každých pět minut. Tato konfigurace se pak použije pro agenta Log Analytics pro konfigurační soubory Linux umístěných na adrese /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* V některých případech agent log Analytics pro konfigurační agent Linuxu nemusí být schopen komunikovat se službou konfigurace portálu, což má za následek nejnovější konfiguraci, která není použita.
  1. Zkontrolujte, `omsconfig` zda je `dpkg --list omsconfig` agent `rpm -qi omsconfig`nainstalován spuštěním nebo .  Pokud není nainstalován, přeinstalujte nejnovější verzi agenta Log Analytics pro Linux.

  2. Zkontrolujte, `omsconfig` zda agent může komunikovat s `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Azure Monitor spuštěním následujícího příkazu . Tento příkaz vrátí konfiguraci, kterou agent obdrží ze služby, včetně nastavení Syslogu, čítačů výkonu Linuxu a vlastních protokolů. Pokud se tento příkaz nezdaří, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Tento příkaz vynutí agenta omsconfig mluvit s Azure Monitor a načíst nejnovější konfiguraci.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problém: Nezobrazují se žádná vlastní data protokolu. 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Registrace do Azure Monitoru se nezdařila.
* Nastavení **Použít následující konfiguraci na serverech Linux** nebylo vybráno.
* omsconfig nezachytil nejnovější vlastní konfiguraci protokolu ze služby.
* Agent Log Analytics `omsagent` pro uživatele Linuxu nemá přístup k vlastnímu protokolu z důvodu oprávnění nebo není nalezen.  Mohou se zobrazit následující chyby:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Známý problém s sporem byl opraven v agentovi Log Analytics pro Linux verze 1.1.0-217

### <a name="resolution"></a>Řešení
1. Ověření registrace na Azure Monitor bylo úspěšné kontrolou, pokud `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`existuje následující soubor: . Pokud ne, buď:  

  1. Reonboard pomocí [pokynů](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)příkazového řádku omsadmin.sh .
  2. V části **Upřesnit nastavení** na webu Azure Portal zkontrolujte, zda je povoleno nastavení **Použít následující konfiguraci na serverech Linux.**  

2. Zkontrolujte, `omsconfig` zda agent může komunikovat s `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Azure Monitor spuštěním následujícího příkazu .  Tento příkaz vrátí konfiguraci, kterou agent obdrží ze služby, včetně nastavení Syslogu, čítačů výkonu Linuxu a vlastních protokolů. Pokud se tento příkaz nezdaří, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Tento příkaz vynutí agenta omsconfig mluvit s Azure Monitor a načíst nejnovější konfiguraci.

**Souvislosti:** Místo agenta Log Analytics pro Linux, který `root`běží jako privilegovaný uživatel - , agent běží jako `omsagent` uživatel. Ve většině případů musí být tomuto uživateli udělena explicitní oprávnění, aby bylo možné číst určité soubory. Chcete-li `omsagent` uživateli udělit oprávnění, spusťte následující příkazy:

1. Přidání `omsagent` uživatele do určité skupiny`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Udělit univerzální přístup pro čtení k požadovanému souboru`sudo chmod -R ugo+rx <FILE DIRECTORY>`

Existuje známý problém s spor s spor s agentem Log Analytics pro linuxovou verzi starší než 1.1.0-217. Po aktualizaci na nejnovějšího agenta, spusťte následující `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`příkaz získat nejnovější verzi výstupního pluginu .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problém: Pokoušíte se znovu nastoupit do nového pracovního prostoru
Při pokusu o reonboard agenta do nového pracovního prostoru, konfigurace agenta Log Analytics je třeba vyčistit před reonboarding. Chcete-li vyčistit starou konfiguraci od agenta, spusťte balíček skořepin`--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Nebo

```
sudo sh ./onboard_agent.sh --purge
```

Můžete pokračovat reonboard po `--purge` použití možnosti

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Rozšíření agenta Analýzy protokolů na webu Azure Portal je označeno stavem selhání: Zřizování se nezdařilo

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Agent log Analytics byl odebrán z operačního systému.
* Služba agenta Log Analytics je vypnutá, zakázaná nebo není nakonfigurována.

### <a name="resolution"></a>Řešení 
Chcete-li problém opravit, proveďte následující kroky.
1. Odeberte rozšíření z webu Azure Portal.
2. Nainstalujte agenta podle [pokynů](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Restartujte agenta spuštěním `sudo /opt/microsoft/omsagent/bin/service_control restart`následujícího příkazu: .
* Počkejte několik minut a změny stavu zřizování **zřizování byly úspěšné**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problém: Upgrade agenta Log Analytics na vyžádání

### <a name="probable-causes"></a>Pravděpodobné příčiny

Balíčky agenta Log Analytics na hostiteli jsou zastaralé.

### <a name="resolution"></a>Řešení 
Chcete-li problém opravit, proveďte následující kroky.

1. Zkontrolujte nejnovější verzi na [stránce](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Stáhnout instalační skript (1.4.2-124 jako příklad verze):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Inovujte `sudo sh ./omsagent-*.universal.x64.sh --upgrade`balíčky spuštěním .
