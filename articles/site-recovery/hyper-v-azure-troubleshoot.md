---
title: Poradce při potížích s zotavením po havárii technologie Hyper-V pomocí azure site recovery
description: Popisuje, jak řešit problémy s zotavením po havárii s replikací Hyper-V na Azure pomocí Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 0a3e5c922009353e4ba9ccab12cf70ea2b5992da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961488"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Řešení potíží s replikací a převzetím služeb při selhání Hyper-V do Azure

Tento článek popisuje běžné problémy, se kterými se můžete sejít při replikaci místních virtuálních počítačů Hyper-V do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Povolení problémů s ochranou

Pokud narazíte na problémy při povolení ochrany pro virtuální aplikace Hyper-V, zkontrolujte následující doporučení:

1. Zkontrolujte, zda hostitelé a virtuální počítače Hyper-V splňují všechny [požadavky a požadavky](hyper-v-azure-support-matrix.md).
2. Pokud jsou servery Hyper-V umístěny v cloudech Správce virtuálních strojů (VMM) systému System Center, ověřte, zda jste připravili [server VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Zkontrolujte, zda je služba Správy virtuálních strojů Hyper-V spuštěna na hostitelích Hyper-V.
4. Zkontrolujte problémy, které se zobrazí v přihlašování Hyper-V-VMMS\Admin k virtuálnímu účtu. Tento protokol je umístěn v **protokolech aplikací a služeb systému** > **Microsoft** > **Windows**.
5. Na virtuálním počítači hosta ověřte, že služba WMI je povolená a přístupná.
   - [Seznamte se se](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) základními testováními wmi.
   - [Poradce při potížích](https://aka.ms/WMiTshooting) Služby wmi.
   - [Poradce při potížích](https://technet.microsoft.com/library/ff406382.aspx#H22) se skripty a službami služby WMI.
6. Na hostovaném virtuálním počítači se ujistěte, že je spuštěna nejnovější verze integration services.
    - [Zkontrolujte, zda](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) máte nejnovější verzi.
    - [Zachovat](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integrační služby aktuální.
    
## <a name="replication-issues"></a>Potíže s replikací

Při řešení potíží s počáteční a probíhající replikací postupujte následovně:

1. Ujistěte se, že používáte [nejnovější verzi](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) služeb Site Recovery.
2. Ověřte, zda je replikace pozastavena:
   - Zkontrolujte stav virtuálního manažera v konzole Správce Technologie Hyper-V.
   - Pokud je to důležité, klikněte pravým tlačítkem myši na stav > **replikace zobrazení** **replikace**virtuálního > .
   - Pokud je replikace pozastavena, klepněte na tlačítko **Pokračovat v replikaci**.
3. Zkontrolujte, zda jsou spuštěny požadované služby. Pokud tomu tak není, restartujte je.
    - Pokud replikujete technologie Hyper-V bez vm, zkontrolujte, zda jsou tyto služby spuštěny na hostiteli Technologie Hyper-V:
        - Služba Správa virtuálních počítačů
        - Služba agenta Microsoft Azure Recovery Services
        - Služba Microsoft Azure Site Recovery
        - Hostitelská služba zprostředkovatele rozhraní WMI
    - Pokud replikujete s VMM v prostředí, zkontrolujte, že tyto služby jsou spuštěny:
        - Na hostiteli Hyper-V zkontrolujte, zda je spuštěna služba Správa virtuálních strojů, agent a hostitel zprostředkovatele služby WMI.
        - Na serveru VMM zkontrolujte, zda je spuštěna služba Správce virtuálních strojů system center.
4. Zkontrolujte připojení mezi serverem Hyper-V a Azure. Chcete-li zkontrolovat připojení, otevřete Správce úloh na hostiteli Hyper V. Na kartě **Výkon** klepněte na tlačítko **Otevřít nástroj Sledování prostředků**. Na kartě **Síť** > **Proces se síťovou aktivitou**zkontrolujte, zda nástroj cbengine.exe aktivně odesílá velké svazky (Mbs) dat.
5. Zkontrolujte, jestli se hostitelé Hyper-V můžou připojit k adrese URL objektu blob úložiště Azure. Chcete-li zkontrolovat, zda se hostitelé mohou připojit, vyberte a zkontrolujte **soubor cbengine.exe**. Zobrazením **připojení TCP** ověřte připojení z hostitele k objektu blob úložiště Azure.
6. Zkontrolujte problémy s výkonem, jak je popsáno níže.
    
### <a name="performance-issues"></a>Problémy s výkonem

Omezení šířky pásma sítě může mít vliv na replikaci. Řešení problémů následujícím způsobem:

1. [Zkontrolujte,](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) zda ve vašem prostředí existují omezení šířky pásma nebo omezení.
2. Spusťte [profiler Plánovač nasazení](hyper-v-deployment-planner-run.md).
3. Po spuštění profileru postupujte podle [doporučení pro šířku pásma](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště.](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
4. Zkontrolujte [omezení změn dat](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Pokud se na virtuálním počítači zobrazí vysoká data, postupujte takto:
   - Zkontrolujte, jestli je váš virtuální počítač označený pro resynchronizaci.
   - Chcete-li prozkoumat zdroj změny, postupujte [takto.](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/)
   - K churn může dojít, pokud soubory protokolu HRL překročí 50 % dostupného místa na disku. Pokud se jedná o problém, zřídit více místa úložiště pro všechny virtuální počítačové, na kterém k problému dochází.
   - Zkontrolujte, zda replikace není pozastavena. Pokud ano, pokračuje v zápisu změn do souboru hrl, což může přispět k jeho větší velikosti.
 

## <a name="critical-replication-state-issues"></a>Kritické problémy se stavem replikace

1. Chcete-li zkontrolovat stav replikace, připojte se k místní konzoli Správce Technologie Hyper-V, vyberte virtuální počítač a ověřte stav.

    ![Stav replikace](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Kliknutím na **Zobrazit stav replikace** zobrazíte podrobnosti:

    - Pokud je replikace pozastavena, klepněte pravým tlačítkem myši na**replikaci obnovení** **replikace replikace replikace replikace virtuálního** > počítače > .
    - Pokud virtuální počítač na hostiteli Hyper-V nakonfigurované v site recovery migruje do jiného hostitele Hyper-V ve stejném clusteru nebo do samostatného počítače, replikace pro virtuální počítač není ovlivněna. Stačí zkontrolovat, zda nový hostitel Hyper-V splňuje všechny požadavky a je nakonfigurován v site recovery.

## <a name="app-consistent-snapshot-issues"></a>Problémy se snímky konzistentními s aplikací

Snímek konzistentní s aplikací je snímek dat aplikace v čase uvnitř virtuálního počítače. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači jsou v konzistentním stavu při pořízení snímku.  Tato část podrobně popisuje některé běžné problémy, které mohou nastat.

### <a name="vss-failing-inside-the-vm"></a>VSS selhání uvnitř virtuálního mísy

1. Zkontrolujte, zda je nainstalována a spuštěna nejnovější verze integračních služeb.  Zkontrolujte, jestli je k dispozici aktualizace spuštěním následujícího příkazu z výzvy se zvýšenými oprávněními k prostředí PowerShell na hostiteli Hyper-V: **get-vm | vyberte Name, State, IntegrationServicesState**.
2. Zkontrolujte, zda jsou služby VSS spuštěné a v pořádku:
   - Chcete-li zkontrolovat služby, přihlaste se k virtuálnímu virtuálnímu virtuálnímu ms hosta. Potom otevřete příkazový řádek správce a spusťte následující příkazy a zkontrolujte, zda jsou všichni autoři Služby VSS v pořádku.
       - **Vssadmin seznam spisovatelů**
       - **Stíny seznamu Vssadmin**
       - **Poskytovatelé seznamu Vssadmin**
   - Zkontrolujte výstup. Pokud jsou autoři ve stavu selhání, postupujte takto:
       - Zkontrolujte protokol událostí aplikace na virtuálním počítači pro chyby operace VSS.
   - Zkuste restartovat tyto služby přidružené k zapisovateli, který selhal:
     - Stínová kopie svazku
       - Obnovení webu Azure vss zprostředkovatele
   - Po provedení této chvíli počkejte několik hodin a zjistěte, zda jsou úspěšně generovány snímky konzistentní s aplikací.
   - Jako poslední možnost zkuste restartovat virtuální počítač. To může vyřešit služby, které jsou v nereagujícím stavu.
3. Zkontrolujte, zda nemáte dynamické disky ve virtuálním počítače. THis není podporován pro snímky konzistentní s aplikací. Můžete zkontrolovat Správu disků (diskmgmt.msc).

    ![Dynamický disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Zkontrolujte, zda nemáte disk iSCSI připojený k virtuálnímu počítače. Toto není podporováno.
5. Zkontrolujte, zda je povolena služba Zálohování. Ověřte, zda je povolena v >  **nastavení technologie Hyper-V**Integration**Services**.
6. Ujistěte se, že nejsou žádné konflikty s aplikacemi pořizovat snímky VSS. Pokud se více aplikací pokouší pořizovat snímky Služby VSS současně, může dojít ke konfliktům. Například pokud aplikace zálohování pořizovat snímky VSS při obnovení lokality je naplánováno zásady replikace pořizovat snímek.   
7. Zkontrolujte, jestli virtuální hod dochází k vysoké rychlosti změn:
    - Můžete měřit denní rychlost změny dat pro hostované virtuální chod, pomocí čítačů výkonu na hostiteli Hyper-V. Chcete-li měřit rychlost změny dat, povolte následující čítač. Agregovat ukázku této hodnoty na discích virtuálních počítačů po dobu 5 až 15 minut, abyste získali změny virtuálního počítače.
        - Kategorie: "Virtuální paměťové zařízení Hyper-V"
        - Čítač: "Zapsat bajty / s"</br>
        - Tato rychlost změn dat se zvýší nebo zůstane na vysoké úrovni, v závislosti na tom, jak zaneprázdněný virtuální počítač nebo jeho aplikace jsou.
        - Průměrná konve dat zdrojového disku je 2 MB/s pro standardní úložiště pro obnovení webu. [Další informace](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Kromě toho můžete [ověřit cíle škálovatelnosti úložiště](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).
8. Spusťte [plánovač nasazení](hyper-v-deployment-planner-run.md).
9. Projděte si doporučení pro [síť](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS selhání uvnitř hostitele Hyper-V

1. Zkontrolujte protokoly událostí pro chyby a doporučení VSS:
    - Na hostitelském serveru Hyper-V otevřete protokol událostí Hyper-V Admin v**protokolech** > aplikací a služeb **prohlížeče** > událostí**Microsoft** > **Windows** > **Hyper-V** > **Admin**.
    - Ověřte, zda existují nějaké události, které označují selhání snímek konzistentní aplikace.
    - Typická chyba je: "Hyper-V se nepodařilo generovat sadu snímků VSS pro virtuální počítač 'XYZ': Zapisovatel došlo k nepřechodné chybě. Restartování služby VSS může vyřešit problémy, pokud služba neodpovídá."

2. Chcete-li generovat snímky VSS pro virtuální počítač, zkontrolujte, zda jsou služby integrace Technologie Hyper-V nainstalované na virtuálním počítači a zda je povolena služba integrace zálohování (VSS).
    - Ujistěte se, že služba nebo demony Služby VSS integrační služby jsou spuštěny na hosta a jsou ve stavu **OK.**
    - Můžete to zkontrolovat z relace prostředí PowerShell se zvýšenými oprávněními na hostiteli Hyper-V s příkazem **Get-VMIntegrationService -VMName\<VMName> Název VSS** Můžete tyto informace získat také přihlášením k hostovanému virtuálnímu počítači. [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Ujistěte se, že služby integrace zálohování/VSS na virtuálním počítači jsou spuštěné a v pořádku. Pokud ne, restartujte tyto služby a službu aplikace Hyper-V Volume Shadow Copy na hostitelském serveru Hyper-V.

### <a name="common-errors"></a>Běžné chyby

**Kód chyby** | **Zprávu** | **Podrobnosti**
--- | --- | ---
**0x800700EA** | "Technologie Hyper-V nepodařilo generovat sadu snímků VSS pro virtuální počítač: Je k dispozici další data. (0x800700EA). Generování sady snímků VSS může selhat, pokud probíhá operace zálohování.<br/><br/> Operace replikace pro virtuální počítač se nezdařila: Je k dispozici další data." | Zkontrolujte, jestli je váš virtuální počítač povolen dynamický disk. Toto není podporováno.
**0x80070032** | "Žadateli o stínovou kopii svazku Hyper-V se nepodařilo připojit k < virtuálního počítače./VMname>, protože verze neodpovídá verzi očekávané technologiem Hyper-V. | Zkontrolujte, zda jsou nainstalovány nejnovější aktualizace systému Windows.<br/><br/> [Upgradujte](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) na nejnovější verzi integration services.



## <a name="collect-replication-logs"></a>Shromažďování protokolů replikace

Všechny události replikace Hyper-V jsou zaznamenány v protokolu Hyper-V-VMMS\Admin, který se nachází v **protokolech** > aplikací a**služeb, microsoft** > **windows**. Kromě toho můžete povolit protokol Analytic pro službu Správy virtuálních strojů Hyper-V takto:

1. Znázornit protokoly Analytic a Ladění v Prohlížeči událostí. Chcete-li protokoly zpřístupnit, klepněte v Prohlížeči událostí na **položku Zobrazit** > **analytické a ladicí protokoly.**. Protokol Analytick se zobrazí v části **Hyper-V-VMMS**.
2. V podokně **Akce** klepněte na tlačítko **Povolit protokol**. 

    ![Povolit protokol](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Po jeho povolení se zobrazí v **programu Sledování výkonu**jako relace **trasování událostí** v části Sady **kolekcí dat**. 
4. Chcete-li zobrazit shromážděné informace, zastavte relaci trasování zakázáním protokolu. Potom protokol uložte a znovu jej otevřete v Prohlížeči událostí nebo jej podle potřeby převeďte pomocí jiných nástrojů.


### <a name="event-log-locations"></a>Umístění protokolu událostí

**Protokol událostí** | **Podrobnosti** |
--- | ---
**Aplikace a protokoly služeb/Microsoft/VirtualMachineManager/Server/Admin** (server VMM) | Protokoly k řešení problémů v mm.
**Protokoly aplikací a služeb/MicrosoftAzureRecoveryServices/Replication** (hostitel technologie Hyper-V) | Protokoly k řešení problémů agenta služby Microsoft Azure Recovery Services. 
**Aplikace a protokoly služeb/Microsoft/Azure Site Recovery/Provider/Operational** (hostitel Hyper-V)| Protokoly k řešení problémů microsoft azure site recovery service.
**Aplikace a protokoly služeb/Microsoft/Windows/Hyper-V-V-VMMS/Admin** (hostitel Hyper-V) | Protokoly k řešení problémů se správou virtuálních vod Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Kolekce protokolů pro řešení potíží pro pokročilé

Tyto nástroje vám mohou pomoci s řešením potíží pro pokročilé účely:

-   V nástroji VMM proveďte kolekci protokolů obnovení webu pomocí [nástroje SDP (Support Diagnostics Platform).](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
-   Pro Hyper-V bez VMM, [stáhněte si tento nástroj](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)a spusťte jej na hostitele Hyper-V shromažďovat protokoly.

