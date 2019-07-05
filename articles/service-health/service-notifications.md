---
title: Zobrazení oznámení o stavu služby s využitím webu Azure Portal
description: Oznámení o stavu služby umožňují zobrazit zprávy služby service health publikování Microsoft Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538351"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Zobrazení oznámení o stavu služby s využitím webu Azure Portal

Oznámení o stavu služby jsou publikovány na infrastrukturu Azure [protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md).  Tato oznámení obsahují informace o prostředky v rámci vašeho předplatného. Zadaný může být velký objem informací uložených v protokolu aktivit, je samostatné uživatelské rozhraní, aby bylo snazší zobrazit a nastavení výstrah u Oznámení o stavu služby. 

Oznámení o stavu služby může být informační nebo praktické, v závislosti na třídě.

Další informace o různých tříd oznámení o stavu služby, najdete v části [vlastností oznámení o stavu služby](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Zobrazit oznámení o stavu služby na webu Azure Portal

1. V [webu Azure portal](https://portal.azure.com)vyberte **monitorování**.

    ![Nabídka portálu snímek obrazovky Azure s monitorováním vybrané](./media/service-notifications/home-monitor.png)

    Azure Monitor spojuje všechny vaše monitorování nastavení a data v jednom konsolidovaném zobrazení. Nejprve se otevře část **Protokol aktivit**.

1. Vyberte **výstrahy**.

    ![Snímek obrazovky monitorování aktivity protokolu s vybrané výstrahy](./media/service-notifications/service-health-summary.png)

1. Vyberte **+ přidat upozornění protokolu aktivit**a nastavte upozornění, abyste zajistili, se zobrazí oznámení pro budoucí služby oznámení. Další informace najdete v tématu [vytvoření upozornění protokolu aktivit pro oznámení služby](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Další postup

* Další informace o [upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md).
