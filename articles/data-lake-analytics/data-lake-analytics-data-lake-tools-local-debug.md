---
title: Ladění kódu Azure Data Lake Analytics místně | Dokumentace Microsoftu
description: Další informace o použití Azure Data Lake Tools pro Visual Studio k ladění úloh U-SQL na místní pracovní stanici.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889510"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Ladění kódu Azure Data Lake Analytics místně

Azure Data Lake Tools pro Visual Studio můžete použít ke spuštění a ladění kódu Azure Data Lake Analytics na pracovní stanici, stejně jako ve službě Azure Data Lake.

Přečtěte si [jak spouštět skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Místní ladění skriptů a sestavení C#

Ladění sestavení C# bez odeslali a zaregistrovali do služby Azure Data Lake Analytics. V souboru kódu i v odkazovaném projektu C# můžete nastavit zarážky.

### <a name="to-debug-local-code-in-code-behind-file"></a>Postup ladění místního kódu v souboru kódu

1. Nastavte zarážky v souboru kódu.
2. Stisknutím klávesy F5 začít ladit skript místně.

> [!NOTE]
   > Následující postup funguje pouze v sadě Visual Studio 2015. Ve starší sadě Visual Studio je pravděpodobně nutné ručně přidat soubory PDB.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Postup ladění místního kódu v odkazovaném projektu C#

1. Vytvořte projekt sestavení C# a sestavte jej tak, aby generoval výstupní knihovnu DLL.
2. Zaregistruje knihovnu DLL pomocí příkazu U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Nastavte zarážky v kódu C#.
4. Stisknutím klávesy F5 začít ladit skript s odkazující na C# knihovny dll místně.


## <a name="next-steps"></a>Další postup

- Pokud chcete zobrazit komplexnější dotaz, naleznete v tématu [analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Chcete-li zobrazit podrobnosti o úloze, naleznete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Použití zobrazení provádění vrcholů, najdete v článku [použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
