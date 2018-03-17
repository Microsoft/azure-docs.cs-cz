---
title: Upgrade agenta Azure Backup
description: "Tyto informace vysvětluje, proč byste měli upgradovat agenta Azure Backup a kde lze stáhnout upgradu."
services: backup
cloud: 
suite: 
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS
Verze agenta služby Microsoft Azure obnovení služby (MARS) pod 2.0.9083.0 jsou závislé na službě Řízení přístupu Azure (ACS). V 2018 Azure bude [přestat používat službu Řízení přístupu Azure (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Od 19. března 2018, všechny verze agenta MARS níže 2.0.9083.0 bude dojde k selhání zálohování. Chcete-li vyhnout nebo je vyřešit selhání zálohování, [upgradovat na nejnovější verzi agenta MARS](https://go.microsoft.com/fwlink/?linkid=229525). Pokud chcete identifikovat servery, které vyžadují agenta MARS upgrade, postupujte podle kroků v [blog zálohování pro upgrade agentů Azure Backup](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Agenta MARS se používá k zálohovat následující typy dat do Azure:
- Soubory 
- Data o stavu systému
- zálohování System Center DPM do Azure
- Server Azure Backup (MABS)
