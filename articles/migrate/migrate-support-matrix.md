---
title: Azure Migrateová matice podpory
description: Poskytuje souhrn nastavení podpory a omezení pro službu Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: af0b8a4d3dfbce32e412f5294fb19ade61fd7661
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628165"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrateová matice podpory

[Službu Azure Migrate](./migrate-services-overview.md) můžete použít k vyhodnocení a migraci serverů do cloudu Microsoft Azure. Tento článek shrnuje obecná nastavení podpory a omezení pro Azure Migrate scénáře a nasazení.

## <a name="supported-assessmentmigration-scenarios"></a>Podporované scénáře posouzení/migrace

Tabulka shrnuje podporované scénáře zjišťování, hodnocení a migrace.

**Nasazení** | **Podrobnosti** 
--- | --- 
**Zjišťování** | Můžete zjišťovat metadata serveru a dynamická data o výkonu.
**Zjišťování aplikací** | Můžete zjišťovat aplikace, role a funkce spuštěné na virtuálních počítačích VMware. V tuto chvíli je tato funkce omezená jenom na zjišťování. Posouzení je aktuálně na úrovni serveru. Ještě nenabízíme vyhodnocení na základě aplikací, rolí nebo funkcí. 
**Posouzení** | Vyhodnoťte místní úlohy a data běžící na virtuálních počítačích VMware, virtuálních počítačích Hyper-V a fyzických serverech. Vyhodnotit pomocí Azure Migrate: posouzení serveru, Microsoft Data Migration Assistant (DMA) i další nástroje a nabídky ISV.
**Migrace** | Migrujte úlohy a data spuštěná na fyzických serverech, virtuálních počítačích VMware, virtuálních počítačích Hyper-V, fyzických serverech a cloudových virtuálních počítačích do Azure. Migrace pomocí Azure Migrate: Server Assessment and Azure Database Migration Service (DMS) a také další nástroje a nabídky ISV.

> [!NOTE]
> V současné době nástroje ISV nemůžou odesílat data Azure Migrate v Azure Government. Můžete použít integrované nástroje společnosti Microsoft nebo nezávisle na nich používat partnerské nástroje.

## <a name="supported-tools"></a>Podporované nástroje


Konkrétní podpora nástrojů je shrnuta v tabulce.

