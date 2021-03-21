---
title: Uživatelsky definované funkce v Azure Stream Analytics
description: Tento článek představuje přehled uživatelsky definovaných funkcí v Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020498"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Uživatelsky definované funkce v Azure Stream Analytics

Dotazovací jazyk podobný SQL v Azure Stream Analytics usnadňuje implementaci logiky analýzy v reálném čase pro streamovaná data. Stream Analytics poskytuje další flexibilitu prostřednictvím vlastních funkcí, které jsou vyvolány v dotazu. Následující příklad kódu je volána metoda UDF `sampleFunction` , která přijímá jeden parametr, každý vstupní záznam, který úloha přijímá, a výsledek je zapsán do výstupu jako `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typy funkcí

Azure Stream Analytics podporuje následující čtyři typy funkcí: 

* Uživatelem definované funkce jazyka JavaScript 
* Uživatelsky definované agregace JavaScriptu 
* Uživatelsky definované funkce jazyka C# (pomocí sady Visual Studio) 
* Azure Machine Learning 

Tyto funkce můžete použít pro scénáře, jako je například bodování v reálném čase pomocí modelů strojového učení, manipulace s řetězci, komplexní matematické výpočty, kódování a dekódování dat. 

## <a name="limitations"></a>Omezení

Uživatelsky definované funkce jsou bezstavové a návratová hodnota může být pouze skalární hodnota. Z těchto uživatelsky definovaných funkcí nemůžete volat externí koncové body REST, protože to bude mít pravděpodobně vliv na výkon vaší úlohy. 

Azure Stream Analytics neuchovává záznam o všech voláních funkcí a vrácených výsledcích. Pro zajištění opakovatelnosti – například opětovné spuštění úlohy ze staršího časového razítka vytváří stejné výsledky znovu – nepoužívejte funkce, jako například `Date.GetData()` nebo `Math.random()` , protože tyto funkce nevracejí stejný výsledek pro každé vyvolání.  

## <a name="resource-logs"></a>Protokoly prostředků

Jakékoli chyby za běhu se považují za závažné a jsou provedené prostřednictvím protokolů aktivit a prostředků. Doporučuje se, aby vaše funkce zpracovává všechny výjimky a chyby a vrátila platný výsledek dotazu. Tím zabráníte tomu, aby se vaše úloha přečetla do [stavu selhání](job-states.md).  

## <a name="exception-handling"></a>Ošetření výjimek

Jakákoli výjimka při zpracování dat se považuje za závažnou chybu při využívání dat v Azure Stream Analytics. Uživatelsky definované funkce mají vyšší potenciál k vyvolání výjimek a způsobují zastavení zpracování. Chcete-li se tomuto problému vyhnout, použijte blok *try-catch* v jazyce JavaScript nebo C# k zachycení výjimek během provádění kódu. Zachycené výjimky mohou být protokolovány a zpracovány, aniž by došlo k selhání systému. Doporučujeme vždy zabalit vlastní kód do bloku *try-catch* , aby nedošlo k vyvolání neočekávaných výjimek pro výpočetní modul.

## <a name="next-steps"></a>Další kroky

* [Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics uživatelsky definovaných agregací jazyka JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integrace Azure Stream Analytics s Azure Machine Learning](machine-learning-udf.md)
