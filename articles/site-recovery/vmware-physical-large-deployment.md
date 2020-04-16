---
title: Škálování obnovení vmware/fyzické katastrofy pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro velký počet místních virtuálních počítačů VMware nebo fyzických serverů s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409772"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Nastavení zotavení po havárii ve velkém měřítku pro virtuální zařízení/fyzické servery VMware

Tento článek popisuje, jak nastavit zotavení po havárii do Azure pro velký počet (> 1000) místních virtuálních počítačů VMware nebo fyzických serverů ve vašem produkčním prostředí pomocí služby [Azure Site Recovery.](site-recovery-overview.md)


## <a name="define-your-bcdr-strategy"></a>Definujte svou strategii BCDR

Jako součást strategie kontinuity podnikání a zotavení po havárii (BCDR) definujete cíle bodů obnovení (RPO) a cíle doby obnovení (RTO) pro vaše obchodní aplikace a úlohy. RTO měří dobu trvání a úroveň služeb, během kterých musí být obchodní aplikace nebo proces obnovena a dostupná, aby se zabránilo problémům s kontinuitou.
- Obnovení sítě poskytuje nepřetržitou replikaci pro virtuální sítě VMware a fyzické servery a [sla](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) pro RTO.
- Při plánování rozsáhlého zotavení po havárii pro virtuální počítače VMware a zjištění prostředků Azure, které potřebujete, můžete zadat hodnotu RTO, která se použije pro výpočty kapacity.


## <a name="best-practices"></a>Osvědčené postupy

Některé obecné doporučené postupy pro rozsáhlé zotavení po havárii. Tyto osvědčené postupy jsou podrobněji popsány v dalších částech dokumentu.

- **Identifikujte cílové požadavky**: Odhadněte kapacitu a potřeby prostředků v Azure před nastavením zotavení po havárii.
- **Plán pro součásti site recovery**: Zjistěte, jaké součásti site recovery (konfigurační server, procesní servery) potřebujete ke splnění odhadované kapacity.
- **Nastavte jeden nebo více horizontálních procesních serverů**: Nepoužívejte procesní server, který je spuštěn ve výchozím nastavení na konfiguračním serveru. 
- **Spusťte nejnovější aktualizace**: Tým site recovery vydává nové verze komponent site recovery pravidelně a měli byste se ujistit, že používáte nejnovější verze. Chcete-li s tím pomoci, sledujte, [co je nového](site-recovery-whats-new.md) pro aktualizace, a [povolte a nainstalujte aktualizace](service-updates-how-to.md) při jejich vydání.
- **Monitorování proaktivně**: Jak zprovozníte zotavení po havárii, měli byste proaktivně sledovat stav a stav replikovaných počítačů a prostředků infrastruktury.
- **Cvičení zotavení po havárii**: Měli byste pravidelně spouštět cvičení pro zotavení po havárii. Ty nemají vliv na produkční prostředí, ale pomáhají zajistit, že převzetí služeb při selhání do Azure bude fungovat podle očekávání v případě potřeby.



## <a name="gather-capacity-planning-information"></a>Shromažďování informací o plánování kapacity

Shromážděte informace o místním prostředí, abyste vám pomohli posoudit a odhadnout potřeby kapacity cíle (Azure).
- V systému VMware spusťte Plánovač nasazení pro virtuální počítače VMware.
- U fyzických serverů shromažďujte informace ručně.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Spuštění Plánovače nasazení pro virtuální počítače VMware

Plánovač nasazení vám pomůže shromáždit informace o místním prostředí VMware.

