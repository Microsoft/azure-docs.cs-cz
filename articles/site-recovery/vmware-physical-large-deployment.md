---
title: Škálování VMware/fyzického zotavení po havárii pomocí Azure Site Recovery
description: Naučte se, jak nastavit zotavení po havárii do Azure pro velké počty místních virtuálních počítačů VMware nebo fyzických serverů s Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 869702c9eaac4a38f6b0e64311c772892ddd6d90
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580438"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Nastavení zotavení po havárii ve velkém měřítku pro virtuální počítače VMware nebo fyzické servery

Tento článek popisuje, jak nastavit zotavení po havárii do Azure pro velké počty (> 1000) místních virtuálních počítačů VMware nebo fyzických serverů v produkčním prostředí pomocí služby [Azure Site Recovery](site-recovery-overview.md) .


## <a name="define-your-bcdr-strategy"></a>Definování strategie BCDR

Jako součást strategie pro provozní kontinuitu a zotavení po havárii (BCDR) definujete cíle bodů obnovení (RPO) a plánovaný čas obnovení (RTO) pro vaše obchodní aplikace a úlohy. RTO měří dobu trvání a úroveň služeb, ve které musí být obnovená a dostupná obchodní aplikace nebo proces, aby se předešlo problémům s kontinuitou.
- Site Recovery poskytuje průběžnou replikaci pro virtuální počítače VMware a fyzické servery a [smlouvu SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) pro RTO.
- Při plánování rozsáhlého zotavení po havárii pro virtuální počítače VMware a navýšení prostředků Azure, které potřebujete, můžete zadat hodnotu RTO, která se bude používat k výpočtům kapacity.


## <a name="best-practices"></a>Osvědčené postupy

Některé obecné osvědčené postupy pro zotavení po havárii ve velkém měřítku. Tyto osvědčené postupy jsou podrobněji popsány v dalších částech dokumentu.

- **Identifikujte požadavky na cíle**: odhadované nároky na kapacitu a prostředky v Azure před nastavením zotavení po havárii.
- **Plán pro Site Recovery komponenty**: Zjistěte, jaké Site Recovery komponenty (konfigurační server, procesové servery) potřebujete ke splnění odhadované kapacity.
- **Nastavte jeden nebo víc procesových serverů se škálováním na více** instancí: Nepoužívejte procesový Server, který je ve výchozím nastavení spuštěný na konfiguračním serveru. 
- **Spusťte nejnovější aktualizace**: Site Recovery tým pravidelně vydává nové verze Site Recovery komponent a měli byste se ujistit, že používáte nejnovější verze. Abychom vám to usnadnili, sledujte, [co je nového](site-recovery-whats-new.md) pro aktualizace, a [Povolte a nainstalujte aktualizace](service-updates-how-to.md) podle jejich vydání.
- **Proaktivně monitorovat**: při obnovení a zprovoznění zotavení po havárii byste měli aktivně monitorovat stav a stav replikovaných počítačů a prostředků infrastruktury.
- Postup **zotavení po havárii**: v pravidelných intervalech byste měli provozovat zotavení po havárii. To nemá vliv na vaše produkční prostředí, ale zajistěte, aby převzetí služeb při selhání do Azure podle potřeby fungovalo podle očekávání.



## <a name="gather-capacity-planning-information"></a>Shromáždit informace o plánování kapacity

Shromážděte informace o místním prostředí, které vám pomůžou zhodnotit a odhadnout požadavky na kapacitu cíle (Azure).
- Pro VMware spusťte Plánovač nasazení pro virtuální počítače VMware.
- Pro fyzické servery Shromážděte informace ručně.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Spuštění Plánovač nasazení pro virtuální počítače VMware

Plánovač nasazení vám pomůže shromáždit informace o místním prostředí VMware.

