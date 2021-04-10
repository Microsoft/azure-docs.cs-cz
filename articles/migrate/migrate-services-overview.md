---
title: Informace o službě Azure Migrate
description: Přečtěte si o službě Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fd8806a02e48481042eb756a0a077d801583cd2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870768"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje rychlý přehled služby Azure Migrate.

Azure Migrate poskytuje centralizované centrum pro vyhodnocení a migraci na místní servery, infrastrukturu, aplikace a data Azure. Poskytuje následující:

- **Jednotná platforma migrace**: jeden portál pro spuštění, spuštění a sledování migrace do Azure.
- **Rozsah nástrojů**: řada nástrojů pro účely posouzení a migrace. Mezi Azure Migrate nástroje patří Azure Migrate: zjišťování a vyhodnocení a Azure Migrate: Migrace serveru. Azure Migrate se taky integruje s dalšími službami a nástroji Azure a nabídkami nezávislého výrobce softwaru (ISV).
- **Posouzení a migrace**: v centru Azure Migrate můžete vyhodnotit a migrovat:
    - **Windows, Linux a SQL Server**: vyhodnoťte místní servery, včetně SQL Server instancí a jejich migrace do virtuálních počítačů Azure nebo Azure VMware Solution (AVS) (Preview).
    - **Databáze**: vyhodnoťte místní databáze a migrujte je do Azure SQL Database nebo do spravované instance SQL.
    - **Webové aplikace**: vyhodnoťte místní webové aplikace a migrujte je na Azure App Service pomocí Pomocník s migrací Azure App Service.
    - **Virtuální plochy**: vyhodnoťte místní infrastrukturu virtuálních klientských počítačů (VDI) a migrujte ji na virtuální plochu Windows v Azure.
    - **Data**: můžete rychle a efektivně migrovat velké objemy dat do Azure pomocí Azure Data box produktů.

## <a name="integrated-tools"></a>Integrované nástroje

Centrum Azure Migrate zahrnuje tyto nástroje:

