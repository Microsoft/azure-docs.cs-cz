---
title: Vytváření a odstraňování úloh v clusteru Azure Stream Analytics
description: Informace o spravovaných úlohách Stream Analytics v clusteru Azure Stream Analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 09a67d11ac4daf3e87a50ee2171f1ca49060c5bf
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018118"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Přidávání a odebírání úloh v clusteru Azure Stream Analytics

V clusteru Stream Analytics můžete spustit více Azure Stream Analytics úloh. Spuštění úloh v clusteru je jednoduchý proces se dvěma kroky: Přidání úlohy do clusteru a spuštění úlohy. V tomto článku se dozvíte, jak přidat a odebrat úlohy z existujícího clusteru. Pomocí rychlého startu [vytvořte cluster Stream Analytics](create-cluster.md) , pokud ho ještě nemáte.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Přidání úlohy Stream Analytics do clusteru

Do clusterů se dají přidat jenom existující úlohy Stream Analytics. Postupujte podle pokynů pro rychlý Start, [kde se naučíte vytvořit úlohu](stream-analytics-quick-create-portal.md) pomocí Azure Portal. Jakmile budete mít úlohu, kterou chcete přidat do clusteru, pomocí následujícího postupu přidejte úlohu do clusteru.

1. V Azure Portal Najděte cluster Stream Analytics a vyberte ho.

1. V části **Nastavení** vyberte **Stream Analytics úlohy**. Pak vyberte **Přidat existující úlohu**.

1. Vyberte předplatné a Stream Analytics úlohu, kterou chcete přidat do clusteru. Do clusteru je možné přidat pouze úlohy Stream Analytics, které jsou ve stejné oblasti jako cluster.

   ![Přidat úlohu do clusteru](./media/manage-jobs-cluster/add-job.png)

1. Po přidání úlohy do clusteru přejděte do prostředku úlohy a [Spusťte úlohu](start-job.md#azure-portal). Úloha se pak spustí v clusteru.

Všechny ostatní operace, jako jsou monitorování, výstrahy a diagnostické protokoly, můžete provádět na stránce prostředku úlohy Stream Analytics.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Odebrání Stream Analytics úlohy z clusteru

Aby bylo možné úlohy Stream Analytics odebrat z clusteru, je nutné, aby byly v zastaveném stavu. Pokud je vaše úloha pořád spuštěná, před přechodem k následujícím krokům tuto úlohu zastavte.

1. Vyhledejte a vyberte svůj cluster Stream Analytics.

1. V části **Nastavení** vyberte **Stream Analytics úlohy**.

1. Vyberte úlohy, které chcete z clusteru odebrat, a pak vyberte **Odebrat**.

   ![odebrat úlohu z clusteru](./media/manage-jobs-cluster/remove-job.png)

   Když se úloha odebere z Stream Analyticsho clusteru, vrátí se do standardního víceklientské prostředí.

## <a name="next-steps"></a>Další kroky

Nyní víte, jak přidávat a odebírat úlohy ve Azure Stream Analyticsm clusteru. V dalším kroku se dozvíte, jak spravovat privátní koncové body a škálovat clustery:

* [Škálování Azure Stream Analytics clusteru](scale-cluster.md)
* [Správa privátních koncových bodů v clusteru Azure Stream Analytics](private-endpoints.md)
