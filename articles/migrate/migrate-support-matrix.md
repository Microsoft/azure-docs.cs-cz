---
title: Azure Migrateová matice podpory
description: Poskytuje souhrn nastavení podpory a omezení pro službu Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: raynew
ms.openlocfilehash: 90b1250009e6efdb2f8cb9351fe270c8324cc77a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715478"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrateová matice podpory

[Službu Azure Migrate](migrate-overview.md) můžete použít k vyhodnocení a migraci počítačů do Microsoft Azure cloudu. Tento článek shrnuje obecná nastavení podpory a omezení pro Azure Migrate scénáře a nasazení.


## <a name="azure-migrate-versions"></a>Verze Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: pomocí této verze můžete vytvořit nové projekty Azure Migrate, zjišťovat místní vyhodnocení a orchestrovat posouzení a migrace. [Další informace](whats-new.md#release-version-july-2019)
- **Předchozí verze**: pro zákazníky používající předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), měli byste teď použít aktuální verzi. V předchozí verzi nemůžete vytvářet nové projekty Azure Migrate ani provádět nové zjišťování.

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
Migrace serveru Azure Migrate | Není k dispozici | Migrujte [virtuální počítače VMware](tutorial-migrate-vmware.md), [virtuální počítače Hyper-V](tutorial-migrate-hyper-v.md)a [fyzické servery](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Není k dispozici | Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. | Není k dispozici
[Technologie spolupronájmu](https://go.microsoft.com/fwlink/?linkid=2084928) | Vyhodnoťte a migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. |  Migrujte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu.
[Zařízení 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu.| Není k dispozici
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Vyhodnoťte místní SQL Server databáze. | Není k dispozici
[DOKUMENTŮ](https://docs.microsoft.com/azure/dms/dms-overview) | Migrujte SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. | Není k dispozici
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Posouzení infrastruktury virtuálních klientských počítačů (VDI) | Není k dispozici
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Posouzení virtuálních počítačů VMWare, virtuálních počítačů Hyper-V, virtuálních počítačů, virtuálních počítačů, pracovních stanic (včetně infrastruktury VDI), úloh veřejných cloudů | Není k dispozici
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Není k dispozici | Migrace virtuálních počítačů VMWare, virtuálních počítačů Hyper-V, virtuálních počítačů Xen, KVM virtuálních počítačů, fyzických počítačů, úloh veřejných cloudů 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery a úlohy veřejného cloudu. | Není k dispozici
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Vyhodnoťte virtuální počítače VMware, virtuální počítače Hyper-V, fyzické servery, úlohy veřejného cloudu a SQL Server databáze. | Není k dispozici
[WebApp Pomocník s migrací](https://appmigration.microsoft.com/) | Posouzení webových aplikací | Migrujte webové aplikace.


## <a name="azure-migrate-projects"></a>Azure Migrate projekty

**Podpora** | **Podrobnosti**
--- | ---
Předplatné | V předplatném můžete mít více Azure Migratech projektů.
Oprávnění Azure | Chcete-li vytvořit projekt Azure Migrate, potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného.
Virtuální počítače VMware  | Vyhodnoťte až 35 000 virtuálních počítačů VMware v jednom projektu.
Virtuální počítače Hyper-V | Vyhodnoťte až 35 000 virtuálních počítačů Hyper-V v jednom projektu.

Projekt může zahrnovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do limitů hodnocení.

## <a name="supported-geographies"></a>Podporovaná geografická oblast

Azure Migrate projekt můžete vytvořit v řadě geografických oblastí. I když v těchto geografických oblastech můžete vytvářet pouze projekty, můžete vyhodnocovat nebo migrovat počítače pro jiná cílová umístění. Geografie projektu se používá pouze k uložení zjištěných metadat.

**Zeměpisné oblasti** | **Umístění úložiště metadat**
--- | ---
Azure Government | USA (Gov) – Virginia
Asie a Tichomoří | Východní Asie nebo jihovýchodní Asie
Austrálie | Austrálie – východ nebo Austrálie – jihovýchod
Brazílie | Brazílie – jih
Kanada | Kanada – střed nebo Kanada – východ
Evropa | Severní Evropa nebo Západní Evropa
Francie | Francie – střed
Indie | Střed Indie nebo Jižní Indie
Japonsko |  Japonsko – východ nebo Japonsko – západ
Jižní Korea | Korea – střed nebo Korea – jih
Spojené království | Velká Británie – jih nebo Velká Británie – západ
Spojené státy | Střed USA nebo Západní USA 2


 > [!NOTE]
 > Podpora Azure Government je v tuto chvíli dostupná jenom pro [starší verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate.



## <a name="vmware-assessment-and-migration"></a>Posouzení a migrace VMware

[Přečtěte](migrate-support-matrix-vmware.md) si matrici podpory Azure Migrate serveru a podpora migrace serveru pro virtuální počítače VMware.

## <a name="hyper-v-assessment-and-migration"></a>Vyhodnocení a migrace Hyper-V

[Přečtěte](migrate-support-matrix-hyper-v.md) si matrici podpory Azure Migrate serveru a podpora migrace serveru pro virtuální počítače Hyper-V.


## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače VMware](tutorial-assess-vmware.md) pro migraci.
- [Vyhodnoťte virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) pro migraci.

