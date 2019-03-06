---
title: Spuštění úlohy Azure Stream Analytics
description: Tento článek popisuje, jak spustit úlohu Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: aa089ed53554ec697bd9430cc95a7cce78aabed2
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411526"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Spuštění úlohy Azure Stream Analytics

Můžete spustit úlohy Azure Stream Analytics pomocí webu Azure portal, sady Visual Studio a PowerShell. Při spuštění úlohy, vyberte dobu spuštění, vytváření výstupu úlohy. Web Azure portal, sady Visual Studio a PowerShell mají různé metody pro nastavení času spuštění. Tyto metody jsou popsané níže.

## <a name="azure-portal"></a>portál Azure

Přejděte do úlohy v Azure portal a vyberte **Start** na stránce Přehled. Vyberte **čas spuštění výstupu úlohy** a pak vyberte **Start**.

Existují tři možnosti pro **čas spuštění výstupu úlohy**: *Nyní*, *vlastní*, a *při posledním zastavení*. Výběr *nyní* spustí úlohu v současné době. Výběr *vlastní* umožňuje nastavit vlastní čas v minulosti nebo budoucnost je pro úlohu, která má začít. Pokud chcete zastavenou úlohu Obnovit bez ztráty dat, zvolte. Při posledním zastavení *.

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

Další informace o `Start-AzStreamAnalyitcsJob` rutiny, zobrazení [Start AzStreamAnalyticsJob odkaz](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob.md).

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí Azure Powershellu](stream-analytics-quick-create-powershell.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md)