---
title: Řešení potíží s technologií Hyper-V na Azure replikaci s Azure Site Recovery | Microsoft Docs
description: Popisuje, jak k řešení potíží s technologií Hyper-V do Azure replikaci s využitím Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/09/2018
ms.author: rayne
ms.openlocfilehash: 8b16f495a21ff0b3da415390877777650a9fbb82
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048199"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Řešení potíží s technologií Hyper-V do Azure replikace a převzetí služeb při selhání

Tento článek popisuje běžné problémy, které se můžete setkat při replikovat místní virtuální počítače Hyper-V do Azure, pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Povolit potíže s ochranou

Pokud dojde k potížím při povolení ochrany pro virtuální počítače Hyper-V, zkontrolujte následující:

1. Zkontrolujte, zda hostitelé Hyper-V a virtuálních počítačů v souladu se všemi [požadavky a předpoklady](hyper-v-azure-support-matrix.md).
2. Pokud jsou servery Hyper-V v cloudech System Center Virtual Machine Manager (VMM), ověřte, že jste připravili [serveru VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Zkontrolujte, zda je spuštěna služba Správa virtuálních počítačů technologie Hyper-V v hostitelích Hyper-V.
4. Zkontrolujte problémy, které se zobrazují v protokolu technologie Hyper-V-VMMS\Admin ve virtuálním počítači. Tento protokol je umístěný v **protokoly aplikací a služeb** > **Microsoft** > **Windows**.
5. Na virtuálním počítači hosta ověřte, že rozhraní WMI je povoleno a přístupné.
  - [Další informace o](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) základní testování služby WMI.
  - [Řešení potíží s](https://aka.ms/WMiTshooting) rozhraní WMI.
  - [Řešení potíží s ](https://technet.microsoft.com/library/ff406382.aspx#H22) problémy s skripty WMI a služby.
5. Na virtuálním počítači hosta zajistěte, aby používal nejnovější verzi integrační služby.
    - [Zkontrolujte](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) , že máte nejnovější verzi.
    - [Zachovat](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) aktuální integrační služby.
    
## <a name="replication-issues"></a>Potíže s replikací

Řešení problémů s replikací počátečních a pokračujících následujícím způsobem:

1. Ujistěte se, kterou používáte [nejnovější verzi](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) služby Site Recovery.
2. Ověřte, zda je replikace pozastavena:
  - Zkontrolujte stav virtuálního počítače v konzole Správce technologie Hyper-V.
  - Pokud je důležité, klikněte pravým tlačítkem na virtuální počítač > **replikace** > **zobrazit stav replikace**.
  - Pokud replikace je pozastavena, klikněte na tlačítko **obnovit replikaci**.
3. Zkontrolujte, zda jsou spuštěny požadované služby. Pokud nejsou, je restartujte.
    - Pokud replikujete technologie Hyper-V bez VMM, zkontrolujte, zda na hostitele Hyper-V jsou spuštěny tyto služby:
        - Služba Správa virtuálních počítačů
        - Služba agenta služeb zotavení Microsoft Azure
        - Služba Microsoft Azure Site Recovery
        - Služba Hostitel poskytovatele rozhraní WMI
    - Pokud replikujete s nástrojem VMM v prostředí, zkontrolujte, zda jsou spuštěny tyto služby:
        - Na hostiteli technologie Hyper-V zkontrolujte, že jsou spuštěná služba Správa virtuálních počítačů, agenta služeb zotavení Microsoft Azure a službou hostitel poskytovatele rozhraní WMI.
        - Na serveru VMM ověřte, zda je spuštěna služba System Center Virtual Machine Manager.
4. Zkontrolujte připojení mezi serverem Hyper-V a Azure. Chcete-li to provést, otevřete Správce úloh na hostiteli Hyper V. Na **výkonu** , klikněte na **otevřete sledování prostředků**. Na **sítě** kartě > **postupy s síťové aktivity**, zkontrolujte, zda cbengine.exe aktivně odesílá velké objemy dat (MB).
5. Zkontrolujte, zda hostitelé Hyper-V může připojit k adresy URL objektu blob úložiště Azure. Chcete-li to provést, vyberte a zkontrolujte **cbengine.exe**. Zobrazení **připojení TCP** k ověření připojení od hostitele do objektu blob úložiště Azure.
6. Problémy s výkonem, zkontrolujte, jak je popsáno níže.
    
### <a name="performance-issues"></a>Problémy s výkonem

Omezení šířky pásma sítě může mít vliv na replikaci. Řešení problémů takto:

1. [Zkontrolujte](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) Pokud existují šířky pásma nebo omezení omezení ve vašem prostředí.
2. Spustit [nasazení Planner profileru](hyper-v-deployment-planner-run.md).
3. Po spuštění profileru, postupujte podle [šířky pásma](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) doporučení.
4. Zkontrolujte [data změn omezení](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Pokud se zobrazí vysoké data změn na virtuálním počítači, postupujte takto:
  - Zkontrolujte, pokud virtuální počítač je označen pro opětovnou synchronizaci.
  - Postupujte podle [tyto kroky](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) prozkoumat zdroj provozu.
  - Změn může dojít, když soubor protokolu HRL přesahuje 50 % volného místa. Pokud je problém, zřídíte další prostor úložiště pro všechny virtuální počítače, na kterých se k danému problému dojde.
  - Zkontrolujte, zda že není pozastavená replikace. Pokud se jedná, pokračuje zápisu změny do souboru hrl, což může přispívat k jeho vyšší velikost.
 

## <a name="critical-replication-state-issues"></a>Důležité replikaci problémy stavu

1. Pokud chcete zkontrolovat stav replikace, připojení ke konzole Správce technologie Hyper-V místní, vyberte virtuální počítač a ověřte stav.

    ![Stav replikace](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klikněte na tlačítko **zobrazit stav replikace** a zobrazit podrobnosti:

    - Pokud replikace je pozastavena, klikněte pravým tlačítkem na virtuální počítač > **replikace** > **obnovit replikaci**.
    - Pokud virtuální počítač na hostitele Hyper-V nakonfigurovaná ve službě Site Recovery migrovat na jiného hostitele Hyper-V ve stejném clusteru nebo na samostatný počítač, není dopad na replikaci pro virtuální počítač. Jenom zkontrolujte, zda splňuje všechny požadavky na nového hostitele technologie Hyper-V a je nakonfigurovaný v Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Snímky konzistentní s aplikací problémy

Konzistentní snímku se v okamžiku snímek dat aplikací ve virtuálním počítači. Služba Stínová kopie svazku (VSS) zajistí aplikace ve virtuálním počítači v konzistentním stavu při pořízení snímku.  Tato část podrobně popisuje některé běžné problémy, kterým může dojít.

### <a name="vss-failing-inside-the-vm"></a>Ve virtuálním počítači selhání služby Stínová kopie svazku

1. Zkontrolujte, že je nainstalovaná a spuštěná nejnovější verzi integrační služby.  Zkontrolujte, jestli jsou aktualizace dostupné spuštěním následujícího příkazu prostředí PowerShell řádku se zvýšenými oprávněními na hostitele Hyper-V: **get-vm | vyberte název, stav, IntegrationServicesState**.
2. Zkontrolujte, zda jsou služby VSS běží a je v pořádku:
    - K tomu, přihlaste se na virtuálním počítači hosta. Potom otevřete příkazový řádek správce a spusťte následující příkazy a zkontrolujte, zda jsou všechny zapisovače VSS v pořádku.
        - **Seznam zapisovačů vssadmin**
        - **Seznam stínů vssadmin**
        - **Vssadmin seznam poskytovatelů**
    - Zkontrolujte výstup. Pokud jsou zapisovače ve stavu selhání, postupujte takto:
        - Zkontrolujte protokol událostí aplikace na virtuálním počítači pro chyby operaci služby Stínová kopie svazku.
    - Zkuste restartovat tyto služby přidružené k selhání zapisovače:
        - Stínová kopie svazku
         - Zprostředkovatele Azure Site Recovery služby Stínová kopie svazku
    - Až to uděláte, počkejte pár hodin, které chcete zobrazit, pokud jsou snímky konzistentní aplikace úspěšně vygenerováno.
    - Jako poslední možnost zkuste restartování virtuálního počítače. Tento problém může vyřešit služby, které jsou ve stavu neodpovídá.
3. Zkontrolujte, že nemáte dynamické disky ve virtuálním počítači. To není podporováno pro snímky konzistentní vzhledem k aplikaci. Můžete zkontrolovat v nástroji Správa disků (diskmgmt.msc).

    ![Dynamický disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Zkontrolujte, že nemáte disk iSCSI připojen k virtuálnímu počítači. Toto není podporováno.
5. Zkontrolujte, že je povolena služba zálohování. Ověřte v **nastavení technologie Hyper-V** > **integrační služby**.
6. Zkontrolujte, zda nedochází ke konfliktům s aplikacemi pořizování snímků služby Stínová kopie svazku. Může dojít, pokud víc aplikací se pokoušíte pořízení snímků služby Stínová kopie svazku na stejný čas konfliktů. Například pokud aplikace Zálohování trvá snímků služby VSS, když Site Recovery je naplánováno vaší zásady replikace k pořízení snímku.   
7. Zkontrolujte, pokud virtuální počítač se vyskytuje vysokou frekvenci přenosů:
    - Můžete měřit denní míry změny dat pro virtuální počítače hostované na hostiteli Hyper-V pomocí čítačů výkonu. Chcete-li to provést, povolte následující čítač. Aggregrate ukázku této hodnoty mezi disky virtuálních počítačů pro 5 až 15 minut, získat změn virtuálních počítačů.
        - Kategorie: "zařízení virtuálního úložiště technologie Hyper-V"
        - Čítač: "zápis bajtů za sekundu"</br>
        - Tato data klidové vytížení zvyšuje nebo zůstat na vysoké úrovni, v závislosti na jejich vytížení virtuálního počítače nebo jeho aplikace jsou.
        - Mísení dat průměrná zdrojový disk je 2 MB/s pro standardní úložiště pro Site Recovery. [Další informace](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Kromě toho můžete [ověřte cíle škálovatelnosti úložiště](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#scalability-targets-for-a-storage-account).
8. Spustit [nasazení Planner](hyper-v-deployment-planner-run.md).
9. Zkontrolovat doporučení pro [sítě](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) a [úložiště](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Nejsou-li v hostiteli technologie Hyper-V služby Stínová kopie svazku

1. Zkontrolujte protokoly událostí pro chyby služby Stínová kopie svazku a doporučení:
    - Na hostitelském serveru Hyper-V, otevřete Správce technologie Hyper-V protokolu událostí v **Prohlížeč událostí** > **protokoly aplikací a služeb** > **Microsoft**  >  **Windows** > **technologie Hyper-V** > **správce**.
    - Ověřte, zda jsou všechny události, které označují selhání snímky konzistentní s aplikací.
    - Typické chyba je: "technologie Hyper-V se nepodařilo vygenerovat sadě snímků služby Stínová kopie svazku pro virtuální počítač 'XYZ': modul pro zápis nastala chyba pouze dočasné. Restartování služby VSS může vyřešit problémy Pokud služba je reagovat."

2. Ke generování snímků služby VSS pro virtuální počítač, zkontrolujte, zda jsou nainstalovány integrační služby technologie Hyper-V na virtuálním počítači a zda je povolena Služba integrace zálohování (VSS).
    - Zkontrolujte, zda služba integrace služby VSS nebo démoni běží hosta a jsou v **OK** stavu.
    - To můžete zkontrolovat z zvýšenými relace prostředí PowerShell na hostiteli technologie Hyper-V pomocí příkazu **et-VMIntegrationService - VMName<VMName>– název služby Stínová kopie svazku** tyto informace můžete získat také pomocí přihlášení na virtuálním počítači hosta. [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Zajistěte, aby integrační služby zálohování nebo služby Stínová kopie svazku na virtuálním počítači běží a v dobrém stavu. Pokud ne, spusťte znovu tyto služby a a žadatel služby Stínová kopie svazku technologie Hyper-V na serveru hostitele technologie Hyper-V.

### <a name="common-errors"></a>Běžné chyby

**Kód chyby** | **Zpráva** | **Podrobnosti**
--- | --- | ---
**0x800700EA.** | "Technologie Hyper-V se nepodařilo vytvořit sadu snímků služby Stínová kopie svazku pro virtuální počítač: další data nejsou k dispozici. (0x800700EA). Nastavte generování snímků služby Stínová kopie svazku může selhat, pokud probíhá operace zálohování.<br/><br/> Operace replikace pro virtuální počítač se nezdařilo: další data nejsou k dispozici. " | Zkontrolujte, jestli virtuální počítač má povolen dynamický disk. Toto není podporováno.
**0x80070032** | "Se nepodařilo připojit k virtuálnímu počítači technologie Hyper-V svazku stínové kopie žadatele <. / VMname > protože verze neodpovídá verzi očekávanou technologie Hyper-V | Zkontrolujte, pokud jsou nainstalovány nejnovější aktualizace systému Windows.<br/><br/> [Upgrade](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services.md#keep-integration-services-up-to-date) na nejnovější verzi integrační služby.



## <a name="collect-replication-logs"></a>Protokoly shromažďování replikace

Všechny události replikace technologie Hyper-V se protokolují v Hyper-V-VMMS\Admin protokolu umístěný v **protokoly aplikací a služeb** > **Microsoft** > **Windows**. Kromě toho můžete povolit analytické protokolu pro službu správy virtuálního počítače technologie Hyper-V následujícím způsobem:

1. Zkontrolujte protokoly analýzy a ladění lze zobrazit v prohlížeči událostí. Chcete-li to provést, v prohlížeči událostí, klikněte na tlačítko **zobrazení** > **zobrazit protokoly pro ladění a.**. Analytické protokol se zobrazí v části **technologie Hyper-V-VMMS**.
2. V **akce** podokně klikněte na tlačítko **povolit protokol**. 

    ![Povolení protokolu](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Když je tato funkce povolená, zobrazí se v **sledování výkonu**, jako **relaci trasování událostí** pod **sady kolekcí dat**. 
4. Chcete-li zobrazit shromážděné informace, ukončete relaci trasování zakázáním v protokolu. Potom uložte protokol a znovu ho otevřete v prohlížeči událostí nebo použít jiné nástroje pro převod podle potřeby.


### <a name="event-log-locations"></a>Umístění protokolu událostí

**V protokolu událostí** | **Podrobnosti** |
--- | ---
**Aplikace a služby protokoly/Microsoft/VirtualMachineManager/Server/Admin** (VMM server) | Protokoly řešení problémů s VMM.
**Aplikace a služby protokoly/MicrosoftAzureRecoveryServices/replikace** (hostitele Hyper-V) | Protokoly k řešení potíží agenta služeb zotavení Microsoft Azure. 
**Aplikace a služby Logs/Microsoft/Azure lokality obnovení/poskytovatele/Operational** (hostitele Hyper-V)| Protokoly a řešení problémů pro službu Microsoft Azure Site Recovery.
**Aplikace a služby protokoly/Microsoft/Windows/Hyper-V-VMMS/Admin** (hostitele Hyper-V) | Protokoly k řešení potíží se správou virtuálních počítačů technologie Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Shromažďování protokolů u pokročilá řešení problémů

Tyto nástroje vám mohou pomoci s pokročilá řešení problémů:

-   VMM, proveďte obnovení lokality protokolu kolekce pomocí [nástroj pro podporu diagnostiky Platform (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Pro Hyper-V bez VMM [stáhnout tento nástroj](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), a spusťte jej na hostiteli technologie Hyper-V, aby shromažďovat protokoly.

