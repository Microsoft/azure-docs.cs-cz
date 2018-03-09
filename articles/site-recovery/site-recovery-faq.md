---
title: "Azure Site Recovery: Časté otázky | Microsoft Docs"
description: "Tento článek popisuje oblíbených otázky o Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 8c6f577560442f28204b633d5f45f6d4c46ea4b8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: Časté otázky (FAQ)
Tento článek obsahuje nejčastější dotazy týkající se Azure Site Recovery. Pokud po přečtení tohoto článku máte dotazy, odešlete je na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Obecné
### <a name="what-does-site-recovery-do"></a>K čemu Site Recovery slouží?
Site Recovery přispívá k kontinuity podnikových procesů a po havárii (BCDR) strategie, tím, že orchestruje a automatizuje replikaci virtuálních počítačů Azure mezi oblastí, lokálních virtuálních počítačů a fyzických serverů do Azure a místních počítačů do sekundárního datacentra. [Další informace](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Co můžete chránit pomocí Site Recovery?
* **Virtuální počítače Azure**: Site Recovery dokáže replikovat jakoukoli úlohu spuštěnou na podporovaném virtuálním počítači Azure
* **Virtuální počítače Hyper-V**: jakoukoli úlohu spuštěnou na virtuálním počítači technologie Hyper-V můžete chránit pomocí Site Recovery.
* **Fyzické servery**: fyzické servery se systémem Windows nebo Linux můžete chránit pomocí Site Recovery.
* **Virtuální počítače VMware**: jakoukoli úlohu spuštěnou ve virtuálním počítači VMware můžete chránit pomocí Site Recovery.



### <a name="can-i-replicate-azure-vms"></a>Můžete replikovat virtuální počítače Azure?
Ano, můžete provádět replikaci mezi oblastmi Azure podporovaných virtuálních počítačích Azure. [Další informace](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co potřebuji v Hyper-V pro orchestraci replikace pomocí Site Recovery?
U hostitelských serverů Hyper-V se vaše potřeby odvíjí od scénáře nasazení. Požadavky pro Hyper-V si můžete projít v těchto tématech:

* [Replikace virtuálních počítačů technologie Hyper-V (bez VMM) do Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikace virtuálních počítačů technologie Hyper-V (s nástrojem VMM) do Azure](site-recovery-vmm-to-azure.md)
* [Replikace virtuálních počítačů technologie Hyper-V do sekundárního datacentra](site-recovery-vmm-to-vmm.md)
* Pokud replikujete do sekundárního datacentra, přečtěte si informace o [podporované hostované operační systémy pro virtuální počítače Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Pokud replikujete do Azure, Site Recovery podporuje všechny hostované operační systémy, které jsou [nepodporuje v Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Může chránit virtuální počítače, když běží technologie Hyper-V na klientský operační systém?
Ne, virtuální počítače se musí nacházet na hostitelském serveru Hyper-V, který běží na podporovaném serveru s Windows. Pokud potřebujete ochránit klientský počítač, můžete jej replikovat jako fyzický počítač do [Azure](site-recovery-vmware-to-azure.md) nebo [sekundárního datacentra](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jaké úlohy mohu ochránit pomocí Site Recovery?
Site Recovery můžete použít k ochraně většinu úloh běžících na podporovaném virtuálním počítači nebo fyzickém serveru. Site Recovery poskytuje podporu pro aplikace s cílem replikace, tak, aby je bylo možné obnovit do použitelného stavu. Se integruje s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a služby Active Directory a úzce spolupracuje s hlavními dodavateli včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Mají hostitelů Hyper-V v cloudech VMM?
Pokud chcete replikovat do sekundárního datacentra, musí být virtuální počítače Hyper-V na Hyper-V hostuje servery umístěné v cloudu VMM. Pokud chcete replikovat do Azure, můžete s nebo bez cloudech VMM replikovat virtuální počítače. [Další informace](tutorial-hyper-v-to-azure.md) o replikaci technologie Hyper-V do Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Mohu nasadit Site Recovery s VMM, když mám jen jeden server VMM?

Ano. Můžete buď replikovat virtuální počítače na serverech Hyper-V v cloudu VMM do Azure, nebo můžete replikovat mezi cloudy VMM na stejném serveru. Pro místní na místní replikaci doporučujeme mít VMM server v primárních a sekundárních lokalit.  

### <a name="what-physical-servers-can-i-protect"></a>Jaké fyzické servery mohu ochránit?
Můžete replikovat fyzické servery se systémem Windows a Linux, do Azure nebo do sekundární lokality. [Další informace o](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) požadavky na operační systém.  Stejné požadavky platí zda fyzické servery replikujete do Azure nebo do sekundární lokality.


Všimněte si, že fyzické servery se spustí jako virtuální počítače v Azure případě výpadku na místním serveru. Navrácení služeb po obnovení na místní fyzický server není aktuálně podporován. Pro chráněný jako fyzický počítač můžete pouze navrácení služeb po obnovení k virtuálnímu počítači VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Jaké virtuální počítače VMware mohu ochránit?

Pokud chcete ochránit virtuální počítače VMware, potřebujete hypervisor vSphere a virtuální počítače, ve kterých běží nástroje VMware. Pro správu hypervisorů také doporučujeme mít server VMware vCenter. [Další informace](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) o přesných požadavcích na replikaci serverů VMware a virtuálních počítačů do Azure nebo do sekundární lokality.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Mohu pomocí Site Recovery spravovat zotavení po havárii pro pobočky?
Ano. Pokud používáte Site Recovery k orchestraci replikace a převzetí služeb při selhání ve firemních pobočkách, získáte jednotná orchestration a zobrazit všechny úlohy poboček v centrálním umístění. Z centrály můžete snadno provádět převzetí služeb při selhání a spravovat zotavení po havárii na všech pobočkách, aniž byste na nich museli být přítomni.

## <a name="pricing"></a>Ceny
Ceny dotazy související s, naleznete v části Nejčastější dotazy na [cenách Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Zabezpečení
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nemá nezachycuje replikovaná data a nemá žádné informace o co běží na virtuálních počítačích nebo fyzických serverů.
Replikační data se vyměňují mezi lokálními hostiteli Hyper-V, hypervisory VMware nebo fyzickými servery a úložištěm Azure nebo sekundární lokalitou. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikaci a probíhá vyhodnocování SOC2 a FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Kvůli dodržování předpisů i naše místní metadata musí zůstat ve stejné geografické oblasti. Site Recovery, budeme moct snadněji?
Ano. Když vytvoříte trezor Site Recovery v oblasti, je zajištěno, že všechna metadata, která potřebujeme k umožnění a orchestraci replikace a převzetí služeb při selhání zůstávají uvnitř této oblasti je geografické hranice.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Pro virtuální počítače a fyzické servery je podporováno replikaci mezi místními lokalitami šifrování během přenosu. Pro virtuální počítače a fyzické servery replikující se do Azure, jak šifrování během přenosu a [šifrování na rest (v Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Mohu replikovat přes síť site-to-site VPN do Azure?
Azure Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod. Replikace není přes síť site-to-site VPN. Síť site-to-site VPN, můžete vytvořit pomocí virtuální sítě Azure. To není v konfliktu se Site Recovery replikace.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Lze použít k replikaci virtuálních počítačů do Azure ExpressRoute?
Ano, ExpressRoute slouží k replikaci virtuálních počítačů do Azure. Azure Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod. Budete muset nastavit [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) chcete použít ExpressRoute pro replikaci Site Recovery. Po virtuální počítače mají byla při selhání pro virtuální síť Azure se dostanete pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) nastavení s virtuální síť Azure.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Vztahují se na replikaci virtuálních počítačů do Azure nějaké požadavky?
Virtuální počítače, které chcete replikovat do Azure, by měly splňovat [požadavky pro Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

Azure uživatelský účet musí mít určité [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) k povolení replikace nového virtuálního počítače do Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Mohu do Azure replikovat virtuální počítače Hyper-V generace 2?
Ano. Site Recovery převede z 2. generace 1. generace během převzetí služeb při selhání. Na navrácení služeb po obnovení je na počítač převede zpět na generaci 2. [Přečtěte si další informace](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Pokud replikuji do Azure, jak platím za virtuální počítače Azure?
Během běžné replikace se data replikují do geograficky redundantního úložiště Azure a vy nemusíte platit žádné poplatky za virtuální počítače Azure IaaS poskytuje významné výhody. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS. Následně se vám budou fakturovat výpočetní prostředky, které v Azure spotřebujete.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Můžete automatizovat scénáře obnovení lokality pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře pro nasazení Site Recovery pomocí prostředí PowerShell:

* [Replikace virtuálních počítačů technologie Hyper-V v cloudech VMMs pro správce prostředků Azure PowerShell](hyper-v-vmm-powershell-resource-manager.md)
* [Replikace virtuálních počítačů technologie Hyper-V bez VMM pro správce prostředků Azure PowerShell](hyper-v-azure-powershell-resource-manager.md)
* [Replikace VMware do Azure pomocí Správce prostředků PowerShell](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Pokud replikuji do Azure, jaký typ účtu úložiště potřebuji?
Budete potřebovat účet úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Účet musí být ve stejné oblasti jako trezor Služeb zotavení. Storage úrovně Premium je podporována pro virtuální počítač VMware, virtuální počítač Hyper-V a replikaci fyzický server, při nasazování Site Recovery na portálu Azure.

### <a name="how-often-can-i-replicate-data"></a>Jak často je možné replikovat data?
* **Technologie Hyper-V:** virtuálních počítačů Hyper-V je možné replikovat každých 30 sekund (s výjimkou storage úrovně premium), 5 minut nebo 15 minut. Pokud jste nastavili replikaci sítě SAN je replikace synchronní.
* **VMware a fyzické servery:** Četnost replikací zde není relevantní. Replikace je souvislý.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Můžete rozšířit replikaci z existující lokality pro obnovení do jiné lokality terciární?
Rozšířená nebo zřetězená replikace není podporována. Tato funkce v žádosti o [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Mohu při první replikaci do Azure provést offline replikaci?
Toto není podporováno. Tato funkce v žádosti [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Mohu z replikace vyloučit konkrétní disky?
Toto je podporováno, když replikujete virtuální počítače VMware a virtuálních počítačů Hyper-V do Azure pomocí portálu Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Můžete replikovat virtuální počítače s dynamickými disky?
Dynamické disky jsou podporovány, pokud se provádí replikace virtuálních počítačů Hyper-V. Podporovány jsou i při replikaci virtuálních počítačů VMware a fyzické počítače do Azure. Disk operačního systému musí být základní disk.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Můžete přidat nového počítače do existující skupiny replikace?
Přidání nových počítačů do existující skupiny replikace je podporováno. To pokud chcete udělat, vyberte replikační skupiny (v okně "Replikované položky") a klikněte pravým tlačítkem nebo vyberte kontextové nabídky pro replikační skupinu a vyberte příslušnou možnost.

![Přidat do replikační skupiny](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Je možné omezovat šířku pásma vyhrazenou pro přenosy replikace Hyper-V?
Ano. Další informace o omezování šířky pásma v článcích nasazení:

* [Plánování kapacity pro replikaci virtuálních počítačů VMware a fyzické servery](site-recovery-plan-capacity-vmware.md)
* [Plánování kapacity pro replikaci virtuálních počítačů Hyper-V do Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Převzetí služeb při selhání
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Pokud se se selhání do Azure, jak lze získat přístup virtuální počítače Azure po převzetí služeb při selhání?
K virtuálním počítačům Azure můžete přistoupit přes zabezpečené internetové připojení, síť site-to-site VPN nebo přes Azure ExpressRoute. Budete muset připravit existuje řada věcí, aby bylo možné připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Pokud selhání do Azure, jak Azure Ujistěte se, je odolný svá data?
Služba Azure je pro odolnost navržena. Site Recovery je vytvořena pro převzetí služeb při selhání do sekundárního datacentra Azure, v souladu s smlouva Azure SLA již v případě potřeby. Pokud k tomu dojde, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Pokud replikuji mezi dvěma datovými centry co se stane, když dojde k nečekanému výpadku mé primární datové centrum?
Ze sekundární lokality můžete aktivovat neplánované převzetí služeb při selhání. Site Recovery k provedení převzetí služeb při selhání nepotřebuje připojení z primární lokality.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
Převzetí služeb při selhání není automatické. Zahajte převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít [PowerShell obnovení lokality](/powershell/module/azurerm.siterecovery) k aktivaci převzetí služeb při selhání. Navrácení služeb po obnovení je jednoduchý akce na portálu Site Recovery.

Můžete automatizovat může pomocí místního Orchestratoru nebo Operations Manageru detekovat selhání virtuálního počítače a potom aktivovat převzetí služeb při selhání pomocí sady SDK.

* [Další informace](site-recovery-create-recovery-plans.md) o plány obnovení.
* [Další informace](site-recovery-failover.md) o převzetí služeb při selhání.
* [Další informace](site-recovery-failback-azure-to-vmware.md) o selhání zálohovat virtuální počítače VMware a fyzické servery

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Pokud není Moje místní hostitel neodpovídá nebo zhroucené, se dá převzetí služeb při selhání zpátky do jiného hostitele?
Ano, můžete obnovení do náhradního umístění navrácení služeb po obnovení do jiného hostitele z Azure. Další informace o možnostech v následující odkazy pro virtuální počítače VMware a Hyper-V.

* [U virtuálních počítačů VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Pro virtuální počítače Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="service-providers"></a>Poskytovatelé služeb
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jsem poskytovatel služeb. Funguje Site Recovery pro vyhrazené tak sdílené modely infrastruktury?
Ano, Site Recovery podporuje jak vyhrazené, tak sdílené modely infrastruktury.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Pro poskytovatele služeb je identita klienta sdílet s služby Site Recovery?
Ne. Zůstane anonymní identity klienta. Vaši klienti nepotřebují přístup k portálu Site Recovery. Pouze správce poskytovatele služeb pracuje s portálem.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Data aplikací klienta někdy přejde do Azure?
Při replikaci mezi lokalitami ve vlastnictví poskytovatele služeb se data aplikací do Azure nikdy nepřenášejí. Data se šifrují během přenosu a replikují se přímo mezi lokalitami poskytovatele služeb.

Pokud replikujete do Azure, data aplikací se posílají do úložiště Azure, ale nikoli do služby Site Recovery. Data jsou šifrovaná na cestě a v Azure zůstávají zašifrovaná.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Obdrží mí klienti fakturu za služby Azure?
Ne. Azure má fakturační vztah přímo s poskytovatelem služeb. Za generování konkrétních faktur pro své klienty má plnou odpovědnost poskytovatel služeb.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Pokud replikuji do Azure, potřebujeme mít virtuální počítače v Azure spuštěné nepřetržitě?
Ne, Data se replikují do účtu úložiště Azure v rámci vašeho předplatného. Když provedete testovací převzetí služeb při selhání (rutina pro zotavení po havárii) nebo skutečné převzetí, Site Recovery ve vašem předplatném automaticky vytvoří virtuální počítače.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Zajišťujete při replikaci do Azure izolaci na úrovni klienta?
Ano.

### <a name="what-platforms-do-you-currently-support"></a>Jaké platformy aktuálně podporujete?
Podporujeme sady Azure Cloud Platform System a System Center na základě nasazení (2012 a vyšší). [Další informace](https://technet.microsoft.com/library/dn850370.aspx) informace o integraci sady Azure a Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Podporujete nasazení s jediným Azure Packem a jediným serverem VMM?
Ano, můžete replikovat virtuální počítače Hyper-V do Azure nebo mezi lokalitami poskytovatele služeb.  Všimněte si, že pokud budete replikovat mezi lokalitami poskytovatele služeb, integrace runbooku Azure není k dispozici.

## <a name="next-steps"></a>Další postup
* Projděte si [přehled Site Recovery](site-recovery-overview.md).
* Seznamte se s [architekturou Site Recovery](site-recovery-components.md).  
