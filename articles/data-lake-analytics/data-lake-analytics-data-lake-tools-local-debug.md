---
title: Ladění kódu Azure Data Lake Analytics místně
description: Další informace o použití Azure Data Lake Tools pro Visual Studio k ladění úloh U-SQL na místní pracovní stanici.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 9e73fc4db1404842e6d3878d88d8f02511587bc9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044973"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Ladění kódu Azure Data Lake Analytics místně

Azure Data Lake Tools pro Visual Studio můžete použít ke spuštění a ladění kódu Azure Data Lake Analytics v místní pracovní stanici, stejně jako ve službě Azure Data Lake Analytics.

Zjistěte, jak [spustit skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Místní ladění skriptů a sestavení C#

Ladění sestavení C# bez odeslali a zaregistrovali ve službě Azure Data Lake Analytics. Můžete nastavit zarážky v souboru kódu na pozadí a v odkazovaném projektu C#.

### <a name="debug-local-code-in-a-code-behind-file"></a>Ladění místního kódu v souboru kódu na pozadí

1. Nastavte zarážky v souboru kódu na pozadí.
2. Vyberte **F5** a laďte skript místně.

> [!NOTE]
   > Následující postup funguje pouze v sadě Visual Studio 2015. Ve starších verzích sady Visual Studio, může být nutné ručně přidat **PDB** soubory.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Ladění místního kódu v odkazovaném projektu C#

1. Vytvořte projekt sestavení C# a sestavte ho generovat výstup **DLL** souboru.
2. Zaregistrujte **DLL** soubor pomocí příkazu U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Nastavte zarážky v kódu C#.
4. Vyberte **F5** a laďte skript pomocí odkazu na C# **DLL** soubor místně.


## <a name="next-steps"></a>Další postup

- Příkladem složitějšího dotazu, naleznete v tématu [analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Chcete-li zobrazit podrobnosti o úloze, naleznete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Použití zobrazení provádění vrcholů, najdete v článku [použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
