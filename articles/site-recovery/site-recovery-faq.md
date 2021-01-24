---
title: Obecné otázky ke službě Azure Site Recovery
description: Tento článek popisuje oblíbené obecné otázky týkající se Azure Site Recovery.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: ca30f9ba190dfa3c7e224e47b90be4d3bc5d47ae
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746471"
---
# <a name="general-questions-about-azure-site-recovery"></a>Obecné dotazy ke službě Azure Site Recovery

Tento článek shrnuje Nejčastější dotazy týkající se Azure Site Recovery. V případě konkrétních scénářů si Projděte tyto články

- [Dotazy k zotavení po havárii virtuálního počítače Azure do Azure](azure-to-azure-common-questions.md)
- [Dotazy k zotavení po havárii virtuálního počítače VMware do Azure](vmware-azure-common-questions.md)
- [Dotazy k zotavení po havárii virtuálního počítače Hyper-V do Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Obecné

### <a name="what-does-site-recovery-do"></a>K čemu Site Recovery slouží?

Site Recovery přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR), a to tím, že orchestruje a automatizuje replikaci virtuálních počítačů Azure mezi oblastmi, místními virtuálními počítači a fyzickými servery do Azure a místními počítači do sekundárního datacentra. [Přečtěte si další informace](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Můžu chránit virtuální počítač, který má disk Docker?

Ne, jedná se o nepodporovaný scénář.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Co Site Recovery udělat, aby se zajistila integrita dat?

Existují různé míry, které Site Recovery, aby se zajistila integrita dat. Mezi všemi službami se vytvoří zabezpečené připojení pomocí protokolu HTTPS. Tím se zajistí, že jakákoli malware nebo externí entity nemůžou manipulovat s daty. K použití kontrolních součtů se používá jiná míra. Přenos dat mezi zdrojem a cílem se spouští pomocí výpočetního součtu dat mezi nimi. Tím se zajistí konzistence přenesených dat.

## <a name="service-providers"></a>Poskytovatelé služeb

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jsem poskytovatel služeb. Funguje Site Recovery pro vyhrazené a sdílené modely infrastruktury?
Ano, Site Recovery podporuje jak vyhrazené, tak sdílené modely infrastruktury.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Pro poskytovatele služeb je identitou mého tenanta sdíleného pomocí služby Site Recovery?
Ne. Identita tenanta zůstává anonymní. Vaši klienti nepotřebují přístup k portálu Site Recovery. Pouze správce poskytovatele služeb pracuje s portálem.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Budou data aplikace tenanta někdy přejít do Azure?
Při replikaci mezi lokalitami ve vlastnictví poskytovatele služeb se data aplikací do Azure nikdy nepřenášejí. Data se šifrují během přenosu a replikují se přímo mezi lokalitami poskytovatele služeb.

Pokud replikujete do Azure, data aplikací se posílají do úložiště Azure, ale nikoli do služby Site Recovery. Data se šifrují během přenosu a zůstávají šifrovaná v Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Obdrží mí klienti fakturu za služby Azure?
Ne. Azure má fakturační vztah přímo s poskytovatelem služeb. Za generování konkrétních faktur pro své klienty má plnou odpovědnost poskytovatel služeb.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Pokud replikuji do Azure, potřebujeme mít virtuální počítače v Azure spuštěné nepřetržitě?
Ne, data se replikují do úložiště Azure v rámci vašeho předplatného. Když provedete testovací převzetí služeb při selhání (rutina pro zotavení po havárii) nebo skutečné převzetí, Site Recovery ve vašem předplatném automaticky vytvoří virtuální počítače.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Zajišťujete při replikaci do Azure izolaci na úrovni klienta?
Ano.

### <a name="what-platforms-do-you-currently-support"></a>Jaké platformy aktuálně podporujete?
Podporujeme nasazení pro Azure Pack, Cloud Platform System a System Center (2012 a vyšší). [Přečtěte si další informace](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) o sadě Azure Pack a Site Recovery integraci.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Podporujete nasazení s jediným Azure Packem a jediným serverem VMM?
Ano, virtuální počítače Hyper-V můžete replikovat do Azure nebo mezi lokalitami poskytovatele služeb.  Pamatujte na to, že pokud provádíte replikaci mezi lokalitami poskytovatele služeb, Integrace Azure Runbook není k dispozici.

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-pricing-information"></a>Kde najdu informace o cenách?
Přečtěte si informace o [cenách Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) .


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak mohu vypočítat přibližné poplatky za použití Site Recovery?

Pomocí [cenové kalkulačky](https://aka.ms/asr_pricing_calculator) můžete odhadnout náklady při používání Site Recovery.

Pro detailní odhad nákladů spusťte nástroj Plánovač nasazení pro [VMware](./site-recovery-deployment-planner.md) nebo [Hyper-V](https://aka.ms/asr-deployment-planner)a použijte [sestavu odhad nákladů](./site-recovery-vmware-deployment-planner-cost-estimation.md).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Spravované disky se teď používají k replikaci virtuálních počítačů VMware a fyzických serverů. Účtují se vám další poplatky za účet úložiště mezipaměti se službou Managed disks?

Ne, za mezipaměť se neúčtují žádné další poplatky. Při replikaci do účtu úložiště úrovně Standard je toto úložiště mezipaměti součástí stejného cílového účtu úložiště.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jsem uživatel Azure Site Recovery po dobu delší než měsíc. Je pro každou chráněnou instanci stále k dispozici prvních 31 dní zdarma?

Ano. Každá chráněná instance nevzniká žádné Azure Site Recovery poplatky za prvních 31 dní. Pokud jste například v posledních 6 měsících chránili 10 instancí a připojíte k Azure Site Recovery jedenáctou instanci, pro jedenáctou instanci se za prvních 31 dní neúčtují žádné poplatky. Za prvních 10 instancí se nadále účtují Azure Site Recovery poplatky, protože jsou chráněné po dobu více než 31 dní.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Během prvních 31 dnů se mi účtují nějaké další poplatky za Azure?

Ano, i když je Site Recovery během prvních 31 dní chráněné instance zadarmo, můžou vám být účtovány poplatky za Azure Storage, transakce úložiště a přenos dat. U obnoveného virtuálního počítače můžou být účtovány i poplatky za výpočetní výkon Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Jsou nějaké náklady spojené s prováděním cvičení a testování převzetí služeb při selhání pro zotavení po havárii?

Pro postup zotavení po havárii se neúčtují žádné samostatné náklady. Po vytvoření virtuálního počítače po testovacím převzetí služeb při selhání budou platit poplatky za výpočetní výkon.



## <a name="security"></a>Zabezpečení

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nezachycují replikovaná data a nemá žádné informace o tom, co běží na virtuálních počítačích nebo fyzických serverech.
Replikační data se vyměňují mezi lokálními hostiteli Hyper-V, hypervisory VMware nebo fyzickými servery a úložištěm Azure nebo sekundární lokalitou. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001:2013, 27018, HIPAA, DPA Certified a je v procesu SOC2 a FedRAMP JAB Assessments.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Z důvodu dodržování předpisů musí i naše místní metadata zůstat ve stejné geografické oblasti. Může nám Site Recovery pomáhat?
Ano. Když vytvoříte trezor Site Recovery v oblasti, zajišťujeme, aby všechna metadata, která potřebujeme k povolení a orchestraci replikace a převzetí služeb při selhání, zůstala v rámci geografické hranice této oblasti.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
V případě virtuálních počítačů a fyzických serverů se podporuje replikace mezi místními lokalitami – přenos v provozu. Pro virtuální počítače a fyzické servery, které se replikují do Azure, se podporují jak šifrování, tak i [šifrování v klidovém režimu (v Azure)](../storage/common/storage-service-encryption.md) .

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Používá Azure-to-Azure Site Recovery TLS 1,2 pro veškerou komunikaci napříč mikroslužbami Azure?
Ano, protokol TLS 1,2 se ve výchozím nastavení vynutil pro scénář Azure-to-Azure Site Recovery. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Jak vynutili TLS 1,2 na Azure a fyzické scénáře pro Azure Site Recovery na platformě Azure?
Agenti mobility nainstalované na replikovaných položkách komunikují pouze na procesovém serveru TLS 1,2. Nicméně komunikace z konfiguračního serveru do Azure a z procesového serveru do Azure může být v TLS 1,1 nebo 1,0. Postupujte prosím podle [pokynů](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) , abyste vynutili TLS 1,2 na všech konfiguračních serverech a procesových serverech, které nastavíte sami.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Jak vynutili TLS 1,2 ve scénářích Hyper-to-Azure Site Recovery?
Veškerá komunikace mezi mikroslužbami Azure Site Recovery se stane protokolem TLS 1,2. Site Recovery používá poskytovatele zabezpečení nakonfigurovaná v systému (OS) a používá nejnovější dostupný protokol TLS. Jedna bude muset v registru explicitně povolit TLS 1,2 a pak Site Recovery začít používat protokol TLS 1,2 ke komunikaci se službami. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Jak vynutili omezený přístup na mých účtech úložiště, ke kterým přistupuje služba Site Recovery Service pro čtení a zápis dat replikace?
Můžete přepnout na spravovanou identitu trezoru služby Recovery Services tak, že přejdete na nastavení *identity* . Jakmile se trezor zaregistruje ve službě Azure Active Directory, můžete přejít na účty úložiště a přiřadit následující přiřazení rolí k trezoru:

- Účty úložiště založené na Správce prostředků (standardní typ):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)
  - [Přispěvatel dat v objektech blob služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Účty úložiště založené na Správce prostředků (typ Premium):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor)
  - [Vlastník dat v objektech blob služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Účty klasického úložiště:
  - [Přispěvatel klasických účtů úložiště](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Role služby operátora klíče klasického účtu úložiště](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Zotavení po havárii

### <a name="what-can-site-recovery-protect"></a>Co může Site Recovery chránit?
* **Virtuální počítače Azure**: Site Recovery můžou replikovat jakékoli úlohy spuštěné na PODPOROVANÉm virtuálním počítači Azure.
* **Virtuální počítače s technologií Hyper-v**: Site Recovery můžou chránit jakékoli úlohy běžící na virtuálním počítači s technologií Hyper-v.
* **Fyzické servery**: Site Recovery mohou chránit fyzické servery se systémem Windows nebo Linux.
* **Virtuální počítače VMware**: Site Recovery můžou chránit jakékoli úlohy spuštěné ve virtuálním počítači VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jaké úlohy mohu ochránit pomocí Site Recovery?
Pomocí Site Recovery můžete chránit většinu úloh spuštěných na podporovaném virtuálním počítači nebo fyzickém serveru. Site Recovery poskytuje podporu pro replikaci zohledňující aplikace, aby bylo možné aplikace obnovit do inteligentního stavu. Integruje se s aplikacemi Microsoftu, jako jsou SharePoint, Exchange, Dynamics, SQL Server a Active Directory, a úzce spolupracuje s předními dodavateli, včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Mohu pomocí Site Recovery spravovat zotavení po havárii pro pobočky?
Ano. Když použijete Site Recovery k orchestraci replikace a převzetí služeb při selhání ve firemních pobočkách, získáte jednotnou orchestraci a zobrazení všech úloh vaší pobočky v centrálním umístění. Z centrály můžete snadno provádět převzetí služeb při selhání a spravovat zotavení po havárii na všech pobočkách, aniž byste na nich museli být přítomni.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Podporuje se zotavení po havárii pro virtuální počítače Azure?

Ano, Site Recovery podporuje havárie pro virtuální počítače Azure mezi oblastmi Azure. [Přečtěte si běžné otázky](azure-to-azure-common-questions.md) týkající se zotavení po havárii virtuálních počítačů Azure. Pokud chcete replikovat mezi dvěma oblastmi Azure na stejné kontinentu, využijte Azure do nabídky Azure DR. Není nutné nastavovat konfigurační server/procesový Server a ExpressRoute připojení.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Podporuje se zotavení po havárii pro virtuální počítače VMware?

Ano, Site Recovery podporuje zotavení po havárii místních virtuálních počítačů VMware. [Přečtěte si běžné otázky](vmware-azure-common-questions.md) pro zotavení po havárii virtuálních počítačů VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Podporuje se zotavení po havárii pro virtuální počítače Hyper-V?
Ano, Site Recovery podporuje zotavení po havárii místních virtuálních počítačů Hyper-V. [Přečtěte si běžné otázky](hyper-v-azure-common-questions.md) pro zotavení po havárii virtuálních počítačů Hyper-V.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Podporuje se zotavení po havárii pro fyzické servery?
Ano, Site Recovery podporuje zotavení po havárii místních fyzických serverů se systémem Windows a Linux do Azure nebo do sekundární lokality. Přečtěte si o požadavcích na zotavení po havárii do [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)a na[sekundární lokalitu](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Všimněte si, že po převzetí služeb při selhání budou fyzické servery běžet jako virtuální počítače v Azure. Navrácení služeb po obnovení z Azure na místní fyzický server není v současné době podporováno. Navrácení služeb po obnovení se dá provést jenom na virtuálním počítači VMware.





## <a name="replication"></a>Replikace

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Můžu replikovat přes síť VPN typu Site-to-site do Azure?
Azure Site Recovery replikuje data na účet služby Azure Storage nebo spravované disky prostřednictvím veřejného koncového bodu. Replikace nepřekračuje síť VPN typu Site-to-site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Proč nejde replikovat přes síť VPN?

Při replikaci do Azure dosáhne provoz replikace veřejné koncové body Azure Storage. To znamená, že se dá replikovat jenom přes veřejný Internet nebo přes ExpressRoute (partnerský vztah Microsoftu nebo stávající veřejný partnerský vztah).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Můžu pro replikaci použít Riverbed SteelHeads?

Náš partner, Riverbed, poskytuje podrobné pokyny k práci s Azure Site Recovery. Přečtěte si [Průvodce řešením](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Můžu použít ExpressRoute k replikaci virtuálních počítačů do Azure?
Ano, [ExpressRoute se dá použít](concepts-expressroute-with-site-recovery.md) k replikaci místních virtuálních počítačů do Azure.

- Azure Site Recovery replikuje data do Azure Storage přes Veřejný koncový bod. Pro použití ExpressRoute pro replikaci Site Recovery musíte nastavit [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) nebo použít stávající [veřejný partnerský vztah](../expressroute/about-public-peering.md) (zastaralý pro nové okruhy).
- Partnerský vztah Microsoftu je doporučená doména směrování pro replikaci.
- Replikace není u privátního partnerského vztahu podporována.
- Pokud chráníte počítače VMware nebo fyzické počítače, zajistěte, aby byly splněny také [požadavky na síť](vmware-azure-configuration-server-requirements.md#network-requirements) pro konfigurační server. Konfigurační server vyžaduje k orchestraci replikace Site Recovery připojení ke konkrétním adresám URL. ExpressRoute nelze použít pro toto připojení.
- Po převzetí služeb při selhání virtuálních počítačů do služby Azure Virtual Network můžete k nim přistupovat pomocí nastavení [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering) s virtuální sítí Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Pokud repliku do Azure, jaký druh účtu úložiště nebo spravovaný disk potřebuji?

Potřebujete úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Účet musí být ve stejné oblasti jako trezor Služeb zotavení. Služba Premium Storage je podporovaná pro virtuální počítač VMware, virtuální počítač Hyper-V a replikaci fyzického serveru při nasazení Site Recovery v Azure Portal. Spravované disky podporují jenom LRS.

### <a name="how-often-can-i-replicate-data"></a>Jak často je možné replikovat data?
* **Hyper-V:** Virtuální počítače Hyper-V se dají replikovat každých 30 sekund (s výjimkou prémiového úložiště), 5 minut nebo 15 minut.
* **Virtuální počítače Azure, virtuální počítače VMware, fyzické servery:** Frekvence replikace tady není relevantní. Replikace je nepřetržitá.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Je možné roztáhnout replikaci z existující lokality pro obnovení na jinou terciární lokalitu?
Rozšířená nebo zřetězená replikace není podporována. Vyžádejte si tuto funkci na [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Mohu při první replikaci do Azure provést offline replikaci?
Toto není podporováno. Vyžádejte si tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Mohu z replikace vyloučit konkrétní disky?
To se podporuje při replikaci virtuálních počítačů VMware a virtuálních počítačů Hyper-V do Azure pomocí Azure Portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Můžu replikovat virtuální počítače s dynamickými disky?
Dynamické disky se podporují při replikaci virtuálních počítačů Hyper-V a při replikaci virtuálních počítačů VMware a fyzických počítačů do Azure. Disk s operačním systémem musí být základní disk.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Můžu omezit šířku pásma vyhrazenou pro provoz replikace?
Ano. Další informace o omezování šířky pásma najdete v těchto článcích:

* [Plánování kapacity pro replikaci virtuálních počítačů VMware a fyzických serverů](site-recovery-plan-capacity-vmware.md)
* [Plánování kapacity pro replikaci virtuálních počítačů Hyper-V do Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Můžu povolit replikaci s konzistencí aplikací na serverech se systémem Linux? 
Ano. Azure Site Recovery pro operační systém Linux podporuje vlastní skripty aplikace pro konzistenci aplikací. Vlastní skript s předchozími a post-možnostmi bude používat agent Azure Site Recovery mobility během konzistence aplikací. Níže jsou uvedené kroky, jak je povolit.

1. Přihlaste se jako kořenový adresář do počítače.
2. Změňte adresář na Azure Site Recovery umístění instalace agenta mobility. Výchozí hodnota je "/usr/local/ASR".<br>
    `# cd /usr/local/ASR`
3. V části umístění instalace změňte adresář na VX/Scripts.<br>
    `# cd VX/scripts`
4. Vytvořte skript prostředí bash s názvem "customscript.sh" s oprávněními Execute pro uživatele root user.<br>
    a. Tento skript by měl podporovat "--pre" a "--post" (Všimněte si dvojitých pomlček) možností příkazového řádku.<br>
    b. Při volání skriptu s předvolbou by měl zablokovat vstup/výstup aplikace a při volání s parametrem post-možnost by měl rozmrazit vstup/výstup aplikace.<br>
    c. Ukázková šablona –<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Přidejte vstupně-výstupní příkazy pro zablokování a odblokování v předběžných a následných krocích pro aplikace vyžadující konzistenci aplikací. Můžete zvolit, že se má přidat další skript, který určí, a vyvolá ho z "customscript.sh" s předběžnými a post-možnostmi.

>[!Note]
>Aby bylo možné podporovat vlastní skripty, musí být verze agenta Site Recovery 9,24 nebo vyšší.

## <a name="replication-policy"></a>Zásady replikace

### <a name="what-is-a-replication-policy"></a>Co je zásada replikace?

Zásady replikace definují nastavení pro historii uchovávání bodů obnovení. Zásady také definují četnost snímků konzistentních vzhledem k aplikacím. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozími nastaveními:

- 24 hodin pro historii uchovávání bodů obnovení.
- 4 hodiny pro četnost snímků konzistentních vzhledem k aplikacím.

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k selháním?

Bod obnovení konzistentní s chybou obsahuje data na disku, jako kdyby jste ze serveru během snímku vyžádali napájecí kabel. Bod obnovení konzistentní vzhledem k chybě neobsahuje cokoli, co bylo v paměti při pořízení snímku.

V současné době se většina aplikací může zotavit i z snímků konzistentních vzhledem k chybě. Bod obnovení konzistentní vzhledem k selháním je obvykle dostačující pro operační systémy bez databáze a aplikace jako souborové servery, servery DHCP a tiskové servery.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaká je četnost generování bodu obnovení konzistentního vzhledem k chybě?

Site Recovery vytvoří bod obnovení konzistentní vzhledem k selháním každých 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co je bod obnovení konzistentní vzhledem k aplikacím?

Body obnovení konzistentní vzhledem k aplikacím se vytvářejí z snímků konzistentních vzhledem k aplikacím. Body obnovení konzistentní vzhledem k aplikacím zachycují stejná data jako snímky konzistentní s chybou a zároveň zachytí data v paměti a všechny probíhající transakce.

Vzhledem k tomu, že se jedná o další obsah, jsou nejdůležitější snímky konzistentní vzhledem k aplikacím a trvat nejdéle. Pro databázové operační systémy a aplikace, jako je například SQL Server, doporučujeme body obnovení konzistentní vzhledem k aplikacím.

>[!Note]
>Při vytváření bodů obnovení konzistentních vzhledem k aplikacím dojde v počítači s Windows k chybě, pokud má více než 64 svazků.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaký je dopad bodů obnovení konzistentních vzhledem k aplikacím na výkon aplikace?

Body obnovení konzistentní vzhledem k aplikacím zachytí všechna data v paměti a v procesu. Vzhledem k tomu, že body obnovení zachytí tato data, vyžadují rozhraní, jako služba Stínová kopie svazku ve Windows, aby bylo možné aplikaci neuvést. Pokud je proces zachytávání častý, může to mít vliv na výkon, pokud je zatížení už zaneprázdněné. Nedoporučujeme používat pro úlohy mimo databázi nízkou frekvenci pro body obnovení konzistentní vzhledem k aplikacím. I pro databázová zatížení je k dispozici 1 hodina.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaká je minimální frekvence generování bodu obnovení konzistentního vzhledem k aplikacím?

Site Recovery může vytvořit bod obnovení konzistentní vzhledem k aplikacím s minimální frekvencí 1 hodina.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak se vygenerovaly a ukládají body obnovení?

Pokud chcete pochopit, jak Site Recovery generuje body obnovení, Podívejme se na příklad zásady replikace. Tato zásada replikace má bod obnovení s časovým intervalem pro uchovávání v 24hodinovém formátu a snímkem frekvence konzistentního vzhledem k aplikacím 1 hodina.

Site Recovery vytvoří bod obnovení konzistentní vzhledem k selháním každých 5 minut. Tuto frekvenci nemůžete změnit. Za poslední hodinu můžete vybrat z 12 bodů konzistentních z havárie a 1 bodu konzistentního vzhledem k aplikacím. V průběhu času Site Recovery vyřadí všechny body obnovení za poslední hodinu a uloží pouze 1 bod obnovení za hodinu.

Příklad ukazuje následující snímek obrazovky. Na snímku obrazovky:

- Během poslední hodiny existují body obnovení s frekvencí 5 minut.
- Po uplynutí poslední hodiny zachovává Site Recovery jenom 1 bod obnovení.

   ![Seznam generovaných bodů obnovení](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko zpátky můžu obnovit?

Nejstarší bod obnovení, který můžete použít, je 72 hodin.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mám zásady replikace za 24 hodin. Co se stane, když problém zabrání Site Recovery generování bodů obnovení po dobu delší než 24 hodin? Ztratí se předchozí body obnovení?

Ne, Site Recovery bude uchovávat všechny předchozí body obnovení. V závislosti na okně pro uchování bodů obnovení Site Recovery nahradí nejstarší bod pouze v případě, že generuje nové body. Kvůli problému Site Recovery nemůže vygenerovat žádné nové body obnovení. Dokud nebudou k dispozici nové body obnovení, všechny staré body zůstanou po dosažení okna uchování.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Jak se po replikaci na virtuálním počítači povolí replikace, jak změním zásady replikace?

Přejít na **Site Recovery trezor**  >  **Site Recovery**  >  **Zásady replikace** infrastruktury. Vyberte zásadu, kterou chcete upravit, a uložte změny. Všechny změny se projeví i u všech stávajících replikací.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Má všechny body obnovení úplnou kopii virtuálního počítače nebo rozdílu?

První vygenerovaný bod obnovení má úplnou kopii. Všechny úspěšné body obnovení mají rozdílové změny.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Zvyšuje doba uchování bodů obnovení náklady na úložiště?

Ano, Pokud zvýšíte dobu uchovávání dat z 24 hodin na 72 hodin, Site Recovery bude body obnovení ukládat po dobu dalších 48 hodin. Přidaný čas účtuje poplatky za úložiště. Například jeden bod obnovení může mít rozdílové změny 10 GB s $0,16 za GB za měsíc. Další poplatky by byly $1,60 × 48 za měsíc.


## <a name="failover"></a>Převzetí služeb při selhání
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Pokud převezmem služby při selhání do Azure, jak mám přístup k virtuálním počítačům Azure po převzetí služeb při selhání?

K virtuálním počítačům Azure můžete přistoupit přes zabezpečené internetové připojení, síť site-to-site VPN nebo přes Azure ExpressRoute. Aby bylo možné se připojit, je nutné připravit množství věcí. [Přečtěte si další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Při převzetí služeb při selhání do Azure, jak Azure zajišťuje, aby moje data byla odolná?
Služba Azure je pro odolnost navržena. Site Recovery už je navržená pro převzetí služeb při selhání do sekundárního datacentra Azure v souladu se smlouvou SLA Azure. Pokud k tomu dojde, zajistěte, aby vaše metadata a trezory zůstaly ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Pokud se jedná o replikaci mezi dvěma datacentry, co se stane, když dojde k neočekávanému výpadku v primárním datovém centru?
Ze sekundární lokality můžete aktivovat neplánované převzetí služeb při selhání. Site Recovery k provedení převzetí služeb při selhání nepotřebuje připojení z primární lokality.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
Převzetí služeb při selhání není automatické. Převzetí služeb při selhání můžete zahájit jediným kliknutím na portálu, nebo můžete pomocí [Site Recovery PowerShellu](/powershell/module/az.recoveryservices) aktivovat převzetí služeb při selhání. Navrácení služeb po obnovení je jednoduchá akce na portálu Site Recovery.

K automatizaci můžete použít místní Orchestrator nebo Operations Manager k detekci selhání virtuálního počítače a pak aktivovat převzetí služeb při selhání pomocí sady SDK.

* [Přečtěte si další](site-recovery-create-recovery-plans.md) informace o plánech obnovení.
* [Přečtěte si více](site-recovery-failover.md) o převzetí služeb při selhání.
* [Přečtěte si další](./vmware-azure-failback.md) informace o selhání back-VM virtuálních počítačů a fyzických serverů VMware.

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Pokud můj místní hostitel neodpovídá nebo selhal, můžu se vrátit na jiného hostitele?
Ano, můžete použít obnovení do alternativního umístění pro navrácení služeb po obnovení na jiného hostitele z Azure.

* [Pro virtuální počítače VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Pro virtuální počítače s technologií Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Jaký je rozdíl mezi dokončením migrace, potvrzením a zakázáním replikace?

Po převzetí služeb při selhání počítače ze zdrojového umístění do cílového umístění jsou k dispozici tři možnosti, ze kterých si můžete vybrat. Všechny tři různé účely –

1.  **Dokončení migrace** znamená, že se už nebudete moct zpátky do zdrojového umístění. Migrujete se do cílové oblasti a teď jste hotovi. Kliknutím na Dokončit aktivační události migrace potvrďte a pak interně zakažte replikaci. 
2.  **Potvrzení** znamená, že se nejedná o konec procesu replikace. Položka replikace spolu se všemi konfiguracemi zůstane v platnosti a později můžete znovu zapnout **ochranu** a umožnit tak replikaci vašich počítačů zpátky do zdrojové oblasti. 
3.  **Vypnutím replikace** zakážete replikaci a odeberete všechny související konfigurace. Nebude to mít vliv na už existující počítač v cílové oblasti.

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Můžu Site Recovery scénáře automatizovat pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře nasazení Site Recovery pomocí prostředí PowerShell:

* [Replikace virtuálních počítačů Hyper-V v cloudech VMMs do Azure PowerShell Správce prostředků](hyper-v-vmm-powershell-resource-manager.md)
* [Replikace virtuálních počítačů Hyper-V bez nástroje VMM do Azure PowerShell Správce prostředků](hyper-v-azure-powershell-resource-manager.md)
* [Replikace VMware do Azure s využitím PowerShellu Správce prostředků](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Upgrade součásti nebo zprostředkovatele

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Kde najdu poznámky k verzi a kumulativní aktualizace Site Recovery upgrady

[Přečtěte si](site-recovery-whats-new.md) o nových aktualizacích a [Získejte souhrnné informace](service-updates-how-to.md).

## <a name="next-steps"></a>Další kroky
* Projděte si [přehled Site Recovery](site-recovery-overview.md).