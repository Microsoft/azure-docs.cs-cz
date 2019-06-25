---
title: Nastavení zotavení po havárii do Azure pro velký počet virtuálních počítačů VMware nebo fyzické servery s využitím Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro velký počet místních virtuálních počítačů VMware nebo fyzické servery s využitím Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: e96aafe61c0d8547ffca9e97bfd9e90c9529155f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237268"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Nastavení zotavení po havárii ve velkém měřítku pro virtuální počítače VMware a fyzických serverů

Tento článek popisuje, jak nastavit zotavení po havárii do Azure pro velké množství (1 000 >) na místních virtuálních počítačů VMware nebo fyzických serverů v produkčním prostředí, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="define-your-bcdr-strategy"></a>Definování strategie BCDR

Jako součást kontinuita podnikových procesů a strategie po havárii (BCDR) můžete definovat bodů obnovení (rpo) a plánované doby obnovení (RTO) pro podnikové aplikace a úlohy. RTO měří dobu trvání služby a úrovně, ve kterém obchodní aplikace nebo proces musí být obnovena a k dispozici, aby se zabránilo problémům kontinuity podnikových procesů.
- Site Recovery poskytuje průběžnou replikaci pro virtuální počítače VMware a fyzických serverů a [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) pro RTO.
- Při plánování zotavení po havárii ve velkém měřítku pro virtuální počítače VMware a zjistit, prostředků Azure, které potřebujete, můžete zadat hodnotu RTO, který se použije k výpočtu kapacity.


## <a name="best-practices"></a>Osvědčené postupy

Některé obecné osvědčené postupy pro zotavení po havárii ve velkém měřítku. Tyto osvědčené postupy jsou podrobněji popsány v následujících částech dokumentu.

- **Identifikaci požadavků na cílový**: Odhadněte si nároky na kapacitu a prostředků v Azure před nastavením zotavení po havárii.
- **Plánování pro součásti Site Recovery**: Zjistěte, jaké součásti Site Recovery (konfigurační server, procesových serverů), je potřeba splnit vaše odhadovaná kapacity.
- **Nastavit jeden nebo více horizontální navýšení kapacity procesových serverů**: Nepoužívejte procesový server, na kterém běží ve výchozím nastavení na konfiguračním serveru. 
- **Spustit nejnovější aktualizace**: Tým Site Recovery vydává nové verze komponent Site Recovery v pravidelných intervalech a ujistěte se, že používáte nejnovější verze. Abyste s ním, sledovat [novinky](site-recovery-whats-new.md) aktualizací, a [povolte a nainstalujte aktualizace](service-updates-how-to.md) jako jejich verzi.
- **Proaktivní monitorování**: Až se zotavení po havárii rychle zprovoznit, by měl aktivně monitorovat stav a stav replikované počítače a prostředků infrastruktury.
- **Zotavení po havárii**: Nácvik zotavení po havárii by měl spouštět v pravidelných intervalech. Tyto nebudou mít vliv na vaše produkční prostředí, ale zajistit, že převzetí služeb při selhání do Azure budou fungovat podle očekávání, pokud je nepotřebujete.



## <a name="gather-capacity-planning-information"></a>Shromážděte informace o plánování kapacity

Shromážděte informace o v místním prostředí, vám pomůže posoudit a odhadnout, že potřebuje kapacitu pro cíl (Azure).
- Pro replikaci z VMware spuštění plánovače nasazení pro virtuální počítače VMware, chcete-li to provést.
- Pro fyzické servery shromážděte informace ručně.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Spuštění plánovače nasazení pro virtuální počítače VMware

Deployment Planner vám pomůže shromažďovat informace o vašem VMware v místním prostředí.

