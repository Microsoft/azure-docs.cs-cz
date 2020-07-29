---
title: Běžné otázky týkající se migrace Azure Migrate serveru
description: Získejte odpovědi na běžné otázky týkající se použití migrace serveru Azure Migrate k migraci počítačů.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: af40aecaa1614542074cf87ce95eb81492233bdc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321220"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrace Azure Migrate serveru: běžné otázky

Tento článek obsahuje odpovědi na běžné dotazy k nástroji Azure Migrate: Server Migration Tool. Pokud máte další otázky, ověřte tyto prostředky:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- Dotazy týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Dotazy týkající [se vizualizace zjišťování, hodnocení a závislostí](common-questions-discovery-assessment.md)
- Získání otázek zodpovězených ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Jaké geografické oblasti jsou podporovány pro migraci pomocí Azure Migrate?

Projděte si podporované oblasti pro [veřejný cloud](migrate-support-matrix.md#supported-geographies-public-cloud) a [cloud pro státní správu](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-does-agentless-vmware-replication-work"></a>Jak funguje replikace VMware bez agentů?

Metoda replikace bez agenta pro VMware používá snímky VMware a sledování změn ve službě VMware (CBT).

Postup je následující:

1. Při spuštění replikace se naplánuje počáteční cyklus replikace. V počátečním cyklu se vybere snímek virtuálního počítače. Snímek se používá k replikaci virtuálních počítačů VMDK (discích). 
2. Po dokončení počátečního cyklu replikace se pravidelně naplánovaly rozdílové replikační cykly.
    - Během rozdílové replikace se pořídí snímek a bloky dat, které se od posledního replikačního cyklu změnily, se replikují.
    - VMware CBT slouží k určení bloků, které se od posledního cyklu změnily.
    - Frekvence pravidelných replikačních cyklů je automaticky spravovaná pomocí Azure Migrate a závisí na tom, kolik dalších virtuálních počítačů a disků se souběžně replikuje ze stejného úložiště dat. V ideálních podmínkách se replikace nakonec konverguje na jeden cyklus za hodinu pro každý virtuální počítač.

Při migraci je naplánováno, aby počítač mohl zachytit zbývající data v cyklu replikace na vyžádání. Aby se zajistila nulová ztráta dat a konzistence aplikací, můžete zvolit vypnutí počítače během migrace.

## <a name="why-isnt-resynchronization-exposed"></a>Proč není opětovná synchronizace zveřejněna?

Při migraci bez agenta se v každém rozdílovém cyklu zapisuje rozdíl mezi aktuálním a předchozím snímkem. Vždy se jedná o rozdíl mezi snímky a skládáním dat v. Pokud se konkrétní sektor napíše *N* mezi snímky, stačí přenést jenom poslední zápis, protože vás zajímá jenom poslední synchronizace. Tento proces se liší od replikace založené na agentech, během které sledujeme a aplikujeme každý zápis. V tomto procesu je každý cyklus rozdílů opakovanou synchronizací. Takže není dostupná žádná možnost opětovné synchronizace. Pokud se disky v tuto minulosti nesynchronizují kvůli selhání, bude opraveno v dalším cyklu. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Jak četnost změn ovlivňuje replikaci bez agentů?

Vzhledem k tomu, že replikace bez agentů je přeložena v datech, je *vzor* změn důležitější než *frekvence četnosti*změn. Když se znovu zapíše a znovu vytvoří soubor, míra nebude mít žádný vliv. Vzor, ve kterém je zápis všech ostatních sektorů, ale způsobuje vysokou ztrátu v dalším cyklu. Vzhledem k tomu, že minimalizujeme objem přenášených dat, umožňujíme, aby data byla přeložena co nejvíce, než naplánujeme další cyklus.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak často je naplánováno replikační cyklus?

Vzorec k naplánování dalšího cyklu replikace je (předchozí čas cyklu/2) nebo jedna hodina, podle toho, co je vyšší.

Pokud třeba virtuální počítač pro rozdílový cyklus trvá čtyři hodiny, další cyklus se naplánuje za dvě hodiny, a ne za příští hodinu. Proces se liší od počáteční replikace, když je první cyklus rozdílu naplánován okamžitě.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Vliv replikace bez agentů na servery VMware?

Replikace bez agentů vede k nějakým dopadům na výkon VMware vCenter Server a VMware ESXi hostitelů. Vzhledem k tomu, že replikace bez agenta používá snímky, spotřebovává v úložišti IOPS, takže se vyžaduje některá šířka pásma úložiště IOPS. Pokud máte ve svém prostředí omezení úložiště nebo IOPs, nedoporučujeme používat replikaci bez agentů.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Můžu bez agenta migrovat virtuální počítače UEFI do Azure Gen 2?

Ne. Pomocí Azure Site Recovery migrujte tyto virtuální počítače na virtuální počítače s 2. generace. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Můžu při migraci připnout virtuální počítače na Zóny dostupnosti Azure?

Ne. Zóny dostupnosti Azure se pro migraci Azure Migrate nepodporuje.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Jaký transportní protokol Azure Migrate použít během replikace?

Azure Migrate používá protokol NBD (Network Block Device) s šifrováním TLS.

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Jak se data přenáší z prostředí Prem do Azure? Je před přenosem zašifrovaný? 
Zařízení Azure Migrate v případě replikace bez agenta komprimuje data a šifruje před odesláním. Data se přenáší přes protokol HTTPS přes zabezpečený komunikační kanál a používají TLS 1,2 nebo novější. Navíc Azure Storage automaticky šifruje vaše data při jejich trvalém ukládání do cloudu (šifrování v klidovém prostředí).  

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Jaká je minimální vCenter Server verze, která se vyžaduje pro migraci?

Musíte mít minimálně vCenter Server 5,5 a hostitele ESXi verze 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Můžou zákazníci migrovat svoje virtuální počítače na nespravované disky?

Ne. Azure Migrate podporuje migraci jenom na spravované disky (HDD úrovně Standard, SSD úrovně Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Kolik virtuálních počítačů je možné najednou replikovat pomocí migrace bez agentů?

V současné době můžete migrovat 300 virtuálních počítačů na instanci vCenter Server současně. Migrace v dávkách s 10 virtuálními počítači

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Návody omezení replikace při používání zařízení Azure Migrate pro bezagentskou replikaci VMware?  

Můžete omezit pomocí NetQosPolicy. Například:

AppNamePrefix pro použití v NetQosPolicy je "GatewayWindowsService.exe". Můžete vytvořit zásadu na zařízení Azure Migrate, abyste omezili provoz replikace ze zařízení tím, že vytvoříte zásadu, jako je tato:
 
New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>Můžu migrovat virtuální počítače, které už jsou replikované do Azure? 

Pokud se virtuální počítače už do Azure replikují jiným způsobem, nemůžete tyto počítače migrovat jako virtuální počítače pomocí migrace serveru Azure Migrate. Alternativním řešením je, že virtuální počítače můžete považovat za fyzické servery a migrovat je v souladu s [podporovanou migrací fyzického serveru](migrate-support-matrix-physical-migration.md).

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Kdy můžu migrovat počítače jako fyzické servery?

Migrace počítačů jejich použitím jako fyzických serverů je užitečná v různých scénářích:

- Migrace místních fyzických serverů.
- Pokud migrujete virtuální počítače virtualizované pomocí platforem, jako je například Xen, KVM.
- Migrace virtuálních počítačů Hyper-V nebo VMware, pokud z nějakého důvodu nemůžete použít standardní proces migrace pro [Hyper-v](tutorial-migrate-hyper-v.md)nebo migraci [VMware](server-migrate-overview.md) . Například pokud nepoužíváte VMware vCenter a používáte jenom hostitele ESXi.
- Migrace virtuálních počítačů, které jsou aktuálně spuštěné v privátních cloudech do Azure
- Pokud chcete migrovat virtuální počítače běžící ve veřejných cloudech, jako je Amazon Web Services (AWS) nebo Google Cloud Platform (GCP), do Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Nasadil (a) jsem dva zařízení pro zjišťování virtuálních počítačů v mém vCenter Server. Při pokusu o migraci virtuálních počítačů se ale zobrazí jenom virtuální počítače, které odpovídají jednomu ze zařízení.

Pokud je nastavené více zařízení, je nutné, aby se mezi virtuálními počítači na dostupných účtech vCenter překrývat. Zjišťování s takovým překrytím je nepodporovaný scénář.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Potřebuji pro migraci virtuálních počítačů VMware Server VMware vCenter?
Pokud chcete [migrovat virtuální počítače VMware](server-migrate-overview.md) pomocí agenta VMware nebo migrace bez agentů, musí být hostitelé ESXi, na kterých jsou virtuální počítače umístěné, spravované pomocí vCenter Server. Pokud nemáte vCenter Server, můžete migrovat virtuální počítače VMware tak, že je migrujete jako fyzické servery. [Přečtěte si další informace](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
