---
title: Řešení potíží s technologií Hyper-V k zotavení po havárii Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak řešit problémy obnovení po havárii s technologií Hyper-V do Azure pomocí Azure Site Recovery replikaci
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/10/2018
ms.author: ramamill
ms.openlocfilehash: c7626c6edceddcfbd4d95ff6efc4678836a4502c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247989"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Řešení potíží s technologií Hyper-V do Azure replikace a převzetí služeb při selhání

Tento článek popisuje běžné problémy, které se můžete setkat při replikaci místních virtuálních počítačů Hyper-V do Azure, pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Povolit potíže s ochranou

Pokud máte problémy při povolení ochrany pro virtuální počítače Hyper-V, zkontrolujte následující doporučení:

1. Zkontrolujte, že hostitelé Hyper-V a virtuální počítače splňují všechny [požadavky a předpoklady](hyper-v-azure-support-matrix.md).
2. Pokud jsou servery Hyper-V umístěni v cloudech System Center Virtual Machine Manager (VMM), ověřte, že jste připravili [serveru VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Zkontrolujte, jestli je spuštěná služba Správa virtuálních počítačů Hyper-V na hostitelích Hyper-V.
4. Zkontrolovat problémy, které se zobrazují v Hyper-V-VMMS\Admin přihlášení k virtuálnímu počítači. Tento protokol se nachází v **protokoly aplikací a služeb** > **Microsoft** > **Windows**.
5. Na virtuálním počítači hosta ověřte, že rozhraní WMI povolené a přístupné.
  - [Další informace o](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) základní testování služby WMI.
  - [Řešení potíží s](https://aka.ms/WMiTshooting) rozhraní WMI.
  - [Řešení potíží s ](https://technet.microsoft.com/library/ff406382.aspx#H22) problémy s WMI skripty a službami.
6. Na virtuálním počítači hosta Ujistěte se, že je spuštěna nejnovější verzi integrační služby.
    - [Zkontrolujte](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) , že máte nejnovější verzi.
    - [Zachovat](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) nejnovější integrační služby.
    
## <a name="replication-issues"></a>Potíže s replikací

Řešení potíží s počáteční a průběžné replikace takto:

1. Ujistěte se, že máte spuštěnou [nejnovější verzi](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) služby Site Recovery.
2. Ověřte, zda replikace se pozastaví:
  - Zkontrolujte stav virtuálního počítače v konzole Správce technologie Hyper-V.
  - Pokud je důležité, klikněte pravým tlačítkem na virtuální počítač > **replikace** > **zobrazit stav replikace**.
  - Pokud replikace se pozastaví, klikněte na tlačítko **obnovit replikaci**.
3. Zkontrolujte, zda jsou spuštěny požadované služby. Pokud nejsou, je restartujte.
    - Pokud provádíte replikaci technologie Hyper-V bez nástroje VMM, zkontrolujte, jestli hostitel Hyper-V běží tyto služby:
        - Služba Správa virtuálních počítačů
        - Služba agenta Microsoft Azure Recovery Services
        - Služba Microsoft Azure Site Recovery
        - Služba hostitele zprostředkovatele rozhraní WMI
    - Pokud replikujete pomocí nástroje VMM v prostředí, zkontrolujte, že běží tyto služby:
        - Na hostiteli Hyper-V zkontrolujte, že jsou spuštěná služba Správa virtuálních počítačů, Microsoft Azure Recovery Services Agent a služby hostitele zprostředkovatele rozhraní WMI.
        - Na serveru VMM Ujistěte se, že je spuštěna služba System Center Virtual Machine Manager.
4. Zkontrolujte připojení mezi serverem Hyper-V a Azure. Aby se ověřilo připojení, otevřete Správce úloh na hostiteli Hyper-V. Na **výkonu** klikněte na tlačítko **Monitor otevřít zdroj**. Na **sítě** kartu > **zpracování pomocí síťové aktivity**, zkontrolujte, zda cbengine.exe aktivně odesílá velké objemy dat (MB).
5. Zkontrolujte, zda hostitelé Hyper-V může připojit k URL objektu blob úložiště Azure. Chcete-li zkontrolovat, zda hostitele můžete připojit, vyberte a zkontrolujte **cbengine.exe**. Zobrazení **připojení TCP** k ověření připojení z hostitele do objektu blob Azure storage.
6. Problémy s výkonem, zkontrolujte, jak je popsáno níže.
    
### <a name="performance-issues"></a>Problémy s výkonem

Omezení šířky pásma sítě může mít vliv na replikaci. Řešení potíží následujícím způsobem:

1. [Zkontrolujte](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) Pokud šířky pásma nebo omezení šířky pásma omezení ve vašem prostředí.
2. Spustit [plánovače nasazení služby profiler](hyper-v-deployment-planner-run.md).
3. Po spuštění profileru, postupujte [šířky pásma](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) doporučení.
4. Zkontrolujte [omezení četnost změn dat](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Pokud se zobrazí vysoké četnosti změn na virtuálním počítači dat, postupujte takto:
  - Zaškrtněte, pokud se váš virtuální počítač označí pro resynchronizaci.
  - Postupujte podle [tyto kroky](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) prozkoumat zdroje změn.
  - Četnost změn může dojít, pokud soubory protokolů HRL delší než 50 % místa na disku. Pokud je to problém, zřídíte další úložný prostor pro všechny virtuální počítače, ve kterých k problému dochází.
  - Zkontrolujte, že není pozastavené, že je replikace. Pokud se jedná, pokračuje v zápisu změny do souboru hrl, což může přispívat k jeho zvětšení velikosti.
 

## <a name="critical-replication-state-issues"></a>Problémy se stavem důležité replikace

1. Pokud chcete zkontrolovat stav replikace, připojení ke konzole Správce technologie Hyper-V v místním, vyberte virtuální počítač a ověřte stav.

    ![Stav replikace](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klikněte na tlačítko **zobrazit stav replikace** zobrazíte podrobnosti:

    - Pokud replikace se pozastaví, klikněte pravým tlačítkem na virtuální počítač > **replikace** > **obnovit replikaci**.
    - Pokud virtuální počítač na hostitele Hyper-V nakonfigurovaná ve službě Site Recovery migrovat na jiného hostitele Hyper-V ve stejném clusteru nebo na samostatný počítač, replikace pro virtuální počítač nemá žádný vliv. Jenom zkontrolujte, zda splňuje všechny požadavky nové hostitele Hyper-V a je nakonfigurovaný ve službě Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Potíže se snímky konzistentní vzhledem k

Konzistentní vzhledem k snímku je bodu v čase snímek dat aplikací ve virtuálním počítači. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači v konzistentním stavu při pořízení snímku.  Tato část podrobně popisuje některé běžné problémy, které se mohou vyskytnout.

### <a name="vss-failing-inside-the-vm"></a>VSS služeb při selhání ve virtuálním počítači

1. Zkontrolujte, že je nainstalované a spuštěné nejnovější integrační služby.  Zkontrolujte, jestli jsou aktualizace dostupné spuštěním následujícího příkazu Powershellu řádku se zvýšenými oprávněními na hostiteli Hyper-V: **get-vm | vyberte název, stav, IntegrationServicesState**.
2. Zkontrolujte, zda jsou služby VSS běží a je v pořádku:
    - Ke kontrole služby, přihlaste se na virtuálním počítači hosta. Pak otevřete příkazový řádek správce a spusťte následující příkazy ke kontrole, jestli jsou všechny zapisovače VSS jsou v pořádku.
        - **Vssadmin list writers**
        - **Vssadmin list shadows**
        - **Seznam poskytovatelů vssadmin**
    - Zkontrolujte výstup. Pokud jsou zapisovače ve stavu selhání, postupujte takto:
        - Zkontrolujte protokol událostí aplikace na virtuálním počítači aplikacevss nevyskytují chyby VSS. operace.
    - Zkuste restartovat těchto služeb přidružený k zapisovač, který se nezdařilo:
        - Stínová kopie svazku
         - Azure Site Recovery VSS Provider
    - Až to uděláte, počkejte několik hodin, které chcete zobrazit, pokud jsou snímky konzistentní vzhledem k úspěšně vygenerována.
    - Jako poslední možnost akci restartování virtuálního počítače. To může vyřešit služby, které jsou ve stavu neodpovídá.
3. Zkontrolujte, že nemáte dynamických disků ve virtuálním počítači. To není podporováno pro snímky konzistentní. Můžete zkontrolovat v nástroji Správa disků (diskmgmt.msc).

    ![Dynamický disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Zkontrolujte, že není nutné disk iSCSI připojené k virtuálnímu počítači. Toto není podporováno.
5. Zkontrolujte, zda je povolena služba zálohování. Ověřte, zda je povolena v **nastavení technologie Hyper-V** > **integrační služby**.
6. Zkontrolujte, zda nedochází ke konfliktům s aplikacemi pro pořizování snímků VSS. Může dojít, pokud více aplikací se pokoušíte pořizování snímků VSS ve stejný čas konflikty. Pokud například zálohování aplikace je pořizování snímků VSS plánu ve vašich zásadách replikace Site Recovery k vytvoření snímku.   
7. Zaškrtněte, pokud virtuální počítač dochází k vysoké výpovědí:
    - Můžete měřit denní četnost změn dat u virtuálních počítačů hosta pomocí čítačů výkonu na hostiteli Hyper-V. Pokud chcete změřit četnost změn dat, povolte následující čítač. Aggregrate ukázku této hodnoty na discích virtuálních počítačů po dobu 5 – 15 minut, chcete-li získat četnost změn virtuálním počítači.
        - Kategorie: "zařízení virtuálního úložiště technologie Hyper-V"
        - Čítač: "zapsané bajty za sekundu"</br>
        - Tato data četnost změn dat zvyšuje nebo zůstat na vysoké úrovni, v závislosti na jejich vytížení virtuálního počítače nebo jeho aplikace je.
        - Zdroj průměrná četnost změn dat na disku je 2 MB za sekundu pro úložiště úrovně standard pro Site Recovery. [Další informace](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Kromě toho můžete [ověřte cíle škálovatelnosti úložiště](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#scalability-targets-for-a-storage-account).
8. Spustit [Deployment Planner](hyper-v-deployment-planner-run.md).
9. Zkontrolujte doporučení pro [sítě](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS služeb při selhání v hostiteli Hyper-V

1. Zkontrolujte protokoly událostí pro chyby VSS a doporučení:
    - Na hostitelském serveru Hyper-V, otevřete Správce technologie Hyper-V protokolu událostí v **Prohlížeč událostí** > **protokoly aplikací a služeb** > **Microsoft**  >  **Windows** > **technologie Hyper-V** > **správce**.
    - Ověřte, zda jsou všechny události, které označují selhání snímky konzistentní s aplikací.
    - Typické chyba je: "Hyper-V se nepodařilo vytvořit sadu snímků VSS pro virtuální počítač"XYZ": modul pro zápis došlo k chybě nepřechodných. Restartování služby VSS může vyřešit problémy Pokud služba přestane reagovat."

2. Chcete-li generovat snímky VSS pro virtuální počítač, zkontrolujte, že jsou na virtuálním počítači nainstalované integrační služby Hyper-V, a zda je povolena Služba integrace zálohování (VSS).
    - Zkontrolujte, zda běží na hostovaném služby/procesy démon integrace služby VSS a jsou v **OK** stavu.
    - Můžete to ověřit z relace Powershellu se zvýšenými oprávněními na hostiteli Hyper-V pomocí příkazu **et-VMIntegrationService - VMName<VMName>– název VSS** tuto informaci můžou získat po přihlášení na virtuálním počítači hosta. [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Ujistěte se, že integrační služby Backup/VSS ve virtuálním počítači jsou spuštěné a v dobrém stavu. Pokud tomu tak není, znovu tyto služby a služby Stínová kopie svazku technologie Hyper-V žadatele na hostitelském serveru Hyper-V.

### <a name="common-errors"></a>Běžné chyby

**Kód chyby:** | **Zpráva** | **Podrobnosti**
--- | --- | ---
**0x800700EA.** | "Hyper-V se nepodařilo vytvořit sadu snímků VSS pro virtuální počítač: k dispozici další data. (0x800700EA). Nastavte generování snímků služby VSS může selhat, pokud probíhá operace zálohování.<br/><br/> Operace replikace pro virtuální počítač se nepovedlo: je k dispozici další data. " | Zkontrolujte, jestli váš virtuální počítač má povoleno dynamického disku. Toto není podporováno.
**0x80070032** | "Se nepodařilo připojit k virtuálnímu počítači Hyper-V svazek stínové kopie žadatel <. / VMname > protože verze neodpovídá verzi očekává technologie Hyper-V | Zaškrtněte, pokud jsou nainstalované nejnovější aktualizace Windows.<br/><br/> [Upgrade](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) na nejnovější verzi integrační služby.



## <a name="collect-replication-logs"></a>Replikace shromažďování protokolů

Všechny události replikace Hyper-V jsou zaznamenána do protokolu Hyper-V-VMMS\Admin umístěné v **protokoly aplikací a služeb** > **Microsoft** > **Windows**. Kromě toho můžete povolit analytický protokol pro službu Správa virtuálních počítačů Hyper-V, následujícím způsobem:

1. Ujistěte se, protokoly ladění a zobrazitelné v prohlížeči událostí. Protokoly dostupné v Event Vieweru, klikněte opakovaně na **zobrazení** > **zobrazit protokoly ladění a analýzu.**. Analytický protokol se zobrazí v části **Hyper-V-VMMS**.
2. V **akce** podokně klikněte na tlačítko **povolit protokol**. 

    ![Povolení protokolu](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Po jeho povolení, zobrazí se v **sledování výkonu**, jako **relace trasování událostí** pod **sady kolekcí dat**. 
4. Chcete-li zobrazit shromážděné informace, zastavte relaci trasování tím, že zakážete protokolu. Potom uložte protokol a znovu ho otevřete v prohlížeči událostí nebo použít jiné nástroje pro převod podle potřeby.


### <a name="event-log-locations"></a>Umístění protokolu událostí

**Protokol událostí** | **Podrobnosti** |
--- | ---
**Aplikace a služby protokoly/Microsoft/VirtualMachineManager/Server/Admin** (VMM server) | Protokoly pro řešení potíží s VMM.
**Aplikace a protokoly/MicrosoftAzureRecoveryServices/replikace služby** (hostitele Hyper-V) | Protokoly pro řešení potíží s Microsoft Azure Recovery Services Agent. 
**Aplikace a služby protokoly/Microsoft/Azure Site Recovery/poskytovatele/Operational** (hostitele Hyper-V)| Protokoly pro řešení potíží s Microsoft Azure Site Recovery Service.
**Aplikace a služby protokoly/Microsoft/Windows/Hyper-V-VMMS/Admin** (hostitele Hyper-V) | Protokoly k řešení potíží se správou virtuálních počítačů Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Shromažďování protokolů u pokročilého odstraňování problémů

Tyto nástroje můžou pomoct s pokročilá řešení problémů:

-   Pro nástroj VMM, provést pomocí Site Recovery protokolu kolekce [nástroj pro podporu diagnostiky Platform (SDP)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Pro Hyper-V bez nástroje VMM [stáhnout tento nástroj](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), a spusťte ho na hostitele Hyper-V ke shromažďování protokolů.