**Nástroj** | **Posouzení a migrace** | **Podrobnosti**
--- | --- | ---
**Azure Migrate: zjišťování a posouzení** | Zjišťování a posouzení serverů včetně SQL | Zjišťování a posouzení místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V a fyzických serverů v přípravě na migraci do Azure
**Azure Migrate: Migrace serveru** | Migrace serverů | Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery, další virtualizované servery a veřejné cloudové virtuální počítače do Azure.
**Data Migration Assistant** | Vyhodnoťte SQL Server databáze pro migraci do Azure SQL Database, spravované instance Azure SQL nebo virtuálních počítačů Azure se systémem SQL Server. | Data Migration Assistant je samostatný nástroj pro vyhodnocení SQL Severs.It pomáhá identifikovat potenciální problémy blokující migraci. Identifikuje nepodporované funkce, nové funkce, které vám po migraci můžou těžit, a správnou cestu k migraci databáze. [Další informace](/sql/dma/dma-overview).
**Azure Database Migration Service** | Migrace místních databází do virtuálních počítačů Azure s SQL Server, Azure SQL Database nebo spravovanými instancemi SQL | [Přečtěte si další informace](../dms/dms-overview.md) o Database Migration Service.
**Movere** | Vyhodnotit servery | [Přečtěte si další informace](#movere) o stěhovací společnosti.
**Pomocník s migrací webové aplikace** | Vyhodnoťte místní webové aplikace a migrujte je do Azure. |  K vyhodnocení místních webů pro migraci na Azure App Service použijte Azure App Service Pomocník s migrací.<br/><br/> Pomocí Pomocník s migrací můžete migrovat webové aplikace .NET a PHP do Azure. [Přečtěte si další informace](https://appmigration.microsoft.com/) o Azure App Service Pomocník s migrací.
**Azure Data Box** | Migrace offline dat | Pomocí Azure Data Box produktů můžete do Azure přesunout velké objemy dat offline. [Další informace](../databox/index.yml).

> [!NOTE]
> Pokud jste v Azure Government, externí integrované nástroje a nabídky ISV nemůžou odesílat data do Azure Migrate. Nástroje můžete používat nezávisle.

## <a name="isv-integration"></a>Integrace ISV

Azure Migrate se integruje s několika nabídkami ISV. 

**Nezávislý výrobce softwaru**    | **Funkce**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrujte servery.
[Cloudamize](https://www.cloudamize.com/platform) | Vyhodnoťte servery.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Vyhodnoťte a migrujte servery.
[Device42](https://docs.device42.com/) | Vyhodnoťte servery.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Vyhodnotit VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrujte servery.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Vyhodnoťte servery.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Vyhodnoťte servery a databáze.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Migrujte servery.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Azure Migrate: Nástroj pro zjišťování a vyhodnocení

Nástroj Azure Migrate: zjišťování a hodnocení zjišťuje a vyhodnocuje místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery pro migraci do Azure. 

Tady je popis toho, co nástroj dělá:

- **Připravenost na Azure**: posuzuje, jestli jsou místní servery připravené na migraci do Azure.
- **Změny velikosti Azure**: odhaduje velikost virtuálních počítačů Azure/konfigurace Azure SQL/počtu uzlů řešení Azure VMware po migraci.
- **Odhad nákladů Azure**: odhaduje náklady na provozování místních serverů v Azure.
- **Analýza závislostí**: identifikuje závislosti mezi servery a optimalizační strategie pro přesun vzájemně závislých serverů do Azure. Přečtěte si další informace o zjišťování a hodnocení s [analýzou závislostí](concepts-dependency-visualization.md).

Zjišťování a hodnocení používá odlehčené [Azure Migrate zařízení](migrate-appliance.md) , které nasazujete místně.

- Zařízení běží na virtuálním počítači nebo na fyzickém serveru. Můžete ji snadno nainstalovat pomocí stažené šablony.
- Zařízení zjišťuje místní servery. Také průběžně odesílá metadata a data o výkonu serveru do Azure Migrate.
- Zjišťování zařízení je bez agenta. Na zjištěných serverech není nainstalována žádná instalace.
- Po zjištění zařízení můžete shromáždit zjištěné servery do skupin a spustit posouzení pro každou skupinu.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Nástroj pro migraci serveru

Azure Migrate: Nástroj pro migraci serveru pomáhá při migraci serverů do Azure:

**Migrate** | **Podrobnosti**
--- | ---
Místní virtuální počítače VMware | Migrujte virtuální počítače do Azure pomocí agenta bez agentů nebo migrace založené na agentech.<br/><br/> U migrace bez agentů používá migrace serveru stejné Azure Migrate zařízení, které je možné použít i zjišťováním a posouzením pro zjišťování a vyhodnocení virtuálních počítačů VMware.<br/><br/> Pro migraci na základě agenta používá migrace serveru replikační zařízení.
Místní virtuální počítače Hyper-V | Migrujte virtuální počítače do Azure.<br/><br/> Migrace serveru používá pro migraci agenty poskytovatele nainstalované na hostiteli Hyper-V.
Místní fyzické servery nebo servery hostované v jiných cloudech | Můžete migrovat fyzické servery do Azure. Můžete také migrovat další virtualizované servery a virtuální počítače z jiných veřejných cloudů tak, že je považujete jako fyzické servery pro účely migrace. Migrace serveru používá pro migraci replikační zařízení.


## <a name="selecting-assessment-and-migration-tools"></a>Výběr nástrojů pro posouzení a migraci

V centru Azure Migrate vyberte nástroj, který chcete použít pro vyhodnocení nebo migraci a přidejte ho do projektu. Pokud přidáte nástroj ISV nebo stěhovací společnost:

- Pokud chcete začít, získejte licenci nebo si zaregistrujte bezplatnou zkušební verzi pomocí pokynů k nástroji. Každý ISV nebo nástroj určuje licencování nástrojů.
- Každý nástroj má možnost připojit se k Azure Migrate. Postupujte podle pokynů k nástroji pro připojení.
- Sledujte vaši migraci mezi všemi nástroji v rámci projektu.

## <a name="movere"></a>Movere

Stěhovací společnosti je platforma typu software jako služba (SaaS). Zvyšuje business intelligence tím, že v jednom dni přesně prezentuje celá IT prostředí. Organizace a podniky rozšiřují, mění a digitálně optimalizují. V takovém případě dostanou pracovníky k potřebnou jistotu, aby viděli a ovládají svá prostředí, ať už jde o platformu, aplikaci nebo zeměpisnou oblast.

Microsoft [získal](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) stěhovací společnost a už se neprodává jako samostatná nabídka. Stěhovací společnosti je k dispozici prostřednictvím programu Microsoft Solution Assessment a Microsoft Cloud ekonomie. [Přečtěte si další informace](https://www.movere.io) o stěhovací společnosti.

Doporučujeme také podívat se na Azure Migrate, naši integrovanou službu migrace. Azure Migrate poskytuje centrální centrum pro zjednodušení migrace do cloudu. Centrum bude obsáhlě podporovat úlohy, jako jsou fyzické a virtuální servery, databáze a aplikace. Koncová viditelnost umožňuje snadno sledovat průběh během zjišťování, posuzování a migrace.

Díky integrovanému předdefinovanému softwaru Azure i partnerům ISV má Azure Migrate rozsáhlou škálu funkcí, včetně těchto:

- Zjišťování virtuálních a fyzických serverů.
- Snižování na základě výkonu.
- Plánování nákladů.
- Posouzení na základě importu.
- Analýza závislostí aplikací bez agentů.

Pokud hledáte odbornou pomoc, jak začít, společnost Microsoft má odborné [poskytovatele spravovaných služeb Azure expertů, kteří](https://azure.microsoft.com/partners) vás provedou. Podívejte se na [web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Verze Azure Migrate

Existují dvě verze služby Azure Migrate.

- **Aktuální verze**: Tato verze slouží k vytváření projektů, zjišťování místních serverů a orchestraci posouzení a migrace. [Přečtěte si další](whats-new.md) informace o novinkách v této verzi.
- **Předchozí verze**: předchozí verze Azure Migrate známá taky jako klasická Azure Migrate podporuje jenom posouzení místních virtuálních počítačů VMware. Vyřazení z klasického Azure Migrate do února 2024. Po 2024. února již nebude klasická verze Azure Migrate podporována a metadata inventáře v klasických projektech budou odstraněna. Nemůžete upgradovat projekty nebo součásti v předchozí verzi na novou verzi. Musíte [vytvořit nový projekt](create-manage-projects.md)a přidat do něj [Nástroje pro posouzení a migraci](./create-manage-projects.md) . Pomocí kurzů můžete pochopit, jak jsou dostupné nástroje pro posouzení a migraci. Pokud byl pracovní prostor Log Analytics připojený k klasickému projektu, můžete ho po odstranění klasického projektu připojit k projektu aktuální verze.

    Chcete-li získat přístup k existujícím projektům v Azure Portal, vyhledejte a vyberte možnost **Azure Migrate**. Řídicí panel **Azure Migrate** obsahuje oznámení a odkaz pro přístup ke starým projektům.

## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si naše kurzy, abyste vyhodnotili [virtuální počítače VMware](./tutorial-discover-vmware.md), [virtuální počítače Hyper-V](./tutorial-discover-hyper-v.md)nebo [fyzické servery](./tutorial-discover-physical.md).
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.