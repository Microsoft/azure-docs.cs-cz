---
title: Běžné otázky, které-Hyper-V k zotavení po havárii Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek shrnuje běžné otázky týkající se nastavení zotavení po havárii pro místní virtuální počítače Hyper-V do Azure pomocí webu Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 11/27/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 3e71c4e31c6d57cb54a654e0e1c28dcb0fa82cda
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875327"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Časté otázky – Hyper-V do Azure zotavení po havárii

Tento článek obsahuje odpovědi na běžné dotazy, které můžeme vidět při replikaci místních virtuálních počítačů Hyper-V do Azure. 


## <a name="general"></a>Obecné

### <a name="how-is-site-recovery-priced"></a>Jak se účtuje Site Recovery?
Kontrola [ceny za Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) podrobnosti.

### <a name="how-do-i-pay-for-azure-vms"></a>Jak zaplatím za virtuální počítače Azure?
Během replikace data se replikují do služby Azure storage a neplatíte žádné změny virtuálního počítače. Při spuštění převzetí služeb při selhání do Azure Site Recovery automaticky vytvoří virtuální počítače Azure IaaS. Následně se vám budeme fakturovat výpočetní prostředky, které využívají v Azure.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-azure"></a>Co musím udělat v Azure?
Budete potřebovat předplatné Azure, trezor služby Recovery Services, účet úložiště a virtuální síť. Trezor, účet úložiště a síť musí být ve stejné oblasti.