- Spusťte Plánovač nasazení během období, které představuje typické změny pro vaše virtuální počítače. Tím se vygeneruje přesnější odhady a doporučení.
- Doporučujeme spustit Plánovač nasazení na počítači konfiguračního serveru, protože Plánovač vypočítá propustnost ze serveru, na kterém je spuštěná. [Přečtěte si další informace](site-recovery-vmware-deployment-planner-run.md#get-throughput) o měření propustnosti.
- Pokud ještě nemáte nastavený konfigurační server:
    - [Získejte přehled](vmware-physical-azure-config-process-server-overview.md) o komponentách Site Recovery.
    - [Nastavte konfigurační server](vmware-azure-deploy-configuration-server.md), aby bylo možné spustit Plánovač nasazení.

Pak spusťte Plánovač následujícím způsobem:

1. [Přečtěte si o](site-recovery-deployment-planner.md) Plánovač nasazení. Nejnovější verzi si můžete stáhnout z portálu nebo [si ji stáhnout přímo](https://aka.ms/asr-deployment-planner).
2. Zkontrolujte [požadavky](site-recovery-deployment-planner.md#prerequisites) a [nejnovější aktualizace](site-recovery-deployment-planner-history.md) pro Plánovač nasazení a [Stáhněte a extrahujte](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) nástroj.
3. [Spusťte Plánovač nasazení](site-recovery-vmware-deployment-planner-run.md) na konfiguračním serveru.
4. [Vygenerujte sestavu](site-recovery-vmware-deployment-planner-run.md#generate-report) pro Shrnutí odhadů a doporučení.
5. Analyzujte [doporučení k sestavám](site-recovery-vmware-deployment-planner-analyze-report.md) a [odhad nákladů](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Ve výchozím nastavení je tento nástroj nakonfigurovaný tak, aby profiloval a vygeneroval sestavu pro až 1000 virtuálních počítačů. Tento limit můžete změnit zvýšením hodnoty MaxVMsSupported klíče v souboru ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Plánování požadavků a kapacity cílů (Azure)

Pomocí shromážděných odhadů a doporučení můžete naplánovat cílové prostředky a kapacitu. Pokud jste spustili Plánovač nasazení pro virtuální počítače VMware, můžete použít řadu [doporučení k sestavám](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) , které vám pomůžou.

- **Kompatibilní virtuální počítače**: Toto číslo použijte k určení počtu virtuálních počítačů, které jsou připravené na zotavení po havárii do Azure. Doporučení týkající se šířky pásma sítě a jader Azure jsou založena na tomto počtu.
- **Požadovaná šířka pásma sítě**: Poznamenejte si šířku pásma, kterou potřebujete pro rozdílovou replikaci kompatibilních virtuálních počítačů. 
    - Při spuštění plánovače zadáte požadovaný cíl bodu obnovení v minutách. Doporučení ukazují šířku pásma potřebnou ke splnění tohoto bodu RPO 100% a 90% času. 
    - Doporučení šířky pásma sítě se berou v úvahu šířky pásma potřebné pro celkový počet konfiguračních serverů a procesových serverů doporučených v plánovači.
- **Požadované jádro Azure**: Poznamenejte si počet jader, které v cílové oblasti Azure potřebujete, na základě počtu kompatibilních virtuálních počítačů. Pokud nemáte dostatek jader, při převzetí služeb při selhání Site Recovery nepůjde vytvořit požadované virtuální počítače Azure.
- **Doporučená velikost dávky virtuálních počítačů**: Doporučená velikost dávky je založena na možnosti dokončení počáteční replikace pro dávku během 72 hodin ve výchozím nastavení při splnění cíle bodu obnovení 100%. Hodnotu hodiny lze upravit.

Tato doporučení můžete použít k plánování prostředků Azure, šířky pásma sítě a dávkování virtuálních počítačů.

## <a name="plan-azure-subscriptions-and-quotas"></a>Plánování předplatných a kvót Azure

Chceme zajistit, aby dostupné kvóty v cílovém předplatném byly dostatečné pro zpracování převzetí služeb při selhání.

**Úkol** | **Podrobnosti** | **Akce**
--- | --- | ---
**Kontrolovat jádra** | Pokud se jádra v dostupné kvótě nerovnají nebo překračují celkový počet cílů v době převzetí služeb při selhání, převzetí služeb při selhání se nezdaří. | V případě virtuálních počítačů VMware ověřte, že je v cílovém předplatném dostatek jader, aby splňovaly doporučení Plánovač nasazení Core.<br/><br/> U fyzických serverů ověřte, že Azure Core vyhovují vašim ručním odhadům.<br/><br/> Pokud chcete kontrolovat kvóty, klikněte v **Předplatném** Azure Portal > na **využití + kvóty**.<br/><br/> [Přečtěte si další informace](../azure-portal/supportability/resource-manager-core-quotas-request.md) o zvýšení kvót.
**Kontrolovat omezení převzetí služeb při selhání** | Počet převzetí služeb při selhání nesmí překračuje Site Recovery limity převzetí služeb při selhání. |  Pokud převzetí služeb při selhání překročilo limity, můžete přidat odběry, převzít služby při selhání na více předplatných nebo zvýšit kvótu pro předplatné. 


### <a name="failover-limits"></a>Omezení převzetí služeb při selhání

Omezení označují počet převzetí služeb při selhání, které Site Recovery do jedné hodiny podporují, a předpokládá tři disky na jeden počítač.

Co to znamená? Aby bylo možné spustit virtuální počítač Azure, Azure vyžaduje, aby byly některé ovladače v počátečním stavu spouštění, a služby jako DHCP mají být nastavené tak, aby se spouštěly automaticky.
- Počítače, které vyhovují, již mají tato nastavení zavedena.
- V případě počítačů s Windows můžete proaktivní kontrolu dodržování předpisů a v případě potřeby je v případě potřeby nastavit jako vyhovující. [Další informace](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Počítače se systémem Linux se v době převzetí služeb při selhání zanesou do dodržování předpisů.

**Počítač vyhovuje Azure?** | **Omezení virtuálních počítačů Azure (převzetí služeb při selhání spravovaného disku)**
--- | --- 
Yes | 2000
No | 1000

- Omezení předpokládají, že v cílové oblasti pro předplatné probíhá minimálně jiné úlohy.
- Některé oblasti Azure jsou menší a můžou mít mírně nižší omezení.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Plánování infrastruktury a připojení virtuálních počítačů

Po převzetí služeb při selhání do Azure budete potřebovat vaše úlohy, aby fungovaly v místním prostředí, a umožnili uživatelům přístup k úlohám běžícím na virtuálních počítačích Azure.

- [Přečtěte si další informace](site-recovery-active-directory.md#test-failover-considerations) o převzetí služeb při selhání v rámci služby Active Directory nebo místní infrastruktury DNS do Azure.
- [Přečtěte si víc](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) o přípravě připojení k virtuálním počítačům Azure po převzetí služeb při selhání.



## <a name="plan-for-source-capacity-and-requirements"></a>Plánování zdrojové kapacity a požadavků

Je důležité, abyste měli dostatek konfiguračních serverů a procesových serverů se škálováním na více instancí, aby splňovaly požadavky na kapacitu. Jak zahájíte rozsáhlé nasazení, začněte s jediným konfiguračním serverem a jedním procesovým serverem se škálováním na více instancí. Až se dostanete k předepsaným limitům, přidejte další servery.

>[!NOTE]
> V případě virtuálních počítačů VMware Plánovač nasazení provede některá doporučení týkající se konfiguračních a procesních serverů, které potřebujete. Místo následujících doporučení Plánovač nasazení doporučujeme použít tabulky zahrnuté v následujících postupech. 


## <a name="set-up-a-configuration-server"></a>Nastavení konfiguračního serveru
 
Kapacita konfiguračního serveru je ovlivněná počtem počítačů, které se replikují, a ne podle míry četnosti změn dat. Pokud chcete zjistit, jestli potřebujete další konfigurační servery, použijte tyto definované limity virtuálních počítačů.

**Procesor** | **Memory (Paměť)** | **Disk mezipaměti** | **Limit replikovaného počítače**
 --- | --- | --- | ---
8 vCPU<br> 2 sokety × 4 jádra @ 2,5 GHz | 16 GB | 600 GB | Až 550 počítačů<br> Předpokládá, že každý počítač má tři disky o velikosti 100 GB.

- Tato omezení vycházejí z konfiguračního serveru nastaveného pomocí šablony OVF.
- Omezení předpokládají, že nepoužíváte procesový Server, který je ve výchozím nastavení spuštěn na konfiguračním serveru.

Pokud potřebujete přidat nový konfigurační server, postupujte podle těchto pokynů:

- [Nastavení konfiguračního serveru](vmware-azure-deploy-configuration-server.md) pro zotavení po havárii virtuálního počítače VMware pomocí šablony OVF
- Ruční [nastavení konfiguračního serveru](physical-azure-set-up-source.md) pro fyzické servery nebo pro nasazení VMware, která nemůžou používat šablonu OVF

Při nastavování konfiguračního serveru mějte na paměti, že:

- Při nastavování konfiguračního serveru je důležité zvážit předplatné a trezor, ve kterém se nachází, protože by se neměly po instalaci změnit. Pokud potřebujete změnit trezor, musíte zrušit přidružení konfiguračního serveru od trezoru a znovu ho zaregistrovat. Tím se zastaví replikace virtuálních počítačů v trezoru.
- Pokud chcete nastavit konfigurační server s více síťovými adaptéry, měli byste to provést během nastavování. Nemůžete to provést po registraci konfiguračního serveru v trezoru.

## <a name="set-up-a-process-server"></a>Nastavení procesového serveru

Kapacita procesového serveru je ovlivněná mírami četnosti změn dat, nikoli počtem počítačů povolených pro replikaci.

- U rozsáhlých nasazení byste měli mít vždycky aspoň jeden procesový Server se škálováním na více instancí.
- Pokud chcete zjistit, jestli potřebujete další servery, použijte následující tabulku.
- Doporučujeme přidat server s nejvyšší specifikací. 


**Procesor** | **Memory (Paměť)** | **Disk mezipaměti** | **Míra četnosti změn**
 --- | --- | --- | --- 
12 vCPU<br> 2 sokety × 6 jader @ 2,5 GHz | 24 GB | 1 GB | Až 2 TB za den

Procesový Server nastavte následujícím způsobem:

1. Zkontrolujte [požadavky](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Nainstalujte server na [portál](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)nebo z [příkazového řádku](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Nakonfigurujte replikované počítače tak, aby používaly nový server. Pokud už máte počítače, které se replikují:
    - Celou úlohu procesového serveru můžete [přesunout](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) na nový procesový Server.
    - Případně můžete [přesunout](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) konkrétní virtuální počítače na nový procesový Server.



## <a name="enable-large-scale-replication"></a>Povolit velkou škálu replikace

Po naplánování kapacity a nasazení požadovaných komponent a infrastruktury Povolte replikaci pro velký počet virtuálních počítačů.

1. Seřadí počítače do dávek. Povolíte replikaci pro virtuální počítače v rámci dávky a pak přejdete k další dávce.

    - U virtuálních počítačů VMware můžete použít [doporučenou velikost dávky virtuálního počítače](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) v sestavě Plánovač nasazení.
    - U fyzických počítačů doporučujeme identifikovat dávky založené na počítačích, které mají podobnou velikost a množství dat, a na dostupné propustnosti sítě. Cílem je dávkování počítačů, které mohou během stejné doby dokončit počáteční replikaci.
    
2. Pokud je velikost disku v počítači vysoká nebo překročí omezení v thePlanner nasazení, můžete nepostradatelné soubory přesunout (například výpisy protokolů nebo dočasné soubory) mimo počítač. Pro virtuální počítače VMware můžete tyto soubory přesunout na samostatný disk a pak [Tento disk vyloučit](vmware-azure-exclude-disk.md) z replikace.
3. Než povolíte replikaci, ověřte, jestli počítače splňují [požadavky na replikaci](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Nakonfigurujte zásady replikace pro [virtuální počítače VMware](vmware-azure-set-up-replication.md#create-a-policy) nebo [fyzické servery](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Povolte replikaci pro [virtuální počítače VMware](vmware-azure-enable-replication.md) nebo [fyzické servery](physical-azure-disaster-recovery.md#enable-replication). Tím se aktivuje počáteční replikace pro vybrané počítače.

## <a name="monitor-your-deployment"></a>Monitorování nasazení

Po zahájení replikace pro první dávku virtuálních počítačů spusťte monitorování nasazení následujícím způsobem:  

1. Přiřaďte správce zotavení po havárii pro monitorování stavu replikovaných počítačů.
2. [Monitoruje události](site-recovery-monitor-and-troubleshoot.md) pro replikované položky a infrastrukturu.
3. [Monitorujte stav](vmware-physical-azure-monitor-process-server.md) serverových serverů se škálováním na více instancí.
4. Zaregistrujte se a získejte [e-mailová oznámení](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) pro události, abyste mohli snadněji sledovat.
5. Provádějte pravidelná [cvičení zotavení po havárii](site-recovery-test-failover-to-azure.md), abyste zajistili, že všechno funguje podle očekávání.


## <a name="plan-for-large-scale-failovers"></a>Plán pro převzetí služeb při selhání ve velkém měřítku

V případě havárie možná budete muset převzít služby při selhání velkého počtu počítačů nebo úloh do Azure. Připravte se na tento typ události následujícím způsobem.

Pro převzetí služeb při selhání se můžete připravit předem následujícím způsobem:

- [Připravte svoji infrastrukturu a virtuální počítače](#plan-infrastructure-and-vm-connectivity) tak, aby vaše úlohy byly dostupné po převzetí služeb při selhání, takže uživatelé budou mít přístup k virtuálním počítačům Azure.
- Poznamenejte si [omezení převzetí služeb při selhání](#failover-limits) výše v tomto dokumentu. Ujistěte se, že převzetí služeb při selhání spadá do těchto limitů.
- Spusťte běžné [cvičení zotavení po havárii](site-recovery-test-failover-to-azure.md). Podrobnější informace k podrobnostem:
    - Před převzetím služeb při selhání Najděte ve svém nasazení mezery.
    - Odhadněte kompletní RTO pro vaše aplikace.
    - Odhad kompletních RPO pro vaše úlohy.
    - Identifikujte konflikty rozsahu IP adres.
    - Při spouštění podrobností doporučujeme, abyste nepoužívali provozní sítě pro procházení a po každém přechodu provedli vyčištění testovacího převzetí služeb při selhání.

Pro spuštění rozsáhlého převzetí služeb při selhání doporučujeme následující:

1. Vytvořte plány obnovení pro převzetí služeb při selhání pro úlohu.
    - Každý plán obnovení může aktivovat převzetí služeb při selhání až 100 počítačů.
    - [Přečtěte si další informace](recovery-plan-overview.md) o plánech obnovení.
2. Přidejte Azure Automation skripty Runbook do plánů obnovení, abyste mohli automatizovat ruční úlohy v Azure. Mezi obvyklé úlohy patří konfigurace nástrojů pro vyrovnávání zatížení, aktualizace DNS atd. [Další informace](site-recovery-runbook-automation.md)
2. Před převzetím služeb při selhání připravte počítače s Windows tak, aby splňovaly prostředí Azure. [Limity převzetí služeb při selhání](#plan-azure-subscriptions-and-quotas) jsou pro počítače, které vyhovují, vyšší. [Přečtěte si další informace](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) o sadách Runbook.
4.  Aktivujte převzetí služeb při selhání pomocí rutiny [Start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) prostředí PowerShell společně s plánem obnovení.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování Site Recovery](site-recovery-monitor-and-troubleshoot.md)
