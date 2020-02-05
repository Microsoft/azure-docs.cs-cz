---
title: Azure Migrateová matice podpory
description: Poskytuje souhrn nastavení podpory a omezení pro službu Azure Migrate.
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: raynew
ms.openlocfilehash: 5c3adecf62f9bd15c820baf116dbc01e5d3542fa
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014007"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrateová matice podpory

[Službu Azure Migrate](migrate-overview.md) můžete použít k vyhodnocení a migraci počítačů do Microsoft Azure cloudu. Tento článek shrnuje obecná nastavení podpory a omezení pro Azure Migrate scénáře a nasazení.

## <a name="supported-assessmentmigration-scenarios"></a>Podporované scénáře posouzení/migrace

Tabulka shrnuje podporované scénáře zjišťování, hodnocení a migrace.

**Nasazení** | **Podrobnosti** 
--- | --- 
**Zjišťování specifické pro aplikaci** | Můžete zjišťovat aplikace, role a funkce spuštěné na virtuálních počítačích VMware. V tuto chvíli je tato funkce omezená jenom na zjišťování. Posouzení je aktuálně na úrovni počítače. Ještě nenabízíme hodnocení specifické pro aplikace, role ani funkce. 
**Místní posouzení** | Vyhodnoťte místní úlohy a data běžící na virtuálních počítačích VMware, virtuálních počítačích Hyper-V a fyzických serverech. Vyhodnoťte pomocí Azure Migrate Server Assessment and Microsoft Data Migration Assistant (DMA) a také další nástroje a nabídky ISV.
**Místní migrace do Azure** | Migrujte úlohy a data spuštěná na fyzických serverech, virtuálních počítačích VMware, virtuálních počítačích Hyper-V, fyzických serverech a cloudových virtuálních počítačích do Azure. Migrujte pomocí Azure Migrate Server Assessment and Azure Database Migration Service (DMS) a také další nástroje a nabídky ISV.


## <a name="supported-tools"></a>Podporované nástroje

Konkrétní podpora nástrojů je shrnuta v tabulce.

