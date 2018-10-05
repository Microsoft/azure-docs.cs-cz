---
title: Pomocí webu Azure portal k monitorování spravované aplikace | Dokumentace Microsoftu
description: Ukazuje, jak pomocí webu Azure portal ke sledování dostupnosti a výstrahy pro spravované aplikace.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ea9f55d7c6002aaba2fd4fdc2a76b93f98e1d6b0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809317"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Sledovat nasazené instance spravované aplikace

Jakmile nasadíte spravovanou aplikaci se svým předplatným Azure, můžete chtít zkontrolovat stav aplikace. Tento článek popisuje možnosti na portálu Azure Portal pro kontrolu stavu. Monitoruje dostupnost prostředků ve vaší spravované aplikaci. Můžete také vytvořit a zobrazit výstrahy.

## <a name="view-resource-health"></a>Zobrazení stavu prostředků

1. Vyberte instanci spravované aplikace.

   ![Vyberte spravovanou aplikaci](./media/monitor-managed-application-portal/select-managed-application.png)

1. Vyberte **Resource Health**.

   ![Vyberte stav prostředku](./media/monitor-managed-application-portal/select-resource-health.png)

1. Zkontrolovat dostupnost prostředků ve spravované aplikaci.

   ![Zobrazení stavu prostředků](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Zobrazení upozornění

1. Vyberte **výstrahy**.

   ![Vyberte výstrahy](./media/monitor-managed-application-portal/select-alerts.png)

1. Pokud máte nakonfigurovaný pravidla upozornění se zobrazí informace o výstrahách, které byly.

   ![Zobrazení upozornění](./media/monitor-managed-application-portal/view-alerts.png)

1. Chcete-li přidat pravidla upozornění **+ nové pravidlo upozornění**.

   ![Vytvoření upozornění](./media/monitor-managed-application-portal/create-new-alert.png)

Můžete vytvořit výstrahy pro vaše instance spravované aplikace nebo prostředků ve spravované aplikaci. Informace o vytváření výstrah najdete v tématu [přehled výstrah v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="next-steps"></a>Další postup

* Spravované aplikace příklady najdete v tématu [ukázkových projektů Azure spravované aplikace](sample-projects.md).
* Nasazení spravované aplikace najdete v tématu [nasazení služby app Service catalog prostřednictvím webu Azure portal](deploy-service-catalog-quickstart.md).