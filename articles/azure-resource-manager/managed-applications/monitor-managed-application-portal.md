---
title: Použití Azure Portal k monitorování spravované aplikace
description: Ukazuje, jak použít Azure Portal k monitorování dostupnosti a výstrah pro spravovanou aplikaci.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651745"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorování nasazené instance spravované aplikace

Po nasazení spravované aplikace do předplatného Azure budete možná chtít zjistit stav aplikace. Tento článek ukazuje možnosti Azure Portal pro kontrolu stavu. Můžete monitorovat dostupnost prostředků ve spravované aplikaci. Můžete také nastavit a zobrazit výstrahy.

## <a name="view-resource-health"></a>Zobrazení stavu prostředků

1. Vyberte instanci spravované aplikace.

   ![Vybrat spravovanou aplikaci](./media/monitor-managed-application-portal/select-managed-application.png)

1. Vyberte **Stav prostředku**.

   ![Vybrat stav prostředku](./media/monitor-managed-application-portal/select-resource-health.png)

1. Zobrazení dostupnosti prostředků ve spravované aplikaci.

   ![Zobrazení stavu prostředků](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Zobrazení upozornění

1. Vyberte **výstrahy**.

   ![Vybrat výstrahy](./media/monitor-managed-application-portal/select-alerts.png)

1. Pokud máte konfigurovaná pravidla upozornění, zobrazí se informace o výstrahách, které byly vyvolány.

   ![Zobrazení upozornění](./media/monitor-managed-application-portal/view-alerts.png)

1. Chcete-li přidat pravidla upozornění, vyberte **+ nové pravidlo výstrahy**.

   ![Vytvoření upozornění](./media/monitor-managed-application-portal/create-new-alert.png)

Můžete vytvořit upozornění pro instanci spravované aplikace nebo prostředky ve spravované aplikaci. Informace o vytváření výstrah najdete v tématu [Přehled výstrah v Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Další kroky

* Příklady spravovaných aplikací najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Pokud chcete nasadit spravovanou aplikaci, přečtěte si téma [nasazení aplikace Service Catalog prostřednictvím Azure Portal](deploy-service-catalog-quickstart.md).