---
title: Informace o službě Azure Migrate
description: Přečtěte si o službě Azure Migrate.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: c336b2f95a17896edcc7eaad2b2586c6859804f9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361857"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje rychlý přehled služby Azure Migrate.

K migraci do Azure použijte Azure Migrate. Azure Migrate poskytuje centralizované centrum pro vyhodnocení a migraci místní infrastruktury, aplikací a dat do Azure s následujícími funkcemi:

- **Sjednocená platforma pro migraci**: jeden portál, který umožňuje spustit, spustit a sledovat cestu migrace do Azure.
- **Rozsah nástrojů**: řada nástrojů pro účely posouzení a migrace. Centrum zahrnuje Azure Migrate: vyhodnocení serveru a Azure Migrate: Migrace serveru. Integruje se s ostatními službami Azure a s jinými nástroji a nabídkami nezávislého výrobce softwaru (ISV).
- **Posouzení a migrace**: v centru Azure Migrate můžete vyhodnocovat a migrovat:
    - **Servery**: posouzení a migrace místních serverů do virtuálních počítačů Azure.
    - **Databáze**: posouzení a migrace místních databází do Azure SQL DB nebo do spravované instance Azure SQL.
    - **Webové aplikace**: použití pomocníka Azure App Service k vyhodnocení a migraci místních webových aplikací na Azure App Service.
    - **Virtuální plochy**: posouzení a migrace místní infrastruktury virtuálních klientských počítačů (VDI) do virtuálního počítače s Windows v Azure.
    - **Data**: pomocí Azure Data box produktů můžete rychle a efektivně migrovat velké objemy dat do Azure. 


## <a name="integrated-tools"></a>Integrované nástroje

Centrum Azure Migrate poskytuje následující nástroje.

