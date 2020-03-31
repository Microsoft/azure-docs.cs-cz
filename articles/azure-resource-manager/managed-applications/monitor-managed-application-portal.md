---
title: Monitorování spravované aplikace pomocí portálu Azure Portal
description: Ukazuje, jak používat portál Azure ke sledování dostupnosti a výstrah pro spravovanou aplikaci.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651745"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Sledování nasazené instance spravované aplikace

Po nasazení spravované aplikace do předplatného Azure můžete zkontrolovat stav aplikace. Tento článek ukazuje možnosti na webu Azure portal pro kontrolu stavu. Můžete sledovat dostupnost prostředků ve spravované aplikaci. Můžete také nastavit a zobrazit výstrahy.

## <a name="view-resource-health"></a>Zobrazení stavu prostředků

1. Vyberte instanci spravované aplikace.

   ![Vybrat spravovanou aplikaci](./media/monitor-managed-application-portal/select-managed-application.png)

1. Vyberte **možnost Stav prostředků**.

   ![Vybrat stav zdroje](./media/monitor-managed-application-portal/select-resource-health.png)

1. Zobrazení dostupnosti prostředků ve spravované aplikaci.

   ![Zobrazení stavu prostředků](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Zobrazení upozornění

1. Vyberte **možnost Výstrahy**.

   ![Vybrat výstrahy](./media/monitor-managed-application-portal/select-alerts.png)

1. Pokud máte nakonfigurovaná pravidla výstrah, zobrazí se informace o výstrahách, které byly vyvolány.

   ![Zobrazení upozornění](./media/monitor-managed-application-portal/view-alerts.png)

1. Chcete-li přidat pravidla výstrah, vyberte **možnost + Nové pravidlo výstrah**.

   ![Vytvoření upozornění](./media/monitor-managed-application-portal/create-new-alert.png)

Můžete vytvořit výstrahy pro instanci spravované aplikace nebo prostředky ve spravované aplikaci. Informace o vytváření výstrah najdete [v tématu Přehled výstrah v Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Další kroky

* Příklady spravovaných aplikací najdete v [tématu Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Informace o nasazení spravované aplikace najdete v [tématu Nasazení aplikace katalogu služeb prostřednictvím portálu Azure](deploy-service-catalog-quickstart.md).