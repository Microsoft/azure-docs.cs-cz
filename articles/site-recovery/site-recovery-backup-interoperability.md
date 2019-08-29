---
title: Podpora použití Azure Site Recovery s Azure Backup
description: Poskytuje přehled o tom, jak lze Azure Site Recovery a Azure Backup použít společně.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146875"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Podpora použití Site Recovery s Azure Backup

Tento článek shrnuje podporu pro používání [služby Site Recovery](site-recovery-overview.md) společně se [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Akce** | **Podpora Site Recovery** | **Podrobnosti**
--- | --- | ---
**Nasazení služeb společně** | Podporováno | Služby jsou interoperabilní a dají se nakonfigurovat společně.
**Zálohování a obnovení souborů** | Podporováno | Když se pro virtuální počítač povolí zálohování a replikace, dojde k žádnému problému při obnovování souborů na virtuálních počítačích na straně zdroje nebo na skupině virtuálních počítačů. Replikace pokračuje obvyklým způsobem bez změny stavu replikace.
**Zálohování a obnovení disku** | Žádná aktuální podpora | Pokud obnovíte zálohovaný disk, budete muset znovu zakázat a znovu povolit replikaci virtuálního počítače.
**Zálohování a obnovení virtuálního počítače** | Žádná aktuální podpora | Pokud zálohujete nebo obnovíte virtuální počítač nebo skupinu virtuálních počítačů, musíte pro virtuální počítač Zakázat a znovu povolit replikaci.  


