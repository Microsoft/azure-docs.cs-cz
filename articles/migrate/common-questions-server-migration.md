---
title: Běžné otázky týkající se migrace Azure Migrate serveru
description: Získejte odpovědi na běžné otázky týkající se migrace počítačů pomocí migrace serveru Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425829"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrace Azure Migrate serveru: běžné otázky

Tento článek obsahuje odpovědi na běžné dotazy k nástroji Azure Migrate: Server Migration Tool. Pokud máte další otázky i po přečtení tohoto článku, přečtěte si tyto články:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- [Otázky](common-questions-appliance.md) týkající se zařízení Azure Migrate.
- [Dotazy](common-questions-discovery-assessment.md) týkající se vizualizace zjišťování, hodnocení a závislostí.
- Vystavení otázek na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Jak funguje replikace VMware bez agentů?

Metoda replikace bez agenta pro VMware používá snímky VMware a sledování změn (CBT) VMware.

1. Při spuštění replikace se naplánuje počáteční cyklus replikace. V počátečním cyklu se vybere snímek virtuálního počítače. Tento snímek se používá k replikaci virtuálních počítačů VMDK (discích). 
2. Po dokončení počátečního cyklu replikace se pravidelně naplánovaly rozdílové replikační cykly.
    - Během rozdílové replikace se pořídí snímek a bloky dat, které se od posledního replikačního cyklu změnily, se replikují.
    - VMware CBT slouží k určení bloků, které se od posledního cyklu změnily.
    - Frekvence pravidelných replikačních cyklů je automaticky spravovaná Azure Migrate v závislosti na tom, kolik dalších virtuálních počítačů a disků se souběžně replikuje ze stejného úložiště dat. V ideálních podmínkách se replikace nakonec konverguje na jeden cyklus za hodinu pro každý virtuální počítač.

Při migraci se pro tento počítač naplánuje cyklus replikace na vyžádání, která zachycuje zbývající data. Můžete zvolit vypnutí počítače během migrace, aby se zajistila nulová ztráta dat a konzistence aplikací.

## <a name="why-isnt-resynchronization-exposed"></a>Proč není opětovná synchronizace zveřejněna?

Při migraci bez agentů se v každém rozdílovém cyklu zapisuje rozdíl mezi aktuálním snímkem a dříve provedeným snímkem. Vzhledem k tomu, že je vždy rozdíl mezi snímky a skládáním dat v. Takže pokud se konkrétní sektor napíše N mezi snímky, stačí přenést jenom poslední zápis, protože vás zajímá jenom poslední synchronizace. To se liší od replikace založené na agentech, kde sledujeme a aplikujeme každý zápis. To znamená, že každý cyklus Delta je opakovaná synchronizace. Proto není k dispozici žádná možnost opětovné synchronizace. Pokud se disky v důsledku chyby nesynchronizují, je tato aktualizace vyřešena v dalším cyklu. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Jak četnost změn ovlivňuje replikaci bez agentů?

Vzhledem k tomu, že data přeložení bez agentů se přeloží, je model četnosti důležitější. Když se znovu zapíše a znovu vytvoří soubor, míra nebude mít žádný vliv. Vzor, ve kterém je zápis všech ostatních sektorů, ale způsobuje vysokou ztrátu v dalším cyklu. Vzhledem k tomu, že minimalizujeme objem přenášených dat, umožňujíme, aby data byla co nejvíc přeložena před plánováním dalšího cyklu.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak často je naplánováno replikační cyklus?

Vzorec pro naplánování dalšího cyklu replikace: (předchozí čas cyklu/2) nebo 1 hodina, podle toho, co je vyšší.

Pokud třeba virtuální počítač pro rozdílový cyklus trvá čtyři hodiny, další cyklus se naplánuje za dvě hodiny, a ne za příští hodinu. To se liší od počáteční replikace, při které je první cyklus rozdílu naplánován okamžitě.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>Vliv replikace bez agentů na servery VMware?

Na hostitelích vCenter Server/ESXi existuje nějaký dopad na výkon. Vzhledem k tomu, že replikace bez agentů používá snímky, spotřebovává IOPs za úložiště a vyžaduje se i některá šířka pásma úložiště IOPS. Pokud ve vašem prostředí existují omezení úložiště/IOPs, nedoporučujeme používat replikaci bez agentů.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Můžu bez agenta migrovat virtuální počítače UEFI do Azure Gen 2?

Ne. Pomocí Azure Site Recovery migrujte tyto virtuální počítače na virtuální počítače s 2. generace. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Můžu při migraci připnout virtuální počítače na Zóny dostupnosti Azure?

Ne, Zóny dostupnosti Azure se nepodporují.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Který transportní protokol používá Azure Migrate během replikace?

Azure Migrate používá protokol NBD (Network Block Device) se šifrováním SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Jaká je minimální vCenter Server verze, která se vyžaduje pro migraci?

Musíte mít aspoň vCenter Server 5,5 a VMware vSphere hostitele ESXi verze 5,5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Můžou zákazníci migrovat svoje virtuální počítače na nespravované disky?

Ne. Azure Migrate podporuje migraci jenom na spravované disky (standardní HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Kolik virtuálních počítačů je možné replikovat spolu s migrací bez agenta?

V současné době můžete migrovat 100 virtuálních počítačů na jeden vCenter Server současně. Migrace v dávkách s 10 virtuálními počítači
 



