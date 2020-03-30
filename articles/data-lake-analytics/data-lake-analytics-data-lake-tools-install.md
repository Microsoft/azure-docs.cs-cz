---
title: Instalace nástrojů Azure Data Lake pro Visual Studio
description: Tento článek popisuje, jak nainstalovat Nástroje Datového jezera Azure pro Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914096"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Data Lake Tools pro Visual Studio

Přečtěte si, jak pomocí Visual Studia vytvářet účty Azure Data Lake Analytics. Můžete definovat úlohy v [U-SQL](data-lake-analytics-u-sql-get-started.md) a odeslat úlohy do služby Data Lake Analytics. Další informace o službě Data Lake Analytics najdete v [tématu Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Požadavky

* **Visual Studio:** Podporovány jsou všechny edice kromě Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Sada **Microsoft Azure SDK pro .NET** verze 2.7.1 nebo novější. Nainstalujte ji pomocí [Instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* **Účet Data Lake Analytics**. Informace o vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Instalace nástrojů Azure Data Lake pro Visual Studio 2017 nebo Visual Studio 2019

Nástroje datového jezera Azure pro Visual Studio je podporované ve Visual Studiu 2017 15.3 nebo novější. Nástroj je součástí **úloh úložiště a zpracování dat a** vývoje **Azure.** V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.

Povolte úlohu **ukládání a zpracování dat,** jak je znázorněno:

![Povolení úlohy ukládání a zpracování dat](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Povolte **zatížení vývoje Azure,** jak je znázorněno:

![Výběr úlohy vývoje Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalace nástrojů Azure Data Lake pro Visual Studio 2013 a 2015

Stáhněte a nainstalujte [nástroje Microsoft Azure Data Lake a Stream Analytics pro Visual Studio ](https://aka.ms/adltoolsvs). Po instalaci visual studio má následující změny:

* Uzel**Azure** **Průzkumníka** > serveru obsahuje uzel Data **Lake Analytics.**
* Nabídka **Nástroje** obsahuje položku **Data Lake**.

## <a name="next-steps"></a>Další kroky

* Informace o diagnostice najdete [v tématu Přístup k protokolům diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pokud chcete použít zobrazení spuštění vrcholu, [přečtěte si informace o použití zobrazení spuštění vrcholu v nástrojích datového jezera pro visual studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
