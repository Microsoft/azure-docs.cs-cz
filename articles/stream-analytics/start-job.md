---
title: Jak spustit úlohu Azure Stream Analytics
description: Tento článek popisuje, jak spustit úlohu Stream Analytics z Azure Portal, PowerShellu a Visual Studia.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426471"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Jak spustit úlohu Azure Stream Analytics

Úlohu Azure Stream Analytics můžete spustit pomocí portálu Azure, Visual Studia a PowerShellu. Při spuštění úlohy vyberete čas, kdy má úloha začít vytvářet výstup. Portál Azure, Visual Studio a PowerShell mají různé metody pro nastavení času zahájení. Tyto metody jsou popsány níže.

## <a name="start-options"></a>Možnosti spuštění
Pro zahájení úlohy jsou k dispozici tři následující možnosti. Všimněte si, že všechny níže uvedené časy jsou ty, které jsou uvedeny v [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Pokud timestamp BY není zadán, bude použit čas příjezdu.
* **Nyní**: Naněj počáteční bod výstupní události datového proudu stejné jako při spuštění úlohy. Pokud se používá časový operátor (například časové okno, Lag nebo JOIN), Azure Stream Analytics se automaticky vrátí zpět na data ve vstupním zdroji. Například pokud spustíte úlohu "Nyní" a pokud váš dotaz používá 5 minut omílání okno, Azure Stream Analytics bude hledat data z 5 minut před vstupem.
První možné výstupní události by časové razítko stejné nebo větší než aktuální čas a ASA zaručuje, že všechny vstupní události, které mohou logicky přispět k výstupu byla započítána. Například nejsou generovány žádné částečné windowed agregace. Je to vždy kompletní agregovaná hodnota.

* **Vlastní**: Můžete zvolit počáteční bod výstupu. Podobně jako **možnost Nyní** azure stream analytics automaticky přečte data před touto dobou, pokud se používá časový operátor 

* **Při posledním zastavení**. Tato možnost je k dispozici při předchozím spuštění úlohy, ale byla zastavena ručně nebo se nezdařila. Při výběru této možnosti Azure Stream Analytics použije poslední výstupní čas k restartování úlohy, takže nedojde ke ztrátě žádných dat. Podobně jako předchozí možnosti Azure Stream Analytics automaticky přečte data před touto dobou, pokud se používá časový operátor. Vzhledem k tomu, že několik vstupních oddílů může mít jiný čas, používá se nejbližší doba zastavení všech oddílů, v důsledku čehož mohou být ve výstupu vidět některé duplikáty. Více informací o přesně-jednou zpracování jsou k dispozici na stránce [Garanta doručení událostí](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>portál Azure

Přejděte na svou úlohu na webu Azure portal a na stránce s přehledem vyberte **Start.** Vyberte **čas zahájení výstupu úlohy** a pak vyberte **Start**.

Zvolte jednu z možností **pro čas zahájení výstupu úlohy**. Možnosti jsou *Nyní*, *Vlastní*a Pokud byla úloha dříve spuštěna, *Při posledním zastavení*. Další informace o těchto možnostech naleznete výše.

## <a name="visual-studio"></a>Visual Studio

V zobrazení úlohy vyberte zelené tlačítko pro spuštění úlohy. Nastavte **režim zahájení výstupu úlohy** a vyberte **možnost Start**. Stav úlohy se změní na **Spuštěno**.

Pro **režim spuštění výstupu úlohy**jsou tři možnosti: *JobStartTime*, *CustomTime*a *LastOutputEventTime*. Pokud tato vlastnost chybí, výchozí je *JobStartTime*. Další informace o těchto možnostech naleznete výše.


## <a name="powershell"></a>PowerShell

Pomocí následující rutiny můžete zahájit úlohu pomocí Prostředí PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existují tři možnosti pro **OutputStartMode**: *JobStartTime*, *CustomTime*a *LastOutputEventTime*. Pokud tato vlastnost chybí, výchozí je *JobStartTime*. Další informace o těchto možnostech naleznete výše.

Další informace o `Start-AzStreamAnalyitcsJob` rutině naleznete v [odkazu Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Úvodní příručka: Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu](stream-analytics-quick-create-powershell.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)
