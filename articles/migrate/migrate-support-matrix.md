---
title: Azure Migrateová matice podpory
description: Poskytuje souhrn nastavení podpory a omezení pro službu Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: 49bd193303255cdf7d18fd5da9dec8d84c50a829
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019190"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrateová matice podpory

[Službu Azure Migrate](migrate-overview.md) můžete použít k vyhodnocení a migraci počítačů do Microsoft Azure cloudu. Tento článek shrnuje obecná nastavení podpory a omezení pro Azure Migrate scénáře a nasazení.


## <a name="azure-migrate-versions"></a>Verze Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: Pomocí této verze můžete vytvořit nové projekty Azure Migrate, zjišťovat místní vyhodnocení a orchestrovat posouzení a migrace. [Další informace](whats-new.md#azure-migrate-new-version).
- **Předchozí verze**: Pro zákazníky, kteří používají předchozí verzi Azure Migrate (podporuje se jenom posouzení místních virtuálních počítačů VMware), byste teď měli použít aktuální verzi. V předchozí verzi nemůžete vytvářet nové projekty Azure Migrate ani provádět nové zjišťování.

## <a name="supported-migration-scenarios"></a>Podporované scénáře migrace

Tabulka shrnuje podporované scénáře migrace.

**Nasazení** | **Zobrazí*** 
--- | --- 
**Místní posouzení** | Vyhodnoťte místní úlohy a data běžící na virtuálních počítačích VMware a na virtuálních počítačích Hyper-V. Vyhodnotit pomocí Azure Migrate Server Assessment and Microsoft Data Migration Assistant (DMA) a také nástroje třetích stran, které zahrnují Cloudamize, spolupronajmutí tech and Turbonomic Server.
**Místní migrace do Azure** | Migrujte úlohy a data běžící na fyzických serverech, virtuálních počítačích VMware, virtuálních počítačích Hyper-V a na instancích AWS/GCP na Azure. Migrujte pomocí Azure Migrate Server Assessment and Azure Database Migration Service (DMS) a také pomocí nástrojů třetích stran, které zahrnují Carbonite a CorentTech.

Konkrétní podpora nástrojů je shrnuta následujícím způsobem.

**Nástroj** | **Posouzení/migrace** | **Podrobnosti**
--- | --- | ---
Vyhodnocování serveru Azure Migrate | Posouzení | Vyzkoušejte si vyhodnocování serveru pro [Hyper-V](tutorial-prepare-hyper-v.md) a [VMware](tutorial-prepare-vmware.md).
Cloudamize | Posouzení | [Další informace](https://www.cloudamize.com/platform#tab-0).
CorentTech | Posouzení | [Další informace](https://www.corenttech.com/).
Turbonomic | Posouzení | [Další informace](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migrace serveru Azure Migrate | Migrace | Vyzkoušejte migraci serveru pro [Hyper-V](tutorial-migrate-hyper-v.md) a [VMware](tutorial-migrate-vmware.md).
Carbonite | Migrace | [Další informace](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migrace | [Další informace](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Azure Migrate projekty

**Podpora** | **Podrobnosti**
--- | ---
Subscription | V předplatném můžete mít jeden Azure Migrate projekt.
Oprávnění Azure | Chcete-li vytvořit projekt Azure Migrate, potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného.
Virtuální počítače VMware  | Vyhodnoťte až 35 000 virtuálních počítačů VMware v jednom projektu.
Virtuální počítače Hyper-V | Vyhodnoťte až 10 000 virtuálních počítačů Hyper-V v jednom projektu.

Projekt může zahrnovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do limitů hodnocení.


## <a name="vmware-assessment-and-migration"></a>Posouzení a migrace VMware

[Přečtěte](migrate-support-matrix-vmware.md) si matrici podpory Azure Migrate serveru a podpora migrace serveru pro virtuální počítače VMware.

## <a name="hyper-v-assessment-and-migration"></a>Vyhodnocení a migrace Hyper-V

[Přečtěte](migrate-support-matrix-hyper-v.md) si matrici podpory Azure Migrate serveru a podpora migrace serveru pro virtuální počítače Hyper-V.


## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače VMware](tutorial-assess-vmware.md) pro migraci.
- [Vyhodnoťte virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) pro migraci.

