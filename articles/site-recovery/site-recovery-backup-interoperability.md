---
title: Podpora pro pomocí Azure Site Recovery pomocí služby Azure Backup | Dokumentace Microsoftu
description: Poskytuje přehled o tom, jak Azure Site Recovery a Azure Backup lze použít společně.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312882"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Podpora pro používání služby Site Recovery pomocí služby Azure Backup

Tento článek shrnuje podporu pro použití [služby Site Recovery](site-recovery-overview.md) spolu s [služby Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Akce** | **Site Recovery podporu** | **Podrobnosti**
--- | --- | ---
**Nasazení služeb společně** | Podporováno | Služby jsou interoperabilní a dá se najednou.
**Zálohování a obnovení souborů** | Podporováno | Když jsou povolené zálohování a replikace pro virtuální počítač a zálohy jsou prováděny, neexistuje žádný problém v obnovování souborů na straně zdroje virtuální počítače, nebo skupinu virtuálních počítačů. Replikace bude pokračovat jako obvykle myší bez nutnosti změn ve stavu replikace.
**Zálohování a obnovení disku** | Žádné aktuální podpory | Pokud obnovit zálohy disku, musíte zakázat a znovu povolit replikaci pro virtuální počítač znovu.
**Zálohování a obnovení virtuálních počítačů** | Žádné aktuální podpory | Je-li zálohovat nebo obnovit virtuální počítač nebo skupinu virtuálních počítačů, musíte zakázat a znovu povolit replikaci pro virtuální počítač.  


