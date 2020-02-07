---
title: Běžné otázky týkající se migrace Azure Migrate serveru
description: Získejte odpovědi na běžné otázky týkající se migrace Azure Migrate serveru.
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067380"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrace Azure Migrate serveru: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se Azure Migrate: Migrace serveru. Pokud máte další dotazy po přečtení tohoto článku, publikujte je na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum). Pokud máte další otázky, přečtěte si tyto články:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- [Otázky](common-questions-appliance.md) týkající se zařízení Azure Migrate.
- [Dotazy](common-questions-discovery-assessment.md) týkající se vizualizace zjišťování, hodnocení a závislostí.


## <a name="how-does-agentless-vmware-replication-work"></a>Jak funguje replikace VMware bez agentů?

Metoda replikace bez agenta pro VMware používá snímky VMware a sledování změn (CBT) VMware. Počáteční cyklus replikace je naplánován, když uživatel spustí replikaci. V cyklu počáteční replikace se pořídí snímek virtuálního počítače a tento snímek se používá k replikaci virtuálních počítačů VMDK (discích). Po dokončení počátečního cyklu replikace se pravidelně naplánovaly rozdílové replikační cykly. V cyklu rozdílové replikace se pořídí snímek a bloky dat, které se od posledního replikačního cyklu změnily, se replikují. Pro určení bloků, které se od posledního cyklu změnily, se používá sledování bloků se změněným VMware.
Frekvence pravidelných replikačních cyklů je automaticky spravovaná službou v závislosti na tom, kolik dalších virtuálních počítačů a disků se souběžně replikuje mimo stejné úložiště dat. v ideálním případě se nakonec konverguje na 1 cyklus za hodinu a jeden virtuální počítač.

Při migraci je naplánováno, aby virtuální počítač mohl zachytit zbývající data v cyklu replikace na vyžádání. Můžete zvolit, že se má virtuální počítač vypnout jako součást migrace, aby se zajistila nulová ztráta dat a konzistence aplikací.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Proč se možnost opětovné synchronizace nezveřejňuje v zásobníku bez agenta?

V zásobníku bez agentů se v každém rozdílovém cyklu převádí rozdíl mezi aktuálním a předchozím snímkem, který jsme udělali. Vzhledem k tomu, že je vždy rozdíl mezi snímky, to znamená, že tato výhoda překládá data (tj. Pokud je v určitém sektoru napsaný počet n časů mezi snímky), musíme pouze přenést poslední zápis, protože vás zajímá jenom poslední synchronizace. To se liší od zásobníku založeného na agentech, ve kterém sledujeme všechny zápisy a používají je. To znamená, že každý cyklus Delta je opakovaná synchronizace. Proto není k dispozici žádná možnost opětovné synchronizace. 

Pokud se disky nesynchronizují z důvodu chyby, je tato procedura opravena v dalším cyklu. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Jaký je dopad četnosti změn, když používám replikaci bez agentů?

Vzhledem k tomu, že je zásobník závislý na dat, která jsou složena, je vzor změn v tomto zásobníku. Vzor, ve kterém se soubor znovu zapisuje a nemá velký vliv. Vzor, ve kterém je zápis všech ostatních sektorů, bude ale v dalším cyklu způsobovat velkou ztrátu. Vzhledem k tomu, že minimalizujeme objem přenášených dat, umožňujíme, aby data byla co nejvíc přeložena před plánováním dalšího cyklu.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak často je naplánováno replikační cyklus?

Vzorec pro naplánování dalšího cyklu replikace je následující: (předchozí čas cyklu/2) nebo 1 hodina podle toho, co je vyšší. Pokud třeba virtuální počítač pro rozdílový cyklus trval čtyři hodiny, naplánujeme si další cyklus během 2 hodin, ale ne během příští hodiny. To se liší v případě, že je cyklus hned za IR, kde se okamžitě naplánuje první rozdílový cyklus.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Jaký je dopad na výkon vCenter Server nebo hostitele ESXi při použití replikace bez agentů?

Vzhledem k tomu, že replikace bez agentů používá snímky, bude pro úložiště existovat spotřeba IOPs a zákazníci budou potřebovat určitou rezervu IOPs v úložišti. Tento zásobník nedoporučujeme používat na omezeném prostředí úložiště/IOPs.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>Podporuje zásobník pro přenos bez agentů migraci virtuálních počítačů s rozhraním UEFI na virtuální počítače Azure Gen 2?

Ne, k migraci těchto virtuálních počítačů na virtuální počítače s 2. generace je nutné použít Azure Site Recovery. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Můžu při migraci připnout své virtuální počítače na Zóny dostupnosti Azure?

Ne, podpora pro Zóny dostupnosti Azure není k dispozici.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Který transportní protokol používá Azure Migrate během replikace?

Azure Migrate používá protokol NBD (Network Block Device) se šifrováním SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Jaká je minimální vCenter Server verze, která se vyžaduje pro migraci?

Musíte mít aspoň vCenter Server 5,5 a VMware vSphere hostitele ESXi verze 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Můžou zákazníci migrovat svoje virtuální počítače na nespravované disky?

Ne. Azure Migrate podporuje migraci jenom na spravované disky (standardní HDD, Premium SSD).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Kolik virtuálních počítačů může současně replikovat pomocí agenta VMware bez agentů?

V současné době můžou zákazníci migrovat 100 virtuálních počítačů na vCenter Server současně. To se dá udělat v dávkách s 10 virtuálními počítači.




