---
title: Informace o službě Azure Migrate
description: Přečtěte si o službě Migrace Azure.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: aa999b874b6e8b0075ab905a72ff04f03cb39ac7
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537895"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje rychlý přehled služby Azure Migrate.

Azure Migrate poskytuje centralizované centrum pro posouzení a migraci místních serverů, infrastruktury, aplikací a dat do Azure. Azure Migrate poskytuje následující funkce:

- **Sjednocená migrační platforma:** Jeden portál pro spuštění, spuštění a sledování vaší cesty migrace do Azure.
- **Řada nástrojů**: Řada nástrojů pro hodnocení a migraci. Mezi nástroje patří Migrace Azure: Vyhodnocení serveru a Migrace Azure: Migrace serveru. Azure Migrate se integruje s dalšími službami Azure a s dalšími nástroji a nezávislými dodavateli softwaru (ISV).
- **Hodnocení a migrace**: V centru Migrace Azure můžete vyhodnocovat a migrovat:
    - **Servery:** Vyhodnoťte a migrujte místní servery do virtuálních počítačích Azure.
    - **Databáze:** Vyhodnoťte a migrujte místní databáze do Azure SQL DB nebo do spravované instance Azure SQL.
    - **Webové aplikace:** Vyhodnoťte a migrujte místní webové aplikace do služby Azure App Service pomocí Pomocníka pro aplikační aplikace Azure.
    - **Virtuální plochy: Vyhodnoťte**a migrujte místní infrastrukturu virtuálních desktopů (VDI) do virtuální plochy Windows v Azure.
    - **Data:** Migrace velkého množství dat do Azure rychle a nákladově efektivní, pomocí produktů Azure Data Box. 


## <a name="integrated-tools"></a>Integrované nástroje

Centrum Migrace Azure obsahuje tyto nástroje.

