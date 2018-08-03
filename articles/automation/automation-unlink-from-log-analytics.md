---
title: Zrušení propojení účtu Azure Automation s Log Analytics
description: Tento článek poskytuje přehled o tom, jak zrušit propojení účtu Azure Automation z pracovního prostoru Log Analytics.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1328ce8c306188c32bce5385f58f118a63c08deb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426529"
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Postupu zrušení propojení účtu Automation z pracovního prostoru Log Analytics

Azure Automation se integruje se službou Log Analytics pro nejen podporu monitorování úloh sad runbook napříč všemi účty služby Automation, ale je také nutný, když importujete následující řešení, které jsou závislé na Log Analytics:

* [Správa aktualizací](../operations-management-suite/oms-solution-update-management.md)
* [Sledování změn](../log-analytics/log-analytics-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že již nechcete integraci vašeho účtu Automation s Log Analytics, můžete svůj účet neodpojíte přímo z portálu Azure portal.  Než budete pokračovat, budete nejdřív muset odebrat řešení již bylo zmíněno dříve, jinak nebudou moct tento proces budete pokračovat. Přečtěte si článek pro konkrétní řešení, které jste importovali pochopit kroky nutné k jeho odebrání.

Po odebrání těchto řešení provedením následujících kroků se zrušit propojení účtu Automation.

> [!NOTE]
> Některá řešení, včetně starších verzí řešení monitorování Azure SQL pravděpodobně vytvořena prostředky služby automation a může také muset před rušení propojení pracovního prostoru odebrat.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

1. Z portálu Azure portal otevřete svůj účet Automation, a na automatizaci účtu vyberte stránku **pracovní prostor propojený** části **související prostředky** na levé straně.

1. Na stránce zrušit propojení pracovního prostoru klikněte na tlačítko **zrušit propojení pracovního prostoru**.

   ![Zrušit propojení pracovního prostoru stránky](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

1. Zatímco bude Azure Automation se pokusí zrušit propojení účtu pracovního prostoru Log Analytics, můžete sledovat průběh **oznámení** z nabídky.

Pokud jste použili řešení Update Management, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Plány aktualizace – každá bude mít názvy, které odpovídají vámi vytvořených nasazení aktualizací)

* Skupiny hybridních pracovních procesů vytvořené pro dané řešení – každá bude mít název podobně jako na machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Pokud jste použili spouštění/zastavování virtuálních počítačů špičku, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Spouštět a zastavovat sady runbook plány virtuálního počítače
* Spuštění a zastavení sad runbook virtuálního počítače
* Proměnné

## <a name="next-steps"></a>Další postup

Změna konfigurace účtu Automation pro integraci s Log Analytics, najdete v článku [předávání stavu úlohy a datové proudy úlohy ze služby Automation do Log Analytics](automation-manage-send-joblogs-log-analytics.md).