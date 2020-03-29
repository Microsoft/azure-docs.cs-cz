---
title: Podpora pro používání Azure Site Recovery s Azure Backup
description: Poskytuje přehled o tom, jak azure site recovery a Azure Backup lze použít společně.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376221"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Podpora používání site recovery s Azure Backup

Tento článek shrnuje podporu pro použití [služby Site Recovery](site-recovery-overview.md) společně se [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Akce** | **Podpora Site Recovery** | **Podrobnosti**
--- | --- | ---
**Nasazení služeb společně** | Podporuje se | Služby jsou interoperabilní a lze je konfigurovat společně.
**Zálohování a obnovení souborů** | Podporuje se | Při zálohování a replikace jsou povoleny pro virtuální počítač a zálohy jsou převzaty, není žádný problém při obnovení souborů na straně zdroje virtuálních počítačích nebo skupiny virtuálních počítačích. Replikace pokračuje jako obvykle bez evidencí stavu replikace.
**Obnovení disku** | Žádná aktuální podpora | Pokud obnovíte zálohovaný disk, budete muset zakázat a znovu povolit replikaci pro virtuální počítače znovu.
**Obnovení virtuálního počítače** | Žádná aktuální podpora | Pokud obnovíte virtuální ho virtuálního serveru nebo skupinu virtuálních ms, musíte zakázat a znovu povolit replikaci pro virtuální ho.  


