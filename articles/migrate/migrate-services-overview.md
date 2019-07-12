---
title: Informace o službě Azure Migrate | Microsoft Docs
description: Obsahuje přehled služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 083eaaa5be35d515a477ce6286f226b267569e1e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840363"
---
# <a name="about-azure-migrate"></a>Informace o službě Azure Migrate

Tento článek poskytuje stručný přehled Azure Migrate.

Azure Migrate vám umožní migrovat do Azure. Azure Migrate nabízí centrála sledovat zjišťování, vyhodnocení a migraci z místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje Azure nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV). Tato služba poskytuje:

- **Sjednocené migrace platformy**: Použít jediném portálu ke spuštění, spuštění a sledování průběhu migrace do Azure.
- **Řadu nástrojů**: Azure Migrate nabízí nástroje pro nativní a integruje se s dalšími službami Azure, stejně jako nástroje pro nezávislé výrobce softwaru. Vyberte správné vyhodnocení a migraci nástroje, podle požadavků vaší organizace. 
- **Azure Migrate Server Assessment**: Použijte nástroj Server Assessment pro posouzení místních virtuálních počítačů VMware a virtuálních počítačů Hyper-V pro migraci do Azure.
- **Azure Migrate migrace serveru**: Použijte nástroj pro migraci serveru migrovat místní virtuální počítače VMware, Hyper-V a cloudových virtuálních počítačích a fyzických serverů do Azure.
- **Azure Migrate posouzení databáze**: Posouzení místní databáze pro migraci do Azure.
- **Azure Migrate migrace databáze**: Migrace místních databází do Azure.


## <a name="azure-migrate-versions"></a>Verze služby Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Pomocí této verze můžete vytvářet projekty Azure Migrate, zjišťování místních počítačů a orchestrovat migrace a posouzení. [Další informace](whats-new.md) o tom, co je nového v této verzi.
- **Předchozí verze**: Pokud jste použili v předchozí verzi Azure Migrate (se podporuje pouze posouzení místních virtuálních počítačů VMware), byste teď měli použít aktuální verzi. Už můžete vytvářet projekty Azure Migrate pomocí předchozí verze nebo provedení nové zjišťování. Můžete však stále přistupovat existujících projektů. K tomu, na webu Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**. Na řídicím panelu Azure Migrate je oznámení a odkaz pro přístup k projektům staré Azure Migrate.

## <a name="isv-integration"></a>Integrace nezávislý výrobce softwaru

Kromě nativních nástrojů Azure Azure Migrate se integruje s celou řadou nabídek nezávislý výrobce softwaru. Identifikujete nástroj potřebujete a přidat do projektu Azure Migrate. Cesty migrace z v rámci projektu Azure Migrate můžete sledovat centrálně napříč nástroji Azure a nezávislé výrobce softwaru.

**ISV** | **Funkce**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Posouzení
[Zařízení 42](https://docs.device42.com/) | Posouzení
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Posouzení
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Posouzení
[Společnosti corent Technology](https://www.corenttech.com/AzureMigrate/) | Vyhodnocení a migrace
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrace

### <a name="selecting-an-isv-tool"></a>Výběr nástroj pro nezávislé výrobce softwaru

Po přidání do projektu Azure Migrate je nástroj nezávislých výrobců softwaru, začněte pomocí nástroje pro získání licence nebo registrace pro bezplatnou zkušební verzi, v souladu se zásadami nezávislý výrobce softwaru. V jednotlivých nástrojích je možné se připojit k Azure Migrate. Postupujte podle pokynů nástroj a dokumentaci, připojte nástroj pomocí služby Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate Server Assessment

Azure Migrate Server Assessment zjistí a posuzuje místní virtuální počítače VMware a virtuálních počítačů Hyper-V pro migraci do Azure. To vám pomůže určit následující:

- **Připravenost pro Azure:** Vyhodnoťte, jestli jsou připravené k migraci do Azure na místních počítačích.
- **Azure velikosti:** Odhad velikosti virtuálních počítačů Azure po migraci.
- **Podrobný odhad nákladů na Azure:** Odhaduje náklady na provoz místních serverů v Azure.
- **Vizualizace závislostí:** Určení závislostí mezi servery a nejlepší způsob, jak přesunout závislé servery do Azure. 

Server Assessment používá zjednodušené zařízení nasazovat místně a zaregistrovat Server Assessment.

- Zařízení zjistí místních počítačů, se připojí k Server Assessment a průběžně odešle metadata a data související s výkonem Azure Migrate.
- Zjišťování probíhá bez agenta. Nic musí být nainstalovaný na zjištěných virtuálních počítačů.
- Po zjištění počítačů, shromážděte je do skupin, které se obvykle skládají z virtuálních počítačů, které chcete migrovat společně.
- Vytvoříte posouzení pro skupinu. Potom můžete analyzovat posouzení, zjistěte strategii migrace.

## <a name="azure-migrate-server-migration"></a>Azure Migrate migrace serveru

Azure Migrate migrace serveru pomáhá můžete migrovat místní virtuální počítače VMware, virtuálních počítačů Hyper-V, fyzických serverů, jiné virtualizovaných počítačích a veřejného cloudu virtuálních počítačů do Azure. Počítače můžete migrovat po vyhodnocení jejich nebo bez vyhodnocení. 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate posouzení databáze

Azure Migrate se integruje s Data Migration Assistant (DMA) k posouzení místní databáze SQL serveru na migraci do Azure SQL DB, spravované Instance Azure SQL nebo virtuálních počítačích Azure s SQL serverem. Přímý přístup do paměti poskytuje informace o možných blokujících problémech pro migraci. Identifikuje nepodporované funkce, jakož i nové funkce, že po migraci využívat a pomáhá identifikovat správné cestě pro migraci databáze. [Další informace](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Azure Migrate migrace databáze

Azure Migrate se integruje se Azure Database Migration Service (DMS), migrace místních databází do Azure. Migrace místních databází do virtuálních počítačů Azure s SQL, Azure SQL DB nebo spravované instance SQL Azure pomocí DMS. [Další informace](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Webové aplikace vyhodnocení a migrace

Z centra Azure Migrate můžete posoudit a migrovat místní webové aplikace do Azure.

- **Vyhodnocení webové aplikace online**: Pomocí Azure App Service Pomocníka s migrací posoudit místní weby pro migraci do Azure App Service.
- **Migrace webových aplikací**: Migrace do Azure pomocí služby Azure App Service Pomocníka s migrací webové aplikace .NET a PHP.

[Další informace](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migrace offline dat

Data Box offline sadu produktů můžete použít pro přesun velkých objemů dat do Azure. [Víc se uč](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Další kroky

- Vyzkoušejte si naše kurzy k vyhodnocení [virtuálních počítačů VMware](tutorial-assess-vmware.md) a [virtuálních počítačů Hyper-V](tutorial-assess-hyper-v.md).
- [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.
- [Přečtěte si nejčastější dotazy](resources-faq.md) ohledně služby Azure Migrate.
