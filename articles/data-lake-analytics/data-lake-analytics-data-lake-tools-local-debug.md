---
title: Ladění kódu Azure Data Lake Analytics lokálně
description: Naučte se používat Nástroje Azure Data Lake pro Visual Studio k ladění úloh U-SQL na místní pracovní stanici.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "61472987"
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

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Nastavte zarážky v kódu C#.
4. Vyberte **F5** pro ladění skriptu odkazem na místní soubor **DLL** jazyka C#.


## <a name="next-steps"></a>Další kroky

- Příklad složitějšího dotazu najdete v tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Podrobnosti o úlohách najdete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Chcete-li použít zobrazení spuštění vrcholu, přečtěte si téma [použití zobrazení spuštění vrcholu v nástroji Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
