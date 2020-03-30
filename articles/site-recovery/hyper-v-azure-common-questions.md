---
title: Běžné otázky pro zotavení po havárii technologie Hyper-V s Azure Site Recovery
description: Tento článek shrnuje běžné otázky týkající se nastavení zotavení po havárii pro místní virtuální počítače Hyper-V do Azure pomocí webu Azure Site Recovery.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498203"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Časté dotazy – Zotavení po havárii Hyper-V do Azure

Tento článek obsahuje odpovědi na běžné otázky, které se zobrazí při replikaci místních virtuálních počítačů Hyper-V do Azure. 

## <a name="general"></a>Obecné

### <a name="how-is-site-recovery-priced"></a>Jak je cena site recovery?
Projděte si podrobnosti o [cenách Azure Site Recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="how-do-i-pay-for-azure-vms"></a>Jak zaplatím za virtuální počítače Azure?
Během replikace se data replikují do úložiště Azure a neplatíte žádné změny virtuálních mís. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS. Potom se vám budou účtovat výpočetní prostředky, které spotřebováváte v Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Existuje nějaký rozdíl v nákladech při replikaci na účet úložiště pro obecné účely v2?

Obvykle se zobrazí zvýšení nákladů na transakce vzniklé na účtech úložiště GPv2, protože Azure Site Recovery je transakce těžké. [Přečtěte si více](../storage/common/storage-account-upgrade.md#pricing-and-billing) pro odhad změny.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Co potřebuji v technologii Hyper-V k orchestraci replikace pomocí site recovery?

U hostitelských serverů Hyper-V se vaše potřeby odvíjí od scénáře nasazení. Požadavky pro Hyper-V si můžete projít v těchto tématech:

* [Replikace virtuálních počítačů Hyper-V (bez VMM) do Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikace virtuálních počítačů Hyper-V (s VMM) do Azure](site-recovery-vmm-to-azure.md)
* [Replikace virtuálních počítačů Hyper-V do sekundárního datacentra](site-recovery-vmm-to-vmm.md)
* Pokud replikujete do sekundárního datového centra, přečtěte si o [podporovaných hostovacích operačních systémech pro virtuální počítače Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Pokud replikujete do Azure, Site Recovery podporuje všechny hostované operační systémy, které [jsou podporované Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Můžu chránit virtuální počítač, když je technologie Hyper-V spuštěná v klientském operačním systému?
Ne, virtuální počítače se musí nacházet na hostitelském serveru Hyper-V, který běží na podporovaném serveru s Windows. Pokud potřebujete chránit klientský počítač, můžete ho replikovat jako fyzický počítač do [Azure](site-recovery-vmware-to-azure.md) nebo [do sekundárního datového centra](site-recovery-vmware-to-vmware.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Musí být hostitelé Hyper-V v cloudech VMM?
Pokud chcete replikovat do sekundárního datového centra, pak hyper-V virtuální chody musí být na serverech hostitele Hyper-V umístěných v cloudu VMM. Pokud chcete replikovat do Azure, pak můžete replikovat virtuální počítače s nebo bez cloudů VMM. [Přečtěte si další informace](tutorial-hyper-v-to-azure.md) o replikaci technologie Hyper-V do Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Mohu do Azure replikovat virtuální počítače Hyper-V generace 2?
Ano. Obnovení webu převede z generace 2 na generaci 1 během převzetí služeb při selhání. Při navrácení služeb po selhání je počítač převeden zpět na generaci 2. [Přečtěte si další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Mohu nasadit Site Recovery s VMM, když mám jen jeden server VMM?

Ano. Můžete buď replikovat virtuální počítače na serverech Hyper-V v cloudu VMM do Azure, nebo můžete replikovat mezi cloudy VMM na stejném serveru. Pro místní replikaci do místní replikace doporučujeme mít server VMM v primární i sekundární lokalitě. 

### <a name="what-do-i-need-in-azure"></a>Co v Azure potřebuju?
Potřebujete předplatné Azure, trezor služby Recovery Services, účet úložiště a virtuální síť. Trezor, účet úložiště a síť musí být ve stejné oblasti.

### <a name="what-azure-storage-account-do-i-need"></a>Jaký účet úložiště Azure potřebuji?
Potřebujete účet úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Prémiové úložiště je podporováno.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Potřebuje můj účet Azure oprávnění k vytváření virtuálních počítačů?
Pokud jste správce předplatného, máte oprávnění replikace, které potřebujete. Pokud nejste, potřebujete oprávnění k vytvoření virtuálního počítače Azure ve skupině prostředků a virtuální síti, kterou zadáte při konfiguraci site recovery a oprávnění k zápisu do vybraného účtu úložiště. [Další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Jsou data replikace odesílána do obnovení sítě?
Ne, Site Recovery nezachycuje replikovaná data a nemá žádné informace o tom, co běží na virtuálních počítačích. Data replikace se vyměňují mezi hostiteli Hyper-V a úložištěm Azure. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Obnova webu je ISO 27001:2013, 27018, HIPAA, DPA certified a je v procesu hodnocení SOC2 a FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Můžeme uchovávat místní metadata v rámci geografické oblasti?
Ano. Když vytvoříte úschovnu v oblasti, zajistíme, aby všechna metadata používaná site recovery zůstala v rámci geografické hranice této oblasti.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, šifrování při přenosu i [šifrování v Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporované.


## <a name="deployment"></a>Nasazení

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Co můžu dělat s replikací Technologie Hyper-V na Azure?

- **Zotavení po havárii**: Můžete nastavit úplné zotavení po havárii. V tomto scénáři replikovat místní virtuální počítače Hyper-V do úložiště Azure:
    - Virtuální počítače můžete replikovat do Azure. Pokud vaše místní infrastruktura není dostupná, přejdete na azure.
    - Při převzetí služeb při selhání se virtuální počítače Azure vytvoří pomocí replikovaných dat. Přístup k aplikacím a úlohám na virtuálních počítačích Azure.
    - Když je vaše místní datové centrum znovu k dispozici, můžete si z Azure vrátit z Azure do místního webu.
- **Migrace**: Obnovení webu můžete použít k migraci místních virtuálních počítačů Hyper-V do úložiště Azure. Potom převzetí služeb při selhání z místního do Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure.


### <a name="what-do-i-need-on-premises"></a>Co potřebuji v místním prostředí?

Potřebujete jeden nebo více virtuálních počítačů spuštěných na jednom nebo více samostatných nebo clusterovaných hostitelích Hyper-V. Můžete také replikovat virtuální počítače spuštěné na hostitelích spravovaných správcem virtuálních strojů System Center (VMM).
- Pokud nepoužíváte vm, během nasazení site recovery shromažďujete hostitele a clustery Technologie Hyper-V do webů Hyper-V. Agenti obnovení webu (zprostředkovatel webu Azure a agent služby recovery services) nainstalujíte na každého hostitele Hyper-V.
- Pokud jsou hostitelé technologie Hyper-V umístěni v cloudu VMM, orchestrujete replikaci ve službě VMM. Zprostředkovatele obnovení webu nainstalujete na server VMM a agenta služby Recovery Services na každého hostitele Hyper-V. Mapujete mezi logickými sítěmi VMM a virtuálními sítěmi Azure.
- [Přečtěte si další informace](hyper-v-azure-architecture.md) o architektuře Hyper-V na Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Můžu replikovat virtuální ms umístěné v clusteru Hyper-V?

Ano, site recovery podporuje clusterované hostitele Hyper-V. Poznámky:

- Všechny uzly clusteru by měly být registrovány do stejného trezoru.
- Pokud nepoužíváte VMM, měli by být všichni hostitelé Technologie Hyper-V v clusteru přidáni do stejné lokality Hyper-V.
- Nainstalujete zprostředkovatele obnovení webu Azure a agenta služby recovery services na každého hostitele Hyper-V v clusteru a přidáte každého hostitele do webu Hyper-V.
- V clusteru není nutné provádět žádné konkrétní kroky.
- Pokud spustíte nástroj Plánovač nasazení pro Hyper-V, nástroj shromažďuje data profilu z uzlu, který běží a kde je spuštěn virtuální ho. Nástroj nemůže shromažďovat žádná data z uzlu, který je vypnutý, ale bude sledovat tento uzel. Po spuštění uzlu nástroj začne shromažďovat data profilu virtuálního počítače z něj (pokud je virtuální počítač součástí seznamu virtuálních počítačích profilu a běží na uzlu).
- Pokud virtuální počítač na hostiteli Hyper-V v trezoru obnovení webu migruje do jiného hostitele Hyper-V ve stejném clusteru nebo na samostatného hostitele, replikace pro virtuální počítač není ovlivněna. Hostitel Hyper-V musí splňovat [požadavky](hyper-v-azure-support-matrix.md#on-premises-servers)a musí být nakonfigurován v trezoru site recovery. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Můžu chránit virtuální počítač, když je technologie Hyper-V spuštěná v klientském operačním systému?
Ne, virtuální počítače se musí nacházet na hostitelském serveru Hyper-V, který běží na podporovaném serveru s Windows. Pokud potřebujete chránit klientský počítač, můžete [ho replikovat jako fyzický počítač](physical-azure-disaster-recovery.md) do Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Mohu do Azure replikovat virtuální počítače Hyper-V generace 2?
Ano. Obnovení webu převede z generace 2 na generaci 1 během převzetí služeb při selhání. Při navrácení služeb po selhání je počítač převeden zpět na generaci 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Je možné automatizovat scénáře obnovení webu pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře pro replikaci Technologie Hyper-V do Azure pomocí PowerShellu:

- [Replikace hyperv bez VMM pomocí Prostředí PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replikace technologie Hyper-V pomocí technologie VMM pomocí prostředí Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikace

### <a name="where-do-on-premises-vms-replicate-to"></a>Kde se replikují místní virtuální počítačové servery?
Data se replikují do úložiště Azure. Když spustíte převzetí služeb při selhání, site recovery automaticky vytvoří virtuální počítače Azure z účtu úložiště.

### <a name="what-apps-can-i-replicate"></a>Jaké aplikace mohu replikovat?
Můžete replikovat libovolnou aplikaci nebo úlohu se spuštěným virtuálním virtuálním ms Hyper-V, který splňuje [požadavky na replikaci](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery poskytuje podporu pro replikaci s podporou aplikací, takže aplikace mohou být převzetí služeb při selhání a selhání zpět do inteligentního stavu. Site Recovery se integruje s aplikacemi společnosti Microsoft, jako jsou SharePoint, Exchange, Dynamics, SQL Server a Active Directory, a úzce spolupracuje s předními dodavateli, včetně oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="whats-the-replication-process"></a>Jaký je proces replikace?

1. Při spuštění počáteční replikace je pořízen snímek virtuálního počítače Hyper-V.
2. Virtuální pevné disky na virtuálním počítači se replikují jeden po druhém, dokud se všechny nezkopírují do Azure. To může chvíli trvat, v závislosti na velikosti virtuálního počítače a šířce pásma sítě. Přečtěte si, jak zvýšit šířku pásma sítě.
3. Pokud dojde ke změnám disku během počáteční replikace, nástroj Sledování repliky replik y hyperv sleduje změny jako protokoly replikace Hyper-V (.hrl). Tyto soubory protokolu jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor HRL, který je odeslán do sekundárního úložiště. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní.
5. Všechny změny disku v protokolu jsou synchronizovány a sloučeny s nadřazeným diskem.
6. Po dokončení počáteční replikace se spustí ochrana finalizace na úloze virtuálního počítače. Konfiguruje nastavení sítě a další chod po replikaci, takže virtuální počítač je chráněný.
7. V této fázi můžete zkontrolovat nastavení virtuálního počítače a ujistěte se, že je připravený pro převzetí služeb při selhání. Můžete spustit zotavení po havárii vrták (test převzetí služeb při selhání) pro virtuální ho, zkontrolujte, zda je převzetí služeb při selhání podle očekávání.
8. Po počáteční replikaci začíná rozdílová replikace v souladu se zásadami replikace.
9. Změny jsou zaznamenány .hrl soubory. Každý disk nakonfigurovaný pro replikaci má přidružený soubor .hrl.
10. Protokol je odeslán na účet úložiště zákazníka. Když je protokol při přenosu do Azure, změny na primárním disku jsou sledovány v jiném souboru protokolu ve stejné složce.
11. Během počáteční i delta replikace můžete sledovat virtuální počítač na webu Azure Portal.

[Přečtěte si další informace](hyper-v-azure-architecture.md#replication-process) o procesu replikace.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Můžu se replikovat do Azure pomocí sítě VPN site-to-site?

Site Recovery replikuje data z místního úložiště do úložiště Azure přes veřejný koncový bod nebo pomocí partnerského vztahu Microsoftu ExpressRoute. Replikace prostřednictvím sítě VPN mezi lokalitami není podporována.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Můžu se replikovat do Azure pomocí ExpressRoute?

Ano, ExpressRoute se dá použít k replikaci virtuálních počítačů do Azure. Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod a je potřeba nastavit [partnerský vztah Microsoftu](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) pro replikaci obnovení lokality. Po převzetí služeb při selhání virtuálních počítačích do virtuální sítě Azure, můžete k nim přistupovat pomocí [privátního partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Proč se nedá replikovat přes VPN?

Když replikujete do Azure, provoz replikace dosáhne veřejných koncových bodů účtu Azure Storage. Tak můžete replikovat pouze přes veřejný internet s ExpressRoute (Microsoft peering) a VPN nefunguje. 

### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaný virtuální virtuální mísu?

Pro replikaci musí virtuální modul Hyper-V spustit podporovaný operační systém. Kromě toho musí virtuální počítač splňovat požadavky na virtuální počítače Azure. [Další informace najdete](hyper-v-azure-support-matrix.md#replicated-vms) v matici podpory.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často se můžu replikovat do Azure?

Virtuální aplikace Hyper-V lze replikovat každých 30 sekund (s výjimkou úložiště premium), 5 minut nebo 15 minut.

### <a name="can-i-extend-replication"></a>Mohu prodloužit replikaci?
Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Mohu provést počáteční replikaci offline?
Toto není podporováno. Požádejte o tuto funkci ve [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Mohu vyloučit disky?
Ano, disky můžete vyloučit z replikace. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Můžu replikovat virtuální počítače s dynamickými disky?
Dynamické disky lze replikovat. Disk operačního systému musí být běžný.



## <a name="security"></a>Zabezpečení

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Jaký přístup potřebuje site recovery pro hostitele Hyper-V

Site Recovery potřebuje přístup k hostitelům Hyper-V k replikaci vybraných virtuálních stránek. Site Recovery nainstaluje na hostitele Technologie Hyper-V následující:

- Pokud nepoužíváte VMM, poskytovatel obnovení webu Azure a agent služby recovery services se nainstalují na každém hostiteli.
- Pokud používáte vm, agent služby Recovery Services je nainstalován na každém hostiteli. Zprostředkovatel běží na serveru VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Co site recovery nainstalovat na virtuální chodů Hyper-V?

Obnovení lokality explicitně nenainstaluje nic na virtuální chodů Hyper-V povoleno pro replikaci.




## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení


### <a name="how-do-i-fail-over-to-azure"></a>Jak se dostanu k převzetí služeb při selhání do Azure?

Můžete spustit plánované nebo neplánované převzetí služeb při selhání z místních virtuálních počítačů Hyper-V do Azure.

- Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat.
- Pokud primární lokalita není přístupná, můžete spustit neplánované převzetí služeb při selhání.
- Můžete převzít službu při selhání jednoho počítače nebo vytvořit plány zotavení a orchestrovat převzetí služeb při selhání více počítačů.
- Převzetí služeb při selhání je ve dvou částech:
    - Po dokončení první fáze převzetí služeb při selhání, měli byste být schopni zobrazit vytvořené repliky virtuálních počítačů v Azure. Virtuálnímu počítači můžete přiřadit veřejnou IP adresu, když je to třeba.
    - Potom potvrdíte převzetí služeb při selhání a můžete začít přistupovat k úlohám z repliky virtuálního počítače Azure.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak se po převzetí služeb při selhání dostanu k virtuálním počítačům Azure?
Po převzetí služeb při selhání můžete přistupovat k virtuálním počítačům Azure přes zabezpečené připojení k Internetu, přes síť VPN mezi lokalitami nebo přes Azure ExpressRoute. Budete muset připravit řadu věcí, aby se připojit. [Další informace](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Je převzetí dat odolné za převzetí dat?
Služba Azure je pro odolnost navržena. Site Recovery je navržen pro převzetí služeb při selhání do sekundárního datového centra Azure, v souladu s Azure SLA. Když dojde k převzetí služeb při selhání, zajistíme, aby vaše metadata a trezory zůstaly ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
[Převzetí služeb při selhání](site-recovery-failover.md) není automatické. Převzetí služeb při selhání můžete zahájit jediným kliknutím na portálu nebo můžete pomocí [prostředí PowerShell](/powershell/module/az.recoveryservices) aktivovat převzetí služeb při selhání.

### <a name="how-do-i-fail-back"></a>Jak mohu přiselhání k selhání?

Až bude vaše místní infrastruktura znovu spuštěná, můžete ji znovu navrátit. K navrácení služeb po selhání dochází ve třech fázích:

1. Plánované převzetí služeb při selhání z Azure do místního webu zahájíte pomocí několika různých možností:

    - Minimalizovat prostoje: Pokud použijete tuto možnost Site Recovery synchronizuje data před převzetím služeb při selhání. Kontroluje změněné datové bloky a stáhne je do místního webu, zatímco virtuální počítač Azure bude dál spuštěný a minimalizuje prostoje. Když ručně určíte, že převzetí služeb při selhání by měla být dokončena, virtuální počítač Azure se vypne, všechny konečné změny delta se zkopírují a převzetí služeb při selhání spustí.
    - Úplné stažení: S touto možností jsou data synchronizována během převzetí služeb při selhání. Tato možnost stáhne celý disk. Je to rychlejší, protože se nepočítají žádné kontrolní součty, ale dochází k dalším prostojům. Tuto možnost použijte, pokud už nějakou dobu spouštíte repliky virtuálních počítačů Azure nebo pokud byl odstraněn místní virtuální počítač.

2. Můžete vybrat, chcete-li postoupit zpět do stejného virtuálního aplikace nebo alternativního virtuálního aplikace. Můžete určit, že obnovení webu by měl vytvořit virtuální hod, pokud ještě neexistuje.
3. Po dokončení počáteční synchronizace vyberete k dokončení převzetí služeb při selhání. Po dokončení se můžete přihlásit k místnímu virtuálnímu počítači a zkontrolovat, že vše funguje podle očekávání. Na webu Azure Portal uvidíte, že virtuální počítače Azure byly zastavené.
4. Potvrzení převzetí služeb při selhání dokončit a začít přístup k pracovnímu vytížení z místního virtuálního počítače znovu.
5. Po selhání úloh se nezdařilo zpět, povolíte reverzní replikace, takže místní virtuální počítače replikovat do Azure znovu.

### <a name="can-i-fail-back-to-a-different-location"></a>Mohu se vrátit na jiné místo?
Ano, pokud jste se nezdaří přes Azure, můžete se vrátit do jiného umístění, pokud původní není k dispozici. [Další informace](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