### <a name="what-azure-storage-account-do-i-need"></a>Který účet úložiště Azure budu potřebovat?
Budete potřebovat účet úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Premium storage podporuje.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Oprávnění k vytvoření virtuálních počítačů musí Můj účet Azure?
Pokud si nejste správce předplatného, máte oprávnění replikace, které potřebujete. Pokud si nejste, budete potřebovat oprávnění k vytvoření virtuálního počítače Azure ve skupině prostředků a virtuální síť, kterou zadáte při konfiguraci Site Recovery a oprávnění k zápisu do vybraného účtu úložiště. [Další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Replikační data odeslaná do Site Recovery?
Ne, Site Recovery nezachycuje replikovaná data se nepodporuje a nemá žádné informace o co běží na virtuálních počítačích. Replikovaná data se přemisťují mezi hostiteli technologie Hyper-V a Azure storage. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikace a probíhá SOC2 a FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Jsme zachovat místní metadata v rámci zeměpisné oblasti?
Ano. Když vytvoříte trezor služby v oblasti, zajišťujeme, že všechna metadata používat Site Recovery zůstane v rámci dané oblasti geografické hranice.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, jak šifrování během přenosu a [šifrování v Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.


## <a name="deployment"></a>Nasazení

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Co je možné? s technologií Hyper-V do Azure replikace

- **Zotavení po havárii**: můžete nastavit úplné havárii. V tomto scénáři replikaci místních virtuálních počítačů Hyper-V do Azure storage:
    - Replikace virtuálních počítačů do Azure. Pokud není k dispozici v místní infrastruktuře, můžete převzetí služeb při selhání do Azure.
    - Při převzetí služeb při selhání, se vytvoří virtuální počítače Azure pomocí replikovaná data. Přístupná aplikace a úlohy na virtuálních počítačích Azure.
    - Pokud vaše místní datové centrum opět dostupná, můžete převzít obnovení z Azure do místní lokality.
- **Migrace**: Site Recovery můžete migrovat místní virtuální počítače Hyper-V do Azure storage. Pak převezme služby při selhání z místního Azure. Po převzetí služeb při selhání jsou vaše aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure.


### <a name="what-do-i-need-on-premises"></a>K čemu potřebuji místní?

Potřebujete jeden nebo více virtuálních počítačů spuštěných na jeden nebo více samostatných nebo clusterovaných hostitelů technologie Hyper-V. Se dají taky replikovat virtuální počítače spuštěné na hostitelích, které jsou spravované nástrojem System Center Virtual Machine Manager (VMM).
    - Pokud používáte VMM během nasazování Site Recovery, shromáždíte hostitelé a clustery Hyper-V do lokality Hyper-V. Jste instalovali agenty Site Recovery (zprostředkovatele Azure Site Recovery a agent Recovery Services) na každém hostiteli Hyper-V.
    - Pokud jsou hostitelé Hyper-V v cloudu VMM, můžete orchestrovat replikace v nástroji VMM. Nainstalujete zprostředkovatele Site Recovery na VMM server a agenta služby Recovery Services na každého hostitele Hyper-V. Můžete namapovat mezi logické a VM sítě VMM a virtuálním sítím Azure.
    - 
[Další informace](hyper-v-azure-architecture.md) o Hyper-V do Azure architektury.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Můžete replikovat virtuální počítače na cluster Hyper-V?

Ano, Site Recovery podporuje clusterovaných hostitelů technologie Hyper-V. Poznámky:

- Všechny uzly clusteru by měl být zaregistrován ke stejnému trezoru.
- Pokud nepoužíváte nástroj VMM, měli byste do stejné lokality Hyper-V přidat všichni hostitelé Hyper-V v clusteru.
- Instalovat zprostředkovatele Azure Site Recovery a agent Recovery Services na každém hostiteli Hyper-V v clusteru a přidejte do lokality Hyper-V každém hostiteli.
- Žádné konkrétní kroky je potřeba udělat v clusteru.
- Pokud spouštíte nástroj Plánovač nasazení služby pro technologii Hyper-V, nástroj shromáždí data profilu z uzlu, kterou je spuštěna a kdy je virtuální počítač spuštěný. Nástroj nelze shromáždit všechna data z uzlu, který je vypnutý, ale bude sledovat tento uzel. Po vytvoření a spuštění je uzel, nástroj spustí shromažďování dat profilu virtuálního počítače z něj (Pokud je virtuální počítač je součástí profilu seznam virtuálních počítačů a běží na uzlu).
- Pokud virtuální počítač na hostitele Hyper-V v trezoru Site Recovery migrovat na jiného hostitele Hyper-V ve stejném clusteru nebo na samostatné hostitele, replikace pro virtuální počítač nemá žádný vliv. Hostitel Hyper-V musí splňovat [požadavky](hyper-v-azure-support-matrix.md#on-premises-servers)a nakonfigurovat v trezoru Site Recovery. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Můžete chránit virtuální počítače, pokud Hyper-V běží na operačním systému klienta?
Ne, virtuální počítače se musí nacházet na hostitelském serveru Hyper-V, který běží na podporovaném serveru s Windows. Pokud potřebujete ochránit klientský počítač může [jej replikovat jako fyzický počítač, který](physical-azure-disaster-recovery.md) do Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Mohu do Azure replikovat virtuální počítače Hyper-V generace 2?
Ano. Site Recovery převede z generace 2 na generaci 1 během převzetí služeb při selhání. Při navrácení služeb po obnovení se počítač převede zpět na generaci 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Můžete automatizovat scénáře obnovení lokality pomocí sady SDK?
Ano. Postupy workflow Site Recovery je možné automatizovat pomocí rozhraní REST API, PowerShellu a sady Azure SDK. Aktuálně podporované scénáře pro replikaci Hyper-V do Azure pomocí Powershellu:

- [Replikace Hyper-V bez nástroje VMM pomocí Powershellu](hyper-v-azure-powershell-resource-manager.md)
- [Replikace Hyper-V pomocí nástroje VMM pomocí Powershellu](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikace

### <a name="where-do-on-premises-vms-replicate-to"></a>Kde replikace místních virtuálních počítačů do?
Data se replikují do úložiště Azure. Při spuštění převzetí služeb při selhání Site Recovery automaticky vytvoří virtuální počítače Azure z účtu úložiště.

### <a name="what-apps-can-i-replicate"></a>Jaké aplikace můžou replikovat?
Můžete replikovat jakoukoli aplikaci nebo úlohy spuštění virtuálního počítače s Hyper-V, který splňuje [požadavky na replikaci](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery poskytuje podporu pro replikaci se sledováním aplikací, tak, aby aplikace, můžete převzetí služeb při selhání a zpět do použitelného stavu se nezdařilo. Site Recovery se integruje s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a Active Directory a úzce spolupracuje s předními dodavateli včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="whats-the-replication-process"></a>Co je proces replikace?

1. Při aktivaci počáteční replikace se pořídí snímek virtuálního počítače Hyper-V.
2. Virtuální pevné disky na virtuálním počítači se replikují jeden po druhém, dokud se všechny nezkopírují do Azure. To může chvíli trvat, v závislosti na velikosti virtuálního počítače a šířka pásma sítě. Zjistěte, jak zvětšit šířku pásma sítě.
3. Pokud dojde ke změnám na disku, kdy probíhá počáteční replikace, Hyper-V Replica Replication Tracker zaznamenává změny zaznamená jako protokoly replikace technologie Hyper-V (.hrl). Tyto protokolové soubory jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor .hrl, který se odešle do sekundárního úložiště. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní.
5. Všechny změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.
6. Po dokončení počáteční replikace spustí Finalize ochrany na úlohy virtuálních počítačů. Nakonfiguruje Síťová a další postreplikační nastavení tak, aby virtuální počítač je chráněný.
7. V této fázi můžete zkontrolovat nastavení virtuálního počítače, abyste měli jistotu, že je připravený pro převzetí služeb při selhání. Můžete spustit zotavení po havárii (testovací převzetí služeb) pro virtuální počítač, zkontrolujte, že rutina selže, než podle očekávání.
8. Po počáteční replikaci se zahájí rozdílová replikace, v souladu se zásadami replikace.
9. Změny jsou soubory přihlášeného .hrl. Každý disk nakonfigurovaný pro replikaci má přidružený soubor .hrl.
10. V protokolu se odešle do účtu úložiště zákazníka. Když protokol je při přenosu do Azure, se sledují změny na primárním disku do jiného souboru protokolu, ve stejné složce.
11. Během počáteční a rozdílové replikace můžete sledovat virtuální počítač na webu Azure Portal.

[Další informace](hyper-v-azure-architecture.md#replication-process) o procesu replikace.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Můžete replikovat do Azure pomocí VPN typu site-to-site?

Site Recovery replikuje data z místních do úložiště Azure přes veřejný koncový bod, nebo pomocí veřejného partnerského vztahu ExpressRoute. Replikace přes síť VPN typu site-to-site se nepodporuje.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Můžete replikovat do Azure pomocí ExpressRoute?

Ano, je možné replikovat virtuální počítače Azure ExpressRoute. Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod a je potřeba nastavit [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#publicpeering) replikace Site Recovery. Po převzetí služeb virtuálních počítačů do služby Azure virtual network, k nim můžete přistupovat pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Proč se můžu replikovat přes síť VPN?

Při replikaci do Azure, provoz replikace dosáhne veřejné koncové body účtu úložiště Azure, proto vám můžou replikovat jenom přes veřejný internet s ExpressRoute (veřejný partnerský vztah) a VPN nebude fungovat. 

### <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaných virtuálních počítačů?

Pro účely replikace virtuálních počítačů Hyper-V musí běžet podporovaný operační systém. Kromě toho virtuální počítač musí splňovat požadavky pro virtuální počítače Azure. [Další informace](hyper-v-azure-support-matrix.md#replicated-vms) v matici podpory.

### <a name="how-often-can-i-replicate-to-azure"></a>Jak často můžete replikovat do Azure?

Virtuální počítače Hyper-V je možné replikovat každých 30 sekund (s výjimkou storage úrovně premium), 5 minut nebo 15 minut.

###<a name="can-i-extend-replication"></a>Je možné rozšířit replikaci?
Rozšířená nebo zřetězená replikace není podporována. Žádost o tuto funkci v [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-initial-replication"></a>Můžete provést offline počáteční replikaci?
Toto není podporováno. Žádost o tuto funkci [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Vyloučení disků
Ano, můžete vyloučit disky z replikace. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Můžete replikovat virtuální počítače s dynamickými disky?
Dynamické disky se dají replikovat. Disk s operačním systémem musí být základní disk.



## <a name="security"></a>Zabezpečení

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Jaký přístup Site Recovery potřebuje hostitele Hyper-V

Site Recovery potřebuje přístup k hostitelům Hyper-V pro replikaci virtuálních počítačů, které jste vybrali. Site Recovery nainstaluje na hostitele Hyper-V následující:

- Pokud používáte VMM zprostředkovatele Azure Site Recovery a agenta služby Recovery Services jsou nainstalovány na každém hostiteli.
- Pokud používáte nástroj VMM, je na každém hostiteli nainstaluje agenta služby Recovery Services. Zprostředkovatel běží na serveru VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Co Site Recovery nainstalovat na virtuální počítače Hyper-V?

Site Recovery explicitně nic neinstaluje na virtuálních počítačích Hyper-V povolena replikace.




## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení


### <a name="how-do-i-fail-over-to-azure"></a>Jak můžu převzetí služeb při selhání do Azure?

Můžete spustit plánované nebo neplánované převzetí služeb při selhání z místních virtuálních počítačů Hyper-V do Azure.
    - Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat.
    - Neplánované převzetí služeb při selhání můžete spustit, pokud primární lokality není přístupná.
    - Můžete převzetí služeb při selhání jednoho počítače nebo vytvořit plány obnovení a orchestrovat převzetí služeb při selhání více počítačů.
    - Spuštění převzetí služeb při selhání. Po dokončení první fázi převzetí služeb při selhání byste měli vidět vytvořené replikované virtuální počítače v Azure. Virtuálnímu počítači můžete přiřadit veřejnou IP adresu, když je to třeba. Po potvrzení procesu převzetí služeb při selhání, chcete-li začít používat úlohu na replikovaném virtuálním počítači Azure.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak získám přístup virtuálních počítačů Azure po převzetí služeb při selhání?
Po převzetí služeb při selhání můžete přístup k virtuálním počítačům Azure přes zabezpečené internetové připojení, přes síť site-to-site VPN nebo přes Azure ExpressRoute. Budete muset připravit řada věcí, abyste se mohli připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Se převzaly při selhání data odolná?
Služba Azure je pro odolnost navržena. Site Recovery je navržena pro převzetí služeb při selhání do sekundárního datacentra Azure, v souladu s smlouvy SLA pro Azure. Pokud dojde k převzetí služeb při selhání, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
[Převzetí služeb při selhání](site-recovery-failover.md) neprobíhá automaticky. Zahájení převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít [ Powershellu](/powershell/module/azurerm.siterecovery) k aktivaci převzetí služeb při selhání.

### <a name="how-do-i-fail-back"></a>Jak můžu navrácení služeb po obnovení?

Po znovu spuštěn a je v místní infrastruktuře, můžete službu navrátit. Ve třech fázích, dojde k navrácení služeb po obnovení:

1. Zahájíte plánované převzetí služeb z Azure do místní lokality pomocí několika různých možností:

    - Minimalizovat prostoje: Pokud použijete tuto možnost Site Recovery synchronizuje data před převzetí služeb při selhání. Kontroluje bloky změněná data a stáhne do místní lokality při udržuje virtuálním počítači Azure spuštěný, minimalizace výpadků. Když ručně zadáte, že by se měl dokončit převzetí služeb při selhání, vypnutí virtuálního počítače Azure, se zkopírují všechny finální rozdílové změny a spustí převzetí služeb při selhání.
    - Úplné stažení: pomocí této možnosti se data synchronizují během převzetí služeb při selhání. Tato možnost umožňuje stáhnout celý disk. Je rychlejší, protože se počítají žádné kontrolní součty, ale není k dispozici další prostoje. Tuto možnost použijte, pokud běží repliky virtuálních počítačů Azure nějakou dobu, nebo pokud místní virtuální počítač byl odstraněn.

2. Můžete vybrat selhání zpět do stejného virtuálního počítače nebo do alternativní virtuálního počítače. Můžete určit, že by měl Site Recovery vytvořte virtuální počítač, pokud ještě neexistuje.
3. Po dokončení počáteční synchronizace, je vybrat k dokončení převzetí služeb. Po dokončení, přihlaste se na místní virtuální počítač, zkontrolujte, že všechno funguje podle očekávání. Na webu Azure Portal uvidíte, že virtuální počítače Azure byly zastaveny.
4. Potvrzení převzetí služeb při selhání skončí a začít používat úlohu z místní virtuální počítač znovu.
5. Po úloh se nepodařilo vrátit, povolíte zpětnou replikaci, tak, aby místní virtuální počítače replikovat do Azure znovu.

### <a name="can-i-fail-back-to-a-different-location"></a>Můžu navrátit služby zpět do jiného umístění?
Ano, pokud převzetí služeb při selhání do Azure, můžete navrátit služby zpět do jiného umístění Pokud původní není k dispozici. [Další informace](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment).
