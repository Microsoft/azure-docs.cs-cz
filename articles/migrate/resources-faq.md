---
title: Nejčastější dotazy k migraci do Azure
description: Získejte odpovědi na běžné otázky týkající se služby Migrace Azure.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926735"
---
# <a name="azure-migrate-common-questions"></a>Migrace Azure: Běžné otázky

Tento článek odpovídá na běžné otázky týkající se migrace Azure. Pokud máte otázky po přečtení tohoto článku, můžete je zveřejnit ve [fóru Migrace Azure](https://aka.ms/AzureMigrateForum). Můžete si také prohlédnout tyto články:

- Otázky týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Otázky týkající se [vizualizace zjišťování, hodnocení a závislosti](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Co je Azure Migrate?

Azure Migrate poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace místních aplikací a úloh a virtuálních počítačích privátního a veřejného cloudu do Azure. Centrum poskytuje nástroje pro migraci Azure pro hodnocení a migraci a nabídky nesmějovačů isv třetích stran. [Další informace](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Co můžu dělat s Azure Migrate?

Pomocí Migrace Azure můžete do Azure zjišťovat, vyhodnocovat a migrovat místní infrastrukturu, aplikace a data. Azure Migrate podporuje vyhodnocení a migraci místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných virtuálních počítačů, databází, webových aplikací a virtuálních desktopů. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Jaký je rozdíl mezi migrací Azure a Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) poskytuje centralizované centrum pro hodnocení a migraci do Azure. 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) je řešení zotavení po havárii. 

Nástroj Migrace Azure: Migrace serveru používá některé funkce back-end site recovery pro migraci některých místních počítačů.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaký je rozdíl mezi migrací Azure: Vyhodnocení serveru a sadou nástrojů MAP?

Vyhodnocení serveru poskytuje hodnocení, které vám pomůže s připraveností na migraci a vyhodnocením úloh pro migraci do Azure. Sada [nástrojů Microsoft Assessment and Planning (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) pomáhá s dalšími úkoly, včetně plánování migrace pro novější verze klientských a serverových operačních systémů systému Windows a sledování využití softwaru. V těchto scénářích pokračujte v používání sady mapových nástrojů.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaký je rozdíl mezi vyhodnocením serveru a plánovačem nasazení obnovení webu?

Vyhodnocení serveru je nástroj pro plánování migrace. Plánovač nasazení obnovení lokality je nástroj pro plánování zotavení po havárii.

Vyberte si nástroj na základě toho, co chcete udělat:

- **Plánování místní migrace do Azure**: Pokud plánujete migrovat místní servery do Azure, použijte pro plánování migrace serverové hodnocení. Hodnocení serveru vyhodnocuje místní úlohy a poskytuje pokyny a nástroje, které vám pomohou migrovat. Po plánu migrace je na místě, můžete použít nástroje, jako je Migrace Azure: Migrace serveru k migraci počítačů do Azure.
- **Plánování zotavení po havárii do Azure:** Pokud plánujete nastavit zotavení po havárii z místního do Azure s site recovery, použijte Plánovač nasazení obnovení webu. Plánovač nasazení poskytuje podrobné posouzení místního prostředí specifické pro obnovení webu za účelem zotavení po havárii. Poskytuje doporučení týkající se zotavení po havárii, jako je například replikace a převzetí služeb při selhání.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Jak migrace serveru funguje s obnovením webu?

- Pokud používáte Migraci Azure: Migrace serveru k provedení migrace místních virtuálních počítačů VMware *bez agenta,* migrace se narodí na Azure Migrate a obnovení webu se nepoužívá.
- Pokud používáte Azure Migrate: Migrace serveru k provedení migrace virtuálních počítačů VMware *na základě agenta* nebo pokud migrujete virtuální počítače Hyper-V nebo fyzické servery, migrace Azure: Migrace serveru používá replikační modul Azure Site Recovery.

## <a name="which-geographies-are-supported"></a>Které zeměpisné oblasti jsou podporovány?

- **Virtuální počítače VMware**: Zkontrolujte podporované [geografické oblasti](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) Azure Migrate pro virtuální počítače VMware.
- **Virtuální počítače Hyper-V**: Zkontrolujte podporované [geografické oblasti](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v) Azure pro virtuální počítače Hyper-V.

## <a name="how-do-i-get-started"></a>Jak mám začít?

Identifikujte nástroj, který potřebujete, a pak ho přidejte do projektu Migrace Azure. 

Přidání nástroje ISV nebo movere:

1. Můžete začít získáním licence nebo se zaregistrovat k bezplatné zkušební verzi v souladu se zásadami nástroje. Licencování nástrojů je v souladu s isv nebo nástroj licenční model.
2. V každém nástroji je možnost připojení k Azure Migrate. Podle pokynů k nástroji a dokumentace připojte nástroj k Azure Migrate.

Cestu migrace můžete sledovat z projektu Migrace Azure, přes Azure a v dalších nástrojích.

## <a name="how-do-i-delete-a-project"></a>Jak odstraním projekt?

Přečtěte si, jak [odstranit projekt](how-to-delete-project.md). 

## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Migrace Azure](migrate-services-overview.md).
