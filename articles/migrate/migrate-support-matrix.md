---
title: Matice podpory Azure Migrate
description: Poskytuje přehled podpory nastavení a omezení pro službu Azure Migrate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811554"
---
# <a name="azure-migrate-support-matrix"></a>Matice podpory Azure Migrate

Můžete použít [služby Azure Migrate](migrate-overview.md) vyhodnocovat a migrovat počítače do cloudu Microsoft Azure. Tento článek shrnuje obecných nastavení a omezení pro scénáře Azure Migrate a nasazení.


## <a name="azure-migrate-versions"></a>Verze služby Azure Migrate

Existují dvě verze služby Azure Migrate:

- **Aktuální verze**: V této verzi, můžete si vytvořit nové projekty Azure Migrate, zjistíte místní vyhodnocuje a Orchestrace vyhodnocení a migrace. [Další informace](whats-new.md#azure-migrate-new-version).
- **Předchozí verze**: Pro zákazníky pomocí předchozí verze služby Azure Migrate (se podporuje pouze posouzení místních virtuálních počítačů VMware) byste teď měli použít aktuální verzi. V předchozí verzi nejde vytvořit nové projekty Azure Migrate nebo provedení nové zjišťování.

## <a name="supported-migration-scenarios"></a>Podporované scénáře migrace

Tabulka shrnuje Podporované scénáře migrace.

**Nasazení** | **Podrobnosti*** 
--- | --- 
**Posouzení místních** | Posuzuje místní úlohy a data, která běží na virtuálních počítačích Hyper-V a virtuálních počítačů VMware. Posouzení pomocí Azure Migrate Server Assessment a Microsoft Data Migration Assistant (DMA), jakož i nástroje třetích stran, které zahrnují Cloudamize společnosti Corent technické a Turbonomic serveru.
**Místní migrace do Azure** | Migrace úloh a dat na fyzické servery, virtuální počítače VMware, virtuálních počítačů Hyper-V a na instance AWS a GCP, do Azure. Migrace pomocí Azure Migrate Server Assessment a Azure Database Migration Service (DMS) a a pomocí nástroje třetích stran, které zahrnují Carbonite a CorentTech.

Konkrétní nástroj pro podporu se dají shrnout takto.

**Nástroj** | **Posouzení/migrace** | **Podrobnosti**
--- | --- | ---
Azure Migrate Server Assessment | Posouzení | Vyzkoušejte si server assessment pro [Hyper-V](tutorial-prepare-hyper-v.md) a [VMware](tutorial-prepare-vmware.md).
Cloudamize | Posouzení | [Další informace](https://www.cloudamize.com/platform#tab-0).
CorentTech | Posouzení | [Další informace](https://www.corenttech.com/).
Turbonomic | Posouzení | [Další informace](https://turbonomic.com/solutions/technologies/azure-cloud/).
Azure Migrate migrace serveru | Migrace | Vyzkoušejte si migraci serveru pro [Hyper-V](tutorial-migrate-hyper-v.md) a [VMware](tutorial-migrate-vmware.md).
Carbonite | Migrace | [Další informace](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migrace | [Další informace](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Projekty Azure Migrate

**Podpora** | **Podrobnosti**
--- | ---
Subscription | V jednom předplatném můžete mít jednoho projektu Azure Migrate.
Oprávnění Azure | Potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného pro vytvoření projektu Azure Migrate.
Virtuální počítače VMware  | Posouzení až 35 000 virtuálních počítačů VMware v jednom projektu.
Virtuální počítače Hyper-V | Posouzení až 10 000 virtuálních počítačů Hyper-V v jednom projektu.

Projekt může obsahovat virtuální počítače VMware a virtuálních počítačů Hyper-V, až do omezení hodnocení.


## <a name="vmware-assessment-and-migration"></a>VMware vyhodnocení a migrace

[Kontrola](migrate-support-matrix-vmware.md) Azure Migrate serveru vyhodnocení a migrace serveru matice podpory pro virtuální počítače VMware.

## <a name="hyper-v-assessment-and-migration"></a>Posouzení Hyper-V a migrace

[Kontrola](migrate-support-matrix-hyper-v.md) Azure Migrate serveru vyhodnocení a migrace serveru matice podpory pro virtuální počítače Hyper-V.


## <a name="next-steps"></a>Další postup

- [Posouzení virtuálních počítačů VMware](tutorial-assess-vmware.md) pro migraci.
- [Posouzení virtuálních počítačů Hyper-V](tutorial-assess-hyper-v.md) pro migraci.

