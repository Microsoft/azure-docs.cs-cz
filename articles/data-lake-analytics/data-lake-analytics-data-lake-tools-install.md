---
title: Instalace nástrojů Azure Data Lake pro Visual Studio
description: Tento článek popisuje, jak nainstalovat Nástroje Azure Data Lake pro Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: df3bf715f8571659d0e622729562055428ab5c97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87125934"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Data Lake Tools pro Visual Studio

Naučte se používat Visual Studio k vytváření Azure Data Lake Analyticsch účtů. Můžete definovat úlohy v [u-SQL](data-lake-analytics-u-sql-get-started.md) a odesílat úlohy do služby Data Lake Analytics. Další informace o Data Lake Analytics najdete v tématu [Azure Data Lake Analytics Overview](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Požadavky

* **Visual Studio:** Podporovány jsou všechny edice kromě Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Sada **Microsoft Azure SDK pro .NET** verze 2.7.1 nebo novější. Nainstalujte ji pomocí [Instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* **Účet Data Lake Analytics**. Informace o vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Instalace Nástroje Azure Data Lake pro Visual Studio 2017 nebo Visual Studio 2019

Nástroje Azure Data Lake pro Visual Studio se podporuje v aplikaci Visual Studio 2017 15,3 nebo novější. Tento nástroj je součástí úloh **úložiště a zpracování dat** a **vývoje pro Azure** . V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.

Povolte úlohu **ukládání a zpracování dat** , jak je znázorněno níže:

![Povolení úlohy ukládání a zpracování dat](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Povolte úlohu **vývoj pro Azure** , jak je znázorněno na následujícím obrázku:

![Výběr úlohy vývoj pro Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalace nástrojů Azure Data Lake pro Visual Studio 2013 a 2015

Stáhněte a nainstalujte [Microsoft Azure Data Lake a Stream Analytics nástrojů pro Visual Studio ](https://aka.ms/adltoolsvs). Po instalaci aplikace Visual Studio obsahuje následující změny:

* **Průzkumník serveru**  >  uzel**Azure** obsahuje uzel **Data Lake Analytics** .
* Nabídka **Nástroje** obsahuje položku **Data Lake**.

## <a name="next-steps"></a>Další kroky

* Informace o diagnostice protokolu najdete v tématu [přístup k protokolům diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Chcete-li použít zobrazení spuštění vrcholu, přečtěte si téma [použití zobrazení spuštění vrcholu v nástroji Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
