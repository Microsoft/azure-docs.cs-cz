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
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 2a969240da6e16f5a5ba76605a6efa2a96f9e4dd
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285673"
---
## <a name="upgrade-the-mars-agent"></a>Upgrade agenta MARS

Verze agenta Microsoft Azure Recovery Service (MARS) pod 2.0.9083.0 má závislost na Azure Access Control service (ACS). Agenta MARS se také označuje jako agenta Azure Backup. V roce 2018, Azure [zastaralé Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Od 19. března 2018, všechny verze agenta MARS níže 2.0.9083.0 bude docházet k selháním zálohování. K zamezení nebo řešení selhání zálohování, [upgradovat na nejnovější verzi agenta MARS](https://go.microsoft.com/fwlink/?linkid=229525). Pokud chcete identifikovat servery, které vyžadují aktualizace agenta MARS, postupujte podle kroků v [blogu zálohování pro upgrade agentů MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). MARS agent se používá k zálohování souborů a složek a dat o stavu systému do Azure. System Center DPM a Azure Backup Server pomocí agenta MARS zálohovat data do Azure.
