---
title: Ladění kódu Azure Data Lake Analytics lokálně
description: Naučte se používat Nástroje Azure Data Lake pro Visual Studio k ladění úloh U-SQL na místní pracovní stanici.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 1c7218deac9efba6df6c1284f2578a744e768284
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221022"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Ladění kódu Azure Data Lake Analytics lokálně

Nástroje Azure Data Lake pro Visual Studio můžete použít ke spouštění a ladění kódu Azure Data Lake Analytics na místní pracovní stanici, stejně jako ve službě Azure Data Lake Analytics.

Přečtěte si, jak [na místním počítači spustit skript U-SQL](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Místní ladění skriptů a sestavení C#

Sestavení jazyka C# můžete ladit bez odeslání a registrace do služby Azure Data Lake Analytics. Můžete nastavit zarážky v souboru kódu na pozadí i v odkazovaném projektu C#.

### <a name="debug-local-code-in-a-code-behind-file"></a>Ladění místního kódu v souboru kódu na pozadí

1. Nastavte zarážky v souboru kódu na pozadí.
2. Vyberte **F5** pro místní ladění skriptu.

> [!NOTE]
   > Následující postup funguje pouze v sadě Visual Studio 2015. Ve starších verzích sady Visual Studio může být nutné ručně přidat soubory **PDB** .  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Ladění místního kódu v odkazovaném projektu C#

1. Vytvořte projekt sestavení C# a sestavte ho, aby se vygeneroval výstupní soubor **DLL** .
2. Zaregistrujte soubor **DLL** pomocí příkazu U-SQL:

   ```sql
   CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
   ```
   
3. Nastavte zarážky v kódu C#.
4. Vyberte **F5** pro ladění skriptu odkazem na místní soubor **DLL** jazyka C#.


## <a name="next-steps"></a>Další kroky

- Příklad složitějšího dotazu najdete v tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Podrobnosti o úlohách najdete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Chcete-li použít zobrazení spuštění vrcholu, přečtěte si téma [použití zobrazení spuštění vrcholu v nástroji Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
