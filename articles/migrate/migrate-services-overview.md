---
title: Informace o službě Azure Migrate | Microsoft Docs
description: Obsahuje přehled služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: eadfc4e302f4445e8e0d5105aeac4361328cb259
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715451"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje rychlý přehled Azure Migrate.

Azure Migrate vám pomůže migrovat vaši firmu z místního prostředí do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure.  Azure Migrate poskytuje:

- **Sjednocená platforma pro migraci**: jeden portál, který umožňuje spustit, spustit a sledovat cestu migrace do Azure.
- **Rozsah nástrojů**: centrum poskytuje hodnocení a migraci nástrojů Azure Migrate a integruje se s dalšími službami Azure, a to i dalšími nástroji a nabídkami nezávislého výrobce softwaru (ISV).
- **Úlohy**: Azure Migrate poskytuje vyhodnocení a migraci pro:
    - **Servery**: pro účely posouzení a migrace serverů do virtuálních počítačů Azure použijte Azure Migrate vyhodnocování serveru, migraci Azure Migrate serveru a další nástroje.
    - **Databáze**: Využijte nástroje Microsoftu a ISV pro posouzení a migraci místních databází do Azure SQL DB nebo spravované instance Azure SQL.
    - **Webové aplikace**: použijte pomocníka Azure App Service k vyhodnocení a migraci místních webových aplikací do Azure App Service.
    - **Virtuální plochy**: pomocí nástrojů ISV můžete vyhodnotit a migrovat místní infrastrukturu virtuálních klientských počítačů (VDI) na virtuální plochu Windows v Azure.
    - **Data**: k rychlé a cenově možné migraci velkých objemů dat do Azure využijte Azure Data box rodinu produktů.

## <a name="azure-migrate-versions"></a>Verze Azure Migrate

V současné době existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Tato verze slouží k vytváření Azure Migrate projektů, zjišťování místních počítačů a orchestraci posouzení a migrace. [Přečtěte si další](whats-new.md) informace o novinkách v této verzi.
- **Předchozí verze**: Pokud jste použili předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. Už nemůžete vytvářet Azure Migrate projekty pomocí předchozí verze a nedoporučujeme provádět nové zjišťování. Chcete-li získat přístup ke stávajícím projektům, ve Azure Portal > **všechny služby**vyhledejte **Azure Migrate**. Na řídicím panelu Azure Migrate existuje oznámení a odkaz pro přístup k původním Azure Migratem projektům.



## <a name="isv-integration"></a>Integrace ISV

Kromě nativních nástrojů Azure se Azure Migrate integruje s řadou nabídek nezávislých výrobců softwaru. 