- Spusťte Plánovač nasazení během období, které představuje typické změny pro vaše virtuální počítače. Tím se vygenerují přesnější odhady a doporučení.
- Doporučujeme spustit Plánovač nasazení v počítači konfiguračního serveru, protože Planner vypočítá propustnost ze serveru, na kterém je spuštěn. [Přečtěte si další informace](site-recovery-vmware-deployment-planner-run.md#get-throughput) o měření propustnosti.
- Pokud ještě nemáte nastavený konfigurační server:
    - [Získejte přehled](vmware-physical-azure-config-process-server-overview.md) součástí obnovení webu.
    - [Nastavte konfigurační server](vmware-azure-deploy-configuration-server.md), abyste na něm mohli spustit Plánovač nasazení.

Potom spusťte Plánovač takto:

1. [Přečtěte si o](site-recovery-deployment-planner.md) Plánovači nasazení. Nejnovější verzi si můžete stáhnout z portálu nebo [si ji stáhnout přímo](https://aka.ms/asr-deployment-planner).
2. Zkontrolujte [požadavky](site-recovery-deployment-planner.md#prerequisites) a [nejnovější aktualizace](site-recovery-deployment-planner-history.md) plánovače nasazení a [stáhněte a extrahujte](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) nástroj.
3. [Spusťte Plánovač nasazení](site-recovery-vmware-deployment-planner-run.md) na konfiguračním serveru.
4. [Vygenerujte sestavu,](site-recovery-vmware-deployment-planner-run.md#generate-report) která shrne odhady a doporučení.
5. Analyzujte [doporučení sestavy](site-recovery-vmware-deployment-planner-analyze-report.md) a [odhady nákladů](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Ve výchozím nastavení je nástroj nakonfigurován na profil a generuje sestavu až pro 1000 virtuálních počítače. Toto omezení můžete změnit zvýšením hodnoty klíče MaxVMsPodporovaný v souboru ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Plánování požadavků a kapacity cíle (Azure)

Pomocí shromážděných odhadů a doporučení můžete naplánovat cílové zdroje a kapacitu. Pokud jste spustili Plánovač nasazení pro virtuální počítače VMware, můžete použít řadu [doporučení sestavy,](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) které vám pomohou.

- **Kompatibilní virtuální počítače**: Toto číslo slouží k identifikaci počtu virtuálních počítače, které jsou připravené pro zotavení po havárii do Azure. Doporučení týkající se šířky pásma sítě a jádra Azure jsou založeny na tomto čísle.
- **Požadovaná šířka pásma sítě**: Všimněte si šířky pásma, kterou potřebujete pro rozdílovou replikaci kompatibilních virtuálních počítačů. 
    - Při spuštění plánovače zadáte požadovaný rpo během několika minut. Doporučení ukazují šířku pásma potřebnou ke splnění tohoto RPO 100% a 90% času. 
    - Doporučení šířky pásma sítě berou v úvahu šířku pásma potřebnou pro celkový počet konfiguračních serverů a procesních serverů doporučených v plánovači.
- **Požadovaná jádra Azure**: Všimněte si počtu jader, které potřebujete v cílové oblasti Azure, na základě počtu kompatibilních virtuálních počítačů. Pokud nemáte dostatek jader, při obnovení webu převzetí služeb při selhání nebude možné vytvořit požadované virtuální počítače Azure.
- **Doporučená velikost dávky virtuálního počítače**: Doporučená velikost dávky je založena na možnosti dokončit počáteční replikaci dávky do 72 hodin ve výchozím nastavení, zatímco splnění RPO 100 %. Hodnotu hodiny lze upravit.

Tato doporučení můžete použít k plánování prostředků Azure, šířky pásma sítě a dávkování virtuálních počítačů.

## <a name="plan-azure-subscriptions-and-quotas"></a>Plánování předplatných a kvót Azure

Chceme se ujistit, že dostupné kvóty v cílovém předplatném jsou dostatečné pro zpracování převzetí služeb při selhání.

**Úkol** | **Podrobnosti** | **Akce**
--- | --- | ---
**Kontrola jader** | Pokud se jádra v dostupné kvótě nerovnají nebo nepřekročí celkový počet cílů v době převzetí služeb při selhání, převzetí služeb při selhání se nezdaří. | U virtuálních měnových virtuálních počítače VMware zkontrolujte, zda máte dostatek jader v cílovém předplatném, abyste splnili základní doporučení plánovače nasazení.<br/><br/> U fyzických serverů zkontrolujte, zda jádra Azure splňují vaše ruční odhady.<br/><br/> Chcete-li zkontrolovat kvóty, klikněte na webu Azure Portal > **Subscription**na **položku Využití + kvóty**.<br/><br/> [Přečtěte si další informace](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) o zvyšování kvót.
**Kontrola limitů převzetí služeb při selhání** | Počet převzetí služeb při selhání nesmí překročit limity převzetí služeb při selhání obnovení webu. |  Pokud převzetí služeb při selhání překročí limity, můžete přidat předplatná a převzetí služeb při selhání na více předplatných nebo zvýšit kvótu pro předplatné. 


### <a name="failover-limits"></a>Limity převzetí služeb při selhání

Omezení označují počet převzetí služeb při selhání, které jsou podporovány site recovery během jedné hodiny, za předpokladu, že tři disky na počítač.

Co znamená vyhovět? Chcete-li spustit virtuální počítač Azure, Azure vyžaduje, aby některé ovladače byly ve stavu spuštění spouštění a služby, jako je DHCP, se mají spouštět automaticky.
- Stroje, které splňují, budou mít tato nastavení již na svém místě.
- U počítačů se systémem Windows můžete proaktivně kontrolovat dodržování předpisů a v případě potřeby je provést tak kompatibilními. [Další informace](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linuxové počítače jsou uvedeny do souladu pouze v době převzetí služeb při selhání.

**Počítač vyhovuje Azure?** | **Omezení virtuálních počítačů Azure (převzetí služeb při selhání spravovaného disku)**
--- | --- 
Ano | 2000
Ne | 1000

- Limity předpokládají, že v cílové oblasti pro odběr probíhá minimální počet dalších úloh.
- Některé oblasti Azure jsou menší a mohou mít mírně nižší limity.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Plánování infrastruktury a připojení virtuálních zařízení

Po převzetí služeb při selhání do Azure potřebujete, aby vaše úlohy fungovaly stejně jako místní a aby uživatelům umožnily přístup k úlohám spuštěným na virtuálních počítačích Azure.

- [Přečtěte si další informace](site-recovery-active-directory.md#test-failover-considerations) o převzetí služeb při selhání přes vaši službu Active Directory nebo místní infrastrukturu DNS do Azure.
- [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) o přípravě na připojení k virtuálním počítačům Azure po převzetí služeb při selhání.



## <a name="plan-for-source-capacity-and-requirements"></a>Plán pro zdrojové kapacity a požadavky

Je důležité, abyste měli dostatek konfiguračních serverů a horizontálních navýšení kapacity, aby splňovaly požadavky na kapacitu. Při zahájení rozsáhlého nasazení začněte s jedním konfiguračním serverem a jedním procesovým serverem s horizontálním navýšením kapacity. Jakmile dosáhnete předepsaných limitů, přidejte další servery.

>[!NOTE]
> U virtuálních počítačů VMware poskytuje Plánovač nasazení některá doporučení týkající se potřebných konfiguračních a procesních serverů. Doporučujeme použít tabulky zahrnuté v následujících postupech, namísto následujících doporučení plánovače nasazení. 


## <a name="set-up-a-configuration-server"></a>Nastavení konfiguračního serveru
 
Kapacita konfiguračního serveru je ovlivněna počtem replikace počítačů a nikoli rychlostí změn dat. Chcete-li zjistit, zda potřebujete další konfigurační servery, použijte tyto definované limity virtuálních počítačů.

**Cpu** | **Paměti** | **Disk mezipaměti** | **Limit replikovaného počítače**
 --- | --- | --- | ---
8 virtuálních procesorů<br> 2 zásuvky * 4 jádra @ 2.5 Ghz | 16 GB | 600 GB | Až 550 strojů<br> Předpokládá, že každý počítač má tři disky 100 GB každý.

- Tato omezení jsou založena na konfiguračním serveru nastaveném pomocí šablony OVF.
- Omezení předpokládají, že nepoužíváte procesní server, který je spuštěn ve výchozím nastavení na konfiguračním serveru.

Pokud potřebujete přidat nový konfigurační server, postupujte podle následujících pokynů:

- [Nastavte konfigurační server](vmware-azure-deploy-configuration-server.md) pro zotavení po havárii virtuálního počítače VMware pomocí šablony OVF.
- [Nastavte konfigurační server](physical-azure-set-up-source.md) ručně pro fyzické servery nebo pro nasazení vmware, která nelze použít šablonu OVF.

Při nastavě konfiguračního serveru si uvědomte, že:

- Při nastavování konfiguračního serveru je důležité zvážit odběr a trezor, ve kterém se nachází, protože by se po instalaci neměly měnit. Pokud potřebujete změnit úschovnu, je nutné zrušit přidružení konfiguračního serveru k on-trezoru a znovu jej zaregistrovat. Tím se zastaví replikace virtuálních klíčů v trezoru.
- Chcete-li nastavit konfigurační server s více síťovými adaptéry, měli byste to provést během instalace. To nelze provést po registraci konfiguračního serveru v úschovně.

## <a name="set-up-a-process-server"></a>Nastavení procesního serveru

Kapacita procesního serveru je ovlivněna četností změn dat a nikoli počtem počítačů povolených pro replikaci.

- Pro velká nasazení byste měli mít vždy alespoň jeden proces horizontálnínavýšení kapacity serveru.
- Chcete-li zjistit, zda potřebujete další servery, použijte následující tabulku.
- Doporučujeme přidat server s nejvyšší specifikací. 


**Cpu** | **Paměti** | **Disk mezipaměti** | **Rychlost konve**
 --- | --- | --- | --- 
12 virtuálních procesorů<br> 2 zásuvky * 6 jader @ 2.5 Ghz | 24 GB | 1 GB | Až 2 TB denně

Procesní server nastavte následujícím způsobem:

1. Zkontrolujte [požadavky](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Nainstalujte server na [portál](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)nebo z [příkazového řádku](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Nakonfigurujte replikované počítače tak, aby používaly nový server. Pokud již máte počítače replikovat:
    - Můžete [přesunout](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) celé úlohy procesového serveru na nový procesový server.
    - Případně můžete [přesunout](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) konkrétní virtuální servery na nový procesový server.



## <a name="enable-large-scale-replication"></a>Povolení rozsáhlé replikace

Po plánování kapacity a nasazení požadovaných komponent a infrastruktury povolte replikaci pro velký počet virtuálních klíčů.

1. Třídit stroje do dávek. Povolíte replikaci pro virtuální počítače v rámci dávky a pak přejdete na další dávku.

    - Pro virtuální počítače VMware můžete použít [doporučenou velikost dávky virtuálního počítače](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) v sestavě Plánovač nasazení.
    - Pro fyzické počítače doporučujeme identifikovat dávky na základě počítačů, které mají podobnou velikost a množství dat a na dostupné propustnost sítě. Cílem je dávkové počítače, které pravděpodobně dokončí počáteční replikaci přibližně za stejnou dobu.
    
2. Pokud je konve disku pro počítač vysoká nebo překračuje limity v nasazení planner, můžete přesunout nekritické soubory, které není nutné replikovat (například výpisy protokolu nebo dočasné soubory) z počítače. U virtuálních počítačů VMware můžete tyto soubory přesunout na samostatný disk a potom [tento disk vyloučit](vmware-azure-exclude-disk.md) z replikace.
3. Před povolením replikace zkontrolujte, zda počítače splňují [požadavky na replikaci](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Konfigurace zásad replikace pro [virtuální počítače VMware](vmware-azure-set-up-replication.md#create-a-policy) nebo [fyzické servery](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Povolte replikaci pro [virtuální zařízení VMware](vmware-azure-enable-replication.md) nebo [fyzické servery](physical-azure-disaster-recovery.md#enable-replication). Tím se spustí počáteční replikace pro vybrané počítače.

## <a name="monitor-your-deployment"></a>Monitorování nasazení

Po zahájení replikace pro první dávku virtuálních počítačů začněte sledovat nasazení následujícím způsobem:  

1. Přiřaďte správce zotavení po havárii ke sledování stavu replikovaných počítačů.
2. [Sledujte události](site-recovery-monitor-and-troubleshoot.md) pro replikované položky a infrastrukturu.
3. [Sledujte stav](vmware-physical-azure-monitor-process-server.md) horizontálních navýšení kapacity procesních serverů.
4. Zaregistrujte se a získejte [e-mailová oznámení](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) o událostech, abyste je snadněji sledovali.
5. Proveďte pravidelné [cvičení zotavení po havárii](site-recovery-test-failover-to-azure.md), abyste zajistili, že vše funguje podle očekávání.


## <a name="plan-for-large-scale-failovers"></a>Plánování rozsáhlých převzetí služeb při selhání

V případě havárie budete muset přepojit velký počet počítačů/úloh do Azure. Připravte se na tento typ události následujícím způsobem.

Na převzetí služeb při selhání se můžete předem připravit takto:

- [Připravte si infrastrukturu a virtuální počítače](#plan-infrastructure-and-vm-connectivity) tak, aby vaše úlohy byly po převzetí služeb při selhání dostupné a aby uživatelé měli přístup k virtuálním počítačům Azure.
- Všimněte si [omezení převzetí služeb při selhání](#failover-limits) dříve v tomto dokumentu. Ujistěte se, že vaše převzetí služeb při selhání bude spadat do těchto limitů.
- Spusťte pravidelné [cvičení zotavení po havárii](site-recovery-test-failover-to-azure.md). Vrtáky pomáhají:
    - Najděte mezery v nasazení před převzetím služeb při selhání.
    - Odhadněte komplexní rto pro vaše aplikace.
    - Odhadněte komplexní rpopro pro vaše úlohy.
    - Identifikujte konflikty rozsahu IP adres.
    - Při spouštění cvičení doporučujeme nepoužívat produkční sítě pro cvičení, nepoužívat stejné názvy podsítí v produkčních a testovacích sítích a po každém přechodu k použití zkušebních převzetí služeb při selhání vyčistit.

Chcete-li spustit převzetí služeb při selhání ve velkém měřítku, doporučujeme následující:

1. Vytvořte plány obnovení pro převzetí služeb při selhání pracovního vytížení.
    - Každý plán obnovení může aktivovat převzetí služeb při selhání až 100 počítačů.
    - [Přečtěte si další informace](recovery-plan-overview.md) o plánech obnovení.
2. Přidejte skripty runbooku Azure Automation do plánů obnovení a automatizujte všechny ruční úlohy v Azure. Mezi typické úkoly patří konfigurace vykladačů zatížení, aktualizace DNS atd. [Další informace](site-recovery-runbook-automation.md)
2. Před převzetím služeb při selhání připravte počítače s Windows tak, aby byly v souladu s prostředím Azure. [Limity převzetí služeb při selhání](#plan-azure-subscriptions-and-quotas) jsou vyšší pro počítače, které splňují požadavky. [Přečtěte si další informace](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) o runbookech.
4.  Aktivace převzetí služeb při selhání s rutinou [prostředí Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell spolu s plánem obnovení.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Monitorování Site Recovery](site-recovery-monitor-and-troubleshoot.md)
