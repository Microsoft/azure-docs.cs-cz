---
title: Zobrazení oznámení o stavu služby s využitím webu Azure Portal
description: Oznámení o stavu služby umožňují zobrazit zprávy služby service health publikování Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069357"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Zobrazení oznámení o stavu služby s využitím webu Azure Portal

Oznámení o stavu služby se publikují infrastrukturou Azure. Obsahují informace o prostředcích v rámci vašeho předplatného. Tato oznámení jsou dílčí třídy aktivity protokolu události a proto můžete také najít v protokolu aktivit. Oznámení o stavu služby může být informační nebo praktické, v závislosti na třídě.

Další informace o různých tříd oznámení o stavu služby, najdete v části [vlastností oznámení o stavu služby](../../service-health/service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Zobrazit oznámení o stavu služby na webu Azure Portal

1. V [webu Azure portal](https://portal.azure.com)vyberte **monitorování**.

    ![Nabídka portálu snímek obrazovky Azure s monitorováním vybrané](./media/service-notifications/home-monitor.png)

    Azure Monitor spojuje všechny vaše monitorování nastavení a data v jednom konsolidovaném zobrazení. Nejprve se otevře část **Protokol aktivit**.

1. Vyberte **výstrahy**.

    ![Snímek obrazovky monitorování aktivity protokolu s vybrané výstrahy](./media/service-notifications/service-health-summary.png)

1. Vyberte **+ přidat upozornění protokolu aktivit**a nastavte upozornění, abyste zajistili, se zobrazí oznámení pro budoucí služby oznámení. Další informace najdete v tématu [vytvoření upozornění protokolu aktivit pro oznámení služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Další postup

* Přijímat [oznámení pokaždé, když služba stavu oznámení o upozorněních](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) nový tweet.  
* Další informace o [upozornění protokolu aktivit](../../azure-monitor/platform/activity-log-alerts.md).
