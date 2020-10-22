---
title: Řešení potíží s zotavením po havárii technologie Hyper-V pomocí Azure Site Recovery
description: Popisuje, jak řešit potíže s zotavením po havárii pomocí replikace Hyper-V do Azure pomocí Azure Site Recovery
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: 721e09c2bc0562ba833115361cf33c3daaef380b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364027"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Řešení potíží s replikací a převzetím služeb při selhání Hyper-V do Azure

Tento článek popisuje běžné problémy, se kterými se můžete setkat při replikaci místních virtuálních počítačů Hyper-V do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Povolit problémy s ochranou

Pokud se setkáte s problémy, když povolíte ochranu pro virtuální počítače Hyper-V, přečtěte si následující doporučení:

1. Ověřte, že hostitelé a virtuální počítače Hyper-V splňují všechny [požadavky a](hyper-v-azure-support-matrix.md)požadavky.
2. Pokud se servery Hyper-V nacházejí v cloudech System Center Virtual Machine Manager (VMM), ověřte, že jste připravili [server VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Ověřte, zda je na hostitelích Hyper-V spuštěná služba správy virtuálních počítačů Hyper-V.
4. Vyhledejte problémy, které se zobrazí v Hyper-V-VMMS\Admin přihlášení k virtuálnímu počítači. Tento protokol se nachází v **protokolech aplikací a služeb**v  >  **Microsoft**  >  **systému Microsoft Windows**.
5. Na virtuálním počítači hosta ověřte, zda je služba WMI povolená a přístupná.
   - [Přečtěte si o](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) základních testech rozhraní WMI.
   - [Řešení potíží](/windows/win32/wmisdk/wmi-troubleshooting) WMI.
   - [Řešení](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22) potíží se skripty a službami WMI.
6. Na virtuálním počítači hosta zajistěte, aby byla spuštěná nejnovější verze integračních služeb.
    - [Ověřte](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) , že máte nejnovější verzi.
    - [Zachovat](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integrační služby jsou v aktuálním stavu.
    
## <a name="replication-issues"></a>Potíže s replikací

Při řešení potíží s počáteční a probíhající replikací postupujte následovně:

1. Ujistěte se, že používáte [nejnovější verzi](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) služby Site Recovery Services.
2. Ověřte, zda je replikace pozastavena:
   - Ověřte stav virtuálního počítače v konzole Správce technologie Hyper-V.
   - Pokud je to důležité, klikněte pravým tlačítkem na virtuální počítač > zobrazení **replikace**  >  **stav replikace**.
   - Pokud je replikace pozastavená, klikněte na **obnovit replikaci**.
3. Ověřte, zda jsou spuštěny požadované služby. Pokud nejsou, restartujte je.
    - Pokud provádíte replikaci technologie Hyper-V bez nástroje VMM, ověřte, zda jsou tyto služby spuštěny na hostiteli Hyper-V:
        - Služba Správa virtuálních počítačů
        - Služba agenta Microsoft Azure Recovery Services
        - Služba Microsoft Azure Site Recovery
        - Hostitelská služba zprostředkovatele rozhraní WMI
    - Pokud v prostředí provádíte replikaci pomocí nástroje VMM, ověřte, že jsou spuštěné tyto služby:
        - Na hostiteli Hyper-V ověřte, jestli je spuštěná služba Správa virtuálních počítačů, agent Microsoft Azure Recovery Services a hostitelská služba poskytovatele rozhraní WMI.
        - Na serveru VMM se ujistěte, že je spuštěná služba System Center Virtual Machine Manager.
4. Zkontrolujte připojení mezi serverem Hyper-V a Azure. Chcete-li zjistit připojení, otevřete Správce úloh na hostiteli Hyper-V. Na kartě **výkon** klikněte na **otevřít sledování prostředků**. Na kartě **síť** > **procesu s aktivitou sítě**ověřte, zda cbengine.exe aktivně odesílá velké objemy dat (MB).
5. Ověřte, jestli se hostitelé Hyper-V můžou připojit k adrese URL objektu BLOB služby Azure Storage. Pokud chcete zjistit, jestli se hostitelé můžou připojit, vyberte a zaškrtněte **cbengine.exe**. Zobrazte **připojení TCP** a ověřte připojení z hostitele k objektu BLOB služby Azure Storage.
6. Ověřte problémy s výkonem, jak je popsáno níže.
    
### <a name="performance-issues"></a>Problémy s výkonem

Replikace může ovlivnit omezení šířky pásma sítě. Řešení potíží následujícím způsobem:

1. [Zkontroluje](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) , jestli ve vašem prostředí nejsou omezení šířky pásma nebo omezení.
2. Spusťte [Plánovač nasazení Profiler](hyper-v-deployment-planner-run.md).
3. Po spuštění profileru použijte doporučení pro [šířku pásma](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) .
4. Ověřte [omezení přenosů dat](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Pokud vidíte vysokou četnost změn dat na virtuálním počítači, udělejte toto:
   - Ověřte, jestli je váš virtuální počítač označený pro opakovanou synchronizaci.
   - Pomocí [těchto kroků](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization) můžete prozkoumat zdroj změn.
   - K změnám může dojít, když soubory protokolu HRL překračují 50% dostupného místa na disku. Pokud se jedná o problém, zřiďte pro všechny virtuální počítače, na kterých dochází k problému, další prostor úložiště.
   - Ověřte, zda replikace není pozastavena. Pokud je, pokračuje v zápisu změn do souboru HRL, který může přispět ke zvýšené velikosti.
 

## <a name="critical-replication-state-issues"></a>Problémy se stavem kritické replikace

1. Pokud chcete zkontrolovat stav replikace, připojte se k místní konzole Správce technologie Hyper-V, vyberte virtuální počítač a ověřte stav.

    ![Stav replikace](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kliknutím na **Zobrazit stav replikace** zobrazíte podrobnosti:

    - Pokud je replikace pozastavená, klikněte pravým tlačítkem na virtuální počítač > **replikace**  >  **obnovit replikaci**.
    - Pokud se virtuální počítač na hostiteli Hyper-V nakonfigurovaný v Site Recovery migruje na jiného hostitele Hyper-V ve stejném clusteru nebo na samostatný počítač, nemá to vliv na replikaci virtuálního počítače. Stačí pouze ověřit, zda nový hostitel Hyper-V splňuje všechny požadavky a je nakonfigurován v Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problémy snímků konzistentní vzhledem k aplikacím

Snímek konzistentní vzhledem k aplikacím je snímkem dat aplikací v rámci virtuálního počítače v čase. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači jsou v konzistentním stavu, kdy se snímek vybere.  Tato část podrobně popisuje některé běžné problémy, se kterými se můžete setkat.

### <a name="vss-failing-inside-the-vm"></a>Selhání služby VSS ve virtuálním počítači

1. Ověřte, že je nainstalovaná a spuštěná nejnovější verze integračních služeb.  Ověřte, jestli je k dispozici aktualizace spuštěním následujícího příkazu z příkazového řádku PowerShellu se zvýšenými oprávněními na hostiteli Hyper-V: **Get-VM | vyberte název, stav, IntegrationServicesState**.
2. Ověřte, jestli jsou služby VSS spuštěné a v pořádku:
   - Pokud chcete služby ověřit, přihlaste se k virtuálnímu počítači hosta. Pak otevřete příkazový řádek správce a spusťte následující příkazy, abyste zkontrolovali, jestli jsou všechny zapisovače VSS v dobrém stavu.
       - **Vssadmin list writers**
       - **Vssadmin list – stíny**
       - **Vssadmin list – poskytovatelé**
   - Podívejte se na výstup. Pokud jsou moduly pro zápis ve stavu selhání, postupujte následovně:
       - V protokolu událostí aplikace na virtuálním počítači vyhledejte chyby operace VSS.
   - Zkuste restartovat tyto služby přidružené k neúspěšnému zápisu:
     - Stínová kopie svazku
       - Poskytovatel služby Stínová kopie svazku Azure Site Recovery
   - Až to uděláte, počkejte pár hodin, abyste viděli, jestli se snímky konzistentní vzhledem k aplikacím úspěšně generují.
   - Jako poslední možnost zkuste virtuální počítač restartovat. To může vyřešit služby, které jsou ve stavu nereagující.
3. Ověřte, že na virtuálním počítači nejsou dynamické disky. To se u snímků konzistentních vzhledem k aplikacím nepodporuje. Můžete se vrátit do správy disků (diskmgmt. msc).

    ![Dynamický disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Ověřte, že nemáte k virtuálnímu počítači připojený disk iSCSI. Toto není podporováno.
5. Ověřte, zda je služba zálohování povolena. Ověřte, že je povolený v **nastavení technologie Hyper-v**  >  **Integration Services**.
6. Zajistěte, aby nedocházelo ke konfliktům s aplikacemi, které pobírají snímky VSS. Pokud se více aplikací pokouší pořizovat snímky stínové kopie svazku (VSS), může dojít k těmto konfliktům. Například pokud zálohovací aplikace přebírá snímky VSS, když Site Recovery naplánovala vaše zásada replikace, aby pobírala snímek.   
7. Ověřte, jestli u virtuálního počítače dochází k vysoké míře četnosti přenosů:
    - Můžete změřit denní četnost změn dat pro virtuální počítače hosta pomocí čítačů výkonu na hostiteli Hyper-V. Chcete-li změřit rychlost změny dat, povolte následující čítač. Agregovaná ukázka této hodnoty na discích virtuálních počítačů po dobu 5-15 minut, aby se virtuální počítače mohla zbavit.
        - Kategorie: zařízení virtuálního úložiště Hyper-V
        - Čítač: "zápis bajtů za sekundu"</br>
        - Tato míra četnosti změn dat se zvýší nebo zůstane na vysoké úrovni v závislosti na tom, jak se virtuální počítač nebo jeho aplikace nachází.
        - Průměrná četnost změn dat zdrojového disku je 2 MB/s pro úložiště standard pro Site Recovery. [Další informace](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Kromě toho můžete [ověřit cíle škálovatelnosti úložiště](../storage/common/scalability-targets-standard-account.md).
8. Ujistěte se, že pokud používáte server se systémem Linux, pak na něm jste povolili konzistenci aplikací. [Další informace](./site-recovery-faq.md#replication)
9. Spusťte [Plánovač nasazení](hyper-v-deployment-planner-run.md).
10. Přečtěte si doporučení pro [síť](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Selhání služby VSS v rámci hostitele Hyper-V

1. V protokolech událostí vyhledejte chyby a doporučení služby VSS:
    - Na hostitelském serveru Hyper-v otevřete protokol událostí správce technologie Hyper-v v **Prohlížeč událostí**  >  **aplikace a služby protokoly**  >  **správce Microsoft**  >  **Windows**  >  **Hyper-v**  >  **Admin**.
    - Ověřte, jestli existují nějaké události, které indikují selhání snímku konzistentního vzhledem k aplikacím.
    - Typická chyba je: "technologie Hyper-V selhala při generování sady snímků VSS pro virtuální počítač" XYZ ": modul pro zápis zjistil nepřechodnou chybu. Restartování služby VSS může vyřešit problémy, pokud služba nereaguje. "

2. Pokud chcete pro virtuální počítač vygenerovat snímky VSS, ověřte, že jsou na virtuálním počítači nainstalované integrační služby technologie Hyper-V a že je povolená služba Backup (VSS).
    - Zajistěte, aby na hostovi běžela služba nebo démony služby VSS integrační služby a jsou ve stavu **OK** .
    - Tuto kontrolu můžete provést z relace PowerShellu se zvýšenými oprávněními na hostiteli Hyper-V pomocí příkazu **Get-VMIntegrationService-VMName \<VMName> -Name VSS** . Tyto informace můžete také získat tak, že se přihlásíte do virtuálního počítače hosta. [Další informace](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Ujistěte se, že na virtuálním počítači běží služby Backup/VSS Integration Services, a to v dobrém stavu. V takovém případě tyto služby restartujte a na hostitelském serveru technologie Hyper-V svazek služba žadatele stínové kopie svazku.

### <a name="common-errors"></a>Běžné chyby

**Kód chyby** | **Zpráva** | **Podrobnosti**
--- | --- | ---
**0x800700EA** | Technologii Hyper-V se nepovedlo vygenerovat sadu snímků VSS pro virtuální počítač: jsou k dispozici další data. (0x800700EA). Generace sady snímků služby VSS může selhat, pokud probíhá operace zálohování.<br/><br/> Operace replikace pro virtuální počítač se nezdařila: jsou k dispozici další data. | Ověřte, jestli má virtuální počítač povolený dynamický disk. Toto není podporováno.
**0x80070032** | "Žadateli stínové kopie svazku Hyper-V se nepovedlo připojit k virtuálnímu počítači <./VMname>, protože verze neodpovídá verzi, kterou očekává technologie Hyper-V. | Ověřte, jestli jsou nainstalované nejnovější aktualizace Windows.<br/><br/> [Upgradujte](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) na nejnovější verzi integračních služeb.



## <a name="collect-replication-logs"></a>Shromáždit protokoly replikace

Všechna událost replikace technologie Hyper-V se zaznamenávají do protokolu Hyper-V-VMMS\Admin, který se nachází v protokolech **aplikací a služeb**v  >  **Microsoft**  >  **systému Microsoft Windows**. Kromě toho můžete povolit analytický protokol pro službu správy virtuálních počítačů s technologií Hyper-V následujícím způsobem:

1. Zpřístupněte protokoly pro analýzu a ladění v Prohlížeč událostí. Aby byly protokoly dostupné, klikněte v Prohlížeč událostí na **Zobrazit**  >  **Zobrazit protokoly pro analýzu a ladění.** Analytický protokol se zobrazí pod položkou **Hyper-V-VMMS**.
2. V podokně **Akce** klikněte na **Povolit protokol**. 

    ![Povolit protokol](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Jakmile je tato možnost povolená, zobrazí se v **nástroji Sledování výkonu**jako **relace trasování událostí** v části **sady kolekcí dat**. 
4. Chcete-li zobrazit shromážděné informace, zastavte relaci trasování zakázáním protokolu. Pak uložte protokol a znovu ho otevřete v Prohlížeč událostí, nebo pomocí jiných nástrojů ho podle potřeby převeďte.


### <a name="event-log-locations"></a>Umístění protokolu událostí

**Protokol událostí** | **Podrobnosti** |
--- | ---
**Protokoly aplikací a služeb/Microsoft/VirtualMachineManager/server/admin** (server VMM) | Protokoly pro řešení potíží s nástrojem VMM.
**Protokoly aplikací a služeb/MicrosoftAzureRecoveryServices/replikace** (hostitel technologie Hyper-V) | Protokoly pro řešení potíží s Microsoft Azure Recovery Services agenta. 
**Protokoly aplikací a služeb/Microsoft/Azure Site Recovery/poskytovatel/provoz** (hostitel technologie Hyper-V)| Protokoly pro řešení potíží se službou Microsoft Azure Site Recovery.
**Protokoly aplikací a služeb/Microsoft/Windows/Hyper-v-VMMS/Admin** (hostitel technologie Hyper-V) | Protokoly pro řešení potíží se správou virtuálních počítačů Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Shromažďování protokolů pro pokročilé řešení potíží

Tyto nástroje můžou pomáhat s pokročilým řešením potíží:

-   Pro nástroj VMM proveďte Site Recovery shromažďování protokolů pomocí [nástroje Support Diagnostic Platform (SDP)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Pro Hyper-V bez nástroje VMM [Stáhněte tento nástroj](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)a spusťte ho na hostiteli Hyper-v, abyste mohli protokoly shromažďovat.