**Nástroj** | **Posouzení** | **Migrate** 
--- | --- | ---
Azure Migrate: posouzení serveru | Posouzení [virtuálních počítačů VMware](./tutorial-discover-vmware.md), [virtuálních počítačů Hyper-V](./tutorial-discover-hyper-v.md)a [fyzických serverů](./tutorial-discover-physical.md). |  Není k dispozici (N/A)
Azure Migrate: Migrace serveru | – | Migrujte [virtuální počítače VMware](tutorial-migrate-vmware.md), [virtuální počítače Hyper-V](tutorial-migrate-hyper-v.md)a [fyzické servery](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | – | Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a další cloudové úlohy. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a další cloudové úlohy. | –
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzický server písku dalších cloudových úloh. |  Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu.
[Zařízení 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a další cloudové úlohy.| –
[DMA](/sql/dma/dma-overview) | Vyhodnotit SQL Serveré databáze. | –
[DMS](../dms/dms-overview.md) | – | Migrujte SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posouzení infrastruktury virtuálních klientských počítačů (VDI) | –
[Movere](https://www.movere.io/) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, virtuální počítače Xen, fyzické servery, pracovní stanice (včetně infrastruktury VDI) a další cloudové úlohy. | –
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | – | Migrace virtuálních počítačů VMWare, virtuálních počítačů Hyper-V, virtuálních počítačů Xen, KVM virtuálních počítačů, fyzických serverů a dalších cloudových úloh 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a další cloudové úlohy. | –
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a další cloudové úlohy a SQL Server databáze. | –
[WebApp Pomocník s migrací](https://appmigration.microsoft.com/) | Posouzení webových aplikací | Migrujte webové aplikace.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | – |  Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a další cloudové úlohy.


## <a name="project"></a>Project

**Podpora** | **Podrobnosti**
--- | ---
Předplatné | Může mít více projektů v rámci předplatného.
Oprávnění Azure | Uživatel musí mít v předplatném oprávnění Přispěvatel nebo Owner, aby mohl vytvořit projekt.
Virtuální počítače VMware  | Vyhodnoťte až 35 000 virtuálních počítačů VMware v jednom projektu.
Virtuální počítače Hyper-V    | Vyhodnoťte až 35 000 virtuálních počítačů Hyper-V v jednom projektu.

Projekt může zahrnovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do limitů hodnocení.

## <a name="azure-permissions"></a>Oprávnění Azure

Pokud Azure Migrate pro práci s Azure potřebujete tato oprávnění, než začnete s vyhodnocováním a migrací serverů.

**Úkol** | **Oprávnění** | **Podrobnosti**
--- | --- | ---
Vytvoření projektu | Váš účet Azure potřebuje oprávnění k vytvoření projektu. | Nastavte pro [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)nebo [fyzické servery](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Registrace zařízení Azure Migrate| Azure Migrate využívá odlehčené [Azure Migrate zařízení](migrate-appliance.md) k vyhodnocení serverů pomocí Azure Migrate: posouzení serveru a spouštění migrace virtuálních počítačů VMware bez [agenta](server-migrate-overview.md) pomocí Azure Migrate: Migrace serveru. Toto zařízení zjišťuje servery a odesílá data o metadatech a výkonu Azure Migrate.<br/><br/> Během registrace se registrace zprostředkovatelů (Microsoft. OffAzure, Microsoft. Migruj a Microsoft. klíčů) zaregistrují u předplatného, které jste zvolili v zařízení, aby předplatné spolupracuje s poskytovatelem prostředků. K registraci budete potřebovat přístup pro přispěvatele nebo vlastníka k tomuto předplatnému.<br/><br/> **VMware**– během připojování Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD). První aplikace komunikuje mezi agenty zařízení a službou Azure Migrate. Aplikace nemá oprávnění k provedení volání správy prostředků Azure nebo přístupu k prostředkům v Azure RBAC. Druhá aplikace přistupuje k Azure Key Vault vytvořenému v předplatném uživatele jenom pro migraci VMware bez agenta. Při migraci bez agenta Azure Migrate vytvoří Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. Při zahájení zjišťování ze zařízení má přístup k Azure RBAC na Azure Key Vault (v tenantovi zákazníka).<br/><br/> **Hyper-V**– při připojování. Azure Migrate vytvoří jednu aplikaci Azure AD. Aplikace komunikuje mezi agenty zařízení a službou Azure Migrate. Aplikace nemá oprávnění k provedení volání správy prostředků Azure nebo přístupu k prostředkům v Azure RBAC. | Nastavte pro [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)nebo [fyzické servery](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Vytvoření trezoru klíčů pro migraci bez agentů VMware | Migrace virtuálních počítačů VMware s Azure Migrate bez agentů: Migrace serveru Azure Migrate vytvoří Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. Pokud chcete vytvořit trezor, nastavte oprávnění (vlastníka nebo přispěvatel a správce přístupu uživatele) na skupinu prostředků, ve které se projekt nachází. | [Nastavte](./tutorial-discover-vmware.md#prepare-an-azure-user-account) oprávnění.

## <a name="supported-geographies-public-cloud"></a>Podporovaná geografická oblast (veřejný cloud)

Projekt můžete vytvořit v řadě geografických oblastí ve veřejném cloudu.

- I když v těchto geografických oblastech můžete vytvářet pouze projekty, můžete posoudit nebo migrovat servery pro jiná cílová umístění.
- Geografie projektu se používá pouze k uložení zjištěných metadat.
- Když vytvoříte projekt, vyberete zeměpisnou oblast. Projekt a související prostředky jsou vytvořeny v jedné z oblastí zeměpisné oblasti. Oblast je přidělená službou Azure Migrate.

**Geografie** | **Umístění úložiště metadat**
--- | ---
Asie a Tichomoří | Východní Asie nebo jihovýchodní Asie
Austrálie | Austrálie – východ nebo Austrálie – jihovýchod
Brazílie | Brazílie – jih
Kanada | Kanada – střed nebo Kanada – východ
Evropa | Severní Evropa nebo Západní Evropa
Francie | Francie – střed
Indie | Střed Indie nebo Jižní Indie
Japonsko |  Japonsko – východ nebo Japonsko – západ
Jižní Korea | Korea – střed nebo Korea – jih
Švýcarsko | Švýcarsko – sever
Spojené království | Velká Británie – jih nebo Velká Británie – západ
USA | Střed USA nebo Západní USA 2

> [!NOTE]
> Pro oblast Švýcarsko je Švýcarsko – západ k dispozici jenom pro REST API uživatele a potřebujete schválené předplatné.

## <a name="supported-geographies-azure-government"></a>Podporovaná geografická oblast (Azure Government)

**Úkol** | **Geografie** | **Podrobnosti**
--- | --- | ---
Vytvoření projektu | USA | Metadata jsou uložena v US Gov – Arizona, US Gov – Virginie
Posouzení cíle | USA | Cílové oblasti: US Gov – Arizona, US Gov – Virginie US Gov – Texas
Cílová replikace | USA | Cílové oblasti: US DoD – střed, US DoD – východ, US Gov – Arizona, US Gov – Iowa, US Gov – Texas, US Gov – Virginie


## <a name="vmware-assessment-and-migration"></a>Posouzení a migrace VMware

[Přečtěte si téma](migrate-support-matrix-vmware.md) Azure Migrate: Assessment and Azure Migrate: vyhodnocování serveru pro virtuální počítače VMware.

## <a name="hyper-v-assessment-and-migration"></a>Vyhodnocení a migrace Hyper-V

[Přečtěte si téma](migrate-support-matrix-hyper-v.md) Azure Migrate: Assessment and Azure Migrate: vyhodnocování serveru a podpora pro virtuální počítače Hyper-V v rámci migrace serveru.



## <a name="azure-migrate-versions"></a>Verze Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: pomocí této verze můžete vytvářet nové projekty, zjišťovat místní vyhodnocení a orchestrovat posouzení a migrace. [Další informace](whats-new.md).
- **Předchozí verze**: pro zákazníky používající předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. V předchozí verzi nemůžete vytvářet nové projekty ani provádět nové zjišťování.

## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače VMware](./tutorial-assess-vmware-azure-vm.md) pro migraci.
- [Vyhodnoťte virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) pro migraci.
