---
title: Jak spustit úlohu Azure Stream Analytics
description: Tento článek popisuje, jak spustit úlohu Stream Analytics z Azure Portal, PowerShellu a sady Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79149d8e9862ece24b4b2da4c2ca4afcceb23d63
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016248"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Jak spustit úlohu Azure Stream Analytics

Úlohu Azure Stream Analytics můžete spustit pomocí Azure Portal, sady Visual Studio a PowerShellu. Po spuštění úlohy vyberte čas, kdy má úloha začít vytvářet výstup. Azure Portal, Visual Studio a PowerShell mají různé metody nastavení času spuštění. Tyto metody jsou popsány níže.

## <a name="start-options"></a>Možnosti spuštění
Pro spuštění úlohy jsou k dispozici tři následující možnosti. Všimněte si, že všechny níže uvedené časy jsou ty, které jsou zadány v [časovém razítku](/stream-analytics-query/timestamp-by-azure-stream-analytics). Pokud není zadáno časové RAZÍTKo, bude použit čas doručení.
* **Now**: nastaví počáteční bod streamu výstupních událostí stejně, jako když je úloha spuštěná. Pokud se použije dočasná obsluha (např. časový interval, PRODLEVa nebo spojení), Azure Stream Analytics se automaticky vyhledá v datech ve vstupním zdroji. Pokud například spustíte úlohu Now a pokud dotaz používá okno bubnu na 5 minut, Azure Stream Analytics ve vstupu vyhledá data před 5 minutami.
První možná výstupní událost by měla časové razítko, které se rovná nebo je větší než aktuální čas, a ASA zaručuje, že všechny vstupní události, které mohou logicky přispívat do výstupu, byly zaúčtovány pro. Například nejsou generovány žádné částečné okna agregace. Vždycky je to kompletní agregovaná hodnota.

* **Vlastní**: můžete zvolit výchozí bod výstupu. Podobně jako možnost **nyní** bude Azure Stream Analytics automaticky číst data před touto dobou, pokud se použije dočasný operátor. 

* **Čas posledního zastavení**. Tato možnost je k dispozici, pokud byla úloha dříve spuštěna, ale byla zastavena ručně nebo se nezdařila. Když zvolíte tuto možnost, Azure Stream Analytics použije čas posledního výstupu pro restartování úlohy, takže se neztratí žádná data. Podobně jako u předchozích možností Azure Stream Analytics po tuto dobu automaticky číst data, pokud se použije dočasný operátor. Vzhledem k tomu, že několik vstupních oddílů může mít různou dobu, použije se nejstarší čas ukončení všech oddílů, protože se ve výstupu můžou zobrazit duplicity. Další informace o zpracování právě jednou jsou k dispozici na stránce [záruky doručování událostí](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)stránky.


## <a name="azure-portal"></a>portál Azure

V Azure Portal přejděte na svou úlohu a na stránce Přehled vyberte **Spustit** . Vyberte **počáteční čas výstupu úlohy** a pak vyberte **Spustit**.

Vyberte jednu z možností pro **čas spuštění výstupu úlohy**. Možnosti jsou *nyní*, *vlastní* a, pokud byla úloha předtím spuštěna, pokud byla  *naposledy zastavena*. Další informace o těchto možnostech najdete výše.

## <a name="visual-studio"></a>Visual Studio

V zobrazení úlohy vyberte zelenou šipku tlačítka a úlohu spusťte. Nastavte **režim spuštění výstupu úlohy** a vyberte **Spustit**. Stav úlohy se změní na **spuštěno**.

**Spouštěcí režim výstupu úlohy** obsahuje tři možnosti: *JobStartTime*, *CustomTime* a *LastOutputEventTime*. Pokud tato vlastnost chybí, výchozí hodnota je *JobStartTime*. Další informace o těchto možnostech najdete výše.


## <a name="powershell"></a>PowerShell

Pomocí následující rutiny spusťte úlohu pomocí prostředí PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existují tři možnosti pro **OutputStartMode**: *JobStartTime*, *CustomTime* a *LastOutputEventTime*. Pokud tato vlastnost chybí, výchozí hodnota je *JobStartTime*. Další informace o těchto možnostech najdete výše.

Další informace o `Start-AzStreamAnalyitcsJob` rutině najdete v referenčních informacích k rutině [Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)