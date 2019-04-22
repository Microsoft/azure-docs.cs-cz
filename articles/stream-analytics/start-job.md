---
title: Spuštění úlohy Azure Stream Analytics
description: Tento článek popisuje, jak spustit úlohu Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005940"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Spuštění úlohy Azure Stream Analytics

Můžete spustit úlohy Azure Stream Analytics pomocí webu Azure portal, sady Visual Studio a PowerShell. Při spuštění úlohy, vyberte dobu spuštění, vytváření výstupu úlohy. Web Azure portal, sady Visual Studio a PowerShell mají různé metody pro nastavení času spuštění. Tyto metody jsou popsané níže.

## <a name="start-options"></a>Možnosti spuštění
Tyto tři následující možnosti jsou k dispozici pro spuštění úlohy. Mějte na paměti, že jsou všechny časy uvedené níže jsou zadány v [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Pokud TIMESTAMP BY není zadán, použije se čas doručení.
* **Nyní**: Vytvoří výchozí bod výstupní událost Streamovat, stejně jako při spuštění úlohy. Pokud se používá dočasnou – operátor (třeba časový interval, PRODLEVA nebo JOIN), Azure Stream Analytics bude automaticky podívat na data ve vstupní zdroj zpět. Například pokud spustíte úlohu "Teď", a pokud váš dotaz využívá Přeskakujícího okna 5 minut, Azure Stream Analytics bude vyhledávat data od 5 minut před ve vstupu.
První událost není výstup bude mít časové razítko roven nebo větší než aktuální čas a Azure Stream Analytics zaručuje, že všechny vstupní události, které logicky může přispívat do výstupu má pro zvláštní pozornost. Například jsou generovány částečné agregací. Je vždy úplný agregovanou hodnotu.

* **Vlastní**: Můžete zvolit výchozí bod výstupu. Podobně jako **nyní** možnost, Azure Stream Analytics automaticky načte data před tentokrát Pokud se používá dočasnou – operátor 

* **Při posledním zastavení**. Tato možnost je k dispozici, pokud úloha byla spuštěna dříve, ale byla ručně zastavena nebo se nezdařilo. Při výběru této možnosti Azure Stream Analytics pomocí posledního výstupu restartujte tuto úlohu, aby se neztratila žádná data. Podobně jako na předchozí možnosti Azure Stream Analytics automaticky načte data před tentokrát Pokud dočasné operátor se používá. Protože několik vstupních oddílů může mít jiný čas, Nejdřívější čas ukončení všech oddílů se používá, a díky tomu mohou některé duplicitní položky zobrazit ve výstupu. Další informace o přesně – jedno zpracování jsou k dispozici na stránce [záruky doručení událostí](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>portál Azure

Přejděte do úlohy v Azure portal a vyberte **Start** na stránce Přehled. Vyberte **čas spuštění výstupu úlohy** a pak vyberte **Start**.

Vyberte jednu z možností pro **čas spuštění výstupu úlohy**. Možnosti jsou *nyní*, *vlastní*, a pokud byla úloha spuštěna dříve, *při posledním zastavení*. Další informace o těchto možnostech najdete výše.

## <a name="visual-studio"></a>Visual Studio

V zobrazení úloh vyberte na zelenou šipku tlačítko pro spuštění úlohy. Nastavte **režim spuštění výstupu úlohy** a vyberte **Start**. Stav úlohy změní na **systémem**.

Existují tři možnosti pro **režim spuštění výstupu úlohy**: *JobStartTime*, *CustomTime*, a *LastOutputEventTime*. Pokud je tato vlastnost chybí, výchozí hodnota je *JobStartTime*. Další informace o těchto možnostech najdete výše.


## <a name="powershell"></a>PowerShell

Spuštění vaší úlohy pomocí prostředí PowerShell pomocí následující rutiny:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existují tři možnosti pro **OutputStartMode**: *JobStartTime*, *CustomTime*, a *LastOutputEventTime*. Pokud je tato vlastnost chybí, výchozí hodnota je *JobStartTime*. Další informace o těchto možnostech najdete výše.

Další informace o `Start-AzStreamAnalyitcsJob` rutiny, zobrazení [Start AzStreamAnalyticsJob odkaz](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí webu Azure portal](stream-analytics-quick-create-portal.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí Azure Powershellu](stream-analytics-quick-create-powershell.md)
* [Rychlé zprovoznění: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md)
