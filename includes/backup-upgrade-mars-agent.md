---
title: Upgrade agenta Azure Backup
description: Tyto informace vysvětlují, proč byste měli upgradovat agenta Azure Backup a kam stáhnout upgrade.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: c22fbcd07286ddffedd8fc2fdc12017b9338d7f7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161825"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS

Verze agenta Microsoft Azure Recovery Services (MARS) níže 2.0.9083.0 mají závislost na službě Azure Access Control Service (ACS). Agent MARS se také označuje jako agent Azure Backup. V 2018 se v Azure [zastaralá služba azure Access Control Service (ACS)](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Od 19. března 2018 se ve všech verzích agenta MARS níže 2.0.9083.0 projeví selhání zálohování. Pokud chcete předejít nebo vyřešit selhání zálohování, [Upgradujte agenta Mars na nejnovější verzi](https://go.microsoft.com/fwlink/?linkid=229525). Pokud chcete identifikovat servery, které vyžadují upgrade agenta MARS, postupujte podle kroků v [blogu Backup pro upgrade agentů Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Agent MARS slouží k zálohování souborů a složek a dat stavu systému do Azure. Aplikace System Center DPM a Azure Backup Server k zálohování dat do Azure použít agenta MARS.
