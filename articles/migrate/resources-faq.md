---
title: Běžné otázky týkající se Azure Migrate
description: Získejte odpovědi na běžné otázky týkající se služby Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: 8bfa9237d365636c0bdaa3af06c5af23b683231d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563531"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se Azure Migrate. Pokud máte další dotazy po přečtení tohoto článku, publikujte je na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum). Pokud máte další otázky, přečtěte si tyto články:

- [Otázky](common-questions-appliance.md) týkající se zařízení Azure Migrate.
- [Dotazy](common-questions-discovery-assessment.md) týkající se vizualizace zjišťování, hodnocení a závislostí.


## <a name="what-is-azure-migrate"></a>Co je Azure Migrate?

Azure Migrate poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. [Další informace](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>Co se dá dělat s Azure Migrate?

Pomocí Azure Migrate můžete zjišťovat, hodnotit a migrovat místní infrastrukturu, aplikace a data do Azure. Azure Migrate podporuje posuzování a migraci místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných virtuálních počítačů, databází, webových aplikací a virtuálních ploch. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Jaký je rozdíl mezi Azure Migrate a Site Recovery?

Azure Migrate poskytuje centralizované centrum pro posuzování a migraci do Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) je řešení zotavení po havárii. Azure Migrate: Nástroj pro migraci serveru používá některé funkce back-endu Site Recovery pro migraci některých místních počítačů pomocí přebírání a posunu.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaký je rozdíl mezi Azure Migrate posuzování serveru a mapou sady nástrojů?

Posouzení serveru poskytuje hodnocení, které vám pomůžou s připraveností na migraci, a vyhodnocení úloh pro migraci do Azure. [Sada nástrojů Microsoft Assessment and Planning (map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) pomáhá s dalšími úkoly, včetně plánování migrace pro novější verze klientských a serverových operačních systémů Windows a sledování využívání softwaru. V těchto scénářích pokračujte v používání sady MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaký je rozdíl mezi posuzováním serveru a Site Recovery Plánovač nasazení?

Posuzování serveru je nástroj pro plánování migrace. Plánovač nasazení Site Recovery je nástroj pro plánování zotavení po havárii.

- **Plánování místní migrace do Azure**: Pokud plánujete migrovat místní servery do Azure, použijte k plánování migrace server hodnocení. Vyhodnocuje místní úlohy a poskytuje pokyny a nástroje, které vám pomůžou s migrací. Po dokončení plánu migrace můžete k migraci počítačů do Azure použít nástroje, včetně migrace Azure Migrate serveru.
- **Plánování zotavení po havárii do Azure**: Pokud plánujete nastavovat zotavení po havárii z místního prostředí do azure pomocí Site Recovery, použijte Plánovač nasazení Site Recovery. Plánovač nasazení poskytuje hloubkové a Site Recovery vyhodnocení místního prostředí pro účely zotavení po havárii. Poskytuje doporučení týkající se zotavení po havárii, jako je například replikace a převzetí služeb při selhání.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Jak funguje migrace serveru s Site Recovery?

- Pokud používáte Azure Migrate: Migrace serveru k provedení migrace místních virtuálních počítačů VMware bez agenta, je migrace nativní na Azure Migrate a Site Recovery se nepoužívá.
- Pokud používáte Azure Migrate: Migrace serveru k provedení migrace virtuálních počítačů VMware na základě agenta nebo migraci virtuálních počítačů Hyper-V nebo fyzických serverů, pak Azure Migrate migrace serveru používá Azure Site Recovery replikační modul.


## <a name="which-geographies-are-supported"></a>Které geografické oblasti jsou podporovány?

Projděte si Azure Migrate podporovaných geografických oblastí pro [virtuální počítače VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) a pro [virtuální počítače Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="how-do-i-get-started"></a>Jak začít?

Identifikujte potřebný nástroj a přidejte ho do projektu Azure Migrate. Pokud přidáváte nástroj ISV nebo stěhovací společnost:
- Začněte získáním licence nebo registrací k bezplatné zkušební verzi v souladu se zásadami nástroje. Licencování pro nástroje je v souladu s modelem licencování ISV nebo nástrojů.
- V každém nástroji je k dispozici možnost připojit se k Azure Migrate. Použijte pokyny a dokumentaci k nástroji pro připojení nástroje k Azure Migrate.
Svou cestu k migraci můžete centrálně sledovat v rámci Azure Migrateho projektu v rámci Azure a dalších nástrojů.

### <a name="how-do-i-delete-a-project"></a>Návody odstranit projekt?

[Přečtěte si, jak](how-to-delete-project.md) odstranit projekt. 






## <a name="next-steps"></a>Další kroky
Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
