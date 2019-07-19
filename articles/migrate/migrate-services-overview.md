---
title: Informace o službě Azure Migrate | Microsoft Docs
description: Obsahuje přehled služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227814"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje rychlý přehled Azure Migrate.

Azure Migrate vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) třetích stran. Tato služba poskytuje:

- **Jednotná migrace platformy**: Pomocí jednoho portálu můžete začít, spouštět a sledovat cestu migrace do Azure.
- **Rozsah nástrojů**: Azure Migrate poskytuje nativní nástroje a integruje se s dalšími službami Azure i s nástroji ISV. Na základě požadavků vaší organizace vyberte správné nástroje pro posouzení a migraci. 
- **Vyhodnocování Azure Migrate serveru**: Pomocí nástroje pro vyhodnocení serveru můžete vyhodnotit místní virtuální počítače VMware a virtuální počítače Hyper-V pro migraci do Azure.
- **Migrace Azure Migrate serveru**: Pomocí nástroje pro migraci serveru migrujete místní virtuální počítače VMware, virtuální počítače Hyper-V, cloudové virtuální počítače a fyzické servery do Azure.
- **Azure Migrate vyhodnocení databáze**: Vyhodnoťte místní databáze pro migraci do Azure.
- **Migrace databáze Azure Migrate**: Migrujte místní databáze do Azure.


## <a name="azure-migrate-versions"></a>Verze Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Tato verze slouží k vytváření Azure Migrate projektů, zjišťování místních počítačů a orchestraci posouzení a migrace. [Přečtěte si další](whats-new.md) informace o novinkách v této verzi.
- **Předchozí verze**: Pokud jste používali předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. Už nemůžete vytvářet Azure Migrate projekty pomocí předchozí verze a nedoporučujeme provádět nové zjišťování. Chcete-li získat přístup ke stávajícím projektům, ve Azure Portal > **všechny služby**vyhledejte **Azure Migrate**. Na řídicím panelu Azure Migrate existuje oznámení a odkaz pro přístup k původním Azure Migratem projektům.

## <a name="isv-integration"></a>Integrace ISV

Kromě nativních nástrojů Azure se Azure Migrate integruje s řadou nabídek nezávislých výrobců softwaru. Identifikujte potřebný nástroj a přidejte ho do projektu Azure Migrate. Svou cestu migrace můžete centrálně sledovat v rámci Azure Migrate projektu napříč nástroji Azure a ISV.

**ISV** | **Funkce**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Posouzení
[Zařízení 42](https://docs.device42.com/) | Posouzení
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Posouzení
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Posouzení
[Technologie spolupronájmu](https://www.corenttech.com/AzureMigrate/) | Posouzení a migrace
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrace

### <a name="selecting-an-isv-tool"></a>Výběr nástroje ISV

Po přidání nástroje ISV do projektu Azure Migrate začněte s nástrojem získáním licence nebo registrací bezplatné zkušební verze v souladu se zásadami ISV. V každém nástroji je k dispozici možnost připojit se k Azure Migrate. Použijte pokyny a dokumentaci k nástroji pro připojení nástroje k Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Vyhodnocování serveru Azure Migrate

Azure Migrate Assessment Server zjišťuje a vyhodnocuje místní virtuální počítače VMware a virtuální počítače Hyper-V pro migraci do Azure. Pomůže vám identifikovat následující:

- **Připravenost na Azure:** Posuzuje, jestli jsou místní počítače připravené k migraci do Azure.
- **Velikost pro Azure:** Odhad velikosti virtuálních počítačů Azure po migraci
- **Odhad nákladů Azure:** Odhaduje náklady na provoz místních serverů v Azure.
- **Vizualizace závislostí:** Identifikujte závislosti mezi servery a nejlepší způsob, jak přesunout závislé servery do Azure. 

Posouzení serveru používá odlehčené zařízení, které nasazujete místně, a zaregistrujte se pomocí posouzení serveru.

- Zařízení zjistí místní počítače, připojí se k posouzení serveru a průběžně pošle metadata a data související s výkonem do Azure Migrate.
- Zjišťování je bez agentů. Na zjištěných virtuálních počítačích není potřeba nic instalovat.
- Po zjištění počítačů je budete shromažďovat do skupin, které se typicky skládají z virtuálních počítačů, které chcete migrovat dohromady.
- Vytvoříte posouzení pro skupinu. Pak můžete analyzovat posouzení a zjistit strategii migrace.

## <a name="azure-migrate-server-migration"></a>Migrace serveru Azure Migrate

Migrace Azure Migrate serveru vám pomůže s migrací místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných počítačů a virtuálních počítačů s veřejným cloudem do Azure. Po posouzení můžete počítače migrovat nebo bez posouzení. 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate posouzení databáze

Azure Migrate se integruje s Data Migration Assistant (DMA) k vyhodnocení místních SQL Server databází pro migraci do Azure SQL DB, spravované instance Azure SQL nebo virtuálních počítačů Azure se systémem SQL Server. DMA poskytuje informace o potenciálních blokujících problémech pro migraci. Označuje nepodporované funkce a také nové funkce, které můžete využívat po migraci, a pomůže vám identifikovat správnou cestu k migraci databáze. [Další informace](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Migrace databáze Azure Migrate

Azure Migrate se integruje s Azure Database Migration Service (DMS) pro migraci místních databází do Azure. DMS slouží k migraci místních databází do virtuálních počítačů Azure, na kterých běží SQL, Azure SQL DB a Azure SQL Managed Instances. [Další informace](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Posouzení a migrace webové aplikace

Z centra Azure Migrate můžete vyhodnocovat a migrovat místní webové aplikace do Azure.

- **Vyhodnotit webové aplikace online**: K vyhodnocení místních webů pro migraci na Azure App Service použijte Azure App Service Pomocník s migrací.
- **Migrace webových aplikací**: Migrujte webové aplikace .NET a PHP do Azure pomocí Azure App Service Pomocník s migrací.

[Další informace](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migrace offline dat

K přesunu velkých objemů dat do Azure můžete použít Data Box řady produktů v režimu offline. [Víc se uč](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si naše kurzy, abyste vyhodnotili [virtuální počítače VMware](tutorial-assess-vmware.md) a [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
- [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
