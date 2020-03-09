---
title: Nejčastější dotazy k Azure Migrate
description: Získejte odpovědi na běžné otázky týkající se služby Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926735"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se Azure Migrate. Pokud máte dotazy i po přečtení tohoto článku, můžete je publikovat ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum). Můžete si také projít tyto články:

- Dotazy týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Dotazy týkající [se vizualizace zjišťování, hodnocení a závislostí](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Co je Azure Migrate?

Azure Migrate poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a privátních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci a nabídky nezávislých výrobců softwaru. [Další informace](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Co se dá dělat s Azure Migrate?

Pomocí Azure Migrate můžete zjišťovat, hodnotit a migrovat místní infrastrukturu, aplikace a data do Azure. Azure Migrate podporuje posuzování a migraci místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných virtuálních počítačů, databází, webových aplikací a virtuálních ploch. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Jaký je rozdíl mezi Azure Migrate a Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) poskytuje centralizované centrum pro posuzování a migraci do Azure. 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) je řešení zotavení po havárii. 

Azure Migrate: Nástroj pro migraci serveru používá některé funkce back-Site Recovery Endu pro migraci některých místních počítačů do migrace typu "a Shift".

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaký je rozdíl mezi Azure Migrate: posouzení serveru a MAPOVou Toolkit?

Posouzení serveru poskytuje hodnocení, které vám pomůžou s připraveností na migraci, a vyhodnocení úloh pro migraci do Azure. [Sada nástrojů Microsoft Assessment and Planning (map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) pomáhá s dalšími úkoly, včetně plánování migrace novějších verzí klientských a serverových operačních systémů Windows a sledování využívání softwaru. V těchto scénářích pokračujte v používání sady MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaký je rozdíl mezi posuzováním serveru a Site Recovery Plánovač nasazení?

Posuzování serveru je nástroj pro plánování migrace. Plánovač nasazení Site Recovery je nástroj pro plánování zotavení po havárii.

Vyberte si nástroj podle toho, co chcete udělat:

- **Plánování místní migrace do Azure**: Pokud plánujete migrovat místní servery do Azure, použijte k plánování migrace server hodnocení. Posouzení serveru vyhodnocuje místní úlohy a poskytuje pokyny a nástroje, které vám pomůžou s migrací. Po dokončení plánu migrace můžete použít nástroje jako Azure Migrate: Migrace serveru pro migraci počítačů do Azure.
- **Plánování zotavení po havárii do Azure**: Pokud plánujete nastavovat zotavení po havárii z místního prostředí do azure pomocí Site Recovery, použijte Plánovač nasazení Site Recovery. Plánovač nasazení poskytuje hloubkové a Site Recovery vyhodnocení místního prostředí pro účely zotavení po havárii. Poskytuje doporučení týkající se zotavení po havárii, jako je například replikace a převzetí služeb při selhání.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Jak funguje migrace serveru s Site Recovery?

- Pokud používáte Azure Migrate: Migrace serveru k provedení migrace místních virtuálních počítačů VMware bez *agenta* , je migrace nativní pro Azure Migrate a Site Recovery se nepoužívá.
- Pokud používáte Azure Migrate: Migrace serveru k provedení migrace virtuálních počítačů VMware *na základě agenta* , nebo pokud migrujete virtuální počítače Hyper-V nebo fyzické servery, Azure Migrate: Migrace serveru používá Azure Site Recovery replikační modul.

## <a name="which-geographies-are-supported"></a>Které geografické oblasti jsou podporovány?

- **Virtuální počítače VMware**: Projděte si Azure Migrate [podporovaných geografických](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) oblastí pro virtuální počítače VMware.
- **Virtuální počítače Hyper-v**: přečtěte si Azure Migrate [podporovaných geografických oblastech](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v) pro virtuální počítače Hyper-v.

## <a name="how-do-i-get-started"></a>Jak mám začít?

Identifikujte potřebný nástroj a pak přidejte nástroj do projektu Azure Migrate. 

Postup přidání nástroje nebo stěhovací společnosti ISV:

1. Začněte získáním licence nebo si zaregistrujte bezplatnou zkušební verzi v souladu se zásadami nástroje. Licencování pro nástroje je v souladu s modelem licencování ISV nebo nástrojů.
2. V každém nástroji je k dispozici možnost připojit se k Azure Migrate. Použijte pokyny a dokumentaci k nástroji k připojení nástroje k Azure Migrate.

Svou cestu migrace můžete sledovat z Azure Migrateho projektu v rámci Azure a v jiných nástrojích.

## <a name="how-do-i-delete-a-project"></a>Návody odstranit projekt?

Přečtěte si, jak [Odstranit projekt](how-to-delete-project.md). 

## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
