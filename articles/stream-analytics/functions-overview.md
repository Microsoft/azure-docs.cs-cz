---
title: Uživatelem definované funkce ve službě Azure Stream Analytics
description: Tento článek je přehled uživatelem definovaných funkcí ve službě Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115583"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Uživatelem definované funkce ve službě Azure Stream Analytics

Dotazovací jazyk podobný SQL ve službě Azure Stream Analytics usnadňuje implementaci logiky analýzy v reálném čase na streamovaná data. Stream Analytics poskytuje další flexibilitu prostřednictvím vlastních funkcí, které jsou vyvolány v dotazu. Následující příklad kódu je UDF s názvem, `sampleFunction` který přijímá jeden parametr, každý vstupní záznam, `sampleResult`který úloha obdrží, a výsledek je zapsán do výstupu jako .

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
* Uživatelsky definované agregáty JavaScriptu 
* Uživatelem definované funkce jazyka C# (pomocí sady Visual Studio) 
* Azure Machine Learning 

Tyto funkce můžete použít pro scénáře, jako je vyhodnocování v reálném čase pomocí modelů strojového učení, manipulace s řetězci, složité matematické výpočty, kódování a dekódování dat. 

## <a name="limitations"></a>Omezení

Uživatelem definované funkce jsou bezstavové a vrácená hodnota může být pouze skalární hodnota. Nelze volat na externí koncové body REST z těchto funkcí definované uživatelem, protože to bude pravděpodobně mít vliv na výkon vaší úlohy. 

Azure Stream Analytics nevede záznamy o všech vyvolání funkcí a vrácené výsledky. Chcete-li zaručit opakovatelnost - například opětovné spuštění úlohy ze staršího časového razítka `Date.GetData()` znovu `Math.random()`vytvoří stejné výsledky - nepoužívejte funkce jako nebo , protože tyto funkce nevracejí stejný výsledek pro každé vyvolání.  

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Všechny chyby za běhu jsou považovány za závažné a jsou k podrobnostem prostřednictvím aktivity a diagnostické protokoly. Doporučujese, aby vaše funkce zpracovává všechny výjimky a chyby a vrátit platný výsledek dotazu. Tím zabráníte, aby vaše úloha přechází do [stavu Selhání](job-states.md).  


## <a name="next-steps"></a>Další kroky

* [Uživatelem definované funkce JavaScriptu v Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Uživatelem definované agregáty JavaScriptu Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Vývoj uživatelem definovaných funkcí .NET Standard pro úlohy Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integrace Azure Stream Analytics s Azure Machine Learning](machine-learning-udf.md)

