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
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251534"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS

Verze agenta Microsoft Azure Recovery Services (MARS) níže 2.0.9083.0 mají závislost na službě Azure Access Control Service (ACS). Agent MARS se také označuje jako agent Azure Backup. V 2018 se v Azure zastaralá [Služba azure Access Control Service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Od 19. března 2018 se ve všech verzích agenta MARS níže 2.0.9083.0 projeví selhání zálohování. Pokud chcete předejít nebo vyřešit selhání zálohování, [Upgradujte agenta Mars na nejnovější verzi](https://go.microsoft.com/fwlink/?linkid=229525). Pokud chcete identifikovat servery, které vyžadují upgrade agenta MARS, postupujte podle kroků v [blogu Backup pro upgrade agentů Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Agent MARS slouží k zálohování souborů a složek a dat stavu systému do Azure. Aplikace System Center DPM a Azure Backup Server k zálohování dat do Azure použít agenta MARS.
