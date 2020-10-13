---
title: Zobrazení oznámení o stavu služby s využitím webu Azure Portal
description: Zobrazení oznámení o stavu služby v Azure Portal. Infrastruktura Azure zveřejňuje oznámení o stavu služby do protokolu aktivit Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 615d08b6a04aef9e8ef2033154da8ff8caeebe04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967775"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Zobrazení oznámení o stavu služby s využitím webu Azure Portal

Infrastruktura Azure zveřejňuje oznámení o stavu služby do [protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).  Oznámení obsahují informace o prostředcích v rámci vašeho předplatného. Vzhledem k tomu, že je možné velké množství informací uložených v protokolu aktivit, je k dispozici samostatné uživatelské rozhraní, které usnadňuje zobrazení a nastavení výstrah pro oznámení o stavu služby. 

Oznámení o stavu služby můžou být informativní nebo napadnutelná v závislosti na třídě.

Další informace o různých třídách oznámení o stavu služby najdete v tématu [vlastnosti upozornění na stav služby](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Zobrazení oznámení o stavu služby v Azure Portal

1. V [Azure Portal](https://portal.azure.com)vyberte **monitorovat**.

    ![Snímek obrazovky nabídky Azure Portal s vybraným monitorováním](./media/service-notifications/home-monitor.png)

    Azure Monitor spojí všechna nastavení monitorování a data do jednoho konsolidovaného zobrazení. Nejprve se otevře část **Protokol aktivit**.

1. Vyberte **výstrahy**.

    ![Snímek obrazovky protokolu aktivity monitorování s vybranými výstrahami](./media/service-notifications/service-health-summary.png)

1. Vyberte **+ Přidat upozornění protokolu aktivit**a nastavte výstrahu, abyste se ujistili, že máte oznámení o budoucích oznámeních služby. Další informace najdete v tématu [vytváření výstrah protokolu aktivit u oznámení služby](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [upozorněních protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md).
