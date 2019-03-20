---
title: Spuštění úlohy Azure Stream Analytics
description: Tento článek popisuje, jak spustit úlohu Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886843"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Spuštění úlohy Azure Stream Analytics

Můžete spustit úlohy Azure Stream Analytics pomocí webu Azure portal, sady Visual Studio a PowerShell. Při spuštění úlohy, vyberte dobu spuštění, vytváření výstupu úlohy. Web Azure portal, sady Visual Studio a PowerShell mají různé metody pro nastavení času spuštění. Tyto metody jsou popsané níže.

## <a name="azure-portal"></a>portál Azure

Přejděte do úlohy v Azure portal a vyberte **Start** na stránce Přehled. Vyberte **čas spuštění výstupu úlohy** a pak vyberte **Start**.

Existují tři možnosti pro **čas spuštění výstupu úlohy**: *Nyní*, *vlastní*, a *při posledním zastavení*. Výběr *nyní* spustí úlohu v současné době. Výběr *vlastní* umožňuje nastavit vlastní čas v minulosti nebo budoucnost je pro úlohu, která má začít. Pokud chcete zastavenou úlohu Obnovit bez ztráty dat, zvolte *při posledním zastavení*.

## <a name="visual-studio"></a>Visual Studio

V zobrazení úloh vyberte na zelenou šipku tlačítko pro spuštění úlohy. Nastavte **režim spuštění výstupu úlohy** a vyberte **Start**. Stav úlohy změní na **systémem**.

Existují tři možnosti pro **režim spuštění výstupu úlohy**: *JobStartTime*, *CustomTime*, a *LastOutputEventTime*. Pokud je tato vlastnost chybí, výchozí hodnota je *JobStartTime*.

*JobStartTime* vytvoří výchozí bod výstupní událost stejný jako při spuštění úlohy streamování.

*CustomTime* začíná vlastní časový interval, který je zadán v výstup *OutputStartTime* parametru.

*LastOutputEventTime* vytvoří výchozí bod do výstupního datového proudu událostí stejná jako poslední události čas výstupu.

## <a name="powershell"></a>PowerShell

Spuštění vaší úlohy pomocí prostředí PowerShell pomocí následující rutiny:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existují tři možnosti pro **OutputStartMode**: *JobStartTime*, *CustomTime*, a *LastOutputEventTime*. Pokud je tato vlastnost chybí, výchozí hodnota je *JobStartTime*.

*JobStartTime* vytvoří výchozí bod výstupní událost stejný jako při spuštění úlohy streamování.

*CustomTime* začíná vlastní časový interval, který je zadán v výstup *OutputStartTime* parametru.

*LastOutputEventTime* vytvoří výchozí bod do výstupního datového proudu událostí stejná jako poslední události čas výstupu.

Další informace o `Start-AzStreamAnalyitcsJob` rutiny, zobrazení [Start AzStreamAnalyticsJob odkaz](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí Azure Powershellu](stream-analytics-quick-create-powershell.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md)
