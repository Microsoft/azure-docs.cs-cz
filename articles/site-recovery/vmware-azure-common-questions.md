---
title: Časté otázky – VMware do Azure replikace s Azure Site Recovery | Microsoft Docs
description: Tento článek vysvětluje běžné otázky, kdy replikovat místní virtuální počítače VMware do Azure pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 1764f4ed9dfe73763c288844be85d4805401887e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285963"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Časté otázky – VMware do Azure replikace

Tento článek obsahuje odpovědi na časté otázky, které vidíte při replikaci místní virtuální počítače VMware do Azure. Pokud po přečtení tohoto článku máte dotazy, odešlete je na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Obecné
### <a name="how-is-site-recovery-priced"></a>Jak je cenově Site Recovery?
Zkontrolujte [cenách Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/) podrobnosti.

### <a name="how-do-i-pay-for-azure-vms"></a>Jak platit pro virtuální počítače Azure
Během replikace data se replikují do úložiště Azure a platíte nemáte žádné změny virtuálního počítače. Při spuštění převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS. Poté jste účtovány poplatky za výpočetní prostředky, které v Azure spotřebujete.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Jak se VMware do Azure replikace?
- **Zotavení po havárii**: můžete nastavit zotavení po havárii úplné. V tomto scénáři replikovat místní virtuální počítače VMware do úložiště Azure. Poté Pokud není k dispozici na místní infrastrukturu, můžete selhání do Azure. Při selhání, jsou virtuální počítače Azure vytvořené pomocí replikovaná data. Aplikace a úlohy na virtuálních počítačích Azure, můžete přístup, dokud vašeho místního datového centra je opět k dispozici. Potom můžete může selhat zpět z Azure do místního webu.
- **Migrace**: Site Recovery můžete migrovat místní virtuální počítače VMware do Azure. V tomto scénáři replikovat místní virtuální počítače VMware do úložiště Azure. Potom můžete převzetí služeb při selhání z místního Azure. Po převzetí služeb při selhání jsou aplikace a úlohy dostupné a spuštěné na virtuálních počítačích Azure.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Co potřebuji v Azure?
Potřebujete předplatné Azure, trezoru služeb zotavení, účet úložiště a virtuální sítě. Trezor, účet úložiště a sítě musí být ve stejné oblasti.

