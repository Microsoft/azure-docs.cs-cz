---
title: Instalace nástrojů Azure Data Lake pro Visual Studio
description: Tento článek popisuje, jak nainstalovat Azure Data Lake Tools pro Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 05/03/2018
ms.openlocfilehash: 3269d38b691ec4dd9573a241c89dadc285787143
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254022"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Data Lake Tools pro Visual Studio

Naučte se používat sadu Visual Studio k vytváření účtů Azure Data Lake Analytics, definování úloh v [U-SQL](data-lake-analytics-u-sql-get-started.md) a odesílání úloh do služby Data Lake Analytics. Další informace o Data Lake Analytics najdete v tématu [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Požadavky

* **Visual Studio:** Podporovány jsou všechny edice kromě Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* Sada **Microsoft Azure SDK pro .NET** verze 2.7.1 nebo novější.  Nainstalujte ji pomocí [Instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* **Účet Data Lake Analytics**. Informace o vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Instalace nástrojů Azure Data Lake pro Visual Studio 2017

Nástroje Azure Data Lake pro Visual Studio se podporují v sadě Visual Studio 2017 verze 15.3 nebo novější. Tento nástroj je součástí sad funkcí **Ukládání a zpracování dat** a **Vývoj pro Azure** v Instalačním programu pro Visual Studio. V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.  

Sadu funkcí **Ukládání a zpracování dat** povolíte následujícím způsobem: ![Povolení sady funkcí Ukládání a zpracování dat](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Sadu funkcí **Vývoj pro Azure** povolíte následujícím způsobem: ![Povolení sady funkcí Vývoj pro Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalace nástrojů Azure Data Lake pro Visual Studio 2013 a 2015

Stáhněte nástroje Azure Data Lake pro Visual Studio z webu [Download Center](https://aka.ms/adltoolsvs) a nainstalujte je. Po instalaci si všimněte, že:
* Uzel **Průzkumník serveru** > **Azure** obsahuje uzel **Data Lake Analytics**. 
* Nabídka **Nástroje** obsahuje položku **Data Lake**.


## <a name="next-steps"></a>Další kroky
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Použití zobrazení provádění vrcholů, najdete v článku [použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
