---
title: Zrušení propojení účtu Azure Automation s Log Analytics
description: Tento článek obsahuje přehled postupu zrušení propojení účtu Azure Automation z pracovního prostoru analýzy protokolů.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f51103045f6a0cac1b1ed4f32200eaf7bef9cf24
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193872"
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Postup zrušení propojení účtu Automation z pracovního prostoru analýzy protokolů

Služby Azure Automation se integruje s analýzy protokolů pro nejen podporu monitorování vaše úlohy sady runbook ve všech svých účtů Automation, ale je také nutný, pokud importujete následující řešení, které jsou závislé na analýzy protokolů:

* [Správa aktualizací](../operations-management-suite/oms-solution-update-management.md)
* [Sledování změn](../log-analytics/log-analytics-change-tracking.md)
* [Spuštění a zastavení virtuálních počítačů, která](automation-solution-vm-management.md)

Pokud se rozhodnete, že již nechcete integrovat analýzy protokolů účtu Automation, můžete zrušit propojení účtu přímo z portálu Azure.  Než budete pokračovat, je nutné nejprve odebrat řešení již bylo zmíněno dříve, v opačném případě tento proces nebude možné pokračovat. Přečtěte si téma konkrétní řešení, které jste importovali pochopit na kroky potřebné k jeho odebrání.

Po odebrání těchto řešení, abyste mohli provést následující kroky zrušení propojení účtu Automation.

> [!NOTE]
> Některá řešení, včetně starších verzích řešení monitorování Azure SQL pravděpodobně vytvořena prostředky automation a může také nutné odebrat před odpojuje pracovním prostoru.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

1. Z portálu Azure otevřete účet Automation a na automatizace účet vyberte stránku **zrušit propojení prostoru** části **související prostředky** na levé straně.

   ![Zrušit propojení možnost pracovní prostor](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)

1. Na stránce prostoru zrušit propojení, klikněte na tlačítko **zrušit propojení prostoru**.

   ![Stránka pracovní prostor zrušit propojení](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

1. Zatímco Azure Automation se pokusí o zrušení propojení účtu pracovní prostor analýzy protokolů, můžete sledovat průběh v části **oznámení** z nabídky.

Pokud jste použili řešení pro správu aktualizací, Volitelně můžete odebrat následující položky, které již nejsou potřebné po odebrání řešení.

* Aktualizujte plány.  Každá bude mít názvy, které odpovídají nasazení aktualizací, které jste vytvořili)

* Pro toto řešení vytvořeny skupinám hybrid worker.  Každý bude mít název podobně jako na machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Pokud jste použili spuštění a zastavení virtuálních počítačů během počítačem nepracujete řešení, Volitelně můžete odebrat následující položky, které již nejsou potřebné po odebrání řešení.

* Spuštění a zastavení sady runbook plány virtuálních počítačů
* Spuštění a zastavení sad runbook virtuálních počítačů
* Proměnné

## <a name="next-steps"></a>Další postup

Změna konfigurace účtu Automation k integraci s analýzy protokolů, najdete v části [předávání zpráv o stavu úlohy a datové proudy úlohy ze služby Automation k analýze protokolů](automation-manage-send-joblogs-log-analytics.md).