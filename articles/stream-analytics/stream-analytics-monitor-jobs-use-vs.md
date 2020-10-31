---
title: Monitorování a Správa Azure Stream Analytics pomocí sady Visual Studio
description: Tento článek popisuje, jak pomocí sady Visual Studio monitorovat a spravovat Azure Stream Analytics úlohy.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a151d4ce5a2f9860c3b7919496a2edd25cc58ede
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123621"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Monitorování a správa úloh Stream Analytics pomocí sady Visual Studio

Tento článek ukazuje, jak monitorovat úlohu Stream Analytics v aplikaci Visual Studio. Azure Stream Analytics Tools for Visual Studio nabízí prostředí pro monitorování, které se podobá Azure Portal bez nutnosti opustit IDE. Můžete začít monitorovat úlohu hned po **odeslání do Azure** ze **skriptu. asaql** , nebo můžete monitorovat existující úlohy bez ohledu na to, jak byly vytvořeny. 

## <a name="job-summary"></a>Souhrn úlohy

**Souhrn úlohy** a **metriky úloh** poskytují rychlý snímek úlohy. Na první pohled můžete určit stav úlohy a informace o událostech.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Metriky úlohy

Můžete sbalit **Souhrn úlohy** a kliknout na kartu **metriky úlohy** a zobrazit graf s důležitými metrikami. Umožňuje zaškrtnout a zrušit kontrolu typů metriky, které chcete přidat a odebrat z grafu.

![Stream Analytics metriky v aplikaci Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Sledování chyb

Chyby můžete monitorovat také kliknutím na kartu **chyby** .

![Chyby Stream Analytics v aplikaci Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Získání podpory
Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Nainstalovat Azure Stream Analytics nástroje pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md)