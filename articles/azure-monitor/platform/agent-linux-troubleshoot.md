---
title: Řešení potíží s agentem Azure Log Analytics Linux | Microsoft Docs
description: Popište příznaky, příčiny a řešení nejběžnějších problémů s agentem Log Analytics pro Linux v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 2b811b1ace646cc4e0a93b937fbb90cfbf7aec0f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704890"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Řešení potíží s agentem Log Analytics pro Linux 

Tento článek poskytuje nápovědu k řešení chyb, se kterými se můžete setkat s agentem Log Analytics pro Linux v Azure Monitor a navrhuje možná řešení k jejich řešení.

Pokud žádný z těchto kroků nefunguje za vás, jsou k dispozici i tyto kanály podpory:

* Zákazníci s výhodami Premier Support můžou otevřít žádost o podporu pomocí [Premier](https://premier.microsoft.com/).
* Zákazníci se smlouvou o podpoře Azure můžou na [Azure Portal](https://manage.windowsazure.com/?getsupport=true)otevřít žádost o podporu.
* Diagnostikujte problémy OMI pomocí [Průvodce odstraňováním potíží OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Navštivte stránku Log Analytics zpětnou vazbu pro kontrolu odeslaných nápadů a chyb [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) nebo pro nové soubory. 

## <a name="log-analytics-troubleshooting-tool"></a>Nástroj pro řešení potíží s Log Analytics

Nástroj pro řešení potíží s nástrojem Log Analytics pro Linux agentů je skript určený k hledání a diagnostice problémů s agentem Log Analytics. Je automaticky součástí agenta při instalaci. Spuštění nástroje by mělo být prvním krokem při diagnostikování problému.

### <a name="how-to-use"></a>Jak používat
Nástroj pro řešení potíží lze spustit vložením následujícího příkazu do okna terminálu na počítači s agentem Log Analytics: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Ruční instalace
Nástroj pro řešení potíží je automaticky zahrnutý po instalaci agenta Log Analytics. Pokud se ale instalace nezdařila, můžete ji také nainstalovat ručně pomocí následujícího postupu.

1. Zkopírujte do svého počítače skupinu Poradce při potížích: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Rozbalení sady: `tar -xzvf omsagent_tst.tar.gz`
3. Spusťte ruční instalaci: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Zahrnuté scénáře
Níže je uveden seznam scénářů zkontrolovaných nástrojem pro řešení potíží:

1. Agent není v pořádku, prezenční signál nefunguje správně.
2. Agent se nespustí, nemůže se připojit k Log Analytics Services
3. Protokol syslog agenta nefunguje
4. Agent má vysoké využití procesoru nebo paměti.
5. Agent s problémy s instalací
6. Vlastní protokoly agenta nefungují.
7. Shromažďovat protokoly agentů

Další podrobnosti najdete v [dokumentaci k GitHubu](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md).

 >[!NOTE]
 >Pokud se setkáte s problémem, spusťte prosím nástroj kolektor protokolů. S tím, jak protokoly začnou mít na začátku, výrazně pomůžou tým podpory řešit potíže rychleji.

## <a name="purge-and-re-install-the-linux-agent"></a>Vyprázdnit a Re-Install agenta pro Linux

Zjistili jsme, že při čisté opakované instalaci agenta se vyřeší i většina problémů. Je možné, že se jedná o první návrh z podpory pro získání agenta do uncurropted stavu od našeho týmu podpory. Spuštění Poradce při potížích, shromáždění protokolů a pokus o čistou opětovnou instalaci vám pomůže rychleji řešit problémy.

1. Stáhněte si skript pro vyprázdnění:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Spusťte skript vyprázdnění (s oprávněními sudo):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Důležitá umístění protokolů a nástroj kolektoru protokolů

 Soubor | Cesta
 ---- | -----
 Soubor protokolu Log Analytics agenta pro Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Soubor protokolu konfigurace agenta Log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Doporučujeme použít náš nástroj kolektoru protokolů k načtení důležitých protokolů pro řešení potíží nebo před odesláním problému GitHubu. Můžete si přečíst další informace o nástroji a o tom, jak ho spustit [tady](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Důležité konfigurační soubory

 Kategorie | Umístění souboru
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` nebo `/etc/rsyslog.conf` nebo `/etc/rsyslog.d/95-omsagent.conf`
 Výkon, Nagios, Zabbix, výstup Log Analytics a generální agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Další konfigurace | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Pokud je kolekce nakonfigurovaná z [nabídky data Log Analytics Upřesnit nastavení](./agent-data-sources.md#configuring-data-sources) v Azure Portal pro váš pracovní prostor, upraví se konfigurační soubory pro čítače výkonu a syslog se přepíše. Chcete-li zakázat konfiguraci pro všechny agenty, zakažte shromažďování z Log Analytics **Upřesnit nastavení** nebo pro jednoho agenta spusťte následující příkaz:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Kódy chyb instalace

| Kód chyby | Význam |
| --- | --- |
| NOT_DEFINED | Vzhledem k tomu, že nezbytné závislosti nejsou nainstalovány, modul plug-in auoms audit nebude nainstalován. | Instalace auoms selhala, nainstalujte balíček audited. |
| 2 | Pro sadu prostředí se dala zadat neplatná možnost. Spustit `sudo sh ./omsagent-*.universal*.sh --help` pro použití |
| 3 | Sada Shell neposkytla žádnou možnost. Spustit `sudo sh ./omsagent-*.universal*.sh --help` pro použití. |
| 4 | Neplatný typ balíčku nebo neplatné nastavení proxy serveru; balíčky omsagent-*ot*. sh lze instalovat pouze na systémy na bázi ot./min. a balíčky omsagent-*deb*. sh lze instalovat pouze v systémech založených na Debian. Doporučuje se použít univerzální instalační program z [nejnovější verze](../learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Přečtěte si také téma ověření nastavení proxy serveru. |
| 5 | Sada prostředí musí být spuštěná jako kořenová nebo během připojování se vrátila 403 chyba. Spusťte příkaz pomocí příkazu `sudo` . |
| 6 | Během připojování se vrátila Neplatná architektura balíčku nebo došlo k chybě 200. balíčky omsagent-*x64.sh lze instalovat pouze v systémech 64 a balíčky omsagent-* x86.sh lze instalovat pouze na 32 systémy. Stáhněte si správný balíček pro vaši architekturu z [nejnovější verze](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Instalace balíčku OMS se nezdařila. Prohlédněte si výstup příkazu pro kořenovou chybu. |
| 19 | Instalace balíčku OMI se nezdařila. Prohlédněte si výstup příkazu pro kořenovou chybu. |
| 20 | Instalace balíčku SCX se nezdařila. Prohlédněte si výstup příkazu pro kořenovou chybu. |
| 21 | Instalace sad poskytovatele se nezdařila. Prohlédněte si výstup příkazu pro kořenovou chybu. |
| 22 | Nepovedlo se nainstalovat balíček balíčku. Prohlédněte si výstup příkazu pro kořenovou chybu. |
| 23 | Balíček SCX nebo OMI je již nainstalován. Použijte `--upgrade` místo toho `--install` k instalaci sady prostředků prostředí. |
| 30 | Vnitřní chyba sady prostředků Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 55 | Nepodporovaná verze OpenSSL nebo se nemůže připojit k Azure Monitor nebo bázi dpkg je uzamčený nebo chybí ve složeném programu. |
| 61 | Chybí knihovna ctypes Pythonu. Nainstalujte knihovnu nebo balíček python ctypes (Python-ctypes). |
| 62 | Chybí program TAR, nainstalujte tar. |
| 63 | Chybí program sed, nainstalujte SED. |
| 64 | Chybí kudrlinkou program, nainstalujte kudrlinkou. |
| 65 | Chybějící program gpg, nainstalujte GPG. |

## <a name="onboarding-error-codes"></a>Chybové kódy při připojování

| Kód chyby | Význam |
| --- | --- |
| 2 | Skriptu omsadmin je zadaná neplatná možnost. Spustit `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` pro použití. |
| 3 | Skriptu omsadmin je zadaná neplatná konfigurace. Spustit `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` pro použití. |
| 4 | Skriptu omsadmin byl poskytnut neplatný proxy server. Ověřte proxy server a Prohlédněte si [dokumentaci k používání proxy serveru http](log-analytics-agent.md#firewall-requirements). |
| 5 | z Azure Monitor byla přijata chyba HTTP 403. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 6 | Od Azure Monitor přijata chyba HTTP, která není 200. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 7 | Nepovedlo se připojit k Azure Monitor. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 8 | Při připojování k pracovnímu prostoru Log Analytics došlo k chybě. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 30 | Došlo k vnitřní chybě skriptu. Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 31 | Při generování ID agenta došlo k chybě. Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 32 | Při generování certifikátů došlo k chybě. Podrobnosti najdete v úplném výstupu skriptu omsadmin. |
| 33 | Při generování metaconfiguration pro omsconfig došlo k chybě. Zapište [problém GitHubu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) s podrobnostmi z výstupu. |
| 34 | Skript generování Metaconfiguration není k dispozici. Opakujte registraci pomocí `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` . |

## <a name="enable-debug-logging"></a>Povolit protokolování ladění
### <a name="oms-output-plugin-debug"></a>Ladění v programu OMS výstup plug-in
 Je náročné povolit úrovně protokolování specifické pro modul plug-in, které umožňují zadat různé úrovně protokolu pro vstupy a výstupy. Pokud chcete pro výstup OMS zadat jinou úroveň protokolu, upravte obecnou konfiguraci agenta na adrese `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .  

 V modulu plug-in OMS výstupu nástroje změňte před koncem konfiguračního souboru `log_level` vlastnost `info` na `debug` :

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
Místo používání výstupního modulu plug-in OMS můžete také výstupní datové položky přímo do `stdout` , který je viditelný v souboru protokolu Log Analytics Agent pro Linux.

V části Log Analytics obecný konfigurační soubor agenta na `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` , přidejte před každý řádek do výstupního modulu plug-in OMS `#` .

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

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Proxy server zadaný během připojování byl nesprávný.
* V seznamu schválených v datovém centru nejsou zahrnuté koncové body služby Azure Monitor a Azure Automation. 

### <a name="resolution"></a>Řešení
1. Reonboard se Azure Monitor na Log Analytics agenta pro Linux pomocí následujícího příkazu s `-v` povolenou možností. Umožňuje podrobný výstup agenta připojujícího se prostřednictvím proxy serveru k Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Projděte si část [nastavení proxy serveru](agent-manage.md#update-proxy-settings) , abyste ověřili, že jste správně nakonfigurovali agenta pro komunikaci prostřednictvím proxy server.    

3. Ověřte, že koncové body, které jsou uvedené v seznamu [požadavků brány firewall Azure monitor sítě](log-analytics-agent.md#firewall-requirements) , se přidají do seznamu povolených hodnot správně. Pokud používáte Azure Automation, propojí se i nezbytné kroky konfigurace sítě.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problém: při pokusu o zprovoznění se zobrazí chyba 403.

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Datum a čas není na serveru Linux správný. 
* Použité ID pracovního prostoru a klíč pracovního prostoru nejsou správné.

### <a name="resolution"></a>Řešení

1. Ověřte čas na serveru Linux s datem příkazu. Pokud je čas +/-15 minut od aktuálního času, registrace se nezdařila. Chcete-li tuto aktualizaci opravit, datum nebo časové pásmo serveru Linux. 
2. Ověřte, že máte nainstalovanou nejnovější verzi agenta Log Analytics pro Linux.  Nejnovější verze nyní upozorňuje na to, jestli časové zkosení způsobuje selhání připojování.
3. Reonboard pomocí správného ID pracovního prostoru a klíče pracovního prostoru podle pokynů k instalaci uvedených výše v tomto článku.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problém: v souboru protokolu se zobrazí chyba 500 a 404, která se zobrazuje hned po registraci.
Jedná se o známý problém, který nastane při prvním odeslání dat systému Linux do Log Analyticsho pracovního prostoru. To nemá vliv na přenášená data ani na provozní prostředí.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problém: vidíte omiagent s využitím 100% CPU

### <a name="probable-causes"></a>Pravděpodobné příčiny
Regrese v NSS-PEM Package [v 1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) způsobila závažný problém s výkonem, takže jsme se seznámili s hodně v distribucích RedHat/CentOS 7. x. Další informace o tomto problému najdete v následující dokumentaci: Chyba [1667121 výkon regrese v libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

K chybám souvisejícím s výkonem nedojde po celou dobu a jejich reprodukování je velmi obtížné. Pokud se setkáte s tímto problémem s omiagent, měli byste použít skript omiHighCPUDiagnostics.sh, který bude shromažďovat trasování zásobníku omiagent, pokud překročení určité prahové hodnoty.

1. Stáhnout skript <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Spustit diagnostiku po dobu 24 hodin s 30% prahovou hodnotou procesoru <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Zásobník volání bude v souboru omiagent_trace dumpingové, pokud si všimnete mnoho volání funkcí NSS a, postupujte podle následujících kroků řešení.

### <a name="resolution-step-by-step"></a>Řešení (krok za krokem)

1. Upgradujte balíček NSS-PEM na verzi [v 1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Pokud NSS-PEM není k dispozici pro upgrade (většinou probíhá na CentOS), pak downgrade na 7.29.0-46. Pokud omylem spustíte "Yumu Update" (aktualizace), bude se tato možnost aktualizovat na 7.29.0-51 a k problému dojde znovu. <br/>
`sudo yum downgrade curl libcurl`

3. Restartovat OMI: <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problém: nezobrazuje se předávané zprávy syslog 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Konfigurace použitá pro Linux Server nepovoluje shromažďování úrovní odeslaných zařízení a/nebo protokolů.
* Protokol syslog není správně předáván na server Linux.
* Počet zpráv předávaných za sekundu je moc velký, aby se základní konfigurace agenta Log Analytics pro systém Linux mohla zpracovat.

### <a name="resolution"></a>Řešení
* Ověřte, že konfigurace v pracovním prostoru Log Analytics pro syslog má všechna zařízení a správné úrovně protokolu. Přečtěte si téma [Konfigurace kolekce syslog v Azure Portal](./data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ověřte, jestli se v nativním procesu pro zasílání zpráv syslog ( `rsyslog` , `syslog-ng` ) můžou přijímat předané zprávy.
* Zkontrolujte nastavení brány firewall na serveru syslog, abyste zajistili, že se zprávy neblokují.
* Simulace zprávy syslog pro Log Analytics pomocí `logger` příkazu
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problém: přijímáte errno adresu, která se už používá v souboru protokolu omsagent
Pokud se zobrazí `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` v omsagent. log.

### <a name="probable-causes"></a>Pravděpodobné příčiny
Tato chyba znamená, že diagnostické rozšíření Linux (LAD) je nainstalovaná souběžně s rozšířením virtuálního počítače s Log Analytics Linux a používá stejný port pro shromažďování dat syslog jako omsagent.

### <a name="resolution"></a>Řešení
1. Jako kořen spusťte následující příkazy (Všimněte si, že 25224 je příklad a je možné, že ve vašem prostředí vidíte jiné číslo portu, které používá LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Pak je nutné upravit správný `rsyslogd` nebo `syslog_ng` konfigurační soubor a změnit konfiguraci související s lad na zápis na port 25229.

2. Pokud je virtuální počítač spuštěný `rsyslogd` , soubor, který se má upravit, je: `/etc/rsyslog.d/95-omsagent.conf` (pokud existuje, jinak `/etc/rsyslog` ). Pokud je virtuální počítač spuštěný `syslog_ng` , soubor, který se má upravit, je: `/etc/syslog-ng/syslog-ng.conf` .
3. Restartujte omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart` .
4. Restartujte službu syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problém: nemůžete odinstalovat omsagent pomocí možnosti vyprázdnění.

### <a name="probable-causes"></a>Pravděpodobné příčiny

* Je nainstalované diagnostické rozšíření pro Linux.
* Diagnostické rozšíření Linux bylo nainstalováno a odinstalováno, ale stále se zobrazuje chyba týkající se omsagent používaných nástrojem MDSD a nelze ji odebrat.

### <a name="resolution"></a>Řešení
1. Odinstalujte diagnostické rozšíření pro Linux (LAD).
2. Odeberte soubory diagnostických rozšíření pro Linux z počítače, pokud jsou k dispozici v následujícím umístění: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` a `/var/opt/microsoft/omsagent/LAD/` .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problém: nemůžete zobrazit žádná data Nagios. 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Uživatel Omsagent nemá oprávnění ke čtení ze souboru protokolu Nagios.
* Nagios zdroj a filtr se nepřidaly do souboru omsagent. conf.

### <a name="resolution"></a>Řešení
1. Pomocí následujících [pokynů](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)přidejte uživatele omsagent ke čtení ze souboru Nagios.
2. V obecném konfiguračním souboru Log Analytics agenta pro Linux v `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` Zkontrolujte, že  se Nagios zdroj i filtr odkomentovat.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problém: nevidíte žádná data Linux 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Připojování k Azure Monitor se nezdařilo.
* Připojení k Azure Monitor je blokované.
* Virtuální počítač se restartoval.
* Balíček OMI se ručně upgradoval na novější verzi ve srovnání s instalací balíčku Log Analytics Agent pro Linux.
* OMI je zmrazený, blokuje agenta OMS.
* V souboru protokolu *se nenašla chyba třídy* prostředků DSC. `omsconfig.log`
* Zálohuje se Log Analytics agenta pro data
* *Aktuální konfigurace protokolu DSC neexistuje. Spusťte Start-DscConfiguration příkazu s parametrem-Path pro určení konfiguračního souboru a nejprve vytvořte aktuální konfiguraci.* v `omsconfig.log` souboru protokolu neexistují žádná zpráva protokolu o `PerformRequiredConfigurationChecks` operacích.

### <a name="resolution"></a>Řešení
1. Nainstalujte všechny závislosti jako auditované balíčky.
2. Ověřte, jestli se připojování k Azure Monitor úspěšné, kontrolou, jestli existuje následující soubor: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Pokud tomu tak není, reonboard pomocí [instrukcí](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)příkazového řádku omsadmin.sh.
4. Pokud používáte proxy server, Projděte si výše uvedené kroky pro řešení potíží s proxy serverem.
5. V některých distribučních systémech Azure se démon OMID OMI serveru nespustí po restartování virtuálního počítače. Výsledkem bude, že se nebudou zobrazovat data týkající se auditu, sledování změn ve nebo UpdateManagement řešení. Alternativním řešením je ruční spuštění serveru OMI spuštěním `sudo /opt/omi/bin/service_control restart` .
6. Po manuální aktualizaci balíčku OMI na novější verzi se musí ručně restartovat, aby agent Log Analytics pokračovat v fungování. Tento krok je nutný pro některé distribuce, kde se OMI Server po upgradu nespustí automaticky. Spusťte příkaz `sudo /opt/omi/bin/service_control restart` k RESTARTOVÁNÍ OMI.
* V některých situacích může být OMI zmrazený. Agent OMS může zadat blokovaný stav čekající na OMI, který blokuje všechna shromažďování dat. Proces agenta OMS se spustí, ale neproběhne žádná aktivita, která by prokáže žádné nové řádky protokolu (například odeslané prezenční signály) v `omsagent.log` . Restartujte OMI pomocí `sudo /opt/omi/bin/service_control restart` a obnovte agenta.
7. Pokud se v omsconfig. log *objevila chyba třídy* prostředků DSC, spusťte příkaz `sudo /opt/omi/bin/service_control restart` .
8. V některých případech platí, že když agent Log Analytics pro Linux nemůže komunikovat s Azure Monitor, data v agentovi se zálohují do plné velikosti vyrovnávací paměti: 50 MB. Agenta byste měli restartovat spuštěním následujícího příkazu `/opt/microsoft/omsagent/bin/service_control restart` .

    >[!NOTE]
    >Tento problém je opravený ve verzi agenta 1.1.0-28 nebo novější.
    >

* Pokud `omsconfig.log` soubor protokolu neindikuje, že `PerformRequiredConfigurationChecks` operace pravidelně běží v systému, může se jednat o problém s úlohou nebo službou cron. Ujistěte se, že v části existuje úloha cron `/etc/cron.d/OMSConsistencyInvoker` . V případě potřeby spusťte následující příkazy k vytvoření úlohy cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Také se ujistěte, že je služba cron spuštěná. Můžete použít `service cron status` s Debian, Ubuntu, SUSE nebo `service crond status` s RHEL, CentOS Oracle Linux ke kontrole stavu této služby. Pokud služba neexistuje, můžete nainstalovat binární soubory a službu spustit pomocí následujících možností:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problém: když nakonfigurujete kolekci z portálu pro čítače výkonu syslog nebo Linux, nastavení se neaplikují.

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Agent Log Analytics pro Linux nezískal nejnovější konfiguraci.
* Změněné nastavení na portálu nebylo použito.

### <a name="resolution"></a>Řešení
**Pozadí:** `omsconfig` je agentem Log Analytics agenta pro konfiguraci pro Linux, který každých pět minut vyhledává novou konfiguraci na straně portálu. Tato konfigurace se pak aplikuje na agenta Log Analytics pro konfigurační soubory pro Linux umístěné na adrese/etc/opt/Microsoft/omsagent/conf/omsagent.conf..

* V některých případech nemusí být agent Log Analytics agenta pro Linux Configuration agent schopen komunikovat se službou konfigurace portálu, což má za následek nepoužití poslední konfigurace.
  1. Ověřte, zda `omsconfig` je agent nainstalován pomocí systému `dpkg --list omsconfig` nebo `rpm -qi omsconfig` .  Pokud není nainstalován, přeinstalujte nejnovější verzi agenta Log Analytics pro Linux.

  2. `omsconfig`Spuštěním následujícího příkazu ověřte, že agent může komunikovat s Azure monitor `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . Tento příkaz vrátí konfiguraci, kterou agent přijímá od služby, včetně nastavení syslog, čítačů výkonu pro Linux a vlastních protokolů. Pokud tento příkaz neproběhne úspěšně, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Tento příkaz vynutí, aby měl agent omsconfig komunikaci s Azure Monitor a načíst nejnovější konfiguraci.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problém: nezobrazuje se žádná data vlastního protokolu 

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Připojování k Azure Monitor se nezdařilo.
* Nastavení **použít následující konfiguraci pro servery Linux** nebylo vybráno.
* omsconfig nezískala nejnovější konfiguraci vlastního protokolu ze služby.
* Agent Log Analytics pro systém Linux `omsagent` nemůže získat přístup k vlastnímu protokolu z důvodu oprávnění nebo nenalezených.  Můžou se zobrazit následující chyby:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Známý problém se stavem časování vyřešeným v Log Analytics Agent pro Linux verze 1.1.0 – 217

### <a name="resolution"></a>Řešení
1. Ověření připojení k Azure Monitor bylo úspěšné, když zkontrolujete, jestli tento soubor existuje: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . Pokud ne, proveďte jednu z těchto akcí:  

  1. Reonboard pomocí [instrukcí](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)příkazového řádku omsadmin.sh.
  2. V části **Upřesnit nastavení** v Azure Portal zajistěte, aby bylo povolené nastavení **použít následující konfiguraci na servery se systémem Linux** .  

2. `omsconfig`Spuštěním následujícího příkazu ověřte, že agent může komunikovat s Azure monitor `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  Tento příkaz vrátí konfiguraci, kterou agent přijímá od služby, včetně nastavení syslog, čítačů výkonu pro Linux a vlastních protokolů. Pokud tento příkaz neproběhne úspěšně, spusťte následující příkaz `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Tento příkaz vynutí, aby měl agent omsconfig komunikaci s Azure Monitor a načíst nejnovější konfiguraci.

**Pozadí:** Místo agenta Log Analytics pro Linux spuštěný jako privilegovaný uživatel – `root` Agent se spustí jako `omsagent` uživatel. Ve většině případů musí být pro tohoto uživatele udělené explicitní oprávnění, aby bylo možné některé soubory číst. Chcete-li udělit oprávnění `omsagent` uživateli, spusťte následující příkazy:

1. Přidat `omsagent` uživatele do konkrétní skupiny `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Udělit k požadovanému souboru univerzální přístup pro čtení `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Došlo k známému problému se stavem časování u Log Analytics agenta pro Linux verze starší než 1.1.0-217. Po aktualizaci na nejnovějšího agenta spusťte následující příkaz, který načte nejnovější verzi výstupního modulu plug-in `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problém: Pokoušíte se reonboard k novému pracovnímu prostoru
Když se pokusíte reonboard agenta do nového pracovního prostoru, je nutné před reonboarding vyčistit konfiguraci agenta Log Analytics. Pokud chcete vyčistit starou konfiguraci od agenta, spusťte sadu prostředků prostředí pomocí nástroje. `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Nebo

```
sudo sh ./onboard_agent.sh --purge
```

Po použití možnosti můžete pokračovat v reonboard. `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Rozšíření agenta Log Analytics v Azure Portal je označeno stavem selhání: zřizování se nezdařilo.

### <a name="probable-causes"></a>Pravděpodobné příčiny
* Agent Log Analytics se odebral z operačního systému.
* Služba agenta Log Analytics nefunguje, je vypnutá nebo není nakonfigurovaná.

### <a name="resolution"></a>Řešení 
Problém vyřešíte provedením následujících kroků.
1. Odebere rozšíření z Azure Portal.
2. Nainstalujte agenta podle [pokynů](../learn/quick-collect-linux-computer.md).
3. Restartujte agenta spuštěním následujícího příkazu: `sudo /opt/microsoft/omsagent/bin/service_control restart` .
* Počkejte několik minut a stav zřizování se změní na **zřizování bylo úspěšné**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problém: Upgrade agenta Log Analytics na vyžádání

### <a name="probable-causes"></a>Pravděpodobné příčiny

Balíčky agenta Log Analytics na hostiteli jsou zastaralé.

### <a name="resolution"></a>Řešení 
Problém vyřešíte provedením následujících kroků.

1. Podívejte se na nejnovější verzi na [stránce](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Stáhnout instalační skript (1.4.2-124 jako ukázková verze):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Upgradujte balíčky spuštěním příkazu `sudo sh ./omsagent-*.universal.x64.sh --upgrade` .
