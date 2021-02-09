---
title: Nejčastější dotazy k Azure Migrate
description: Získejte odpovědi na běžné otázky týkající se služby Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 6396ca4483f5e9782890123a7c39a0cbcdbd30a3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832568"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se Azure Migrate. Pokud máte dotazy i po přečtení tohoto článku, můžete je publikovat ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum). Můžete si také projít tyto články:

- Dotazy týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Dotazy týkající [se vizualizace zjišťování, hodnocení a závislostí](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Co je Azure Migrate?

Azure Migrate poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a privátních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci a nabídky nezávislých výrobců softwaru. [Přečtěte si další informace](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Co se dá dělat s Azure Migrate?

Pomocí Azure Migrate můžete zjišťovat, hodnotit a migrovat místní infrastrukturu, aplikace a data do Azure. Azure Migrate podporuje posuzování a migraci místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných virtuálních počítačů, databází, webových aplikací a virtuálních ploch. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Jaký je rozdíl mezi Azure Migrate a Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) poskytuje centralizované centrum pro posuzování a migraci do Azure. 

- Použití Azure Migrate zajišťuje interoperabilitu a budoucí rozšiřitelnost pomocí nástrojů Azure Migrate, dalších služeb Azure a nástrojů třetích stran.
- Azure Migrate: Nástroj pro migraci serveru je založený na účelu navržený pro migraci serveru do Azure. Je optimalizovaná pro migraci. Nemusíte se učit o konceptech a scénářích, které nejsou přímo důležité pro migraci. 
- Pro migraci na 180 dní se neúčtují žádné poplatky za použití nástrojů od spuštění replikace pro virtuální počítač. Poskytuje čas k dokončení migrace. Platíte jenom za úložiště a síťové prostředky, které se používají při replikaci, a za poplatky za výpočetní výkon spotřebované během testovacích migrací.
- Azure Migrate podporuje všechny scénáře migrace podporované Site Recovery. Pro virtuální počítače VMware Azure Migrate taky nabízí možnost migrace bez agenta.
- Pro Azure Migrate nabízíme prioritní nové funkce, které nabízí jenom nástroj pro migraci serveru. Tyto funkce nejsou cílené na Site Recovery.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) by se měly používat jenom pro zotavení po havárii.

Azure Migrate: Nástroj pro migraci serveru používá některé funkce back-Site Recovery Endu pro migraci některých místních počítačů do migrace typu "a Shift".

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Mám projekt s předchozím klasickým prostředím Azure Migrate. Návody začít používat novou verzi?

Vyřazení z klasického Azure Migrate do února 2024. Po 2024. února již nebude klasická verze Azure Migrate podporována a metadata inventáře v klasickém projektu budou odstraněna. Nemůžete upgradovat projekty nebo součásti v předchozí verzi na novou verzi. Musíte [vytvořit nový Azure Migrate projekt](create-manage-projects.md)a přidat do něj [Nástroje pro posouzení a migraci](./create-manage-projects.md) . Pomocí kurzů můžete pochopit, jak jsou dostupné nástroje pro posouzení a migraci. Pokud byl pracovní prostor Log Analytics připojený k klasickému projektu, můžete ho po odstranění klasického projektu připojit k projektu aktuální verze.

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

Projděte si podporované oblasti pro [veřejný cloud](migrate-support-matrix.md#supported-geographies-public-cloud) a [cloud pro státní správu](migrate-support-matrix.md#supported-geographies-azure-government).

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