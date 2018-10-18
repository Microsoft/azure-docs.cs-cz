---
title: 'Azure Site Recovery: Časté otázky | Dokumentace Microsoftu'
description: Tento článek popisuje Oblíbené otázky o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 71d96d7e88404c1f8b617370ef5cc841aad882f8
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390036"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: Časté otázky (FAQ)
Tento článek obsahuje nejčastější dotazy týkající se Azure Site Recovery. Pokud po přečtení tohoto článku máte dotazy, zveřejněte na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Obecné
### <a name="what-does-site-recovery-do"></a>K čemu Site Recovery slouží?
Site Recovery přispívá ke kontinuitu a strategii zotavení (BCDR) po havárii tím, že orchestruje a automatizuje replikaci virtuálních počítačů Azure mezi oblastmi, místních virtuálních počítačů a fyzických serverů do Azure a místních počítačů do sekundární datové centrum. [Další informace](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Co můžete chránit pomocí Site Recovery?
* **Virtuální počítače Azure**: Site Recovery dokáže replikovat jakoukoli úlohu spuštěnou na podporovaném virtuálním počítači Azure
* **Virtuální počítače Hyper-V**: Site Recovery může chránit jakoukoli úlohu spuštěnou na virtuálním počítači Hyper-V.
* **Fyzické servery**: Site Recovery může chránit fyzické servery s Windows nebo Linuxem.
* **Virtuální počítače VMware**: Site Recovery může chránit jakoukoli úlohu spuštěnou na virtuálním počítači VMware.



### <a name="can-i-replicate-azure-vms"></a>Můžete replikovat virtuální počítače Azure?
Ano, můžete replikovat podporovaných virtuálních počítačů Azure mezi oblastmi Azure. [Další informace](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co potřebuji v Hyper-V můžete orchestrovat replikaci pomocí Site Recovery?
U hostitelských serverů Hyper-V se vaše potřeby odvíjí od scénáře nasazení. Požadavky pro Hyper-V si můžete projít v těchto tématech:

* [Replikace virtuálních počítačů Hyper-V (bez VMM) do Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikace virtuálních počítačů Hyper-V (s VMM) do Azure](site-recovery-vmm-to-azure.md)
* [Replikace virtuálních počítačů Hyper-V do sekundárního datového centra](site-recovery-vmm-to-vmm.md)
* Pokud provádíte replikaci do sekundárního datacentra, přečtěte si informace o [podporovaných hostovaných operačních systémech pro virtuální počítače Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Pokud replikujete do Azure, Site Recovery podporuje všechny hostované operační systémy, které jsou [nepodporuje v Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Můžete chránit virtuální počítače, pokud Hyper-V běží na operačním systému klienta?
Ne, virtuální počítače se musí nacházet na hostitelském serveru Hyper-V, který běží na podporovaném serveru s Windows. Pokud potřebujete ochránit klientský počítač, můžete jej replikovat jako fyzický počítač na [Azure](site-recovery-vmware-to-azure.md) nebo [sekundárního datacentra](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jaké úlohy mohu ochránit pomocí Site Recovery?
Site Recovery můžete použít k ochraně většinu úloh, které běží na podporovaném virtuálním počítači nebo fyzickém serveru. Site Recovery poskytuje podporu pro replikaci se sledováním aplikací, takže aplikace je možné obnovit do použitelného stavu. Integruje se s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a Active Directory a úzce spolupracuje s předními dodavateli včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Potřeba hostitele Hyper-V v cloudech VMM?
Pokud chcete replikovat do sekundárního datacentra, virtuální počítače Hyper-V musí být na Hyper-V hostuje servery umístěné v cloudu VMM. Pokud chcete replikovat do Azure, můžete s nebo bez něj cloudech VMM replikovat virtuální počítače. [Přečtěte si další](tutorial-hyper-v-to-azure.md) o replikaci technologie Hyper-V do Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Mohu nasadit Site Recovery s VMM, když mám jen jeden server VMM?

Ano. Můžete buď replikovat virtuální počítače na serverech Hyper-V v cloudu VMM do Azure nebo se dají replikovat mezi cloudy VMM na stejném serveru. Pro místní na místní replikaci doporučujeme mít VMM server v primárních a sekundárních lokalit.  

### <a name="what-physical-servers-can-i-protect"></a>Jaké fyzické servery mohu ochránit?
Můžete replikovat fyzické servery se systémem Windows a Linuxem do Azure nebo do sekundární lokality. Další informace o požadavcích pro [replikaci do Azure](vmware-physical-azure-support-matrix.md#replicated-machines), a [replikace do sekundární lokality](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


Všimněte si, že fyzické servery se spustí jako virtuální počítače v Azure, pokud váš místní server přestane fungovat. Navrácení služeb po obnovení fyzického serveru na místě není aktuálně podporováno. Zapnout ochranu, protože fyzický počítač můžete pouze navrácení služeb po obnovení pro virtuální počítač VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Jaké virtuální počítače VMware mohu ochránit?

Pokud chcete ochránit virtuální počítače VMware, potřebujete hypervisor vSphere a virtuální počítače, ve kterých běží nástroje VMware. Pro správu hypervisorů také doporučujeme mít server VMware vCenter. Další informace o požadavcích pro [replikaci do Azure](vmware-physical-azure-support-matrix.md#replicated-machines), nebo [replikace do sekundární lokality](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Mohu pomocí Site Recovery spravovat zotavení po havárii pro pobočky?
Ano. Když použijete Site Recovery pro orchestraci replikace a převzetí služeb při selhání ve firemních pobočkách, získáte jednotný Orchestrace a zobrazit všechny úlohy poboček v centrálním umístění. Z centrály můžete snadno provádět převzetí služeb při selhání a spravovat zotavení po havárii na všech pobočkách, aniž byste na nich museli být přítomni.

## <a name="pricing"></a>Ceny
Ceny za dotazy související s hesly, najdete v části Nejčastější dotazy na [ceny za Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

## <a name="security"></a>Zabezpečení
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nezachycuje replikovaná data se nepodporuje a nemá žádné informace o co běží na virtuálních počítačích nebo fyzických serverů.
Replikační data se vyměňují mezi lokálními hostiteli Hyper-V, hypervisory VMware nebo fyzickými servery a úložištěm Azure nebo sekundární lokalitou. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikace a probíhá SOC2 a FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Z důvodu dodržování předpisů i naše místní metadata musí zůstat ve stejné geografické oblasti. Site Recovery nám můžete pomoct?
Ano. Když v oblasti vytvoříte trezor Site Recovery, zajišťujeme, že všechna metadata, která potřebujeme k umožnění a orchestraci replikace a převzetí služeb při selhání zůstávají uvnitř této oblasti je geografické hranice.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Pro virtuální počítače a fyzické servery se podporuje replikaci mezi místní lokality šifrování během přenosu. Pro virtuální počítače a fyzické servery replikované do Azure, jak šifrování během přenosu a [šifrování neaktivních (v Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Můžete replikovat přes síť site-to-site VPN k Azure?
Azure Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod. Replikace není přes síť site-to-site VPN. Site-to-site VPN, můžete vytvořit pomocí služby Azure virtual network. To není v konfliktu se replikace Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Můžete použít ExpressRoute pro replikaci virtuálních počítačů do Azure?
Ano, [ExpressRoute je možné](concepts-expressroute-with-site-recovery.md) pro replikaci místních virtuálních počítačů do Azure. Azure Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod. Budete muset nastavit [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) nebo [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) pro účely replikace Site Recovery ExpressRoute. Partnerský vztah Microsoftu je doporučené směrování domény pro replikaci. Po virtuální počítače mají převzalo služby Azure virtual network k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) instalační program s Azure virtual network. Replikace není podporována přes privátní partnerský vztah.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Vztahují se na replikaci virtuálních počítačů do Azure nějaké požadavky?
[Virtuální počítače VMware](vmware-physical-azure-support-matrix.md#replicated-machines) a [virtuálních počítačů Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) chcete replikovat do Azure by měly splňovat požadavky na Azure.

Váš uživatelský účet Azure je potřeba některé [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) k replikaci nového virtuálního počítače do Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Mohu do Azure replikovat virtuální počítače Hyper-V generace 2?
Ano. Site Recovery převede z generace 2 na generaci 1 během převzetí služeb při selhání. Při navrácení služeb po obnovení se počítač převede zpět na generaci 2. [Další informace](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Pokud replikuji do Azure, jak platím za virtuální počítače Azure?
Během běžné replikace se data replikují na geograficky redundantní úložiště Azure a nemusíte platit žádné poplatky za virtuální počítače Azure IaaS poskytuje výraznou výhodu. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS. Následně se vám budou fakturovat výpočetní prostředky, které v Azure spotřebujete.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Můžete automatizovat scénáře obnovení lokality pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře nasazení Site Recovery pomocí Powershellu:

* [Replikace virtuálních počítačů Hyper-V v cloudech VMMs k Resource Manageru prostředí Azure PowerShell](hyper-v-vmm-powershell-resource-manager.md)
* [Replikace virtuálních počítačů Hyper-V bez VMM do Azure PowerShell Resource Manageru](hyper-v-azure-powershell-resource-manager.md)
* [Replikace VMware do Azure pomocí prostředí PowerShell Resource Manageru](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Pokud replikuji do Azure, jaký typ účtu úložiště potřebuji?
Budete potřebovat účet úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Účet musí být ve stejné oblasti jako trezor Služeb zotavení. Premium storage podporuje pro virtuální počítač VMware, virtuálních počítačů Hyper-V a replikaci fyzických serverů, pokud provádíte nasazení Site Recovery na webu Azure Portal.

### <a name="how-often-can-i-replicate-data"></a>Jak často je možné replikovat data?
* **Technologie Hyper-V:** virtuálních počítačů Hyper-V je možné replikovat každých 30 sekund (s výjimkou storage úrovně premium), 5 minut nebo 15 minut. Pokud jste nastavili replikaci sítě SAN je replikace synchronní.
* **VMware a fyzické servery:** Četnost replikací zde není relevantní. Replikace je souvislý.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Je možné rozšířit replikaci z existující lokality pro obnovení do jiné lokality terciární?
Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci v [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Mohu při první replikaci do Azure provést offline replikaci?
Toto není podporováno. Žádost o tuto funkci [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Mohu z replikace vyloučit konkrétní disky?
To je podporováno při replikaci virtuálních počítačů Hyper-V a virtuálních počítačů VMware do Azure pomocí webu Azure portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Můžete replikovat virtuální počítače s dynamickými disky?
Dynamické disky jsou podporovány, pokud se provádí replikace virtuálních počítačů Hyper-V. Jsou také podporovány při replikaci virtuálních počítačů VMware a fyzických počítačů do Azure. Disk s operačním systémem musí být základní disk.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Můžete přidat nového počítače do existující skupiny replikace?
Přidání nového počítače do existující skupiny replikace se nepodporuje. Uděláte to tak, vyberte replikační skupiny (v okně "Replikované položky") a klikněte pravým tlačítkem myši a vyberte kontextovou nabídku replikační skupinu a vyberte příslušnou možnost.

![Přidat do replikační skupiny](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Je možné omezovat šířku pásma vyhrazenou pro přenosy replikace Hyper-V?
Ano. Další informace o omezování šířky pásma v článcích nasazení:

* [Plánování kapacity pro replikaci virtuálních počítačů VMware a fyzických serverů](site-recovery-plan-capacity-vmware.md)
* [Plánování kapacity pro replikaci virtuálních počítačů Hyper-V do Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Převzetí služeb při selhání
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Pokud mám teď převzetí služeb při selhání do Azure, jak získám přístup do virtuálních počítačů Azure po převzetí služeb při selhání?
K virtuálním počítačům Azure můžete přistoupit přes zabezpečené internetové připojení, síť site-to-site VPN nebo přes Azure ExpressRoute. Budete muset připravit řada věcí, abyste se mohli připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Pokud mám převzetí služeb při selhání do Azure, jak Azure Ujistěte se, že je odolné Moje data?
Služba Azure je pro odolnost navržena. Site Recovery je navržen pro převzetí služeb při selhání do sekundárního datacentra Azure, v souladu s smlouvy SLA pro Azure už v případě potřeby. Pokud se to stane, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Pokud replikuji mezi dvěma datacentry co se stane, když můj primární datové centrum, dojde k nečekanému výpadku?
Ze sekundární lokality můžete aktivovat neplánované převzetí služeb při selhání. Site Recovery k provedení převzetí služeb při selhání nepotřebuje připojení z primární lokality.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
Převzetí služeb při selhání není automatické. Zahájení převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít [Powershellu pro Site Recovery](/powershell/module/azurerm.siterecovery) k aktivaci převzetí služeb při selhání. Navrácení služeb po obnovení je jednoduché akce na portálu Site Recovery.

Můžete automatizovat můžou pomocí místního Orchestratoru nebo Operations Manageru detekovat selhání virtuálního počítače a potom aktivovat převzetí služeb při selhání pomocí sady SDK.

* [Přečtěte si další](site-recovery-create-recovery-plans.md) plány obnovení.
* [Přečtěte si další](site-recovery-failover.md) o převzetí služeb při selhání.
* [Přečtěte si další](site-recovery-failback-azure-to-vmware.md) o selhání zálohování virtuálních počítačů VMware a fyzické servery

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Pokud Můj místního hostitele není neodpovídá nebo zhroucené, je možné převzetí služeb při selhání zpátky na jiného hostitele?
Obnovení do alternativního umístění Ano, můžete použít k navrácení služeb po obnovení do jiného hostitele z Azure. Další informace o možnostech v následujících odkazech pro virtuální počítače VMware a Hyper-V.

* [U virtuálních počítačů VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Pro virtuální počítače Hyper-V](hyper-v-azure-failback.md#perform-failback)

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
Ne, Data se replikují do účtu služby Azure storage ve vašem předplatném. Když provedete testovací převzetí služeb při selhání (rutina pro zotavení po havárii) nebo skutečné převzetí, Site Recovery ve vašem předplatném automaticky vytvoří virtuální počítače.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Zajišťujete při replikaci do Azure izolaci na úrovni klienta?
Ano.

### <a name="what-platforms-do-you-currently-support"></a>Jaké platformy aktuálně podporujete?
Podporujeme sady Azure Pack, Cloud Platform System a System Center na základě nasazení (2012 a vyšší). [Další informace](https://technet.microsoft.com/library/dn850370.aspx) o integraci Azure packu a Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Podporujete nasazení s jediným Azure Packem a jediným serverem VMM?
Ano, můžete replikovat virtuální počítače Hyper-V do Azure nebo mezi lokalitami poskytovatele služeb.  Všimněte si, že při replikaci mezi lokalitami poskytovatele služeb, integrace runbooku Azure není k dispozici.

## <a name="next-steps"></a>Další postup
* Projděte si [přehled Site Recovery](site-recovery-overview.md).
* Seznamte se s [architekturou Site Recovery](site-recovery-components.md).  
