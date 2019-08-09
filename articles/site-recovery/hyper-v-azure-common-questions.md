---
title: Běžné dotazy – zotavení po havárii Hyper-V do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek shrnuje běžné otázky týkající se nastavení zotavení po havárii pro místní virtuální počítače Hyper-V do Azure pomocí Azure Site Recovery lokality.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 08/07/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: a6d38a9196d640ebc823b4f25e089cc04193212b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845750"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Běžné dotazy – zotavení po havárii z Hyper-V do Azure

Tento článek obsahuje odpovědi na běžné otázky, které se zobrazí při replikaci místních virtuálních počítačů Hyper-V do Azure. 

## <a name="general"></a>Obecné

### <a name="how-is-site-recovery-priced"></a>Jak se Site Recovery cena?
Přečtěte si informace o [cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="how-do-i-pay-for-azure-vms"></a>Návody platíte za virtuální počítače Azure?
Během replikace se data replikují do úložiště Azure a neplatíte žádné změny virtuálních počítačů. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure s IaaS. Až se vám budou účtovat výpočetní prostředky, které spotřebujete v Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Je při replikaci do Pro obecné účelyho účtu úložiště v2 nějaký rozdíl v ceně?

Obvykle se vám bude zvyšovat náklady na transakce, které se účtují na účtech úložiště GPv2, protože Azure Site Recovery jsou transakce těžké. [Další informace získáte](../storage/common/storage-account-upgrade.md#pricing-and-billing) k odhadu změny.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co potřebuji v rámci technologie Hyper-V k orchestraci replikace pomocí Site Recovery?

U hostitelských serverů Hyper-V se vaše potřeby odvíjí od scénáře nasazení. Požadavky pro Hyper-V si můžete projít v těchto tématech:

* [Replikace virtuálních počítačů Hyper-V (bez VMM) do Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikace virtuálních počítačů Hyper-V (s VMM) do Azure](site-recovery-vmm-to-azure.md)
* [Replikace virtuálních počítačů Hyper-V do sekundárního datového centra](site-recovery-vmm-to-vmm.md)
* Pokud provádíte replikaci do sekundárního datového centra, přečtěte si o [podporovaných hostovaných operačních systémech pro virtuální počítače Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Pokud provádíte replikaci do Azure, Site Recovery podporuje všechny hostované operační systémy, které [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)podporuje.

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Můžu chránit virtuální počítače v případě, že je technologie Hyper-V spuštěná v klientském operačním systému?
Ne, virtuální počítače se musí nacházet na hostitelském serveru Hyper-V, který běží na podporovaném serveru s Windows. Pokud potřebujete chránit klientský počítač, můžete ho replikovat jako fyzický počítač do [Azure](site-recovery-vmware-to-azure.md) nebo do [sekundárního datacentra](site-recovery-vmware-to-vmware.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Musí být hostitelé Hyper-V v cloudech VMM?
Pokud chcete replikovat do sekundárního datového centra, musí být virtuální počítače Hyper-v na serverech hostitele Hyper-V umístěných v cloudu VMM. Pokud chcete replikovat do Azure, můžete replikovat virtuální počítače s cloudy VMM nebo bez nich. [Přečtěte si další](tutorial-hyper-v-to-azure.md) informace o replikaci Hyper-V do Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Mohu do Azure replikovat virtuální počítače Hyper-V generace 2?
Ano. Site Recovery se v průběhu převzetí služeb při selhání převede z generace 2 na generaci 1. Při navrácení služeb po obnovení se počítač převede zpátky na generaci 2. [Další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Mohu nasadit Site Recovery s VMM, když mám jen jeden server VMM?

Ano. Virtuální počítače můžete replikovat na serverech Hyper-V v cloudu VMM do Azure nebo je můžete replikovat mezi cloudy VMM na stejném serveru. V případě replikace z místního prostředí do místní sítě doporučujeme, abyste měli server VMM v primárních i sekundárních lokalitách. 

### <a name="what-do-i-need-in-azure"></a>Co potřebuji v Azure?
Potřebujete předplatné Azure, Recovery Services trezor, účet úložiště a virtuální síť. Trezor, účet úložiště a síť musí být ve stejné oblasti.

### <a name="what-azure-storage-account-do-i-need"></a>Jaký účet Azure Storage potřebuji?
Potřebujete účet úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Podporuje se Premium Storage.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Potřebuje můj účet Azure oprávnění k vytváření virtuálních počítačů?
Pokud jste správcem předplatného, máte oprávnění k replikaci, která potřebujete. Pokud nejste, budete potřebovat oprávnění k vytvoření virtuálního počítače Azure ve skupině prostředků a ve virtuální síti, kterou zadáte při konfiguraci Site Recovery, a oprávnění k zápisu do vybraného účtu úložiště. [Další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Posílají se data replikace do Site Recovery?
Ne, Site Recovery nezachycují replikovaná data a nemá žádné informace o tom, co na vašich virtuálních počítačích běží. Data replikace se vyměňují mezi hostiteli Hyper-V a úložištěm Azure. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001:2013, 27018, HIPAA, DPA Certified a je v procesu SOC2 a FedRAMP JAB Assessments.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Můžeme uchovávat místní metadata v rámci geografické oblasti?
Ano. Při vytváření trezoru v oblasti zajišťujeme, aby všechna metadata používaná Site Recovery zůstala v rámci geografické hranice této oblasti.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, podporuje se šifrování i přenos v [Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) .


## <a name="deployment"></a>Nasazení

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Co se dá dělat při replikaci Hyper-V do Azure?

- **Zotavení po havárii**: Můžete nastavit úplné zotavení po havárii. V tomto scénáři replikujte místní virtuální počítače Hyper-V do služby Azure Storage:
    - Virtuální počítače můžete replikovat do Azure. Pokud vaše místní infrastruktura není k dispozici, převezmete služby při selhání do Azure.
    - Když převezmete služby při selhání, vytvoří se virtuální počítače Azure pomocí replikovaných dat. K aplikacím a úlohám můžete přistupovat na virtuálních počítačích Azure.
    - Po opětovném zpřístupnění místního datacentra můžete navrátit služby po obnovení z Azure do místní lokality.
- **Migrace**: K migraci místních virtuálních počítačů Hyper-V do úložiště Azure můžete použít Site Recovery. Pak převezmete služby při selhání z místního prostředí do Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure.


### <a name="what-do-i-need-on-premises"></a>Co potřebuji v místním prostředí?

Potřebujete jeden nebo více virtuálních počítačů spuštěných na jednom nebo více samostatných hostitelích Hyper-V. Můžete taky replikovat virtuální počítače běžící na hostitelích spravovaných pomocí System Center Virtual Machine Manager (VMM).
- Pokud VMM nepoužíváte, budete během nasazení Site Recovery shromažďovat hostitele a clustery Hyper-V do lokalit technologie Hyper-V. Na každého hostitele Hyper-V nainstalujete Site Recovery agentů (poskytovatel Azure Site Recovery a Agent Recovery Services).
- Pokud jsou hostitelé Hyper-V umístěni v cloudu VMM, orchestrujte replikaci v nástroji VMM. Poskytovatele Site Recovery nainstalujete na server VMM a agenta Recovery Services na každého hostitele Hyper-V. Mapování mezi logickými a VIRTUÁLNÍmi sítěmi VMM a Azure virtuální sítě
- [Přečtěte si další informace](hyper-v-azure-architecture.md) o architektuře Hyper-V do Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Můžu replikovat virtuální počítače umístěné v clusteru Hyper-V?

Ano, Site Recovery podporuje hostitele Hyper-V V clusteru. Všimněte si, že:

- Všechny uzly clusteru by měly být zaregistrované ve stejném trezoru.
- Pokud nepoužíváte VMM, měli byste do stejného webu Hyper-V Přidat všechny hostitele Hyper-V v clusteru.
- Do každého hostitele Hyper-V v clusteru nainstalujete poskytovatele Azure Site Recovery a Recovery Services agenta a přidáte každého hostitele k lokalitě Hyper-V.
- V clusteru není nutné provádět žádné konkrétní kroky.
- Pokud spustíte nástroj Plánovač nasazení pro Hyper-V, nástroj shromáždí data profilu z uzlu, na kterém je spuštěný, a tam, kde je virtuální počítač spuštěný. Nástroj nemůže shromáždit žádná data z uzlu, který je vypnutý, ale tento uzel bude sledovat. Až bude uzel v provozu, nástroj spustí shromažďování dat profilu virtuálního počítače z něj (Pokud je virtuální počítač součástí seznamu virtuálních počítačů profilu a běží na uzlu).
- Pokud se virtuální počítač na hostiteli Hyper-V v trezoru Site Recovery migruje na jiného hostitele Hyper-V ve stejném clusteru nebo na samostatném hostiteli, nemá to vliv na replikaci virtuálního počítače. Hostitel Hyper-V musí splňovat [požadavky](hyper-v-azure-support-matrix.md#on-premises-servers)a nakonfigurovat ho v Site Recoverym trezoru. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Můžu chránit virtuální počítače v případě, že je technologie Hyper-V spuštěná v klientském operačním systému?
Ne, virtuální počítače se musí nacházet na hostitelském serveru Hyper-V, který běží na podporovaném serveru s Windows. Pokud potřebujete chránit klientský počítač, můžete [ho replikovat jako fyzický počítač](physical-azure-disaster-recovery.md) do Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Mohu do Azure replikovat virtuální počítače Hyper-V generace 2?
Ano. Site Recovery se v průběhu převzetí služeb při selhání převede z generace 2 na generaci 1. Při navrácení služeb po obnovení se počítač převede zpátky na generaci 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Můžu Site Recovery scénáře automatizovat pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře pro replikaci Hyper-V do Azure s využitím PowerShellu:

- [Replikace Hyper-V bez VMM pomocí PowerShellu](hyper-v-azure-powershell-resource-manager.md)
- [Replikace technologie Hyper-V pomocí nástroje VMM pomocí prostředí PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikace

### <a name="where-do-on-premises-vms-replicate-to"></a>Kam se replikují místní virtuální počítače?
Data se replikují do služby Azure Storage. Když spustíte převzetí služeb při selhání, Site Recovery automaticky vytvoří virtuální počítače Azure z účtu úložiště.

### <a name="what-apps-can-i-replicate"></a>Jaké aplikace je možné replikovat?
Můžete replikovat jakoukoli aplikaci nebo úlohu, na které běží virtuální počítač Hyper-V, který splňuje [požadavky na replikaci](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery poskytuje podporu pro replikaci zohledňující aplikace, aby bylo možné převzít služby při selhání a návrat do inteligentního stavu aplikací. Site Recovery se integruje s aplikacemi Microsoftu, jako jsou SharePoint, Exchange, Dynamics, SQL Server a Active Directory, a úzce spolupracuje s předními dodavateli, včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="whats-the-replication-process"></a>Co je proces replikace?

1. Při spuštění počáteční replikace se pořídí snímek virtuálního počítače Hyper-V.
2. Virtuální pevné disky virtuálního počítače se replikují jednou po jednom, dokud se všechny nezkopírují do Azure. Tato situace může chvíli trvat, v závislosti na velikosti virtuálního počítače a šířce pásma sítě. Přečtěte si, jak zvýšit šířku pásma sítě.
3. Pokud dojde ke změnám na disku v době, kdy probíhá počáteční replikace, sledování replikace repliky technologie Hyper-V sleduje změny jako protokoly replikace technologie Hyper-V (. hrl). Tyto soubory protokolu jsou umístěné ve stejné složce jako disky. Každý disk má přidružený soubor. hrl, který se odesílá do sekundárního úložiště. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní.
5. Všechny změny disků v protokolu se synchronizují a sloučí s nadřazeným diskem.
6. Po dokončení počáteční replikace se spustí ochrana finalizace úlohy virtuálního počítače. Nakonfiguruje síť a další nastavení po replikaci tak, aby byl virtuální počítač chráněný.
7. V této fázi můžete zkontrolovat nastavení virtuálního počítače, abyste se ujistili, že je připravený na převzetí služeb při selhání. Pro virtuální počítač můžete spustit postupné procházení zotavení po havárii (testovací převzetí služeb při selhání) a zkontrolovat, jestli převezme služby při selhání podle očekávání.
8. Po počáteční replikaci se v souladu se zásadami replikace spustí rozdílová replikace.
9. Změny jsou protokolovány soubory. HRL. Každý disk nakonfigurovaný pro replikaci má přidružený soubor .hrl.
10. Protokol se pošle do účtu úložiště zákazníka. Při přenosu protokolu do Azure jsou změny v primárním disku sledovány v jiném souboru protokolu ve stejné složce.
11. Při počáteční i rozdílové replikaci můžete virtuální počítač monitorovat v Azure Portal.

[Přečtěte si další informace](hyper-v-azure-architecture.md#replication-process) o procesu replikace.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Můžu replikovat do Azure pomocí sítě VPN typu Site-to-site?

Site Recovery replikuje data z místního koncového bodu do služby Azure Storage nebo používá veřejné partnerské vztahy ExpressRoute. Replikace přes síť VPN typu Site-to-site není podporovaná.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Můžu replikovat do Azure pomocí ExpressRoute?

Ano, ExpressRoute se dá použít k replikaci virtuálních počítačů do Azure. Site Recovery replikuje data na účet Azure Storage prostřednictvím veřejného koncového bodu a potřebujete nastavit [veřejné partnerské vztahy](../expressroute/expressroute-circuit-peerings.md#publicpeering) pro Site Recovery replikaci. Po převzetí služeb při selhání virtuálními počítači do služby Azure Virtual Network můžete k nim přistupovat pomocí [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Proč nejde replikovat přes síť VPN?

Při replikaci do Azure dosáhne provoz replikace veřejné koncové body účtu Azure Storage. Takže se dá replikovat jenom přes veřejný Internet s ExpressRoute (veřejným partnerským vztahem) a VPN nefunguje. 

### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaný virtuální počítač?

Pro replikaci musí na VIRTUÁLNÍm počítači s technologií Hyper-V běžet podporovaný operační systém. Virtuální počítač navíc musí splňovat požadavky na virtuální počítače Azure. [Další informace najdete](hyper-v-azure-support-matrix.md#replicated-vms) v části Support Matrix.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často je možné replikovat do Azure?

Virtuální počítače Hyper-V se dají replikovat každých 30 sekund (s výjimkou prémiového úložiště), 5 minut nebo 15 minut.

### <a name="can-i-extend-replication"></a>Je možné rozšířenou replikaci?
Rozšířená nebo zřetězená replikace není podporována. Vyžádejte si tuto funkci na [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Můžu provést počáteční replikaci offline?
Toto není podporováno. Vyžádejte si tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Můžu vyloučit disky?
Ano, disky můžete vyloučit z replikace. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Můžu replikovat virtuální počítače s dynamickými disky?
Dynamické disky je možné replikovat. Disk s operačním systémem musí být základní disk.



## <a name="security"></a>Zabezpečení

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Jaký přístup Site Recovery potřebují pro hostitele Hyper-V

Site Recovery potřebuje k replikaci virtuálních počítačů, které jste vybrali, přístup k hostitelům Hyper-V. Site Recovery nainstaluje na hostitele Hyper-V následující:

- Pokud VMM nepoužíváte, poskytovatel Azure Site Recovery a Agent Recovery Services se nainstalují na každého hostitele.
- Pokud používáte VMM, agent Recovery Services se nainstaluje na každého hostitele. Zprostředkovatel běží na serveru VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Co Site Recovery nainstalovat na virtuální počítače Hyper-V?

Site Recovery do virtuálních počítačů Hyper-V, které jsou povolené pro replikaci, explicitně neinstaluje cokoli.




## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení


### <a name="how-do-i-fail-over-to-azure"></a>Návody převzít služby při selhání do Azure?

Můžete spustit plánované nebo neplánované převzetí služeb při selhání z místních virtuálních počítačů Hyper-V do Azure.
    - Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat.
    - Neplánované převzetí služeb při selhání můžete spustit, pokud není k dispozici žádná primární lokalita.
    - Můžete převzít služby při selhání jednoho počítače nebo vytvořit plány obnovení pro orchestraci převzetí služeb při selhání více počítačů.
    - Spouštíte převzetí služeb při selhání. Po dokončení první fáze převzetí služeb při selhání byste měli být schopni zobrazit vytvořené virtuální počítače repliky v Azure. Virtuálnímu počítači můžete přiřadit veřejnou IP adresu, když je to třeba. Pak potvrdíte převzetí služeb při selhání, abyste mohli začít přistupovat ke úlohám z repliky virtuálního počítače Azure.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Návody přístup k virtuálním počítačům Azure po převzetí služeb při selhání
Po převzetí služeb při selhání získáte přístup k virtuálním počítačům Azure přes zabezpečené připojení k Internetu, přes síť VPN typu Site-to-site nebo přes Azure ExpressRoute. Abyste se mohli připojit, budete muset připravit několik věcí. [Víc se uč](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Nedošlo k přenosu dat odolného proti datům?
Služba Azure je pro odolnost navržena. Site Recovery je navržena pro převzetí služeb při selhání do sekundárního datacentra Azure v souladu se smlouvou SLA Azure. Když dojde k převzetí služeb při selhání, zajistěte, aby vaše metadata a trezory zůstaly ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
[Převzetí služeb při selhání](site-recovery-failover.md) není automatické. Převzetí služeb při selhání můžete zahájit jediným kliknutím na portálu, nebo můžete pomocí [PowerShellu](/powershell/module/az.recoveryservices) aktivovat převzetí služeb při selhání.

### <a name="how-do-i-fail-back"></a>Návody navrácení služeb po obnovení?

Po opětovném zprovoznění místní infrastruktury můžete provést navrácení služeb po obnovení. Navrácení služeb po obnovení proběhne ve třech fázích:

1. Naplánovali jste plánované převzetí služeb při selhání z Azure na místní lokalitu pomocí několika různých možností:

    - Minimalizovat prostoje: Pokud použijete tuto možnost Site Recovery synchronizuje data před převzetím služeb při selhání. Kontroluje změněné bloky dat a stáhne je do místní lokality, zatímco virtuální počítač Azure běží a minimalizuje výpadky. Když ručně určíte, že převzetí služeb při selhání by mělo být dokončené, virtuální počítač Azure se vypne a všechny poslední změny rozdílu se zkopírují a spustí se převzetí služeb při selhání.
    - Úplné stažení: Tato data možností se synchronizují během převzetí služeb při selhání. Tato možnost stáhne celý disk. Je rychlejší, protože nejsou vypočítány žádné kontrolní součty, ale existuje více výpadků. Tuto možnost použijte, pokud jste už nějakou dobu spustili repliky virtuálních počítačů Azure, nebo pokud se místní virtuální počítač odstranil.

2. Můžete vybrat, že navrácení služeb po obnovení do stejného virtuálního počítače nebo na jiný virtuální počítač. Můžete určit, že Site Recovery vytvořit virtuální počítač, pokud ještě neexistuje.
3. Po dokončení počáteční synchronizace můžete vybrat, aby se převzetí služeb při selhání dokončilo. Až se dokončí, můžete se přihlásit k místnímu virtuálnímu počítači a ověřit, jestli všechno funguje podle očekávání. V Azure Portal vidíte, že se virtuální počítače Azure zastavily.
4. Provedete převzetí služeb při selhání a začněte znovu přistupovat k úloze z místního virtuálního počítače.
5. Po úspěšném dokončení úloh se povolí zpětná replikace, aby se místní virtuální počítače znovu replikují do Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>Můžu navrátit služby po obnovení do jiného umístění?
Ano, Pokud převezmete služby při selhání do Azure, můžete navrátit služby po obnovení do jiného umístění, pokud není k dispozici původní. [Další informace](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment).