**Nástroj** | **Posoudit/migrovat** | **Podrobnosti**
--- | --- | ---
**Migrace Azure:Vyhodnocení serveru** | Posoudit servery. | V rámci přípravy na migraci do Azure můžete zjišťovat a vyhodnocovat místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery.
**Migrace Azure:Migrace serveru** | Migrovat servery. | Migrace virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných počítačů a virtuálních počítačů veřejného cloudu do Azure. 
**Pomocník pro migraci databáze (DMA)** | Vyhodnoťte místní databáze SQL Serveru pro migraci do Azure SQL DB, Azure SQL Managed Instance nebo na virtuální počítače Azure se systémem SQL Server. | DMA pomáhá určit potenciální problémy s blokováním migrace. Identifikuje nepodporované funkce, nové funkce, které můžete využít po migraci, a pomůže vám identifikovat správnou cestu pro migraci databáze. [Další informace](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Služba migrace databáze (DMS)** | Migrujte místní databáze do virtuálních počítače Azure se spuštěna SQL, Azure SQL DB a Azure SQL Managed Instance. | [Další informace](https://docs.microsoft.com/azure/dms/dms-overview) o DMS.
**Movere** | Posoudit servery. | [Přečtěte si další informace](#movere) o Movere.
**Pomocník pro migraci webových aplikací** | Vyhodnoťte a migrujte místní webové aplikace do Azure. |  Pomocí Pomocníka pro migraci služby Azure App Service můžete posoudit místní weby pro migraci do služby Azure App Service.<br/><br/> Pomocí Pomocníka pro migraci webových aplikací .NET a PHP do Azure. [Přečtěte si další informace](https://appmigration.microsoft.com/) o Pomocníkovi pro migraci služby Azure App Service.
**Azure Data Box** | Offline migrace dat. | Pomocí produktů Azure Data Box můžete přesunout velké množství dat do Azure offline. [Další informace](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Pokud jste ve službě Azure Government, externí integrované nástroje a nabídky neveřejných virtuálních i nepřístupnících nelze odesílat data do projektů Migrace Azure. Nástroje můžete používat nezávisle.

## <a name="isv-integration"></a>Integrace isv

Azure Migrate se integruje s řadou nabídek nenožejových isv. 

**Isv**    | **Funkce**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrace serverů
[Cloudamize](https://www.cloudamize.com/platform) | Posoudit servery
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Posouzení a migrace serverů
[Zařízení 42](https://docs.device42.com/) | Posoudit servery
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posoudit VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrace serverů
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Posoudit servery
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Posouzení serverů a databází


## <a name="azure-migrate-server-assessment-tool"></a>Nástroj pro vyhodnocení serveru Azure

Nástroj Azure Migrate:Server Assessment zjišťuje a vyhodnocuje místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery pro migraci do Azure. Zde je to, co nástroj dělá:

- **Připravenost Azure:** Vyhodnotí, jestli jsou místní počítače připravené k migraci do Azure.
- **Velikost Azure:** Odhaduje velikost virtuálních počítačích Azure po migraci.
- **Odhad nákladů Azure:** Odhadované náklady na provoz místních serverů v Azure.
- **Analýza závislostí:** Pokud používáte serverassessment s [analýzou závislostí](concepts-dependency-visualization.md), můžete efektivně identifikovat závislosti mezi servery a optimalizovat strategie pro přesun inter závislých serverů do Azure.


Vyhodnocení serveru používá zjednodušené [zařízení Azure Migrate,](migrate-appliance.md) které nasadíte místně.

- Zařízení běží na virtuálním počítači nebo fyzickém serveru. Můžete jej snadno nainstalovat pomocí stažené šablony.
- Zařízení zjišťuje místní počítače a průběžně odesílá metadata počítače a data o výkonu do Migrace Azure.
- Zjišťování zařízení je bez agenta. V zjištěných počítačích není nic nainstalováno.
- Po objevení zařízení shromáždíte zjištěné stroje do skupin a spustíte hodnocení pro skupinu.

## <a name="azure-migrate-server-migration-tool"></a>Nástroj pro migraci serverů Azure Migrate

Nástroj Migrace:migrace serveru Azure vám pomůže migrovat místní virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery, další virtualizované počítače a virtuální počítače veřejného cloudu do Azure. Počítače můžete migrovat po jejich vyhodnocení nebo je migrovat bez posouzení. 

Pro migraci virtuálních počítačů VMware bez agenta a migraci virtuálních počítačů Hyper-V, migrace serveru používá zařízení Azure Migrate, které nasadíte místně. Zařízení se také používá, pokud nastavíte posouzení serveru a je popsáno v předchozí části.


## <a name="selecting-assessmentmigration-tools"></a>Výběr nástrojů hodnocení/migrace

V centru Migrace Azure vyberete nástroj, který chcete použít pro hodnocení nebo migraci, a přidáte ho do projektu Migrace Azure. Pokud přidáte nástroj ISV nebo Movere:

- Chcete-li začít získat licenci, nebo se zaregistrovat na bezplatnou zkušební verzi, v souladu s pokyny k nástroji. Licencování nástrojů je určeno nevýhradním výrobcem nářadí nebo nástrojem. 
- V každém nástroji je možnost připojení k Azure Migrate. Připojte se podle pokynů k nástroji.
- Cestu migrace můžete sledovat z projektu Migrace Azure napříč všemi nástroji.


## <a name="movere"></a>Movere

Movere je platforma SaaS, která zvyšuje business intelligence tím, že přesně prezentuje celá IT prostředí během jednoho dne. S růstem, změnou a digitální optimalizací organizací poskytuje řešení podnikům jistotu, kterou potřebují k tomu, aby měly viditelnost a kontrolu nad svým prostředím bez ohledu na platformu, aplikaci nebo zeměpisnou oblast. Movere byl [získán](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) společností Microsoft a již se neprodává jako samostatná nabídka.  Movere je k dispozici prostřednictvím programů Microsoft Solution Assessment a Cloud Economics. [Přečtěte si další informace](https://www.movere.io) o Movere. 

Doporučujeme vám, abyste se také podívali na Azure Migrate, naši integrovanou službu migrace. Azure Migrate poskytuje centrální rozbočovač pro zjednodušení migrace do cloudu. Centrum nabízí komplexní podporu pro různé úlohy, včetně fyzických a virtuálních serverů, databází a aplikací. Viditelnost od konce usnadňuje sledování průběhu zjišťování, hodnocení a migrace. Azure Migrate má také rozsáhlou škálu funkcí, včetně zjišťování virtuálních a fyzických serverů, správné velikosti založené na výkonu, plánování nákladů, hodnocení na základě importu a analýzy závislostí aplikací bez agentů. Pokud hledáte odbornou pomoc, která vám pomůže začít, má Microsoft kvalifikovaného [poskytovatele spravovaných služeb Azure Expert Managed Service Provider,](https://azure.microsoft.com/partners) který vás provede na cestě. Podívejte se na [web Migrace Azure](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Verze migrace Azure

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Tato verze slouží k vytváření projektů Migrace Azure, zjišťování místních počítačů a orchestraci hodnocení a migrace. [Přečtěte si další informace](whats-new.md) o novince v této verzi.
- **Předchozí verze**: Pokud jste použili předchozí verzi Migrace Azure (bylo podporováno jenom hodnocení místních virtuálních počítačích VMware), měli byste teď použít aktuální verzi. Projekty Migrace Azure už nelze vytvářet pomocí předchozí verze a doporučujeme, abyste neprováděli nové zjišťování. Chcete-li získat přístup k existujícím projektům, vyhledejte a vyberte **Azure Migrate**. Na řídicím panelu **Azure Migrate** je oznámení a odkaz pro přístup ke starým projektům Migrace Azure.



## <a name="next-steps"></a>Další kroky

- Vyzkoušejte naše výukové programy k posouzení [virtuálních měn VMware](tutorial-prepare-vmware.md), [virtuálních zařízení Hyper-V](tutorial-prepare-hyper-v.md)nebo [fyzických serverů](tutorial-prepare-physical.md).
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