### <a name="what-azure-storage-account-do-i-need"></a>Jaké účtu úložiště Azure potřebuji?
Budete potřebovat účet úložiště LRS nebo GRS. Doporučujeme účet úložiště GRS, aby byla zajištěna odolnost dat v případě oblastního výpadku nebo pokud není možné obnovit primární oblast. Storage úrovně Premium se podporuje.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Potřebuje Můj účet Azure oprávnění k vytvoření virtuálních počítačů?
Pokud jste správci předplatného, máte replikace oprávnění, které potřebujete. Pokud si nejste, potřebujete oprávnění k vytvoření virtuálního počítače Azure ve skupině prostředků a virtuální síť, kterou zadáte při konfiguraci Site Recovery a oprávnění k zápisu do vybraného účtu úložiště. [Další informace](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Lokálně 

### <a name="what-do-i-need-on-premises"></a>Co dělat, je nutné místně?
Na místní musíte součásti Site Recovery nainstalovaná na jeden virtuální počítač VMware. Musíte taky infrastruktuře VMware s alespoň jednomu hostiteli ESXi, a doporučujeme vCenter server. Kromě toho je třeba jeden nebo více virtuálních počítačů VMware pro replikaci. [Další informace](vmware-azure-architecture.md) o VMware do Azure architektury.

Místní konfigurační server můžete nasadit v jednom z následujících způsobů

1. Nasazení pomocí šablony virtuálního počítače s předinstalovaným konfigurační server. [Přečtěte si zde](vmware-azure-tutorial.md#download-the-vm-template).
2. Nasaďte pomocí instalace na počítače s Windows Server 2016 podle svého výběru. [Přečtěte si zde](physical-azure-disaster-recovery.md#set-up-the-source-environment).

Chcete-li zjistit, Začínáme kroky nasazení serveru konfigurace na vlastní počítačů Windows serveru, v cíl ochrany povolení ochrany, zvolte **do Azure > není virtualizované/jiné**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Kde replikovat místní virtuální počítače do?
Data se replikují do úložiště Azure. Při spuštění převzetí služeb při selhání, Site Recovery automaticky vytvoří virtuální počítače Azure z účtu úložiště.

### <a name="what-apps-can-i-replicate"></a>Které aplikace můžete replikovat?
Můžete replikovat libovolnou aplikaci nebo úlohu spuštěnou na virtuální počítač VMware, který odpovídá [požadavcích replikace](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery poskytuje podporu pro aplikace s cílem replikace, aby aplikace mohla převzetí služeb při selhání a zpět do použitelného stavu se nezdařilo. Site Recovery se integruje s aplikacemi Microsoftu, například SharePoint, Exchange, Dynamics, SQL Server a služby Active Directory a úzce spolupracuje s hlavními dodavateli včetně Oracle, SAP, IBM a Red Hat. [Další informace](site-recovery-workload.md) o ochraně úloh.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Mohu replikovat do Azure pomocí sítě site-to-site VPN?
Site Recovery replikuje data z místně do úložiště Azure přes veřejný koncový bod, nebo pomocí veřejného partnerského vztahu ExpressRoute. Replikace přes síť VPN typu site-to-site není podporována.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Mohu replikovat do Azure prostřednictvím ExpressRoute?
Ano, ExpressRoute slouží k replikaci virtuálních počítačů do Azure. Site Recovery replikuje data do účtu úložiště Azure přes veřejný koncový bod a je nutné nastavit [veřejného partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) pro replikaci Site Recovery. Po virtuálním počítačům převzetí služeb při selhání pro virtuální síť Azure, se dostanete pomocí [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Proč nelze replikovat prostřednictvím sítě VPN?

Při replikaci do Azure, provoz replikace dosáhne veřejné koncové body účtu úložiště Azure, proto můžete pouze replikovat prostřednictvím veřejného Internetu s s ExpressRoute (veřejný partnerský vztah) a sítě VPN nebude fungovat. 



## <a name="what-are-the-replicated-vm-requirements"></a>Jaké jsou požadavky na replikovaných virtuálních počítačů?

Pro replikaci virtuální počítače VMware musí používat podporovaný operační systém. Kromě toho virtuálního počítače musí splňovat požadavky na virtuálních počítačích Azure. [Další informace](vmware-physical-azure-support-matrix.md##replicated-machines) v matici podpory.

## <a name="how-often-can-i-replicate-to-azure"></a>Jak často můžete replikovat do Azure?
Replikace je souvislý při replikaci virtuálních počítačů VMware do Azure.

## <a name="can-i-extend-replication"></a>Rozšiřte replikaci
Rozšířená nebo zřetězená replikace není podporována. Tato funkce v žádosti o [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Můžete provést offline počáteční replikaci?
Toto není podporováno. Tato funkce v žádosti [fóru pro zpětnou vazbu](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Můžete vyloučit disky?
Ano, můžete z replikace vyloučit disky. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Můžete replikovat virtuální počítače s dynamickými disky?
Dynamické disky, lze replikovat. Disk operačního systému musí být základní disk.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>Můžete přidat nový virtuální počítač do existující skupiny replikace?
Ano.

## <a name="configuration-server"></a>Konfigurace serveru

### <a name="what-does-the-configuration-server-do"></a>Jakým způsobem se konfigurační server?
Konfigurační server běží na místní součásti Site Recovery, včetně: 
- Konfigurace serveru, který koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- Procesový server, který funguje jako replikační brána. Obdrží data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování; a odešle ji Azure storage., které procesový server také nainstaluje služba Mobility na virtuálních počítačích, které chcete replikovat a provádí automatického zjišťování virtuálních počítačů VMware na místě.
- Hlavní cílový server, který zpracovává replikační data během navrácení služeb po obnovení z Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>Kde lze nastavit konfigurační server?
Potřebujete VMware jeden vysoce dostupný místní virtuální počítač pro konfigurační server.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Jaké jsou požadavky na konfigurační server?

Zkontrolujte [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Lze ručně nainstalovat systém konfigurační server místo pomocí šablony?
Doporučujeme vám, že používáte nejnovější verzi šablony OVF k [vytvořit konfigurační server virtuálních počítačů](vmware-azure-deploy-configuration-server.md). Pokud z nějakého důvodu není možné, například nemáte přístup k serveru VMware, můžete [stáhnout Unified instalační soubor](physical-azure-set-up-source.md) z portálu, a potom ho spusťte na virtuálním počítači. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Konfigurace serveru se můžou replikovat do více oblastí?
Ne. Chcete-li to provést, nastavení konfigurace serveru v každé oblasti.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Může hostovat konfigurační server v Azure?
Při možné by bylo nutné virtuální počítač Azure s konfigurační server komunikovat s vaší místní infrastruktury VMware a virtuálních počítačů. Nároky na pravděpodobně není přijatelná.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Kde lze získat nejnovější verzi šablony konfigurace serveru?
Stáhněte si nejnovější verzi z [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Jak aktualizovat konfigurační server?
Nainstalujete kumulativní aktualizace. Najdete nejnovější informace o aktualizaci v [wiki aktualizace stránky](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Služba Mobility

### <a name="where-can-i-find-the-mobility-service-installers"></a>Kde najdu instalační služba Mobility?
Instalační programy, které jsou uložené v **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** složky na konfiguračním serveru.

## <a name="how-do-i-install-the-mobility-service"></a>Instalace služby Mobility
Nainstalujete na každý virtuální počítač, který chcete replikovat, použití [nabízená instalace](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery), nebo ruční instalace z [rozhraní](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui), nebo [pomocí prostředí PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). Alternativně můžete nasadit pomocí nástroje pro nasazení, jako třeba [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md), nebo [Azure Automation a DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Zabezpečení

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Pro servery VMware jaká přístupová potřebuje Site Recovery?
Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Nastavení virtuálního počítače VMware s konfigurační server a další součásti Site Recovery na místě. [Další informace](vmware-azure-deploy-configuration-server.md)
- Automaticky zjistit virtuální počítače pro replikaci. Další informace o přípravě účet pro automatické zjišťování. [Další informace](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Na virtuální počítače VMware jaká přístupová potřebuje Site Recovery?

- Aby bylo možné replikovat, musí mít virtuální počítač VMware služba Mobility obnovení lokality nainstalovaná a spuštěná. Můžete nasadit nástroj ručně, nebo zadat Site Recovery proveďte nabízenou instalaci služby Když aktivujete replikaci pro virtuální počítač. Site Recovery pro nabízenou instalaci, musí účet, který můžete použít k instalaci součásti služby. [Další informace](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Proces serveru (s ve výchozím nastavení na konfiguračním serveru) provede této instalace. [Další informace](vmware-azure-install-mobility-service.md) o instalaci služby Mobility.
- Během replikace virtuálních počítačů komunikovat s Site Recovery následujícím způsobem:
    - Virtuální počítače komunikovat s konfigurační server na portu 443 protokolu HTTPS pro správu replikace.
    - Virtuální počítače odesílat data replikace na procesový server na port HTTPS 9443 (lze upravit).
    - Pokud povolíte konzistence více virtuálních počítačů, virtuálních počítačů vzájemně komunikovat přes port 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Posílají se replikační data Site Recovery?
Ne, Site Recovery nemá nezachycuje replikovaná data a nemá žádné informace o co běží na virtuální počítače. Replikační data se vyměňují mezi hypervisory VMware a úložiště Azure. Site Recovery nemá schopnost tato data zachytit. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  

Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikaci a probíhá vyhodnocování SOC2 a FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Jsme zachovat metadata místně v rámci zeměpisné oblasti?
Ano. Když v oblasti vytvoříte trezor, je zajištěno, že všechna metadata používat Site Recovery zůstane v této oblasti geografické hranice.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, jak šifrování během přenosu a [šifrování v Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.


## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení
### <a name="how-far-back-can-i-recover"></a>Jak daleko zpět můžete obnovit?
Bude nejstarší bod obnovení, které můžete použít pro VMware do Azure je 72 hodin.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Jak získám přístup k virtuálním počítačům Azure po převzetí služeb při selhání?
Po převzetí služeb při selhání můžete přístup k virtuálním počítačům Azure přes zabezpečené internetové připojení, přes síť site-to-site VPN nebo přes Azure ExpressRoute. Budete muset připravit existuje řada věcí, aby bylo možné připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Při selhání data odolná?
Služba Azure je pro odolnost navržena. Site Recovery je vytvořena pro převzetí služeb při selhání do sekundárního datacentra Azure, v souladu s smlouva Azure SLA. Když dojde k převzetí služeb při selhání, zajišťujeme, že metadata a trezory zůstávají ve stejné geografické oblasti, kterou jste zvolili pro svůj trezor.

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?
[Převzetí služeb při selhání](site-recovery-failover.md) neprobíhá automaticky. Zahajte převzetí služeb při selhání s jedním kliknutím na portálu, nebo můžete použít [ prostředí PowerShell](/powershell/module/azurerm.siterecovery) k aktivaci převzetí služeb při selhání.

### <a name="can-i-fail-back-to-a-different-location"></a>Může I navrácení služeb po obnovení do jiného umístění?
Ano, pokud při selhání do Azure můžete Pokud můžou selhat zpět do jiného umístění původního ten není k dispozici. [Další informace](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Proč musím síť VPN nebo ExpressRoute navrácení služeb po obnovení?

Pokud předáte služby zpět z Azure, dat z Azure se zkopíruje zpět na místní virtuální počítač a je nutný privátní přístup. 



## <a name="automation-and-scripting"></a>Automatizace a skriptování

### <a name="can-i-set-up-replication-with-scripting"></a>Můžete nastavit replikaci s skriptování?
Ano. Je možné automatizovat postupy workflow Site Recovery pomocí rozhraní Rest API, Powershellu nebo sadu Azure SDK. [Další](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Výkon a kapacita
### <a name="can-i-throttle-replication-bandwidth"></a>Můžete omezit šířku pásma replikace?
Ano. [Další informace](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Další postup
* [Zkontrolujte](vmware-physical-azure-support-matrix.md) podporu požadavků.
* [Nastavit](vmware-azure-tutorial.md) VMware do Azure replikace.
