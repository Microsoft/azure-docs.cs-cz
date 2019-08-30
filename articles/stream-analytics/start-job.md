---
title: Jak spustit úlohu Azure Stream Analytics
description: Tento článek popisuje, jak spustit úlohu Stream Analytics z Azure Portal, PowerShellu a sady Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 1e4cb63accf7e89ac02451e9c25b9902a8a10812
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173276"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Jak spustit úlohu Azure Stream Analytics

Úlohu Azure Stream Analytics můžete spustit pomocí Azure Portal, sady Visual Studio a PowerShellu. Po spuštění úlohy vyberte čas, kdy má úloha začít vytvářet výstup. Azure Portal, Visual Studio a PowerShell mají různé metody nastavení času spuštění. Tyto metody jsou popsány níže.

## <a name="start-options"></a>Možnosti spuštění
Pro spuštění úlohy jsou k dispozici tři následující možnosti. Všimněte si, že všechny níže uvedené časy jsou ty, které [](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)jsou zadány v časovém razítku. Pokud není zadáno časové RAZÍTKo, bude použit čas doručení.
* **Nyní**: Nastaví počáteční bod streamu výstupních událostí stejně, jako při spuštění úlohy. Pokud se použije dočasná obsluha (např. časový interval, PRODLEVa nebo spojení), Azure Stream Analytics se automaticky vyhledá v datech ve vstupním zdroji. Pokud například spustíte úlohu Now a pokud dotaz používá okno bubnu na 5 minut, Azure Stream Analytics ve vstupu vyhledá data před 5 minutami.
První možná výstupní událost by měla časové razítko, které se rovná nebo je větší než aktuální čas, a ASA zaručuje, že všechny vstupní události, které mohou logicky přispívat do výstupu, byly zaúčtovány pro. Například nejsou generovány žádné částečné okna agregace. Vždycky je to kompletní agregovaná hodnota.

* **Vlastní**: Můžete zvolit výchozí bod výstupu. Podobně jako možnost **nyní** bude Azure Stream Analytics automaticky číst data před touto dobou, pokud se použije dočasný operátor. 

* **Čas posledního zastavení**. Tato možnost je k dispozici, pokud byla úloha dříve spuštěna, ale byla zastavena ručně nebo se nezdařila. Když zvolíte tuto možnost, Azure Stream Analytics použije čas posledního výstupu pro restartování úlohy, takže se neztratí žádná data. Podobně jako u předchozích možností Azure Stream Analytics po tuto dobu automaticky číst data, pokud se použije dočasný operátor. Vzhledem k tomu, že několik vstupních oddílů může mít různou dobu, použije se nejstarší čas ukončení všech oddílů, protože se ve výstupu můžou zobrazit duplicity. Další informace o zpracování právě jednou jsou k dispozici na stránce [záruky doručování událostí](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)stránky.


## <a name="azure-portal"></a>portál Azure

V Azure Portal přejděte na svou úlohu a na stránce Přehled vyberte **Spustit** . Vyberte **počáteční čas výstupu úlohy** a pak vyberte **Spustit**.

Vyberte jednu z možností pro **čas spuštění výstupu úlohy**. Možnosti jsou *nyní*, *vlastní*a, pokud byla úloha předtím spuštěna, pokud byla *naposledy zastavena*. Další informace o těchto možnostech najdete výše.

## <a name="visual-studio"></a>Visual Studio

V zobrazení úlohy vyberte zelenou šipku tlačítka a úlohu spusťte. Nastavte **režim spuštění výstupu úlohy** a vyberte **Spustit**. Stav úlohy se změní na **spuštěno**.

**Spouštěcí režim výstupu úlohy**nabízí tři možnosti: *JobStartTime*, *CustomTime*a *LastOutputEventTime*. Pokud tato vlastnost chybí, výchozí hodnota je *JobStartTime*. Další informace o těchto možnostech najdete výše.


## <a name="powershell"></a>PowerShell

Pomocí následující rutiny spusťte úlohu pomocí prostředí PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existují tři možnosti pro **OutputStartMode**: *JobStartTime*, *CustomTime*a *LastOutputEventTime*. Pokud tato vlastnost chybí, výchozí hodnota je *JobStartTime*. Další informace o těchto možnostech najdete výše.

Další informace o `Start-AzStreamAnalyitcsJob` rutině najdete v referenčních informacích k rutině [Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření Stream Analytics úlohy pomocí Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlé zprovoznění: Vytvoření Stream Analytics úlohy pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-quick-create-vs.md)
