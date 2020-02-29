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
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197112"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS

Verze agenta Microsoft Azure Recovery Services (MARS) níže 2.0.9083.0 být závislá na službě Azure Access Control. Agent MARS se také označuje jako agent Azure Backup.

V 2018 společnost Microsoft [zastarala službu Azure Access Control](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Od 19. března 2018 se ve všech verzích agenta MARS níže 2.0.9083.0 projeví selhání zálohování. Pokud chcete předejít nebo vyřešit selhání zálohování, [Upgradujte agenta Mars na nejnovější verzi](https://go.microsoft.com/fwlink/?linkid=229525). Pokud chcete identifikovat servery, které vyžadují upgrade agenta MARS, postupujte podle kroků v [blogu Backup pro upgrade agentů Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

Agent MARS slouží k zálohování souborů a složek a dat stavu systému do Azure. Aplikace System Center DPM a Azure Backup Server k zálohování dat do Azure použít agenta MARS.
