---
title: Zobrazení oznámení o stavu služby s využitím webu Azure Portal
description: Oznámení o stavu služby umožňují zobrazit zprávy o stavu služby vydané Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748651"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Zobrazení oznámení o stavu služby s využitím webu Azure Portal

Oznámení o stavu služeb publikuje infrastruktura Azure do [protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).  Oznámení obsahují informace o prostředcích v rámci předplatného. Vzhledem k možnému velkému objemu informací uložených v protokolu aktivit existuje samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah na oznámení o stavu služby. 

Oznámení o stavu služby může být informační nebo žalovatelné, v závislosti na třídě.

Další informace o různých třídách oznámení o stavu služby naleznete v tématu [Service health notifications properties](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Zobrazení oznámení o stavu služeb na webu Azure Portal

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Monitor**.

    ![Snímek obrazovky s nabídkou Portál Azure s vybranou možností Monitor](./media/service-notifications/home-monitor.png)

    Azure Monitor sdružuje všechna nastavení monitorování a data do jednoho konsolidovaného zobrazení. Nejprve se otevře část **Protokol aktivit**.

1. Vyberte **možnost Výstrahy**.

    ![Snímek obrazovky s protokolem aktivit monitoru s vybranou možností Výstrahy](./media/service-notifications/service-health-summary.png)

1. Vyberte **+Přidat výstrahu protokolu aktivit**a nastavte výstrahu, abyste měli zajištěno, že budete upozorněni na budoucí oznámení služby. Další informace naleznete v [tématu Vytváření upozornění protokolu aktivit v oznámeních o službách](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Další kroky

* Další informace o [výstrahách protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md).