- Spuštění plánovače nasazení služby v období, která představuje typickou změn pro virtuální počítače. Tím se vygeneruje přesnější odhady a doporučení.
- Doporučujeme spustit Plánovač nasazení služby na konfigurace počítače serveru, protože plánovač vypočítá propustnost ze serveru, na kterém je spuštěný. [Další informace](site-recovery-vmware-deployment-planner-run.md#get-throughput) o měření propustnosti.
- Pokud ještě nemáte nastavit konfigurační server:
    - [Získejte přehled](vmware-physical-azure-config-process-server-overview.md) komponent Site Recovery.
    - [Nastavení konfigurace serveru](vmware-azure-deploy-configuration-server.md), chcete-li spustit Deployment Planner na něj.

Plánovač pak spusťte následujícím způsobem:

1. [Další informace o](site-recovery-deployment-planner.md) plánovače nasazení služby. Nejnovější verzi si můžete stáhnout z portálu, nebo [přímo stahovat](https://aka.ms/asr-deployment-planner).
2. Zkontrolujte [požadavky](site-recovery-deployment-planner.md#prerequisites) a [nejnovější aktualizace](site-recovery-deployment-planner-history.md) pro Plánovač nasazení služby a [stažení a extrakci](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) nástroj.
3. [Spuštění plánovače nasazení služby](site-recovery-vmware-deployment-planner-run.md) na konfiguračním serveru.
4. [Vygenerovat sestavu](site-recovery-vmware-deployment-planner-run.md#generate-report) slouží ke shrnutí odhady a doporučení.
5. Analýza [sestavy doporučení](site-recovery-vmware-deployment-planner-analyze-report.md) a [odhady nákladů](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Ve výchozím nastavení nástroj je nakonfigurován k profilu a generuje sestavy až 1 000 virtuálních počítačů. Tento limit můžete změnit nastavením zvýšení hodnoty klíče MaxVMsSupported v souboru ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Plánování požadavků na cílový (Azure) a kapacity

Pomocí získaného odhady a doporučení, můžete naplánovat pro cíl prostředků a kapacity. Pokud jste spustili Plánovač nasazení pro virtuální počítače VMware, můžete použít několik [sestavy doporučení](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) umožňují.

- **Kompatibilní virtuální počítače**: Toto číslo slouží k identifikaci počet virtuálních počítačů, které jsou připravené pro zotavení po havárii do Azure. Doporučení týkající se šířky pásma sítě a počet jader Azure jsou založené na toto číslo.
- **Požadované šířky pásma sítě**: Mějte na paměti, které potřebujete pro rozdílovou replikaci kompatibilní virtuální počítače šířku pásma. 
    - Spuštění plánovače určíte požadovaný cíl bodu obnovení v minutách. Doporučení zobrazit šířku pásma potřebnou ke splnění tohoto cíle bodu obnovení 100 % až 90 % času. 
    - Doporučení šířky pásma sítě vezměte v úvahu šířku pásma potřebnou pro celkový počet konfiguračních serverů a procesových serverů, doporučujeme v Planneru.
- **Požadovaný počet jader Azure**: Poznámka: počet jader, které budete potřebovat v cílové oblasti Azure podle počet kompatibilních virtuálních počítačů. Pokud nemáte k dispozici dostatek jader, při převzetí služeb při selhání Site Recovery nebude možné vytvořit požadované virtuální počítače Azure.
- **Doporučená velikost dávky virtuálních počítačů**: Doporučená velikost dávky je podle schopnost dokončení počáteční replikace pro dávku během 72 hodin ve výchozím nastavení, při splnění RPO 100 %. Můžete změnit hodnotu hodiny.

Tato doporučení můžete použít k plánování pro prostředky Azure, šířky pásma sítě a dávek virtuálních počítačů.

## <a name="plan-azure-subscriptions-and-quotas"></a>Plánování kvót a předplatných Azure

Chceme, abyste měli jistotu, že jsou k dispozici kvót v cílovém předplatném dostatečné pro zpracování převzetí služeb při selhání.

**Úloha** | **Podrobnosti** | **Akce**
--- | --- | ---
**Zkontrolujte počet jader** | Pokud v dostupnou kvótu jader není rovná nebo přesahuje počet celkový cíl v okamžiku převzetí služeb při selhání, převzetí služeb při selhání se nezdaří. | Pro virtuální počítače VMware zkontrolujte, že máte dostatek jader v cílovém předplatném podle doporučení plánovače nasazení služby jádra.<br/><br/> Pro fyzické servery zkontrolujte, že splňují jader Azure ruční odhady.<br/><br/> Chcete-li zkontrolovat kvóty na webu Azure Portal > **předplatné**, klikněte na tlačítko **využití a kvóty**.<br/><br/> [Další informace](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) o zvýšení kvóty.
**Kontrola omezení převzetí služeb při selhání** | Počet převzetí služeb při selhání nesmí překročit limit pro převzetí služeb při selhání Site Recovery. |  Pokud převzetí služeb při selhání překročí limity, můžete přidat předplatná a převzetí služeb při selhání s více předplatnými nebo zvýšit kvótu pro předplatné. 


### <a name="failover-limits"></a>Převzetí služeb při selhání omezení

Omezení určit počet převzetí služeb při selhání, které jsou podporovány službou Site Recovery do jedné hodiny, za předpokladu, že tři disky na počítač.

Co znamená dodržuje? Spustit virtuální počítač Azure, Azure vyžaduje některé ovladače, spouštěcí počáteční stav a služby jako DHCP nastavená na automatické spouštění.
- Počítače, které jsou v souladu se už tato nastavení na místě.
- Pro počítače se systémem Windows můžete aktivně zkontrolovat dodržování předpisů a díky kterým vyhovují v případě potřeby. [Další informace](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Počítače s Linuxem, přesměrují se pouze do stavu kompatibility v okamžiku převzetí služeb při selhání.

**Počítač splňuje Azure?** | **Omezení virtuálního počítače Azure (převzetí spravovaného disku)**
--- | --- 
Ano | 2000
Ne | 1000

- Omezení se předpokládá to minimální že jiné úlohy probíhají v cílové oblasti pro předplatné.
- Několik oblastí Azure jsou menší a může mít trochu nižší limity.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Plánování infrastruktury a připojení virtuálních počítačů

Po převzetí služeb při selhání do Azure budete potřebovat pracovní vytížení fungovat stejně jako místní a umožňuje uživatelům přístup k úlohy běžící na virtuálních počítačích Azure.

- [Další informace](site-recovery-active-directory.md#test-failover-considerations) o převzetí služeb při selhání místní infrastrukturu služby Active Directory nebo DNS do Azure.
- [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) o přípravě k připojení k virtuálním počítačům Azure po převzetí služeb při selhání.



## <a name="plan-for-source-capacity-and-requirements"></a>Plánování kapacity zdroje a požadavky

Je důležité, abyste měli dostatečná konfiguračních serverů a horizontální navýšení kapacity procesových serverů pro splnění požadavků na kapacitu. Jak můžete začít nasazením ve velkém měřítku, začněte jediný konfigurační server a jeden horizontální navýšení kapacity procesového serveru. Jak dosáhnete předepsanou mezní hodnoty, přidáte další servery.

>[!NOTE]
> Plánovač nasazení pro virtuální počítače VMware, díky několik doporučení, informace o konfiguraci a proces servery, které potřebujete. Doporučujeme vám, že používáte tabulky obsažené v následujících postupech, místo podle doporučení plánovače nasazení služby. 


## <a name="set-up-a-configuration-server"></a>Nastavení konfigurace serveru
 
Konfigurace kapacity serveru je ovlivněna počet počítačů, které se replikují a nikoli podle dat četnost změn dat. Chcete-li zjistit, jestli nepotřebujete další konfigurační servery, použijte tyto definice omezení virtuálního počítače.

**CPU** | **Paměť** | **Disk mezipaměti** | **Replikované počítače limit**
 --- | --- | --- | ---
8 virtuálních CPU<br> sokety 2 * 4 jádra @ 2,5 Ghz | 16 GB | 600 TB | Až 550 počítačů<br> Předpokládá, že každý počítač má tři disky 100 GB.

- Tato omezení se zakládají na konfiguračním serveru nastavit pomocí šablony OVF.
- Omezení se předpokládá, že nepoužíváte procesový server, na kterém běží ve výchozím nastavení na konfiguračním serveru.

Pokud je potřeba přidat nové konfigurační server, postupujte podle těchto pokynů:

- [Nastavení konfigurace serveru](vmware-azure-deploy-configuration-server.md) pro zotavení po havárii virtuálního počítače VMware pomocí šablony OVF.
- [Nastavení konfigurace serveru](physical-azure-set-up-source.md) ručně pro fyzické servery, nebo pro nasazení VMware, které nelze pomocí šablony OVF.

Jak nastavit konfigurační server, Všimněte si, že:

- Když nastavíte konfigurační server, je důležité vzít v úvahu dané předplatné a trezoru, ve kterém se nachází, protože ty by neměla změnit po dokončení instalace. Pokud potřebujete změnit trezor, budete muset zrušit přidružení konfiguračního serveru z trezoru a znovu ho zaregistrujte. Tím se zastaví replikace virtuálních počítačů v trezoru.
- Pokud chcete nastavit konfigurační server s více síťovými adaptéry, byste měli udělat během. Nelze provést po registraci konfiguračního serveru v trezoru.

## <a name="set-up-a-process-server"></a>Nastavit procesový server

Proces kapacity serveru má vliv sazby za četnost změn dat a ne počet počítačů povolena replikace.

- Pro velká nasazení byste měli mít vždy alespoň jeden horizontální navýšení kapacity procesového serveru.
- Chcete-li zjistit, jestli nepotřebujete další servery, použijte následující tabulku.
- Doporučujeme vám, že přidáte server s nejvyšší specifikace. 


**CPU** | **Paměť** | **Disk mezipaměti** | **Četnost změn dat**
 --- | --- | --- | --- 
12 virtuálních procesorů<br> sokety 2 * 6 jader @ 2,5 Ghz | 24 GB | 1 GB | Až 2 TB za den

Procesový server nastavte následujícím způsobem:

1. Zkontrolujte [požadavky](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Instalace serveru v [portál](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), nebo [příkazového řádku](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Nakonfigurujte replikované počítače k použití nového serveru. Pokud už máte počítače, které se replikují:
    - Je možné [přesunout](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) zatížení serveru celý proces pro nový procesový server.
    - Alternativně můžete [přesunout](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) konkrétní virtuální počítače pro nový procesový server.



## <a name="enable-large-scale-replication"></a>Povolení replikace ve velkém měřítku

Po plánování kapacity a nasazení požadované součásti a infrastrukturu, povolte replikaci pro velký počet virtuálních počítačů.

1. Řazení počítače do dávek. Povolení replikace pro virtuální počítače v rámci dávky a potom přejít k další dávku.

    - Pro virtuální počítače VMware, můžete použít [doporučená velikost dávky virtuálních počítačů](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) sestavy plánovače nasazení služby.
    - U fyzických počítačů doporučujeme, abyste že identifikovat dávky založené na počítačích, které mají podobné velikosti a množství dat a dostupné propustnosti sítě. Cílem je stroje služby batch, které jsou pravděpodobné včasné dokončení prvotní replikace během kolem stejné množství času.
    
2. Pokud četnost změn disku pro počítač je vysoká nebo překračují omezení v thePlanner nasazení, můžete přesunout nekritické soubory, které nepotřebujete k replikaci (například výpisy protokolu nebo soubory šablon) vypnout počítač. Pro virtuální počítače VMware můžete přesunout tyto soubory na samostatnou diskovou a potom [vyloučit disk](vmware-azure-exclude-disk.md) z replikace.
3. Než povolíte replikaci, zkontrolujte, jestli počítače, které splňují [požadavky na replikaci](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Konfigurace zásady replikace pro [virtuálních počítačů VMware](vmware-azure-set-up-replication.md#create-a-policy) nebo [fyzických serverů](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Povolení replikace pro [virtuálních počítačů VMware](vmware-azure-enable-replication.md) nebo [fyzických serverů](physical-azure-disaster-recovery.md#enable-replication). To zahajuje počáteční replikace pro vybrané počítače.

## <a name="monitor-your-deployment"></a>Monitorování nasazení

Jakmile zahájíte replikaci pro první dávku virtuálních počítačů, začněte monitorovat nasazení následujícím způsobem:  

1. Přiřadíte jako správce pro zotavení po havárii pro sledování stavu kopie replikovaných počítačů.
2. [Sledování událostí](site-recovery-monitor-and-troubleshoot.md) pro replikované položky a infrastrukturu.
3. [Monitorování stavu](vmware-physical-azure-monitor-process-server.md) z vaší horizontální navýšení kapacity procesových serverů.
4. Přihlášení k odběru [e-mailová oznámení](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) pro události, pro snazší monitorování.
5. Chování pravidelně [zotavení po havárii](site-recovery-test-failover-to-azure.md), aby bylo zajištěno, že vše funguje podle očekávání.


## <a name="plan-for-large-scale-failovers"></a>Plán pro převzetí služeb při selhání ve velkém měřítku

V případě katastrofy můžete potřebovat převzetí služeb při selhání velký počet počítačů a úloh do Azure. Příprava pro tento typ události následujícím způsobem.

Můžete připravit předem převzetí služeb při selhání následujícím způsobem:

- [Příprava infrastruktury a virtuálních počítačů](#plan-infrastructure-and-vm-connectivity) tak, aby vaše úlohy bude k dispozici po převzetí služeb při selhání a tak, aby uživatelé měli přístup k virtuálním počítačům Azure.
- Poznámka: [omezení převzetí služeb při selhání](#failover-limits) výše v tomto dokumentu. Zajistěte, aby že vaše převzetí služeb při selhání bude spadat do těchto omezení.
- Spustit pravidelně [zotavení po havárii](site-recovery-test-failover-to-azure.md). Cvičení pomoct:
    - Najdete mezery v nasazení před převzetí služeb při selhání.
    - Odhad RTO začátku do konce pro vaše aplikace.
    - Odhad začátku do konce cíle bodu obnovení pro vaše úlohy.
    - Určete rozsah adres IP je v konfliktu.
    - Při spouštění cvičení, doporučujeme, abyste není použít produkční sítě pro cvičení, nepoužívejte stejné názvy podsítí v produkci a testování sítě a vyčistit testovací převzetí služeb při selhání za každý přechod.

Ke spuštění ve velkém měřítku převzetí služeb při selhání, doporučujeme následující postup:

1. Vytvářejte plány obnovení pro převzetí služeb při selhání úloh.
    - Každý plán obnovení můžete spustit převzetí služeb při selhání až na 50 počítačů.
    - [Další informace](recovery-plan-overview.md) plány obnovení.
2. Přidáte skripty runbooku Azure Automation do plánů obnovení, automatizovat všechny ručně prováděné úlohy v Azure. Mezi typické úlohy patří konfigurace nástroje pro vyrovnávání zatížení, aktualizace DNS atd. [Další informace](site-recovery-runbook-automation.md)
2. Před převzetí služeb při selhání připravte počítače Windows tak, že jsou v souladu s prostředím Azure. [Převzetí služeb při selhání limity](#plan-azure-subscriptions-and-quotas) vyšší pro počítače, které jsou v souladu. [Další informace](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) o sadách runbook.
4.  Aktivovat převzetí služeb při selhání [Start AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) rutiny Powershellu, společně se plán obnovení.



## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Monitorování Site Recovery](site-recovery-monitor-and-troubleshoot.md)
