---
title: Upgrade agenta zálohování Azure
description: Tyto informace vysvětlují, proč byste měli upgradovat agenta zálohování Azure a kde upgrade stáhnout.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673195"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS

Verze agenta Microsoft Azure Recovery Services (MARS) pod 2.0.9083.0 mají závislost na službě Azure Access Control. Agent MARS se také označuje jako agent zálohování Azure.

V roce 2018 Microsoft [zastaral službu Azure Access Control](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). března 2018 budou všechny verze agenta MARS pod 2.0.9083.0 zažívat selhání zálohování. Chcete-li se vyhnout chybám zálohování nebo je vyřešit, [upgradujte agenta MARS na nejnovější verzi](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Chcete-li identifikovat servery, které vyžadují upgrade agenta MARS, postupujte podle pokynů v [části Upgrade agenta služby Microsoft Azure Recovery Services (MARS).](../articles/backup/upgrade-mars-agent.md)

Agent MARS se používá k zálohování souborů a složek a dat stavu systému do Azure. System Center DPM a Azure Backup Server používají agenta MARS k zálohování dat do Azure.