**Nástroj** | **Vyhodnocení/migrace** | **Podrobnosti**
--- | --- | ---
**Azure Migrate: posouzení serveru** | Vyhodnotit servery | Zjišťování a posouzení místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V a fyzických serverů vám umožní zjistit, jestli jsou připravené na migraci do Azure.
**Azure Migrate: Migrace serveru** | Migrace serverů | Migrace virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů i dalších virtualizovaných počítačů a virtuálních počítačů s veřejným cloudem do Azure. 
**Pomocník s migrací databáze (DMA)** | Vyhodnocení místních SQL Server databází pro migraci do Azure SQL DB, spravované instance Azure SQL nebo virtuálních počítačů Azure se systémem SQL Server. | DMA poskytuje informace o potenciálních blokujících problémech pro migraci. Označuje nepodporované funkce a také nové funkce, které můžete využívat po migraci, a pomůže vám identifikovat správnou cestu k migraci databáze. [Další informace](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Database Migration Service (DMS)** | Migrujte místní databáze na virtuální počítače Azure, na kterých běží SQL, Azure SQL DB a spravované instance Azure SQL. | [Přečtěte si další informace](https://docs.microsoft.com/azure/dms/dms-overview) o DMS.
**Movere** | Vyhodnotit servery | [Přečtěte si další informace](#movere) o stěhovací společnosti.
**Pomocník s migrací webové aplikace** | Posouzení a migrace místních webových aplikací do Azure |  Pomocník s migrací Azure App Service použijte k vyhodnocení místních webů pro migraci na Azure App Service.<br/><br/> Migrujte webové aplikace .NET a PHP do Azure pomocí Azure App Service Pomocník s migrací. [Přečtěte si další informace](https://appmigration.microsoft.com/) o Pomocníkovi.
**Azure Data Box** | Migrace offline dat. | Pomocí Azure Data Box produktů můžete do Azure přesunout velké objemy dat offline. [Další informace](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>Integrace ISV

Azure Migrate se integruje s mnoha nabídkami ISV. 

**ISV** | **Funkce**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrace serverů
[Cloudamize](https://www.cloudamize.com/platform) | Vyhodnotit servery
[Technologie spolupronájmu](https://www.corenttech.com/AzureMigrate/) | Posouzení a migrace serverů
[Zařízení 42](https://docs.device42.com/) | Vyhodnotit servery
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Vyhodnotit VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrace serverů
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Vyhodnotit servery
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Posouzení serverů a databází


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Nástroj pro vyhodnocení serveru

Azure Migrate: Nástroj pro vyhodnocení serveru zjišťuje a vyhodnocuje místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery pro migraci do Azure. Pomůže vám identifikovat následující:

- **Připravenost na Azure:** Posuzuje, jestli jsou místní počítače připravené k migraci do Azure.
- **Velikost pro Azure:** Odhadovaná velikost virtuálních počítačů Azure po migraci
- **Odhad nákladů Azure:** Odhadované náklady na provoz místních serverů v Azure.
- **Vizualizace závislostí:** Pokud používáte hodnocení serveru s vizualizací závislostí, můžete efektivně identifikovat závislosti mezi servery a optimální způsoby přesunu závislých serverů do Azure.

Posouzení serveru používá odlehčené zařízení, které nasazujete místně, a zaregistrujte se pomocí posouzení serveru.

- Zařízení běží na fyzickém serveru nebo na virtuálním počítači a dá se snadno nainstalovat pomocí stažené šablony.
- Zařízení zjišťuje místní počítače a průběžně odesílá data o metadatech a výkonu počítačů do Azure Migrate.
- Zjišťování zařízení je bez agenta. Na zjištěných počítačích není potřeba nic instalovat.
- Po zjištění shromáždíte zjištěné počítače do skupin a vyhodnoťte skupiny pro migraci.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Nástroj pro migraci serveru

Azure Migrate: Nástroj pro migraci serveru vám pomůže migrovat místní virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery, další virtualizované počítače a veřejné cloudové virtuální počítače do Azure. Po posouzení můžete počítače migrovat, nebo je migrovat bez posouzení.


## <a name="select-a-tool"></a>Vybrat nástroj

V centru Azure Migrate vyberte nástroj, který chcete použít pro posouzení, a přidejte ho do projektu Azure Migrate. Pokud přidáte nástroj ISV nebo stěhovací společnost:

- Začněte získáním licence nebo registrací k bezplatné zkušební verzi v souladu s pokyny pro nástroj. Nástroje na licencování jsou určeny nástrojem ISV/. 
- V každém nástroji je k dispozici možnost připojit se k Azure Migrate. Postupujte podle pokynů pro připojení.
- Sledujte svou cestu migrace z Azure Migrate projektu ve všech nástrojích.


## <a name="movere"></a>Movere

Pracovní oddělení je SaaS platforma, která zvyšuje business intelligence tím, že v jednom dni přesně prezentuje celé IT prostředí. Vzhledem k tomu, že organizace rozšiřují, mění a digitálně optimalizují, poskytuje řešení podnikům jistotu, že potřebují mít přehled o svých prostředích, a to bez ohledu na platformu, aplikaci nebo zeměpisnou verzi. Společnost Microsoft [získala](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) stěhovací společnost a už se neprodává jako samostatná nabídka.  K dispozici je stěhovací společnost prostřednictvím hodnocení řešení Microsoftu a programů Cloud ekonomie. [Přečtěte si další informace](https://www.movere.io) o stěhovací společnosti. Pokud máte nějaké dotazy, odešlete je na: movereq@microsoft.com nebo kontaktujte zástupce společnosti Microsoft.

Doporučujeme také podívat se na Azure Migrate, naši integrovanou službu migrace. Azure Migrate poskytuje centrální centrum pro zjednodušení migrace do cloudu. Centrum poskytuje komplexní podporu pro různé úlohy, včetně fyzických a virtuálních serverů, databází a aplikací. Koncová viditelnost usnadňuje sledování průběhu během zjišťování, posuzování a migrace. Díky integrovanému softwaru pro Azure i partnerským výrobcům ISV má Azure Migrate také rozsáhlou škálu funkcí, včetně zjišťování virtuálních a fyzických serverů, správného určení velikosti, plánování nákladů, vyhodnocení na základě importu a aplikace bez agentů. Analýza závislostí. Pokud hledáte odbornou pomoc, jak začít, společnost Microsoft má odborného [poskytovatele spravované služby pro Azure experta](https://azure.microsoft.com/partners) , který vám pomůže na cestě. Podívejte se na [web Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Verze Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Tato verze slouží k vytváření Azure Migrate projektů, zjišťování místních počítačů a orchestraci posouzení a migrace. [Přečtěte si další](whats-new.md) informace o novinkách v této verzi.
- **Předchozí verze**: Pokud jste použili předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. Už nemůžete vytvářet Azure Migrate projekty pomocí předchozí verze a nedoporučujeme provádět nové zjišťování. Chcete-li získat přístup ke stávajícím projektům, v Azure Portal vyhledejte a vyberte možnost **Azure Migrate**. Na řídicím panelu **Azure Migrate** existuje oznámení a odkaz pro přístup k původním Azure Migratem projektům.



## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si naše kurzy, abyste vyhodnotili [virtuální počítače VMware](tutorial-prepare-vmware.md), [virtuální počítače Hyper-V](tutorial-prepare-hyper-v.md)a [fyzické servery](tutorial-prepare-physical.md).
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
