---
title: Informace o službě Azure Migrate | Microsoft Docs
description: Obsahuje přehled služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498756"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje rychlý přehled Azure Migrate.

Azure Migrate vám pomůže migrovat z místního prostředí do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje a služby Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. Azure Migrate poskytuje:

- **Sjednocená platforma pro migraci**: jeden portál, který umožňuje spustit, spustit a sledovat cestu migrace do Azure.
- **Rozsah nástrojů**: nativní nástroje a integrace s dalšími službami Azure a také s nástroji ISV. Na základě požadavků vaší organizace vyberte správné nástroje pro posouzení a migraci.
- **Úlohy**: Azure Migrate poskytuje nástroje pro vyhodnocení a migraci pro:
    - **Servery**: pro účely posouzení a migrace serverů do virtuálních počítačů Azure použijte nástroje Microsoftu nebo nástroje ISV.
    - **Databáze**: Využijte nástroje Microsoftu a ISV pro posouzení a migraci místních databází do Azure SQL DB nebo spravované instance Azure SQL.
    - **Webové aplikace**: použijte pomocníka Azure App Service k vyhodnocení a migraci místních webových aplikací do Azure App Service.
    - **Virtuální plochy**: pomocí nástrojů ISV můžete vyhodnotit a migrovat místní infrastrukturu virtuálních klientských počítačů (VDI) na virtuální plochu Windows v Azure.
    - **Data**: k rychlému a nákladově efektivní migraci dat do Azure využijte Azure Data box rodinu produktů.

## <a name="azure-migrate-versions"></a>Verze Azure Migrate

V současné době existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Tato verze slouží k vytváření Azure Migrate projektů, zjišťování místních počítačů a orchestraci posouzení a migrace. [Přečtěte si další](whats-new.md) informace o novinkách v této verzi.
- **Předchozí verze**: Pokud jste použili předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. Už nemůžete vytvářet Azure Migrate projekty pomocí předchozí verze a nedoporučujeme provádět nové zjišťování. Chcete-li získat přístup ke stávajícím projektům, ve Azure Portal > **všechny služby**vyhledejte **Azure Migrate**. Na řídicím panelu Azure Migrate existuje oznámení a odkaz pro přístup k původním Azure Migratem projektům.

## <a name="isv-integration"></a>Integrace ISV

Kromě nativních nástrojů společnosti Microsoft se Azure Migrate integruje s mnoha nabídkami ISV. 

**ISV** | **Funkce**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Posouzení
[Zařízení 42](https://docs.device42.com/) | Posouzení
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Posouzení
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Posouzení
[Technologie spolupronájmu](https://www.corenttech.com/AzureMigrate/) | Posouzení a migrace
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrace
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posouzení



### <a name="selecting-an-isv-tool"></a>Výběr nástroje ISV

Identifikujte potřebný nástroj a přidejte ho do projektu Azure Migrate.

- Po přidání nástroje ISV můžete začít získáním licence nebo registrací bezplatné zkušební verze v souladu se zásadami ISV. Licencování pro nástroje ISV je v souladu s licenčním modelem ISV.
- V každém nástroji je k dispozici možnost připojit se k Azure Migrate. Použijte pokyny a dokumentaci k nástroji pro připojení nástroje k Azure Migrate. L
- Svou cestu migrace můžete centrálně sledovat v rámci Azure Migrate projektu napříč nástroji Azure a ISV.


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


## <a name="database-assessment"></a>Posouzení databáze

Azure Migrate se integruje s Microsoft Data Migration Assistant (DMA) k vyhodnocení místních SQL Server databází pro migraci do Azure SQL DB, spravované instance Azure SQL nebo virtuálních počítačů Azure se systémem SQL Server. DMA poskytuje informace o potenciálních blokujících problémech pro migraci. Označuje nepodporované funkce a také nové funkce, které můžete využívat po migraci, a pomůže vám identifikovat správnou cestu k migraci databáze. [Další informace](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration"></a>Migrace databáze

Azure Migrate se integruje s Azure Database Migration Service (DMS) pro migraci místních databází do Azure. DMS slouží k migraci místních databází do virtuálních počítačů Azure, na kterých běží SQL, Azure SQL DB a Azure SQL Managed Instances. [Další informace](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-migration"></a>Migrace webové aplikace

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
