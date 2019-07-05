---
title: 'Azure Site Recovery: Nejčastější dotazy | Dokumentace Microsoftu'
description: Tento článek popisuje Oblíbené otázky o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: a9c7aa2be945e4fbaa65bdd2a145d576422c5539
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491757"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: Časté otázky (FAQ)
Tento článek shrnuje nejčastější dotazy týkající se Azure Site Recovery.</br>
Konkrétní dotazy na různých ASR scénáře najdete v tématu věnovaném scénář konkrétní nejčastější dotazy.<br>

- [Zotavení po havárii virtuálního počítače Azure do Azure](azure-to-azure-common-questions.md)
- [Zotavení po havárii virtuálních počítačů VMware do Azure](vmware-azure-common-questions.md)
- [Zotavení po havárii virtuálních počítačů Hyper-V do Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Obecné

### <a name="what-does-site-recovery-do"></a>K čemu Site Recovery slouží?
Site Recovery přispívá ke kontinuitu a strategii zotavení (BCDR) po havárii tím, že orchestruje a automatizuje replikaci virtuálních počítačů Azure mezi oblastmi, místních virtuálních počítačů a fyzických serverů do Azure a místních počítačů do sekundární datové centrum. [Další informace](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Můžete chránit virtuální počítač s diskem Docker?

Ne, jedná se o nepodporovaný scénář.

## <a name="service-providers"></a>Poskytovatelé služeb

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jsem poskytovatel služeb. Funguje Site Recovery pro vyhrazené tak sdílené modely infrastruktury?
Ano, Site Recovery podporuje jak vyhrazené, tak sdílené modely infrastruktury.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Pro poskytovatele služeb je identita klienta sdílené se službou Site Recovery?
Ne. Zůstane anonymní identity tenanta. Vaši klienti nepotřebují přístup k portálu Site Recovery. Pouze správce poskytovatele služeb pracuje s portálem.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Data aplikací tenanta někdy přejde do Azure?
Při replikaci mezi lokalitami ve vlastnictví poskytovatele služeb se data aplikací do Azure nikdy nepřenášejí. Data se šifrují během přenosu a replikují se přímo mezi lokalitami poskytovatele služeb.

Pokud replikujete do Azure, data aplikací se posílají do úložiště Azure, ale nikoli do služby Site Recovery. Je šifrování přenášených dat a v Azure zůstávají zašifrovaná.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Obdrží mí klienti fakturu za služby Azure?
Ne. Azure má fakturační vztah přímo s poskytovatelem služeb. Za generování konkrétních faktur pro své klienty má plnou odpovědnost poskytovatel služeb.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Pokud replikuji do Azure, potřebujeme mít virtuální počítače v Azure spuštěné nepřetržitě?
Ne, Data se replikují do úložiště Azure v rámci vašeho předplatného. Když provedete testovací převzetí služeb při selhání (rutina pro zotavení po havárii) nebo skutečné převzetí, Site Recovery ve vašem předplatném automaticky vytvoří virtuální počítače.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Zajišťujete při replikaci do Azure izolaci na úrovni klienta?
Ano.

### <a name="what-platforms-do-you-currently-support"></a>Jaké platformy aktuálně podporujete?
Podporujeme sady Azure Pack, Cloud Platform System a System Center na základě nasazení (2012 a vyšší). [Další informace](https://technet.microsoft.com/library/dn850370.aspx) o integraci Azure packu a Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Podporujete nasazení s jediným Azure Packem a jediným serverem VMM?
Ano, můžete replikovat virtuální počítače Hyper-V do Azure nebo mezi lokalitami poskytovatele služeb.  Všimněte si, že při replikaci mezi lokalitami poskytovatele služeb, integrace runbooku Azure není k dispozici.

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-pricing-information"></a>Kde najdu informace o cenách?
Kontrola [cenách za Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) podrobnosti.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak můžete vypočítat přibližné náklady během používání služby Site Recovery?

Můžete použít [cenové kalkulačky](https://aka.ms/asr_pricing_calculator) odhadnout náklady při používání Site Recovery.

Podrobný odhad nákladů na, spusťte nástroj Plánovač nasazení pro [VMware](https://aka.ms/siterecovery_deployment_planner) nebo [Hyper-V](https://aka.ms/asr-deployment-planner)a použít [sestavy odhadu nákladů](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Spravované disky jsou teď používanou k replikaci virtuálních počítačů VMware a fyzických serverů. Účtovat další poplatky za účet úložiště mezipaměti se spravovanými disky?

Ne, neúčtují žádné další poplatky pro mezipaměť. Při replikaci do účtu úložiště úrovně standard, toto úložiště mezipaměti je součástí stejný cílový účet úložiště.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Používám Azure Site Recovery už víc než měsíc. Získám i tak prvních 31 dní pro každou chráněnou instanci zadarmo?

Ano. U chráněných instancí se neúčtují žádné poplatky za Azure Site Recovery za prvních 31 dní. Například pokud budete mít 10 instancí třeba posledních 6 měsíců a připojíte k Azure Site Recovery 11. instanci, nejsou 11. instanci žádné poplatky za prvních 31 dní. Za prvních 10 instancí se dál budou účtovat poplatky za Azure Site Recovery, protože jste chráněné déle než 31 dní.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Budu se mi během prvních 31 dní účtovat nějaké další poplatky Azure?

Ano, i když je Site Recovery během prvních 31 dní chráněné instance zadarmo, můžou se vám poplatky za Azure Storage, transakce služby storage a přenosy dat. U obnoveného virtuálního počítače se můžou účtovat taky poplatky za výpočty Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Platí se za přidružené k převzetí služeb cvičení a testování zotavení po havárii?

Neexistuje žádné zvláštní náklady na zotavení. Bude poplatky za výpočty po vytvoření virtuálního počítače po převzetí služeb při selhání testu.



## <a name="security"></a>Zabezpečení

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nezachycuje replikovaná data se nepodporuje a nemá žádné informace o co běží na virtuálních počítačích nebo fyzických serverů.
Replikační data se vyměňují mezi lokálními hostiteli Hyper-V, hypervisory VMware nebo fyzickými servery a úložištěm Azure nebo sekundární lokalitou. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikace a probíhá SOC2 a FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Z důvodu dodržování předpisů i naše místní metadata musí zůstat ve stejné geografické oblasti. Site Recovery nám můžete pomoct?
Ano. Když v oblasti vytvoříte trezor Site Recovery, zajišťujeme, že všechna metadata, která potřebujeme k umožnění a orchestraci replikace a převzetí služeb při selhání zůstávají uvnitř této oblasti je geografické hranice.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Pro virtuální počítače a fyzické servery se podporuje replikaci mezi místní lokality šifrování během přenosu. Pro virtuální počítače a fyzické servery replikované do Azure, jak šifrování během přenosu a [šifrování neaktivních (v Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.




## <a name="disaster-recovery"></a>Zotavení po havárii

### <a name="what-can-site-recovery-protect"></a>Co můžete chránit pomocí Site Recovery?
* **Virtuální počítače Azure**: Site Recovery dokáže replikovat jakoukoli úlohu spuštěnou na podporovaném virtuálním počítači Azure
* **Virtuální počítače Hyper-V**: Site Recovery může chránit jakoukoli úlohu spuštěnou na virtuálním počítači Hyper-V.
* **Fyzické servery**: Fyzické servery s Windows nebo Linuxem můžete chránit pomocí Site Recovery.
* **Virtuální počítače VMware**: Jakoukoli úlohu spuštěnou na virtuálním počítači VMware můžete chránit pomocí Site Recovery.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jaké úlohy mohu ochránit pomocí Site Recovery?
Site Recovery můžete použít k ochraně většinu úloh, které běží na podporovaném virtuálním počítači nebo fyzickém serveru. Site Recovery poskytuje podporu pro replikaci se sledováním aplikací, takže aplikace je možné obnovit do použitelného stavu. Integruje se s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a Active Directory a úzce spolupracuje s předními dodavateli včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Mohu pomocí Site Recovery spravovat zotavení po havárii pro pobočky?
Ano. Když použijete Site Recovery pro orchestraci replikace a převzetí služeb při selhání ve firemních pobočkách, získáte jednotný Orchestrace a zobrazit všechny úlohy poboček v centrálním umístění. Z centrály můžete snadno provádět převzetí služeb při selhání a spravovat zotavení po havárii na všech pobočkách, aniž byste na nich museli být přítomni.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Je podporováno zotavení po havárii pro virtuální počítače Azure?

Ano, Site Recovery podporuje po havárii pro virtuální počítače Azure mezi oblastmi Azure. [Projděte si nejčastější dotazy](azure-to-azure-common-questions.md) o zotavení po havárii virtuálního počítače Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Je podporováno zotavení po havárii pro virtuální počítače VMware?

Ano, Site Recovery podporuje zotavení po havárii místních virtuálních počítačů VMware. [Projděte si nejčastější dotazy](vmware-azure-common-questions.md) pro zotavení po havárii virtuálních počítačů VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Je pro virtuální počítače Hyper-V nepodporuje zotavení po havárii?
Ano, Site Recovery podporuje zotavení po havárii místních virtuálních počítačů Hyper-V. [Projděte si nejčastější dotazy](hyper-v-azure-common-questions.md) pro zotavení po havárii virtuálních počítačů Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Je podporováno zotavení po havárii pro fyzické servery?
Ano, Site Recovery podporuje zotavení po havárii místních fyzických serverů s Windows a Linuxem do Azure nebo do sekundární lokality. Další informace o požadavcích pro zotavení po havárii do [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)a získat[sekundární lokality](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Všimněte si, že fyzické servery se spustí jako virtuální počítače v Azure po převzetí služeb při selhání. Navrácení služeb po obnovení z Azure na fyzickém serveru v místním se momentálně nepodporuje. Můžete předat jenom zpět do virtuálního počítače VMware.





## <a name="replication"></a>Replikace

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Můžete replikovat přes síť site-to-site VPN k Azure?
Azure Site Recovery replikuje data do účtu služby Azure storage nebo spravované disky, přes veřejný koncový bod. Replikace není přes síť site-to-site VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Proč se můžu replikovat přes síť VPN?

Pokud replikujete do Azure, provoz replikace dosáhne veřejné koncové body služby Azure Storage. Proto vám můžou replikovat jenom přes veřejný internet s ExpressRoute (partnerský vztah Microsoftu nebo existující veřejný partnerský vztah) a sítě VPN nebude fungovat.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Můžete použít Riverbed SteelHeads pro replikaci?

Náš partner, Riverbed, poskytuje podrobné informace o práci s Azure Site Recovery. Zkontrolujte jejich [Průvodce řešením](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Můžete použít ExpressRoute pro replikaci virtuálních počítačů do Azure?
Ano, [ExpressRoute je možné](concepts-expressroute-with-site-recovery.md) pro replikaci místních virtuálních počítačů do Azure.

- Azure Site Recovery replikuje data do úložiště Azure přes veřejný koncový bod. Budete muset nastavit [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) nebo použijte existujícího [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#publicpeering) (zastaralé pro nové okruhy) pro účely replikace Site Recovery ExpressRoute.
- Partnerský vztah Microsoftu je doporučené směrování domény pro replikaci.
- Replikace není podporována přes privátní partnerský vztah.
- Pokud chráníte počítače VMware nebo fyzických počítačích a ověřte, že [síťové požadavky](vmware-azure-configuration-server-requirements.md#network-requirements) pro konfigurační Server jsou splněny. Konfigurační Server vyžaduje připojení ke konkrétní adresy URL pro orchestraci replikace Site Recovery. ExpressRoute nelze použít pro toto připojení.
- Po virtuální počítače mají převzalo služby Azure virtual network k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering) instalační program s Azure virtual network.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Pokud replikuji do Azure, jaký typ účtu úložiště nebo spravovaný disk musím mít?

Budete potřebovat úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Účet musí být ve stejné oblasti jako trezor Služeb zotavení. Premium storage podporuje pro virtuální počítač VMware, virtuálních počítačů Hyper-V a replikaci fyzických serverů, pokud provádíte nasazení Site Recovery na webu Azure Portal. Spravované disky podporují pouze LRS.

### <a name="how-often-can-i-replicate-data"></a>Jak často je možné replikovat data?
* **Hyper-V:** Virtuální počítače Hyper-V je možné replikovat každých pět minut, o 30 sekund (s výjimkou storage úrovně premium)
* **Virtuální počítače, virtuální počítače VMware a fyzické servery služby Azure:** Četnost replikací zde není relevantní. Replikace je souvislý.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Je možné rozšířit replikaci z existující lokality pro obnovení do jiné lokality terciární?
Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci v [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Mohu při první replikaci do Azure provést offline replikaci?
Toto není podporováno. Žádost o tuto funkci [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Mohu z replikace vyloučit konkrétní disky?
To je podporováno při replikaci virtuálních počítačů Hyper-V a virtuálních počítačů VMware do Azure pomocí webu Azure portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Můžete replikovat virtuální počítače s dynamickými disky?
Dynamické disky se nepodporuje při replikaci virtuálních počítačů Hyper-V a při replikaci virtuálních počítačů VMware a fyzických počítačů do Azure. Disk s operačním systémem musí být základní disk.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Je možné omezovat šířku pásma vyhrazenou pro přenosy replikace?
Ano. Další informace o omezování šířky pásma v těchto článcích:

* [Plánování kapacity pro replikaci virtuálních počítačů VMware a fyzických serverů](site-recovery-plan-capacity-vmware.md)
* [Plánování kapacity pro replikaci virtuálních počítačů Hyper-V do Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Převzetí služeb při selhání
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Pokud mám teď převzetí služeb při selhání do Azure, jak získám přístup k virtuálním počítačům Azure po převzetí služeb při selhání?

K virtuálním počítačům Azure můžete přistoupit přes zabezpečené internetové připojení, síť site-to-site VPN nebo přes Azure ExpressRoute. Je nutné připravit řada věcí, abyste se mohli připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Pokud mám převzetí služeb při selhání do Azure, jak Azure Ujistěte se, že je odolné Moje data?
Služba Azure je pro odolnost navržena. Site Recovery je už navržené pro převzetí služeb při selhání do sekundárního datacentra Azure, v souladu s smlouvy SLA pro Azure. Pokud se to stane, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Pokud replikuji mezi dvěma datacentry co se stane, když můj primární datové centrum, dojde k nečekanému výpadku?
Ze sekundární lokality můžete aktivovat neplánované převzetí služeb při selhání. Site Recovery k provedení převzetí služeb při selhání nepotřebuje připojení z primární lokality.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
Převzetí služeb při selhání není automatické. Zahájení převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít [Powershellu pro Site Recovery](/powershell/module/az.recoveryservices) k aktivaci převzetí služeb při selhání. Navrácení služeb po obnovení je jednoduché akce na portálu Site Recovery.

Můžete automatizovat můžou pomocí místního Orchestratoru nebo Operations Manageru detekovat selhání virtuálního počítače a potom aktivovat převzetí služeb při selhání pomocí sady SDK.

* [Přečtěte si další](site-recovery-create-recovery-plans.md) plány obnovení.
* [Přečtěte si další](site-recovery-failover.md) o převzetí služeb při selhání.
* [Přečtěte si další](site-recovery-failback-azure-to-vmware.md) o selhání zálohování virtuálních počítačů VMware a fyzické servery

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Pokud Můj místní hostitel nereaguje nebo došlo k chybě, můžu navrátit služby zpět do jiného hostitele?
Obnovení do alternativního umístění Ano, můžete použít k navrácení služeb po obnovení do jiného hostitele z Azure.

* [U virtuálních počítačů VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Pro virtuální počítače Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Můžete automatizovat scénáře obnovení lokality pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře nasazení Site Recovery pomocí Powershellu:

* [Replikace virtuálních počítačů Hyper-V v cloudech VMMs k Resource Manageru prostředí Azure PowerShell](hyper-v-vmm-powershell-resource-manager.md)
* [Replikace virtuálních počítačů Hyper-V bez VMM do Azure PowerShell Resource Manageru](hyper-v-azure-powershell-resource-manager.md)
* [Replikace VMware do Azure pomocí prostředí PowerShell Resource Manageru](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Upgrade komponenty/poskytovatele

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Kde najdu kumulativní poznámky nebo aktualizovat verzi upgradů Site Recovery

[Přečtěte si](site-recovery-whats-new.md) o nové aktualizace, a [získat informace o kumulativní](service-updates-how-to.md).

## <a name="next-steps"></a>Další postup
* Projděte si [přehled Site Recovery](site-recovery-overview.md).

