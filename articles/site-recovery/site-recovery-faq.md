---
title: Obecné otázky týkající se služby Azure Site Recovery
description: Tento článek popisuje obecné obecné otázky týkající se Azure Site Recovery.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257677"
---
# <a name="general-questions-about-azure-site-recovery"></a>Obecné otázky týkající se azure site recovery

Tento článek shrnuje nejčastější dotazy týkající se Azure Site Recovery. Pro konkrétní scénáře zkontrolujte tyto články

- [Otázky týkající se zotavení po havárii virtuálního počítače Azure do Azure](azure-to-azure-common-questions.md)
- [Otázky týkající se obnovení zabezpečení virtuálního počítače VMware do Azure](vmware-azure-common-questions.md)
- [Otázky týkající se zotavení po havárii virtuálních počítačů Hyper-V v do Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Obecné

### <a name="what-does-site-recovery-do"></a>K čemu Site Recovery slouží?
Site Recovery přispívá k vaší strategii kontinuity podnikání a zotavení po havárii (BCDR) tím, že organizuje a automatizuje replikaci virtuálních počítačů Azure mezi oblastmi, místními virtuálními počítači a fyzickými servery do Azure a místních počítačů do sekundárního datového centra. [Další informace](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Můžu chránit virtuální počítač, který má disk Dockeru?

Ne, toto je nepodporovaný scénář.

## <a name="service-providers"></a>Poskytovatelé služeb

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jsem poskytovatel služeb. Funguje site recovery pro vyhrazené a sdílené modely infrastruktury?
Ano, Site Recovery podporuje jak vyhrazené, tak sdílené modely infrastruktury.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Je identita mého tenanta sdílena se službou Site Recovery pro poskytovatele služeb?
Ne. Identita klienta zůstává anonymní. Vaši klienti nepotřebují přístup k portálu Site Recovery. Pouze správce poskytovatele služeb pracuje s portálem.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Budou data aplikací klienta někdy přecházet do Azure?
Při replikaci mezi lokalitami ve vlastnictví poskytovatele služeb se data aplikací do Azure nikdy nepřenášejí. Data jsou šifrována při přenosu a replikována přímo mezi weby poskytovatelů služeb.

Pokud replikujete do Azure, data aplikací se posílají do úložiště Azure, ale nikoli do služby Site Recovery. Data se zašifrují při přenosu a zůstanou šifrovaná v Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Obdrží mí klienti fakturu za služby Azure?
Ne. Azure má fakturační vztah přímo s poskytovatelem služeb. Za generování konkrétních faktur pro své klienty má plnou odpovědnost poskytovatel služeb.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Pokud replikuji do Azure, potřebujeme mít virtuální počítače v Azure spuštěné nepřetržitě?
Ne, data se replikují do úložiště Azure ve vašem předplatném. Když provedete testovací převzetí služeb při selhání (rutina pro zotavení po havárii) nebo skutečné převzetí, Site Recovery ve vašem předplatném automaticky vytvoří virtuální počítače.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Zajišťujete při replikaci do Azure izolaci na úrovni klienta?
Ano.

### <a name="what-platforms-do-you-currently-support"></a>Jaké platformy aktuálně podporujete?
Podporujeme nasazení Azure Pack, Cloud Platform System a System Center (2012 a vyšší). [Přečtěte si další informace](https://technet.microsoft.com/library/dn850370.aspx) o integraci Azure Pack a Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Podporujete nasazení s jediným Azure Packem a jediným serverem VMM?
Ano, virtuální počítače Hyper-V můžete replikovat do Azure nebo mezi weby poskytovatelů služeb.  Všimněte si, že pokud replikujete mezi weby poskytovatelů služeb, integrace sady Runbook Azure není k dispozici.

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-pricing-information"></a>Kde najdu informace o cenách?
Zkontrolujte podrobnosti o [cenách site recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak mohu vypočítat přibližné poplatky během používání site recovery?

Cenovou [kalkulačku](https://aka.ms/asr_pricing_calculator) můžete použít k odhadu nákladů při použití site recovery.

Podrobný odhad nákladů spusťte nástroj plánovače nasazení pro [technologie VMware](https://aka.ms/siterecovery_deployment_planner) nebo [Hyper-V](https://aka.ms/asr-deployment-planner)a použijte [sestavu odhadu nákladů](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Spravované disky se teď používají k replikaci virtuálních počítačů VMware a fyzických serverů. Účtují se mi další poplatky za účet úložiště mezipaměti se spravovanými disky?

Ne, za mezipaměť nejsou účtovány žádné další poplatky. Při replikaci do standardního účtu úložiště je toto úložiště mezipaměti součástí stejného cílového účtu úložiště.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Byl jsem uživatelem Azure Site Recovery více než měsíc. Mám stále získat prvních 31 dní zdarma pro každou chráněnou instanci?

Ano. Každá chráněná instance účtuje žádné poplatky azure site recovery za prvních 31 dní. Například pokud jste byli ochranu 10 instancí za posledních 6 měsíců a připojit jedenácté instance Azure Site Recovery, neexistují žádné poplatky za jedenáctou instanci za prvních 31 dnů. Prvních 10 instancí nadále účtuje poplatky azure site recovery, protože byly chráněny více než 31 dní.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Budou mi během prvních 31 dní účtovány další poplatky za Azure?

Ano, i když je služba Site Recovery zdarma během prvních 31 dnů chráněné instance, může vám vzniknout poplatky za Azure Storage, transakce úložiště a přenos dat. Obnovené virtuální počítač může také vynakládat poplatky za výpočetní prostředky Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Jsou spojeny náklady spojené s prováděním cvičení zotavení po havárii/převzetí služeb při selhání testu?

Neexistuje žádné samostatné náklady na cvičení zotavení po havárii. Po vytvoření virtuálního počítače po převzetí služeb při selhání testem se budou poplatky za výpočetní výkon.



## <a name="security"></a>Zabezpečení

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nezachycuje replikovaná data a nemá žádné informace o tom, co běží na virtuálních počítačích nebo fyzických serverech.
Replikační data se vyměňují mezi lokálními hostiteli Hyper-V, hypervisory VMware nebo fyzickými servery a úložištěm Azure nebo sekundární lokalitou. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Obnova webu je ISO 27001:2013, 27018, HIPAA, DPA certified a je v procesu hodnocení SOC2 a FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Z důvodů dodržování předpisů i naše místní metadata musí zůstat ve stejné geografické oblasti. Může nám site recovery pomoci?
Ano. Když vytvoříte trezor site recovery v oblasti, zajistíme, že všechna metadata, která potřebujeme k povolení a orchestraci replikace a převzetí služeb při selhání, zůstanou v rámci geografické hranice této oblasti.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Pro virtuální počítače a fyzické servery je podporována replikace mezi místními weby šifrování mů e-přenosu. Pro virtuální počítače a fyzické servery replikovat do Azure, šifrování v tranzitu a [šifrování v klidovém stavu (v Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Jak můžu vynutit TLS 1.2 ve všech místních součástech Azure Site Recovery?
Agenti mobility nainstalovaní na replikovaných položkách komunikují s process serverem pouze na tls 1.2. Komunikace z konfiguračního serveru do Azure a z procesního serveru do Azure však může být na TLS 1.1 nebo 1.0. Postupujte podle [pokynů](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) k vynucení protokolu TLS 1.2 na všech konfiguračních serverech a procesních serverech, které jste nastavili.


## <a name="disaster-recovery"></a>Zotavení po havárii

### <a name="what-can-site-recovery-protect"></a>Co může site recovery chránit?
* **Virtuální počítače Azure:** Site Recovery můžete replikovat všechny úlohy spuštěné na podporovaném virtuálním počítači Azure
* **Virtuální počítače Hyper-V**: Site Recovery může chránit všechny úlohy spuštěné na virtuálním počítači Hyper-V.
* **Fyzické servery**: Site Recovery může chránit fyzické servery se systémem Windows nebo Linux.
* **Virtuální počítače VMware**: Site Recovery může chránit všechny úlohy spuštěné ve virtuálním počítači VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jaké úlohy mohu ochránit pomocí Site Recovery?
Site Recovery můžete použít k ochraně většiny úloh spuštěných na podporovaném virtuálním počítači nebo fyzickém serveru. Site Recovery poskytuje podporu pro replikaci s podporou aplikací, takže aplikace lze obnovit do inteligentního stavu. Integruje se s aplikacemi společnosti Microsoft, jako jsou SharePoint, Exchange, Dynamics, SQL Server a Active Directory, a úzce spolupracuje s předními dodavateli, včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Mohu pomocí Site Recovery spravovat zotavení po havárii pro pobočky?
Ano. Při použití site recovery orchestrad replikace a převzetí služeb při selhání v pobočkách, získáte jednotnou orchestraci a zobrazení všech úloh pobočkové sítě v centrálním umístění. Z centrály můžete snadno provádět převzetí služeb při selhání a spravovat zotavení po havárii na všech pobočkách, aniž byste na nich museli být přítomni.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Je zotavení po havárii podporované pro virtuální počítače Azure?

Ano, site recovery podporuje havárii pro virtuální počítače Azure mezi oblastmi Azure. [Projděte si běžné otázky týkající](azure-to-azure-common-questions.md) se zotavení po havárii virtuálního počítače Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Je zotavení po havárii podporováno pro virtuální virtuální moduly VMware?

Ano, Site Recovery podporuje zotavení po havárii místních virtuálních počítačích VMware. [Projděte si běžné otázky](vmware-azure-common-questions.md) týkající se zotavení po havárii virtuálních měn VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Je zotavení po havárii podporované pro virtuální aplikace Hyper-V?
Ano, site recovery podporuje zotavení po havárii místních virtuálních počítačích Hyper-V. [Projděte si běžné otázky](hyper-v-azure-common-questions.md) týkající se zotavení po havárii virtuálních vod Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Je pro fyzické servery podporováno zotavení po havárii?
Ano, site recovery podporuje zotavení po havárii místních fyzických serverů se systémem Windows a Linux do Azure nebo do sekundární lokality. Další informace o požadavcích na zotavení po havárii v [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)a na[sekundární lokalitu](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Všimněte si, že fyzické servery se po převzetí služeb při selhání budou v Azure spouštět jako virtuální počítače. Navrácení služeb po službě Zalená služba Z Alezí Azure na místní fyzický server není momentálně podporována. Můžete pouze navrácení služeb po selhání do virtuálního počítače VMware.





## <a name="replication"></a>Replikace

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Můžu se replikovat přes VPN mezi lokalitami do Azure?
Azure Site Recovery replikuje data do účtu úložiště Azure nebo spravovaných disků přes veřejný koncový bod. Replikace není přes síť VPN mezi lokalitami. 

### <a name="why-cant-i-replicate-over-vpn"></a>Proč se nedá replikovat přes VPN?

Když replikujete do Azure, provoz replikace dosáhne veřejných koncových bodů úložiště Azure. Tak můžete replikovat pouze přes veřejný internet nebo přes ExpressRoute (Microsoft peering nebo existující veřejný partnerský vztah).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Mohu použít Riverbed SteelHeads pro replikaci?

Náš partner, Riverbed, poskytuje podrobné pokyny pro práci s Azure Site Recovery. Přečtěte si jejich [řešení průvodce](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Můžu použít ExpressRoute k replikaci virtuálních počítačů do Azure?
Ano, [ExpressRoute se dá použít](concepts-expressroute-with-site-recovery.md) k replikaci místních virtuálních počítačů do Azure.

- Azure Site Recovery replikuje data do úložiště Azure přes veřejný koncový bod. Chcete-li použít [replikaci](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) služby ExpressRoute for Site Recovery, je třeba nastavit partnerský vztah microsoftu nebo použít existující [veřejný partnerský vztah](../expressroute/about-public-peering.md) (zastaralé pro nové okruhy).
- Partnerský vztah společnosti Microsoft je doporučená směrovací doména pro replikaci.
- Replikace není podporována přes soukromý partnerský vztah.
- Pokud chráníte počítače VMware nebo fyzické počítače, ujistěte se, že jsou [splněny](vmware-azure-configuration-server-requirements.md#network-requirements) také síťové požadavky pro konfigurační server. Konfigurační server vyžaduje připojení ke konkrétním adresám URL pro orchestraci replikace obnovení lokality. ExpressRoute nelze použít pro toto připojení.
- Po převzetí virtuálních počítačů převzetím služby při selhání virtuální sítě Azure můžete k nim přistupovat pomocí nastavení [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering) s virtuální sítí Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Pokud se replikuji do Azure, jaký typ účtu úložiště nebo spravovaného disku potřebuji?

Potřebujete úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Účet musí být ve stejné oblasti jako trezor Služeb zotavení. Úložiště Premium je podporované pro virtuální počítač VMware, virtuální počítač Hyper-VA a replikaci fyzického serveru při nasazení site recovery na webu na webu. Spravované disky podporují pouze LRS.

### <a name="how-often-can-i-replicate-data"></a>Jak často je možné replikovat data?
* **Hyper-V:** Virtuální aplikace Hyper-V lze replikovat každých 30 sekund (s výjimkou úložiště premium), pět minut nebo 15 minut.
* **Virtuální počítače Azure, virtuální počítače VMware, fyzické servery:** Replikační frekvence zde není relevantní. Replikace je nepřetržitá.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Lze rozšířit replikaci ze stávající lokality obnovení do jiné terciární lokality?
Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Mohu při první replikaci do Azure provést offline replikaci?
Toto není podporováno. Požádejte o tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Mohu z replikace vyloučit konkrétní disky?
To je podporované, když replikujete virtuální počítače VMware a virtuální počítače Hyper-V do Azure pomocí portálu Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Můžu replikovat virtuální počítače s dynamickými disky?
Dynamické disky jsou podporované při replikaci virtuálních počítačů Hyper-V a při replikaci virtuálních počítačů VMware a fyzických počítačů do Azure. Disk operačního systému musí být běžný.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Lze omezit šířku pásma přidělenou pro replikační provoz?
Ano. Další informace o omezení šířky pásma si můžete přečíst v těchto článcích:

* [Plánování kapacity pro replikaci virtuálních měn VMware a fyzických serverů](site-recovery-plan-capacity-vmware.md)
* [Plánování kapacity pro replikaci virtuálních počítačů Hyper-V do Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Převzetí služeb při selhání
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Pokud přejdu k selhání do Azure, jak se dostanu k virtuálním počítačům Azure po převzetí služeb při selhání?

K virtuálním počítačům Azure můžete přistoupit přes zabezpečené internetové připojení, síť site-to-site VPN nebo přes Azure ExpressRoute. Musíte připravit řadu věcí, abyste se spojili. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Pokud do Azure přejdu na služebnou služeb, jak Azure zajistí, že moje data budou odolná?
Služba Azure je pro odolnost navržena. Site Recovery je již navrženpro převzetí služeb při selhání do sekundárního datového centra Azure, v souladu s Azure SLA. Pokud k tomu dojde, zajistíme, aby vaše metadata a trezory zůstaly ve stejné geografické oblasti, kterou jste pro svůj trezor zvolili.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Pokud se replikuji mezi dvěma datovými centry, co se stane, když v primárním datovém centru dojde k neočekávanému výpadku?
Ze sekundární lokality můžete aktivovat neplánované převzetí služeb při selhání. Site Recovery k provedení převzetí služeb při selhání nepotřebuje připojení z primární lokality.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
Převzetí služeb při selhání není automatické. Převzetí služeb při selhání můžete zahájit jediným kliknutím na portálu nebo můžete použít [prostředí PowerShell pro obnovení webu](/powershell/module/az.recoveryservices) k aktivaci převzetí služeb při selhání. Vrácení služeb při selhání je jednoduchá akce na portálu site recovery.

Chcete-li automatizovat, můžete použít místní Orchestrator nebo Operations Manager ke zjištění selhání virtuálního počítače a potom spustit převzetí služeb při selhání pomocí sady SDK.

* [Přečtěte si další informace](site-recovery-create-recovery-plans.md) o plánech obnovy.
* [Přečtěte si další](site-recovery-failover.md) informace o převzetí služeb při selhání.
* [Další informace](site-recovery-failback-azure-to-vmware.md) o vrácení virtuálních měn VMware a fyzických serverů v mware

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Pokud můj místní hostitel neodpovídá nebo se zhroutil, můžu si vrátit zpět do jiného hostitele?
Ano, můžete použít alternativní obnovení umístění navrácení služeb po obnovení na jiného hostitele z Azure.

* [Pro virtuální počítače VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Pro virtuální počítače Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automatizace

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Je možné automatizovat scénáře obnovení webu pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře pro nasazení obnovení lokality pomocí prostředí PowerShell:

* [Replikace virtuálních počítačů Hyper-V v cloudech v cloudech VMM do Správce prostředků Azure PowerShellu](hyper-v-vmm-powershell-resource-manager.md)
* [Replikace virtuálních počítačů Hyper-V bez vmm do Správce prostředků Prostředí Azure](hyper-v-azure-powershell-resource-manager.md)
* [Replikace vmware do Azure pomocí Správce prostředků Prostředí PowerShell](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Upgrade komponenty/zprostředkovatele

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Kde najdu poznámky k verzi / kumulativní aktualizace upgradů site recovery

[Získejte informace](site-recovery-whats-new.md) o nových aktualizacích a [získejte souhrnné informace](service-updates-how-to.md).

## <a name="next-steps"></a>Další kroky
* Projděte si [přehled Site Recovery](site-recovery-overview.md).