**ISV** | **Funkce**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrace
[Cloudamize](https://www.cloudamize.com/platform) | Posouzení 
[Technologie spolupronájmu](https://www.corenttech.com/AzureMigrate/) | Posouzení a migrace
[Zařízení 42](https://docs.device42.com/) | Posouzení
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posouzení
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrace
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Posouzení
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Posouzení

## <a name="tool-integration"></a>Integrace nástrojů

Tabulka shrnuje jiné nástroje, které jsou integrované v Azure Migrate.

**Nástroj** | **Podrobnosti**
--- | ---
Azure Migrate: posouzení serveru | Vyhodnotit servery
Azure Migrate: Migrace serveru | Migrace serverů
Pomocník s migrací databáze (DMA) | Vyhodnocení databází
Database Migration Service (DMS) | Migrace databází
Movere | Vyhodnotit servery
Pomocník s migrací webové aplikace | Posouzení a migrace webových aplikací



### <a name="selecting-a-tool"></a>Výběr nástroje

Identifikujte potřebný nástroj a přidejte ho do projektu Azure Migrate.

- Pokud přidáváte nástroj ISV nebo stěhovací společnost:
    - Začněte získáním licence nebo registrací k bezplatné zkušební verzi v souladu se zásadami nástroje. Licencování pro nástroje je v souladu s modelem licencování ISV nebo nástrojů.
    - V každém nástroji je k dispozici možnost připojit se k Azure Migrate. Použijte pokyny a dokumentaci k nástroji pro připojení nástroje k Azure Migrate.
- Svou cestu k migraci můžete centrálně sledovat v rámci Azure Migrateho projektu v rámci Azure a dalších nástrojů.



## <a name="azure-migrate-server-assessment-tool"></a>Nástroj pro vyhodnocení serveru Azure Migrate

Azure Migrate: Nástroj pro vyhodnocení serveru zjišťuje a vyhodnocuje místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery pro migraci do Azure. Pomůže vám identifikovat následující:

- **Připravenost na Azure:** Posuzuje, jestli jsou místní počítače připravené k migraci do Azure.
- **Velikost pro Azure:** Odhadovaná velikost virtuálních počítačů Azure po migraci
- **Odhad nákladů Azure:** Odhadované náklady na provoz místních serverů v Azure.
- **Vizualizace závislostí:** Závislosti mezi servery (Pokud je povolená vizualizace závislosti) a optimální způsob přesunutí závislých serverů do Azure.

Posouzení serveru používá odlehčené zařízení, které nasazujete místně, a zaregistrujte se pomocí posouzení serveru.

- Zařízení zjišťuje místní počítače.
- Připojuje se k posouzení serveru a průběžně odesílá data o metadatech a výkonu počítačů do Azure Migrate.
- Zjišťování zařízení je bez agenta. Na zjištěných počítačích není potřeba nic instalovat.
- Po zjištění se shromažďují zjištěné počítače do skupin. Obvykle se shromáždí společně počítače, které chcete migrovat dohromady.
- Vytvoříte posouzení pro skupinu. Pak analyzujete posouzení a zjistěte strategii migrace.

## <a name="azure-migrate-server-migration-tool"></a>Nástroj pro migraci serverů Azure Migrate

Azure Migrate: Nástroj pro migraci serveru vám pomůže migrovat místní virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery, další virtualizované počítače a veřejné cloudové virtuální počítače do Azure. Po posouzení můžete počítače migrovat nebo bez posouzení.


## <a name="database-migration-assistant"></a>Pomocník s migrací databáze

Azure Migrate se integruje s Microsoft Data Migration Assistant (DMA) k vyhodnocení místních SQL Server databází pro migraci do Azure SQL DB, spravované instance Azure SQL nebo virtuálních počítačů Azure se systémem SQL Server. DMA poskytuje informace o potenciálních blokujících problémech pro migraci. Označuje nepodporované funkce a také nové funkce, které můžete využívat po migraci, a pomůže vám identifikovat správnou cestu k migraci databáze. [Další informace](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)

## <a name="database-migration-service"></a>Database Migration Service

Azure Migrate se integruje s Azure Database Migration Service (DMS) pro migraci místních databází do Azure. DMS slouží k migraci místních databází do virtuálních počítačů Azure, na kterých běží SQL, Azure SQL DB a Azure SQL Managed Instances. [Další informace](https://docs.microsoft.com/azure/dms/dms-overview)

## <a name="movere"></a>Movere

 
Pracovní oddělení je SaaS platforma, která zvyšuje business intelligence tím, že v jednom dni přesně prezentuje celé IT prostředí. Vzhledem k tomu, že organizace rozšiřují, mění a digitálně optimalizují, poskytuje řešení podnikům jistotu, že potřebují mít přehled o svých prostředích, a to bez ohledu na platformu, aplikaci nebo zeměpisnou verzi. Společnost Microsoft [získala](https:/azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools) stěhovací společnost a už se neprodává jako samostatná nabídka.  K dispozici je stěhovací společnost prostřednictvím hodnocení řešení Microsoftu a programů Cloud ekonomie. [Přečtěte si další informace](https://www.movere.io) o stěhovací společnosti. Pokud máte nějaké dotazy, odešlete je na: movereq@microsoft.com nebo kontaktujte zástupce společnosti Microsoft.

Doporučujeme také podívat se na Azure Migrate, naši integrovanou službu migrace. Azure Migrate poskytuje centrální centrum pro zjednodušení migrace do cloudu. Centrum poskytuje komplexní podporu pro různé úlohy, včetně fyzických a virtuálních serverů, databází a aplikací. Koncová viditelnost usnadňuje sledování průběhu během zjišťování, posuzování a migrace. Díky integrovanému softwaru pro Azure i partnerským výrobcům ISV má Azure Migrate také rozsáhlou škálu funkcí, včetně zjišťování virtuálních a fyzických serverů, správného určení velikosti, plánování nákladů, vyhodnocení na základě importu a aplikace bez agentů. Analýza závislostí. Pokud hledáte odbornou pomoc, jak začít, společnost Microsoft má odborného [poskytovatele spravované služby pro Azure experta](https://azure.microsoft.com/partners) , který vám pomůže na cestě. Podívejte se na [web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Pomocník s migrací webové aplikace

Azure Migrate se integruje s Pomocník s migrací Azure App Service. Z centra Azure Migrate můžete do Azure vyhodnocovat a migrovat místní webové aplikace pomocí pomocníka následujícím způsobem:

- **Posouzení Web Apps online**: pomocí Azure App Service Pomocník s migrací vyhodnoťte místní weby pro migraci na Azure App Service.
- **Migrace webových aplikací**: Migrace webových aplikací .NET a php do Azure pomocí Azure App Service Pomocník s migrací.

[Přečtěte si další informace](https://appmigration.microsoft.com/) o Pomocníkovi.



## <a name="offline-data-migration"></a>Migrace offline dat

K přesunu velkých objemů dat do Azure můžete použít Azure Data Box Products. [Další informace](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si naše kurzy, abyste vyhodnotili [virtuální počítače VMware](tutorial-assess-vmware.md) a [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
- [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
