---
title: Ladění kódu Azure Data Lake Analytics místně
description: Zjistěte, jak pomocí nástrojů Datového jezera Azure pro Visual Studio ladit úlohy U-SQL na místní pracovní stanici.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61472987"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Ladění kódu Azure Data Lake Analytics místně

Pomocí nástrojů Azure Data Lake Tools for Visual Studio můžete spouštět a ladit kód Azure Data Lake Analytics na místní pracovní stanici, stejně jako ve službě Azure Data Lake Analytics.

Přečtěte si, jak [spustit skript U-SQL v místním počítači](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Místní ladění skriptů a sestavení C#

Sestavení jazyka C# můžete ladit bez jejich odeslání a registrace do služby Azure Data Lake Analytics. Zarážky můžete nastavit v souboru s kódem na pozadí a v projektu odkazované C#.

### <a name="debug-local-code-in-a-code-behind-file"></a>Ladění místního kódu v souboru s kódem na pozadí

1. Nastavte zarážky v souboru s kódem na pozadí.
2. Chcete-li skript ladit místně, vyberte **možnost F5.**

> [!NOTE]
   > Následující postup funguje pouze v sadě Visual Studio 2015. Ve starších verzích sady Visual Studio může být nutné ručně přidat soubory **PDB.**  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Ladění místního kódu v odkazovaném projektu jazyka C#

1. Vytvořte projekt sestavení jazyka C# a vytvořte jej pro generování výstupního souboru **DLL.**
2. Zaregistrujte soubor **DLL** pomocí příkazu U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Nastavte zarážky v kódu C#.
4. Výběrem **možnosti F5** chcete-li skript ladit odkazem na soubor **DLL** jazyka C# místně.


## <a name="next-steps"></a>Další kroky

- Příklad složitějšího dotazu najdete v [tématu Analýza protokolů webů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Podrobnosti o úloze najdete [v tématu Použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Pokud chcete použít zobrazení spuštění vrcholu, [přečtěte si informace o použití zobrazení spuštění vrcholu v nástrojích datového jezera pro visual studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
