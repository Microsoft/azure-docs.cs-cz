---
title: Řešení problémů s replikací pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o odstraňování potíží pro běžné potíže s replikací během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: mayg
ms.openlocfilehash: ed04c21fc5f3aecb91483dbd1eb7ca5fbf47c3e9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805961"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Řešení problémů replikace pro virtuální počítače VMware a fyzické servery

Tento článek popisuje některé běžné problémy a konkrétní chyby se mohou vyskytnout při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure s využitím [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Krok 1: Monitorování procesu serveru stavu

Site Recovery používá [procesový server](vmware-physical-azure-config-process-server-overview.md#process-server) přijímat a optimalizovat replikovaná data a odeslat ho do Azure.

Doporučujeme, aby monitorování stavu procesových serverů na portálu, pokud jsou připojeny a funguje správně a že replikace probíhá pro zdrojový počítač přidružený k procesového serveru.

- [Další informace o](vmware-physical-azure-monitor-process-server.md) monitorování procesových serverů.
- [Kontrola osvědčených postupů](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Řešení potíží s](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) stav procesového serveru.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Krok 2: Řešení potíží s připojením a replikace

Počáteční a průběžné replikace selhání často jsou způsobeny problémy s připojením mezi zdrojovým serverem a že procesový server nebo mezi procesovým serverem a Azure. 

Chcete-li vyřešit tyto problémy [řešení potíží s připojením a replikace](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Krok 3: Řešení potíží s zdrojové počítače, které nejsou k dispozici pro replikaci

Při pokusu o vyberte zdrojový počítač k replikaci pomocí Site Recovery počítač nemusí být k dispozici pro jednu z následujících důvodů:

* **Dva virtuální počítače se stejnou instancí identifikátoru UUID**: Pokud dva virtuální počítače v rámci vCenter mají stejnou instanci UUID, prvního virtuálního počítače zjištěny konfigurační server se zobrazí na webu Azure Portal. Pokud chcete tento problém vyřešit, ujistěte se, že žádné dva virtuální počítače mají stejnou instanci UUID. Tento scénář obvykle dochází v případech, kde zálohování virtuálního počítače se stane aktivní a se přihlásí do našich záznamů zjišťování. Odkazovat na [Azure Site Recovery VMware do Azure: Návod k vyčištění duplicitní nebo zastaralý položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) vyřešit.
* **Přihlašovací údaje uživatele nesprávné vCenter**: Ujistěte se, že jste přidali přihlašovací údaje k vCenter správná při nastavování konfiguračního serveru pomocí šablony OVF nebo jednotný instalační program. Ověření přihlašovacích údajů, které jste přidali během instalace, najdete v článku [upravit přihlašovací údaje pro automatické zjišťování](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Nedostatečná oprávnění vCenter**: Pokud není k dispozici pro přístup k serveru vCenter oprávnění požadovaná oprávnění, může dojít k selhání se zjistit virtuální počítače. Ujistěte se, že oprávnění popsaná v [Příprava účtu pro automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) jsou přidány do uživatelský účet vCenter.
* **Servery pro správu Azure Site Recovery**: Pokud virtuální počítač slouží jako server pro správu v rámci jednoho nebo více z následujících rolí – konfigurace serveru /scale-out procesový server nebo hlavní cílový server, nebudou moct vybrat virtuální počítač z portálu. Servery pro správu nelze replikovat.
* **Již chráněny/převzetí služeb při selhání prostřednictvím služby Azure Site Recovery**: Pokud virtuální počítač je už chráněná nebo převzetí služeb při selhání prostřednictvím služby Site Recovery, virtuální počítač není dostupný pro vybrané pro ochranu na portálu. Ujistěte se, že virtuální počítač, který hledáte, na portálu ještě nebyla zapnuta libovolným uživatelem nebo v jiném předplatném.
* **vCenter Nepřipojeno**: Zkontrolujte, jestli je vCenter v připojeném stavu. Abyste se přesvědčili, přejděte do trezoru služby Recovery Services > infrastruktura Site Recovery > konfigurační servery > klikněte na příslušný konfigurační server > Otevře se okno na vaše právo s podrobnostmi o přidružené servery. Zkontrolujte, jestli je vCenter připojený. Pokud je ve stavu "Nepřipojeno", vyřešte problém a pak [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Potom virtuální počítač objeví na portálu.
* **Vypnout napájení ESXi**: Pokud hostitele ESXi, ve kterém se nachází virtuální počítač je ve vypnutém stavu, pak virtuální počítač neuvedete nebo nebude možné vybrat na webu Azure portal. Zapnout napájení hostitele ESXi [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Potom virtuální počítač objeví na portálu.
* **Čeká se na restartování**: Pokud čeká na restartování virtuálního počítače se pak nebudete moci vybrat počítač na webu Azure portal. Nezapomeňte dokončit aktivity čekající restartování [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Potom virtuální počítač objeví na portálu.
* **Nebyl nalezen IP**: Pokud virtuální počítač nemá platnou IP adresu s ním spojená, pak nebudete moci vybrat počítač na webu Azure portal. Nezapomeňte přiřadit platnou IP adresu pro virtuální počítač [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Potom virtuální počítač objeví na portálu.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Řešení potíží s chráněných virtuálních počítačů na portálu zašedlé

Virtuální počítače, které se replikují v rámci obnovení lokality nejsou k dispozici na webu Azure Portal, pokud existují duplicitní položky v systému. Zjistěte, jak odstranit zastaralé položky a tento problém vyřešit, najdete v tématu [Azure Site Recovery VMware do Azure: Návod k vyčištění duplicitní nebo zastaralý položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

### <a name="initial-replication-issues-error-78169"></a>Potíže s úvodní replikací [Chyba 78169]

Prostřednictvím výše zajistit, že existují žádné připojení, šířku pásma nebo čas synchronizovat spojeného s potížemi, ujistěte se, že:

- Žádný antivirový software neblokuje Azure Site Recovery. Přečtěte si [Další](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) na vyloučení složek, které jsou potřebné pro Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Chybějící body obnovení konzistentní vzhledem k aplikaci [Chyba 78144]

 K tomu dochází z důvodu problémů s Stínová kopie svazku Service (VSS). Řešení je následující: 
 
- Ověřte, že nainstalovaná verze agenta Azure Site Recovery alespoň 9.22.2. 
- Ověřte, zda je jako služba ve Windows Services nainstalován poskytovatel služby VSS a taky ověřit konzoly MMC služby komponenty ke kontrole, zda je uveden Azure Site Recovery VSS Provider.
- Pokud není nainstalovaný poskytovatel služby VSS, podívejte se [článek pro řešení potíží při selhání instalace](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Pokud se stínové kopie svazku je zakázaná,
    - Zkontrolujte, že typ spouštění služby poskytovatelem služby VSS je rovno **automatické**.
    - Restartuje následující služby:
        - Služba VSS
        - Azure Site Recovery VSS Provider
        - Služba VDS

- Pokud používáte úlohy SQL nebo Exchange, zkontrolujte protokoly tyto aplikace zapisovačů selhání. Častým chybám a jejich řešení jsou zachyceny v následujících článcích:
    -  [Možnost Automatické ukončení databáze systému SQL Server je nastavena na hodnotu TRUE](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 vyvolání-Neopakovatelná chyba](https://support.microsoft.com/help/4504103)
    - [Známý problém nástroje SQL Server 2016 a 2017](https://support.microsoft.com/help/4493364)
    - [Běžný problém Exchange servery 2013 a 2016](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>Zdrojové počítače s vysokou četností změn dat [Chyba 78188]

Možné příčiny:
- Četnost změn dat (zapisované bajty/s) na uvedené disky virtuálního počítače více než [podporované limity Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) pro typ účtu cílového úložiště replikace.
- Náhlá Špička je četnost změn dat kvůli které vysoké množství dat čeká na vyřízení pro nahrávání.

Řešení tohoto problému:
- Ujistěte se, že je zřízený cílový typ účtu úložiště (Standard nebo Premium) podle požadavku frekvence změn ve zdroji.
- Pokud zjištěné změny jsou dočasné, počkejte pár hodin dat čeká na nahrání dohnat a k vytvoření bodů obnovení.
- Pokud problém nezmizí, použijte službu Site Recovery [plánovače nasazení služby](site-recovery-deployment-planner.md#overview) abyste mohli naplánovat replikace.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Zdrojové počítače se žádný prezenční signál [Chyba 78174]

To se stane, když se agent Azure Site Recovery Mobility na zdrojový počítač nekomunikuje s konfigurační Server (CS).

K vyřešení problému, použijte následující kroky k ověření připojení k síti ze zdrojového virtuálního počítače na konfigurační server:

1. Ověřte, že na zdrojovém počítači běží.
2. Přihlaste se ke zdrojovému počítači pomocí účtu, který má oprávnění správce.
3. Zkontrolujte, že tyto služby běží a ne restartujte služby, pokud:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. Na zdrojovém počítači zkontrolujte protokoly v umístění pro podrobnosti o chybě:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Procesový server s žádný prezenční signál [Chyba 806]
V případě, že neexistuje žádný prezenční signál z procesu serveru (PS), zkontrolujte, že:
1. PS Virtuálního počítače je v provozu
2. Zkontrolujte následující přihlásí PS pro podrobnosti o chybě:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Hlavní cílový server s žádný prezenční signál [Chyba 78022]

To se stane, když agenta Azure Site Recovery Mobility na hlavním cíli nekomunikuje s konfiguračním serverem.

Řešení tohoto problému, pomocí následujících kroků ověřte stav služby:

1. Ověřte, zda je spuštěna hlavního cílového virtuálního počítače.
2. Přihlaste se do hlavního cílového virtuálního počítače pomocí účtu, který má oprávnění správce.
    - Ověřte, zda je spuštěna služba svagents. Pokud je spuštěná, restartujte službu
    - Zkontrolujte protokoly v umístění pro podrobnosti o chybě:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID chyby 78144 - nejsou k dispozici pro virtuální počítač během posledních několika minut "XXX" žádný bod obnovení konzistentní vzhledem k aplikaci

Níže jsou uvedeny některé z nejběžnějších problémů

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>1\. příčina: Známý problém v systému SQL server 2008 a 2008 R2 
**K vyřešení** : Existuje známý problém se systémem SQL server 2008 a 2008 R2. Naleznete v tomto článku znalostní BÁZE [agenta Azure Site Recovery nebo jiné ne komponentu VSS zálohování se nezdaří pro server hostující SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-autoclose-dbs"></a>2\. příčina: Selhání úlohy Azure Site Recovery na servery, které hostují všechny verze instance SQL serveru s databází AUTO_CLOSE 
**K vyřešení** : Přečtěte si Kb [článku](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>3\. důvod: Známý problém nástroje SQL Server 2016 a 2017
**K vyřešení** : Přečtěte si Kb [článku](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>Další možné příčiny kvůli VSS související problémy:

Chcete-li pokračovat v řešení potíží, zkontrolujte soubory na zdrojovém počítači získat přesné informace o chybě kód chyby:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Jak najít chyby v souboru?
Vyhledejte řetězec "vacpError" tak, že otevřete soubor vacp.log v editoru
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

V předchozím příkladu **2147754994** je chybový kód, který vás informuje o selhání, jak je znázorněno níže

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Zapisovač VSS není nainstalováno – chyba 2147221164 

*K vyřešení*: Ke generování značka konzistence aplikací, Azure Site Recovery používá Microsoft Stínová kopie svazku Service (VSS). Nainstaluje zprostředkovatele služby VSS pro svou pořizovat snímky konzistence aplikace. Tento zprostředkovatel stínové kopie svazku je nainstalována jako služba. V případě, že není nainstalována služba poskytovatelem služby VSS, vytvoření snímku konzistence aplikací se nezdaří s id chyby 0x80040154 "Třídy není registrováno". </br>
Přečtěte si [článek pro řešení potíží instalace zapisovače VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Zapisovač VSS je zakázáno – chyba 2147943458

**K vyřešení**: Ke generování značka konzistence aplikací, Azure Site Recovery používá Microsoft Stínová kopie svazku Service (VSS). Nainstaluje zprostředkovatele služby VSS pro svou pořizovat snímky konzistence aplikace. Tento zprostředkovatel stínové kopie svazku je nainstalována jako služba. V případě, že je služba poskytovatel služby VSS zakázána, vytvoření snímku konzistence aplikací se nezdaří s id chybového "Zadaná služba je zakázána a nemůže být started(0x80070422)". </br>

- Pokud se stínové kopie svazku je zakázaná,
    - Zkontrolujte, že typ spouštění služby poskytovatelem služby VSS je rovno **automatické**.
    - Restartuje následující služby:
        - Služba VSS
        - Azure Site Recovery VSS Provider
        - Služba VDS

####  <a name="vss-provider-notregistered---error-2147754756"></a>NOT_REGISTERED zprostředkovatele služby VSS – chyba 2147754756

**K vyřešení**: Ke generování značka konzistence aplikací, Azure Site Recovery používá Microsoft Stínová kopie svazku Service (VSS). Zkontrolujte, jestli je nebo není nainstalovaná služba Azure Site Recovery VSS Provider. </br>

- Opakovaný pokus o instalaci poskytovatele pomocí následujících příkazů:
- Odinstalace stávajícího poskytovatele: C:\Program soubory (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Znovu nainstalujte: C:\Program soubory (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Zkontrolujte, že typ spouštění služby poskytovatelem služby VSS je rovno **automatické**.
    - Restartuje následující služby:
        - Služba VSS
        - Azure Site Recovery VSS Provider
        - Služba VDS

## <a name="next-steps"></a>Další postup

Pokud potřebujete další pomoc, zveřejněte svůj dotaz v [fórum pro Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Máme k dispozici aktivní komunitě a jedním z našich techniků, kteří vám můžou pomoct.
