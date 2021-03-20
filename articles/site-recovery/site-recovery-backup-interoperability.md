---
title: Podpora použití Azure Site Recovery s Azure Backup
description: Poskytuje přehled o tom, jak lze Azure Site Recovery a Azure Backup použít společně.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135184"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Podpora použití Site Recovery s Azure Backup

Tento článek shrnuje podporu pro používání [služby Site Recovery](site-recovery-overview.md) společně se [službou Azure Backup](../backup/backup-overview.md).

**Akce** | **Podpora Site Recovery** | **Podrobnosti**
--- | --- | ---
**Nasazení služeb společně** | Podporováno | Služby jsou interoperabilní a dají se nakonfigurovat společně.
**Zálohování a obnovení souborů** | Podporováno | Když se pro virtuální počítač povolí zálohování a replikace, dojde k žádnému problému při obnovování souborů na virtuálních počítačích na straně zdroje nebo na skupině virtuálních počítačů. Replikace pokračuje obvyklým způsobem bez změny stavu replikace.
**Obnovení disku** | Žádná aktuální podpora | Pokud obnovíte zálohovaný disk, budete muset znovu zakázat a znovu povolit replikaci virtuálního počítače.
**Obnovení virtuálního počítače** | Žádná aktuální podpora | Pokud obnovíte virtuální počítač nebo skupinu virtuálních počítačů, je třeba zakázat a znovu povolit replikaci virtuálního počítače.  


