---
title: Řešení potíží s replikací pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek poskytuje informace o odstraňování běžných potíží s replikací během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 8b44a1d6119cc658b9460e0a52fa0629f759964a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336201"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Řešení potíží s replikací fyzických serverů a virtuálních počítačů VMware

Tento článek popisuje některé běžné problémy a konkrétní chyby, se kterými se můžete setkat při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Krok 1: sledování stavu procesového serveru

Site Recovery používá [procesový Server](vmware-physical-azure-config-process-server-overview.md#process-server) pro příjem a optimalizaci replikovaných dat a jejich odeslání do Azure.

Doporučujeme, abyste na portálu sledovali stav procesových serverů, aby se zajistilo jejich připojení a správné fungování a aby replikace probíhala v případě zdrojových počítačů přidružených k procesu serveru.

- [Přečtěte si informace o](vmware-physical-azure-monitor-process-server.md) procesových serverech monitorování.
- [Kontrola osvědčených postupů](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Řešení potíží s](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) stavem procesového serveru.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Krok 2: řešení potíží s připojením a replikací

Úvodní a průběžné selhání replikace často způsobují problémy s připojením mezi zdrojovým serverem a procesovým serverem nebo mezi procesovým serverem a Azure.

Chcete-li tyto problémy vyřešit, [vyřešte potíže s připojením a replikací](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Krok 3: řešení potíží se zdrojovými počítači, které nejsou k dispozici pro replikaci

Když se pokusíte vybrat zdrojový počítač pro povolení replikace pomocí Site Recovery, počítač nemusí být k dispozici z některého z následujících důvodů:

* **Dva virtuální počítače se stejným identifikátorem UUID**: Pokud mají dva virtuální počítače v vCenter stejný identifikátor UUID instance, zobrazí se v Azure Portal první virtuální počítač zjištěný konfiguračním serverem. Pokud chcete tento problém vyřešit, zajistěte, aby žádné dva virtuální počítače neměly stejný UUID instance. Tento scénář se běžně objevuje v případech, kdy se záložní virtuální počítač stává aktivním a je přihlášený k záznamům zjišťování. Přečtěte si [Azure Site Recovery VMware-to-Azure: Jak vyčistit duplicitní nebo zastaralé položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) k vyřešení.
* **Nesprávná uživatelská pověření pro vCenter**: Ujistěte se, že jste při nastavování konfiguračního serveru pomocí šablony OVF nebo sjednocené instalace přidali správné přihlašovací údaje pro vCenter. Postup ověření přihlašovacích údajů, které jste přidali během instalace, najdete v tématu [Úprava přihlašovacích údajů pro automatické zjišťování](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter nedostatečná oprávnění**: Pokud oprávnění poskytnutá pro přístup vCenter nemají požadovaná oprávnění, může dojít k selhání zjišťování virtuálních počítačů. Zajistěte, aby byla do uživatelského účtu vCenter přidána oprávnění popsaná v tématu [Příprava účtu pro automatické zjišťování](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) .
* **Azure Site Recovery servery pro správu**: Pokud se virtuální počítač používá jako Management Server v jedné nebo několika následujících rolích – konfigurační server/Scale-out procesový Server nebo hlavní cílový server, pak nebudete moct vybrat virtuální počítač z portálu. Servery pro správu nelze replikovat.
* Je **už chráněný/převzetí služeb při selhání prostřednictvím služby Azure Site Recovery Services**: Pokud už je virtuální počítač chráněný nebo převzetí služeb při selhání prostřednictvím Site Recovery, virtuální počítač není k dispozici pro výběr ochrany na portálu. Ujistěte se, že virtuální počítač, který hledáte na portálu, už není chráněný žádným jiným uživatelem nebo v jiném předplatném.
* **vCenter Nepřipojeno**: Ověřte, zda je vCenter v připojeném stavu. Pokud chcete ověřit, přejděte na Recovery Services trezor > Site Recovery > konfigurační servery infrastruktury > klikněte na příslušný konfigurační server > otevře se okno s podrobnostmi o přidružených serverech. Ověřte, zda je Server vCenter připojen. Pokud je ve stavu "Nepřipojeno", vyřešte problém a pak [Aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Potom se virtuální počítač zobrazí na portálu.
* **ESXi vypnuto**: Pokud se hostitel ESXi, pod nímž se nachází virtuální počítač, nachází ve vypnutém stavu, pak virtuální počítač nebude uveden nebo nebude možné ho vybrat na Azure Portal. Zapněte na hostiteli ESXi a [Aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) na portálu. Potom se virtuální počítač zobrazí na portálu.
* **Čeká**se na restartování: Pokud je na virtuálním počítači čeká na restartování, nebudete moct vybrat počítač na Azure Portal. Ujistěte se, že se nedokončené aktivity restartování dokončí, a [Aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Potom se virtuální počítač zobrazí na portálu.
* **Nenašla se IP**adresa: Pokud virtuální počítač nemá přidruženou platnou IP adresu, nebudete moct vybrat počítač na Azure Portal. Ujistěte se, že virtuálnímu počítači přiřadíte platnou IP adresu a [aktualizujete konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Potom se virtuální počítač zobrazí na portálu.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Řešení potíží s chráněnými virtuálními počítači, které jsou na portálu šedé

Virtuální počítače, které jsou replikovány v Site Recovery, nejsou k dispozici v Azure Portal, pokud v systému existují duplicitní položky. Informace o tom, jak odstranit zastaralé položky a vyřešit problém, najdete v tématu [Azure Site Recovery VMware-to-Azure: Jak vyčistit duplicitní nebo zastaralé položky](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Pro virtuální počítač není během posledních ' XXX ' minut k dispozici žádný bod obnovení konzistentní vzhledem k selháním.

Níže jsou uvedené některé z nejběžnějších problémů.

### <a name="initial-replication-issues-error-78169"></a>Problémy s počáteční replikací [Chyba 78169]

Nad rámec výše, která zajistí, že nedochází k problémům souvisejícím s připojením, šířkou pásma nebo časem synchronizace, zajistěte, aby:

- Žádný antivirový software neblokuje Azure Site Recovery. Další [informace o](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) vyloučení složek vyžadovaných pro Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Zdrojové počítače s vysokou četností [Chyba 78188]

Možné příčiny:
- Frekvence změny dat (bajty zápisu/s) na uvedených discích virtuálního počítače je větší než [Azure Site Recovery podporovaná omezení](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) pro typ účtu úložiště cíle replikace.
- Rychlost četnosti přenosů je náhlá, což znamená, že pro nahrání čeká na vyřízení vysoké množství dat.

Řešení tohoto problému:
- Ujistěte se, že typ cílového účtu úložiště (Standard nebo Premium) je zřízený podle míry četnosti změn ve zdroji.
- Pokud už jste replikované na spravovaný disk úrovně Premium (typ asrseeddisk), ujistěte se, že velikost disku podporuje pozorovanou frekvenci přenosů podle Site Recovery omezení. V případě potřeby můžete velikost asrseeddisk zvětšit. Postupujte podle následujících kroků:
    - Přejděte do okna disky ovlivněného replikovaného počítače a zkopírujte název disku repliky.
    - Přejít na tento spravovaný disk repliky
    - V okně Přehled se může zobrazit informační zpráva s informací o tom, že se vygenerovala adresa URL SAS. Klikněte na tuto hlavičku a zrušte export. Pokud se banner nezobrazuje, tento krok ignorujte.
    - Jakmile se adresa URL SAS odvolá, otevřete okno konfigurace spravovaného disku a zvyšte velikost tak, aby Azure Site Recovery podporovala pozorovanou četnost změn na zdrojovém disku.
- Pokud jsou pozorované změny dočasné, počkejte několik hodin, než se nahrávání čekajících dat zachytí a vytvoří body obnovení.
- Pokud disk obsahuje Nekritická data, jako jsou dočasné protokoly, data testů atd., zvažte přesunutí těchto dat jinde nebo zcela vyloučit tento disk z replikace.
- Pokud se problém opakuje, použijte [Plánovač nasazení](site-recovery-deployment-planner.md#overview) Site Recovery, který vám může pomáhat s plánováním replikace.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Zdrojové počítače bez prezenčního signálu [Chyba 78174]

K tomu dojde, když Azure Site Recovery agent mobility na zdrojovém počítači nekomunikuje se konfiguračním serverem (CS).

Pokud chcete problém vyřešit, pomocí následujícího postupu ověřte síťové připojení ze zdrojového virtuálního počítače ke konfiguračnímu serveru:

1. Ověřte, že je na zdrojovém počítači spuštěný.
2. Přihlaste se ke zdrojovému počítači pomocí účtu, který má oprávnění správce.
3. Ověřte, zda jsou spuštěny následující služby a zda nerestartuje služby:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. Na zdrojovém počítači prohlédněte protokoly v umístění, kde najdete podrobnosti o chybě:

    *C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents \* . log*

### <a name="process-server-with-no-heartbeat-error-806"></a>Procesový Server bez prezenčního signálu [Chyba 806]
V případě, že není k dispozici žádný prezenční signál z procesového serveru (PS), ověřte, že:
1. Virtuální počítač PS je v provozu.
2. Podrobnosti o chybě najdete v následujících protokolech PS:

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* . log*\
    ani
    *C:\ProgramData\ASR\home\svsystems\ monitor_protection \* . log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Hlavní cílový server bez prezenčního signálu [Chyba 78022]

K tomu dojde, když Azure Site Recovery agent mobility na hlavním cíli nekomunikuje s konfiguračním serverem.

Problém vyřešíte tak, že pomocí následujícího postupu ověříte stav služby:

1. Ověřte, že je hlavní cílový virtuální počítač spuštěný.
2. Přihlaste se k hlavnímu cílovému virtuálnímu počítači pomocí účtu, který má oprávnění správce.
    - Ověřte, zda je spuštěna služba svagents. Pokud je spuštěný, restartujte službu.
    - Podrobnosti o chybě najdete v protokolech v umístění:

        *C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents \* . log*
3. Pokud chcete zaregistrovat hlavní cíl s konfiguračním serverem, přejděte do složky **%ProgramData%\ASR\Agent**a spusťte na příkazovém řádku následující příkaz:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID chyby 78144 – pro virtuální počítač není k dispozici žádný bod obnovení konzistentní vzhledem k aplikacím během posledních ' XXX ' minut

Vylepšení byla provedena v agentech mobility [9,23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher)  &  [9,27](site-recovery-whats-new.md#update-rollup-39) verzí pro zpracování chování při selhání instalace VSS. Při řešení potíží se selháním služby VSS se ujistěte, že používáte nejnovější verze.

Níže jsou uvedené některé z nejběžnějších problémů.

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Příčina 1: známý problém v systému SQL Server 2008/2008 R2
**Jak opravit** : došlo k známému problému s SQL serverem 2008/2008 R2. Přečtěte si prosím tento článek znalostní báze [Azure Site Recovery agent nebo jiná nekomponentová záloha služby VSS u serveru, který hostuje SQL Server 2008 R2, se nezdařil](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2) .

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Příčina 2: Azure Site Recovery úlohy selžou na serverech hostujících jakékoli verze SQL Server instancí AUTO_CLOSE databáze
**Jak opravit** : Přečtěte si [článek](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) znalostní báze


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Příčina 3: známý problém v SQL Server 2016 a 2017
**Jak opravit** : Přečtěte si [článek](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) znalostní báze

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>Příčina 4: konzistence aplikací není na serverech se systémem Linux povolena
**Oprava** : Azure Site Recovery pro operační systém Linux podporuje vlastní skripty aplikace pro konzistenci aplikací. Vlastní skript s možnostmi před a po odeslání bude použit agentem Azure Site Recovery mobility pro konzistenci aplikací. [Tady](./site-recovery-faq.md#replication) je postup, jak ho povolit.

### <a name="more-causes-due-to-vss-related-issues"></a>Další příčiny v důsledku potíží souvisejících se službou VSS:

Pokud chcete problém vyřešit, Projděte si soubory na zdrojovém počítači, abyste získali přesný kód chyby pro selhání:

*C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

Jak najít chyby v souboru?
Vyhledejte řetězec "vacpError" otevřením souboru vacp. log v editoru.

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

Ve výše uvedeném příkladu **2147754994** je kód chyby, který vám oznamuje selhání, jak je uvedeno níže.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>Zapisovač VSS není nainstalovaný – chyba 2147221164

*Jak opravit*: vygeneruje se značka konzistence aplikace Azure Site Recovery používá službu Microsoft Volume stín Copy Service (VSS). Nainstaluje poskytovatele služby VSS, aby jeho operace mohla provádět snímky konzistence aplikací. Tento zprostředkovatel služby Stínová kopie svazku je nainstalován jako služba. V případě, že není nainstalovaná služba poskytovatele VSS, vytvoření snímku konzistence aplikací se nepovede s ID chyby 0x80040154 "třída není zaregistrovaná". </br>
Přečtěte si [článek pro řešení potíží s instalací zapisovače VSS](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Zapisovač VSS je zakázaný – chyba 2147943458

**Jak opravit**: vygeneruje se značka konzistence aplikace Azure Site Recovery používá službu Microsoft Volume stín Copy Service (VSS). Nainstaluje poskytovatele služby VSS, aby jeho operace mohla provádět snímky konzistence aplikací. Tento zprostředkovatel služby Stínová kopie svazku je nainstalován jako služba. V případě, že je služba poskytovatele VSS zakázaná, vytvoření snímku konzistence aplikací se nepovede s ID chyby. Zadaná služba je zakázaná a nedá se spustit (0x80070422). </br>

- Pokud je VSS zakázaný,
    - Ověřte, zda je typ spouštění služby VSS Provider nastaven na hodnotu **automaticky**.
    - Restartujte následující služby:
        - Služba Stínová kopie svazku
        - Poskytovatel služby Stínová kopie svazku Azure Site Recovery
        - Služba VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>Zprostředkovatel služby Stínová kopie svazku NOT_REGISTERED – chyba 2147754756

**Jak opravit**: vygeneruje se značka konzistence aplikace Azure Site Recovery používá službu Microsoft Volume stín Copy Service (VSS).
Ověřte, jestli je nainstalovaná služba poskytovatele VSS Azure Site Recovery, nebo ne. </br>

- Opakujte instalaci poskytovatele pomocí následujících příkazů:
- Odinstalace stávajícího zprostředkovatele: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Přeinstalujte: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install. cmd.

Ověřte, zda je typ spouštění služby VSS Provider nastaven na hodnotu **automaticky**.
    - Restartujte následující služby:
        - Služba Stínová kopie svazku
        - Poskytovatel služby Stínová kopie svazku Azure Site Recovery
        - Služba VDS

## <a name="error-id-95001---insufficient-permissions-found"></a>ID chyby 95001 – nalezena nedostatečná oprávnění

K této chybě dochází při pokusu o povolení replikace a složky aplikace nemají dostatečná oprávnění.

**Jak opravit**: Pokud chcete tento problém vyřešit, zajistěte, aby měl uživatel IUSR roli vlastníka pro všechny níže uvedené složky –

- *C\ProgramData\Microsoft Azure Site Recovery\private*
- Instalační adresář. Například pokud je instalační adresář jednotka F, zadejte správné oprávnění –
    - *Soubory F:\Program (x86) \Microsoft Azure Site Recovery\home\svsystems*
- Složka *\pushinstallsvc* v instalačním adresáři. Například pokud je instalační adresář jednotky F, poskytněte správná oprávnění –
    - *Soubory F:\Program (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc*
- Složka *\etc* v instalačním adresáři. Například pokud je instalační adresář jednotky F, poskytněte správná oprávnění –
    - *Soubory F:\Program (x86) \Microsoft Azure Site Recovery\home\svsystems\etc*
- *C:\Temp*
- *C:\thirdparty\php5nts*
- Všechny položky pod cestou níže –
    - *C:\thirdparty\rrdtool-1.2.15-win32-perl58\rrdtool\Release\**

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další nápovědu, vystavte svůj dotaz na [stránce s dotazem Microsoft Q&Azure Site Recovery](/answers/topics/azure-site-recovery.html). Máme aktivní komunitu a jeden z našich technik vám může pomoct.