**Nástroj** | **Místa** | **Migrace** 
--- | --- | ---
Vyhodnocování serveru Azure Migrate | Posouzení [virtuálních počítačů VMware](tutorial-prepare-vmware.md), [virtuálních počítačů Hyper-V](tutorial-prepare-hyper-v.md)a [fyzických serverů](tutorial-prepare-physical.md). |  Není k dispozici (NA)
Migrace serveru Azure Migrate | není k dispozici | Migrujte [virtuální počítače VMware](tutorial-migrate-vmware.md), [virtuální počítače Hyper-V](tutorial-migrate-hyper-v.md)a [fyzické servery](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | není k dispozici | Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. | není k dispozici
[Technologie spolupronájmu](https://go.microsoft.com/fwlink/?linkid=2084928) | Vyhodnoťte a migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. |  Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu.
[Zařízení 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu.| není k dispozici
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Vyhodnoťte místní SQL Server databáze. | není k dispozici
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | není k dispozici | Migrujte SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posouzení infrastruktury virtuálních klientských počítačů (VDI) | není k dispozici
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Posouzení virtuálních počítačů VMWare, virtuálních počítačů Hyper-V, virtuálních počítačů, virtuálních počítačů, pracovních stanic (včetně infrastruktury VDI), úloh veřejných cloudů | není k dispozici
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | není k dispozici | Migrace virtuálních počítačů VMWare, virtuálních počítačů Hyper-V, virtuálních počítačů Xen, KVM virtuálních počítačů, fyzických počítačů, úloh veřejných cloudů 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. | není k dispozici
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery, úlohy veřejného cloudu a SQL Server databáze. | není k dispozici
[WebApp Pomocník s migrací](https://appmigration.microsoft.com/) | Posouzení webových aplikací | Migrujte webové aplikace.


## <a name="azure-migrate-projects"></a>Azure Migrate projekty

**Podpora** | **Podrobnosti**
--- | ---
Předplatné | V předplatném můžete mít více Azure Migratech projektů.
Oprávnění Azure | Chcete-li vytvořit projekt Azure Migrate, potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného.
Virtuální počítače VMware  | Vyhodnoťte až 35 000 virtuálních počítačů VMware v jednom projektu.
Virtuální počítače Hyper-V | Vyhodnoťte až 35 000 virtuálních počítačů Hyper-V v jednom projektu.

Projekt může zahrnovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do limitů hodnocení.

## <a name="azure-permissions"></a>Oprávnění Azure

Aby Azure Migrate fungoval s Azure, budete potřebovat tato oprávnění předtím, než začnete vyhodnocování a migraci počítačů.

**Úkol** | **Oprávnění** | **Podrobnosti**
--- | --- | ---
Vytvoření projektu Azure Migrate | Váš účet Azure potřebuje oprávnění k vytvoření projektu. | Nastavte pro [VMware](tutorial-prepare-vmware.md#assign-permissions-to-create-project), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)nebo [fyzické servery](tutorial-prepare-physical.md#assign-permissions-to-create-project).
Registrace zařízení Azure Migrate| Azure Migrate využívá odlehčené [Azure Migrate zařízení](migrate-appliance.md) k vyhodnocení počítačů pomocí nástroje Azure Migrateho posouzení serveru a spouštění [migrace](server-migrate-overview.md) virtuálních počítačů VMware bez agenta pomocí migrace serveru Azure Migrate. Toto zařízení zjišťuje počítače a odesílá data o metadatech a výkonu Azure Migrate.<br/><br/> Během registrace se registrace zprostředkovatelů (Microsoft. OffAzure, Microsoft. Migruj a Microsoft. klíčů) zaregistrují u předplatného, které jste zvolili v zařízení, aby předplatné fungovalo s poskytovatelem prostředků. K registraci budete potřebovat přístup pro přispěvatele nebo vlastníka k tomuto předplatnému.<br/><br/> **VMware**– během připojování Azure Migrate vytvoří dvě aplikace Azure Active Directory (Azure AD). První aplikace komunikuje mezi agenty zařízení a službou Azure Migrate. Aplikace nemá oprávnění k provedení volání správy prostředků Azure nebo přístupu k prostředkům RBAC. Druhá aplikace přistupuje k Azure Key Vault vytvořenému v předplatném uživatele jenom pro migraci VMware bez agenta. Při migraci bez agenta Azure Migrate vytvoří Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. Při zahájení zjišťování ze zařízení má přístup RBAC na Azure Key Vault (v tenantovi zákazníka).<br/><br/> **Hyper-V**– při připojování. Azure Migrate vytvoří jednu aplikaci Azure AD. Aplikace komunikuje mezi agenty zařízení a službou Azure Migrate. Aplikace nemá oprávnění k provedení volání správy prostředků Azure nebo přístupu k prostředkům RBAC. | Nastavte pro [VMware](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance), [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)nebo [fyzické servery](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance).
Vytvoření trezoru klíčů pro migraci bez agentů VMware | Pokud chcete migrovat virtuální počítače VMware s migrací Azure Migrate serveru bez agentů, Azure Migrate vytvoří Key Vault pro správu přístupových klíčů k účtu úložiště replikace v rámci vašeho předplatného. Chcete-li vytvořit trezor, nastavte oprávnění (vlastníka nebo přispěvatel a správce přístupu uživatele) na skupinu prostředků, ve které se nachází Azure Migrate projekt. | [Nastavte](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) oprávnění.

## <a name="supported-geographies"></a>Podporovaná geografická oblast

Azure Migrate projekt můžete vytvořit v řadě geografických oblastí. I když v těchto geografických oblastech můžete vytvářet pouze projekty, můžete vyhodnocovat nebo migrovat počítače pro jiná cílová umístění. Geografie projektu se používá pouze k uložení zjištěných metadat.

**Zeměpisné oblasti** | **Umístění úložiště metadat**
--- | ---
Azure Government | US Gov – Virginie
Asie a Tichomoří | Východní Asie nebo jihovýchodní Asie
Austrálie | Austrálie – východ nebo Austrálie – jihovýchod
Brazílie | Brazílie – jih
Kanada | Kanada – střed nebo Kanada – východ
Evropa | Severní Evropa nebo Západní Evropa
Francie | Francie – střed
Indie | Střed Indie nebo Jižní Indie
Japonsko |  Japonsko – východ nebo Japonsko – západ
Korea | Korea – střed nebo Korea – jih
Spojené království | Velká Británie – jih nebo Velká Británie – západ
Spojené státy | Střed USA nebo Západní USA 2


 > [!NOTE]
 > Podpora Azure Government je v tuto chvíli dostupná jenom pro [starší verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>Posouzení a migrace VMware

[Přečtěte](migrate-support-matrix-vmware.md) si matrici podpory Azure Migrate serveru a podpora migrace serveru pro virtuální počítače VMware.

## <a name="hyper-v-assessment-and-migration"></a>Vyhodnocení a migrace Hyper-V

[Přečtěte](migrate-support-matrix-hyper-v.md) si matrici podpory Azure Migrate serveru a podpora migrace serveru pro virtuální počítače Hyper-V.



## <a name="azure-migrate-versions"></a>Verze Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: pomocí této verze můžete vytvořit nové projekty Azure Migrate, zjišťovat místní vyhodnocení a orchestrovat posouzení a migrace. [Další informace](whats-new.md#release-version-july-2019).
- **Předchozí verze**: pro zákazníky používající předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. V předchozí verzi nemůžete vytvářet nové projekty Azure Migrate ani provádět nové zjišťování.

## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače VMware](tutorial-assess-vmware.md) pro migraci.
- [Vyhodnoťte virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) pro migraci.

