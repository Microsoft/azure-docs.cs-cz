---
title: Začínáme s Azure Data Lake Analytics na webu Azure Portal
description: Vytvořte pomocí webu Azure Portal účet služby Azure Data Lake Analytics a odešlete úlohu U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 0830f4a6cffb8c1b2dacc8452e52477bb3a3ec91
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43039364"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Začínáme s Azure Data Lake Analytics na webu Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Tento článek popisuje, jak používat Azure Portal k vytváření účtů Azure Data Lake Analytics, definování úloh v [U-SQL](data-lake-analytics-u-sql-get-started.md) a odesílání úloh do služby Data Lake Analytics.

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, musíte mít **předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics

Teď zároveň vytvoříte účet Data Lake Analytics a Data Lake Store.  Tento krok je jednoduchý a trvá jen asi 60 vteřin.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek** >  **Data a analýzy** > **Data Lake Analytics**.
3. Vyberte hodnoty pro následující položky:
   * **Název:** Pojmenujte svůj účet Data Lake Analytics (povolena jsou pouze malá písmena a číslice).
   * **Předplatné**: Zvolte předplatné Azure použité pro účet Analytics.
   * **Skupina prostředků**. Vyberte některou z existujících skupin prostředků Azure nebo vytvořte novou.
   * **Umístění**. Vyberte datové centrum Azure pro účet Data Lake Analytics.
   * **Data Lake Store**: Postupujte podle pokynů a vytvořte nový účet Data Lake Store nebo vyberte některý z existujících. 
4. Volitelně vyberte cenovou úroveň pro svůj účet Data Lake Analytics.
5. Klikněte na možnost **Vytvořit**. 


## <a name="your-first-u-sql-script"></a>Váš první skript U-SQL

Následující text je velmi jednoduchý skript U-SQL. Celá jeho úloha spočívá v definování malé datové sady v rámci skriptu a následném zapsání této datové sady do výchozího úložiště Data Lake Store jako soubor s názvem `/data.csv`.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Odeslání úlohy U-SQL

1. V účtu Data Lake Analytics vyberte **Nová úloha**.
2. Vložte text předchozího skriptu U-SQL. Pojmenujte úlohu. 
3. Spusťte úlohu tlačítkem **Odeslat**.   
4. Sledujte **stav** úlohy a počkejte, až se změní na **Úspěšné**.
5. Vyberte kartu **Data** a pak vyberte kartu **Výstupy**. Vyberte výstupní soubor s názvem `data.csv` a prohlédněte si výstupní data.

## <a name="see-also"></a>Další informace najdete v tématech

* Pokud chcete začít s vývojem aplikací U-SQL, přejděte k tématu [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informace týkající se úloh správy najdete v tématu [Správa služby Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md).
