---
title: Upgrade agenta Azure Backup
description: Tyto informace vysvětlují, proč byste měli upgradovat agenta Azure Backup a kam stáhnout upgrade.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294163"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS

Verze agenta Microsoft Azure Recovery Services (MARS) níže 2.0.9083.0 být závislá na službě Azure Access Control. Agent MARS se také označuje jako agent Azure Backup.

V 2018 společnost Microsoft [zastarala službu Azure Access Control](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Od 19. března 2018 se ve všech verzích agenta MARS níže 2.0.9083.0 projeví selhání zálohování. Pokud chcete předejít nebo vyřešit selhání zálohování, [Upgradujte agenta Mars na nejnovější verzi](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Pokud chcete identifikovat servery, které vyžadují upgrade agenta MARS, postupujte podle pokynů v části [Upgrade agenta Microsoft Azure Recovery Services (MARS)](../articles/backup/upgrade-mars-agent.md).

Agent MARS slouží k zálohování souborů a složek a dat stavu systému do Azure. Aplikace System Center DPM a Azure Backup Server k zálohování dat do Azure použít agenta MARS.
