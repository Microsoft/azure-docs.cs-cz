---
title: Upgrade agenta Azure Backup
description: Tyto informace se vysvětluje, proč byste měli upgradovat agenta Azure Backup a kde lze stáhnout v upgradu.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750326"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS
Verze agenta Microsoft Azure Recovery Service (MARS) pod 2.0.9083.0 má závislost na Azure Access Control service (ACS). V roce 2018 bude Azure [přestat používat Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Od 19. března 2018, všechny verze agenta MARS níže 2.0.9083.0 bude docházet k selháním zálohování. K zamezení nebo řešení selhání zálohování, [upgradovat na nejnovější verzi agenta MARS](https://go.microsoft.com/fwlink/?linkid=229525). Pokud chcete identifikovat servery, které vyžadují aktualizace agenta MARS, postupujte podle kroků v [blogu zálohování pro upgrade agentů Azure Backup](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). MARS agent se používá k zálohovat následující typy dat do Azure:
- Soubory 
- Data o stavu systému
- Zálohování nástroje System Center DPM do Azure
- Server Azure Backup (MABS)
