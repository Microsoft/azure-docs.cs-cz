---
title: Poradce při potížích s replikací pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o řešení běžných problémů s replikací během zotavení po havárii virtuálních počítačích VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423960"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Poradce při potížích s replikací virtuálních virtuálních měn v systému VMware a fyzických serverů

Tento článek popisuje některé běžné problémy a konkrétní chyby, se kterými se můžete setkat při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí [site recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Krok 1: Sledování stavu serveru procesu

Site Recovery používá [procesní server](vmware-physical-azure-config-process-server-overview.md#process-server) k příjmu a optimalizaci replikovaných dat a jejich odeslání do Azure.

Doporučujeme sledovat stav procesních serverů na portálu, abyste zajistili, že jsou připojeny a fungují správně, a že replikace probíhá pro zdrojové počítače přidružené k procesnímu serveru.

- [Další informace o](vmware-physical-azure-monitor-process-server.md) monitorování procesních serverů.
- [Kontrola osvědčených postupů](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Poradce při potížích se stavem](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) serveru procesu.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Krok 2: Řešení problémů s připojením a replikací

Počáteční a probíhající selhání replikace jsou často způsobeny problémy s připojením mezi zdrojovým serverem a procesovým serverem nebo mezi procesním serverem a Azure. 

Chcete-li tyto problémy vyřešit, [odstraňte potíže s připojením a replikací](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Krok 3: Poradce při potížích se zdrojovými počítači, které nejsou k dispozici pro replikaci

Při pokusu o výběr zdrojového počítače pro povolení replikace pomocí site recovery nemusí být počítač k dispozici z jednoho z následujících důvodů:

* **Dva virtuální počítače se stejnou instancí UUID**: Pokud dva virtuální počítače pod vCenter mají stejnou instanci UUID, první virtuální počítač zjištěný konfiguračním serverem se zobrazí na webu Azure Portal. Chcete-li tento problém vyřešit, ujistěte se, že žádné dva virtuální počítače mají stejnou instanci UUID. Tento scénář se běžně používá v případech, kdy se záložní virtuální modul stane aktivním a je přihlášen do našich záznamů zjišťování. Podívejte se na [Azure Site Recovery VMware-to-Azure: Jak vyčistit duplicitní nebo zastaralé položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) k vyřešení.
* **Nesprávná pověření uživatele vCenter**: Ujistěte se, že jste při nastavovaném konfiguračním serveru pomocí šablony OVF nebo sjednoceného nastavení přidali správná pověření vprogramu. Informace o pověřeních, která jste přidali během instalace, naleznete v [tématu Úprava pověření pro automatické zjišťování](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter nedostatečná oprávnění**: Pokud oprávnění poskytnutá pro přístup k programu vCenter nemají požadovaná oprávnění, může dojít k selhání zjišťování virtuálních počítačů. Ujistěte se, že oprávnění popsaná v [části Příprava účtu na automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) jsou přidána do uživatelského účtu vCenter.
* **Servery pro správu azure site recovery**: Pokud se virtuální počítač používá jako server pro správu pod jednou nebo více následujícími rolemi – Konfigurační server /horizontální navýšení kapacity procesního serveru / Hlavní cílový server, nebudete moct virtuální počítač vybrat z portálu. Servery správy nelze replikovat.
* **Už chráněné nebo převzetí služeb převzetí služeb**azure site recovery : Pokud je virtuální počítač už chráněný nebo převzetí služeb při selhání prostřednictvím site recovery, virtuální počítač není k dispozici pro výběr ochrany na portálu. Ujistěte se, že virtuální počítač, který hledáte na portálu, ještě není chráněný žádným jiným uživatelem nebo pod jiným předplatným.
* **vCenter není připojen**: Zkontrolujte, zda je vCenter v připojeném stavu. Chcete-li oověřit, přejděte do trezoru služby Recovery Services > infrastruktury obnovení sítě > konfigurační servery > Klikněte na příslušný konfigurační server > se po pravé straně otevře okno s podrobnostmi o přidružených serverech. Zkontrolujte, jestli je vCenter připojený. Pokud je ve stavu "Není připojeno", vyřešte problém a [aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Poté bude virtuální počítač uveden na portálu.
* **ESXi vypnutý:** Pokud je hostitel ESXi, pod kterým se virtuální počítač nachází, ve stavu vypnutý, virtuální počítač nebude uveden nebo nebude možné vybrat na webu Azure Portal. Zapnutím hostitele ESXi [aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Poté bude virtuální počítač uveden na portálu.
* **Čeká na restartování**: Pokud je čekající restartování na virtuálním počítači, pak nebudete moci vybrat počítač na webu Azure Portal. Ujistěte se, že dokončení čekajících restartování, [aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Poté bude virtuální počítač uveden na portálu.
* **IP nebyl nalezen**: Pokud virtuální počítač nemá k dispozici platnou IP adresu, nebudete moct vybrat počítač na webu Azure Portal. Ujistěte se, že chcete virtuálnímu počítači přiřadit platnou adresu IP, [aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Poté bude virtuální počítač uveden na portálu.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Odstraňování problémů s chráněnými virtuálními počítači zašedlých na portálu

Virtuální počítače, které jsou replikované v rámci obnovení webu nejsou k dispozici na webu, pokud existují duplicitní položky v systému. Informace o tom, jak odstranit zastaralé položky a problém vyřešit, najdete v [článku Azure Site Recovery VMware-to-Azure: Jak vyčistit duplicitní nebo zastaralé položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>V posledních minutách 'XXX' není k dispozici žádný bod obnovení konzistentní selhání pro virtuální hod.

Některé z nejčastějších problémů jsou uvedeny níže

### <a name="initial-replication-issues-error-78169"></a>Počáteční problémy s replikací [chyba 78169]

Nad výše uvedené zajištění, že neexistují žádné problémy připojení, šířku pásma nebo čas synchronizace, ujistěte se, že:

- Azure Site Recovery neblokuje žádný antivirový software. [Další](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) informace o vyloučení složek požadovaných pro Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Zdrojové stroje s vysokou konve [chyba 78188]

Možné příčiny:
- Rychlost změny dat (zápis bajtů/s) na uvedených discích virtuálního počítače je víc než [limity podporované obnovení webu Azure](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) pro typ účtu úložiště cíle replikace.
- Tam je náhlý nárůst rychlosti konve kvůli které vysoké množství dat čeká na nahrání.

Chcete-li tento problém vyřešit:
- Ujistěte se, že typ cílového účtu úložiště (Standard nebo Premium) je zřízena podle požadavku na míru konve u zdroje.
- Pokud již replikujete na disk spravovaný premium (typ asrseeddisk), ujistěte se, že velikost disku podporuje pozorované změny podle limitů obnovení lokality. V případě potřeby můžete zvětšit velikost asrseeddisku. Postupujte podle následujících kroků:
    - Přejděte do okna Disky ovlivněného replikovaného počítače a zkopírujte název disku repliky.
    - Přechod na tento spravovaný disk repliky
    - Na okně Přehled se může zobrazit nápis, který říká, že byla vygenerována adresa URL SAS. Klikněte na tento banner a zrušte export. Tento krok ignorujte, pokud nevidíte banner.
    - Jakmile je adresa URL SAS odvolána, přejděte do okna Konfigurace spravovaného disku a zvětšete velikost tak, aby nástroj ASR podporoval zjištěnou rychlost změn na zdrojovém disku.
- Pokud pozorované změny je dočasné, počkejte několik hodin pro čekající nahrazování dat dohnat a vytvořit body obnovení.
- Pokud disk obsahuje nedůležitá data, jako jsou dočasné protokoly, testovací data atd., zvažte přesunutí těchto dat jinam nebo zcela vyloučit tento disk z replikace.
- Pokud problém přetrvává, použijte plánovač [nasazení](site-recovery-deployment-planner.md#overview) site recovery k plánování replikace.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Zdrojové počítače bez prezenčního signálu [chyba 78174]

K tomu dochází, když agent mobility webu Azure ve zdrojovém počítači nekomunikuje s konfiguračním serverem (CS).

Chcete-li tento problém vyřešit, ověřte připojení k síti ze zdrojového virtuálního virtuálního zařízení ke serveru Config Server následujícím postupem:

1. Ověřte, zda je spuštěn zdrojový počítač.
2. Přihlaste se ke zdrojovému počítači pomocí účtu, který má oprávnění správce.
3. Ověřte, zda jsou spuštěny následující služby, a pokud nejsou restartována služby:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. Ve zdrojovém počítači zkontrolujte protokoly v umístění podrobnosti o chybě:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Procesní server bez prezenčního signálu [chyba 806]
V případě, že z procesního serveru (PS) nedochází k prezenčnímu signálu, zkontrolujte, zda:
1. Virtuální vod systému PS je v provozu
2. Podrobnosti o chybě na systému PS naleznete v následujících protokolech:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Hlavní cílový server bez prezenčního signálu [chyba 78022]

K tomu dochází, když agent mobility webu Azure na hlavním cíli nekomunikuje s konfiguračním serverem.

Chcete-li tento problém vyřešit, ověřte stav služby pomocí následujících kroků:

1. Ověřte, že je spuštěný hlavní cílový virtuální hotel.
2. Přihlaste se k hlavnímu cílovému virtuálnímu virtuálnímu účtu pomocí účtu, který má oprávnění správce.
    - Ověřte, zda je spuštěna služba svagents. Pokud je spuštěna, restartujte službu
    - Podrobnosti o chybě naleznete v protokolech v umístění:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Chcete-li zaregistrovat hlavní cíl pomocí konfiguračního serveru, přejděte do složky **%PROGRAMDATA%\ASR\Agent**a na příkazovém řádku spusťte následující:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID chyby 78144 – v posledních minutách XXX není pro virtuální virtuální mě k dispozici žádný bod obnovení konzistentní s aplikací.

Byla provedena vylepšení ve verzích agenta mobility [9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39) pro zpracování chování při selhání instalace služby VSS. Ujistěte se, že jste na nejnovější verze pro nejlepší pokyny k řešení potíží selhání VSS.

Některé z nejčastějších problémů jsou uvedeny níže

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Příčina 1: Známý problém v serveru SQL Server 2008/2008 R2 
**Jak opravit** : Je známý problém s SQL server 2008/2008 R2. Naleznete tento článek kb [Azure Site Recovery Agent nebo jiné nesoučásti VSS zálohování selže pro server hostující SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Příčina 2: Úlohy obnovení webu Azure selhávají na serverech hostujících libovolnou verzi instancí SQL Serveru s AUTO_CLOSE dbs 
**Jak opravit** : Odkazujte na [článek](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) Kb 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Příčina 3: Známý problém v SQL Server 2016 a 2017
**Jak opravit** : Odkazujte na [článek](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) Kb 


### <a name="more-causes-due-to-vss-related-issues"></a>Další příčiny v důsledku problémů souvisejících s VSS:

Chcete-li dále řešit potíže, zkontrolujte soubory ve zdrojovém počítači, abyste získali přesný kód chyby pro selhání:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Jak najít chyby v souboru?
Hledání řetězce "vacpError" otevřením souboru vacp.log v editoru
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

Ve výše uvedeném příkladu **2147754994** je kód chyby, který informuje o selhání, jak je znázorněno níže

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Zapisovač VSS není nainstalován - chyba 2147221164 

*Jak opravit*: Ke generování značky konzistence aplikace azure site recovery používá službu Microsoft Volume Shadow copy Service (VSS). Nainstaluje zprostředkovatele VSS pro jeho provoz, aby pořizovat snímky konzistence aplikace. Tento zprostředkovatel VSS je nainstalován jako služba. V případě, že služba Zprostředkovatel VSS není nainstalována, vytvoření snímku konzistence aplikace se nezdaří s chybou id 0x80040154 "Třída není registrována". </br>
Článek [popisuje řešení potíží s instalací zapisovače VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Zapisovač VSS je zakázán - chyba 2147943458

**Jak opravit**: Ke generování značky konzistence aplikace azure site recovery používá službu Microsoft Volume Shadow copy Service (VSS). Nainstaluje zprostředkovatele VSS pro jeho provoz, aby pořizovat snímky konzistence aplikace. Tento zprostředkovatel VSS je nainstalován jako služba. V případě, že je služba Zprostředkovatel VSS zakázána, vytvoření snímku konzistence aplikace se nezdaří s id chyby "Zadaná služba je zakázána a nelze ji spustit(0x80070422)". </br>

- Pokud je VSS zakázán,
    - Ověřte, zda je typ spuštění služby Zprostředkovatel VSS nastaven na **možnost Automaticky**.
    - Restartujte následující služby:
        - Služba VSS
        - Obnovení webu Azure vss zprostředkovatele
        - Služba VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - chyba 2147754756

**Jak opravit**: Ke generování značky konzistence aplikace azure site recovery používá službu Microsoft Volume Shadow copy Service (VSS). Zkontrolujte, jestli je nainstalovaná služba Zprostředkovatel webu Azure. </br>

- Opakujte instalaci zprostředkovatele pomocí následujících příkazů:
- Odinstalace stávajícího zprostředkovatele: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Přeinstalace: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Ověřte, zda je typ spuštění služby Zprostředkovatel VSS nastaven na **možnost Automaticky**.
    - Restartujte následující služby:
        - Služba VSS
        - Obnovení webu Azure vss zprostředkovatele
        - Služba VDS

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc, zadejte svůj dotaz ve [fóru Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Máme aktivní komunitu a jeden z našich inženýrů vám může pomoci.
