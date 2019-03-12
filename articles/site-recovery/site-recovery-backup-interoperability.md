---
title: Azure Site Recovery – zálohování vzájemná funkční spolupráce | Dokumentace Microsoftu
description: Poskytuje přehled o tom, jak Azure Site Recovery a Azure Backup lze použít společně.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731867"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>O Site Recovery a Backup Interoperability

Tento článek obsahuje pokyny pro úspěšné používání zálohování virtuálních počítačů Azure IaaS a virtuálních počítačů Azure pro zotavení po havárii.

## <a name="azure-backup"></a>Azure Backup

Azure Backup pomáhá chránit data pro místní servery, virtuální počítače, virtualizované úlohy, SQL servery, servery služby SharePoint a další. Azure Site Recovery orchestruje a spravuje zotavení po havárii pro virtuální počítače Azure, místních virtuálních počítačů a fyzických serverů.

## <a name="azure-site-recovery"></a>Azure Site Recovery

Je možné nakonfigurovat na virtuální počítač nebo skupinu virtuálních počítačů Azure Backup a Azure Site Recovery. Interoperabilní jsou oba produkty. Několik scénářů, kde je vzájemná funkční spolupráce mezi Backup a Azure Site Recovery důležitá jsou následující:

### <a name="file-backuprestore"></a>Zálohování a obnovení souborů

Pokud zálohování a replikace jsou povoleny, a provést zálohu, neexistuje žádný problém s obnovováním soubory na straně zdrojový virtuální počítač nebo skupinu virtuálních počítačů. Replikace bude pokračovat jako obvykle myší bez nutnosti změn ve stavu replikace.

### <a name="disk-backuprestore"></a>Zálohování a obnovení disku

Pokud disk obnovit ze zálohy pak ochranu virtuálního počítače musí znovu povolit.

### <a name="vm-backuprestore"></a>Zálohování a obnovení virtuálních počítačů

Zálohování a obnovení virtuálních počítačů nebo skupiny virtuálních počítačů se nepodporuje. Aby to fungovalo, musí být znovu zapnout ochranu.

**Scénář** | **Podporuje Azure Site Recovery?** | **Alternativní řešení, pokud existuje**  
--- | --- | ---
Zálohování souborů a složek | Ano | Netýká se
Zálohování na disk | Není aktuálně | Zakázání a povolení ochrany
Zálohování virtuálních počítačů | Ne | Zakázání a povolení ochrany
