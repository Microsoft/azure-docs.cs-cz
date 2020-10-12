---
title: Vytvořit & dotaz Azure Data Lake Analytics-Azure Portal
description: Vytvořte pomocí webu Azure Portal účet služby Azure Data Lake Analytics a odešlete úlohu U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: d8f0a5697a128deb75537f3d48a3830dc7ba9df9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132530"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Začínáme s Azure Data Lake Analytics na webu Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Tento článek popisuje, jak používat Azure Portal k vytváření účtů Azure Data Lake Analytics, definování úloh v [U-SQL](data-lake-analytics-u-sql-get-started.md) a odesílání úloh do služby Data Lake Analytics.

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, musíte mít **předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics

Nyní vytvoříte Data Lake Analytics a účet Azure Data Lake Storage Gen1 ve stejnou dobu.  Tento krok je jednoduchý a trvá jen asi 60 vteřin.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Klikněte na **vytvořit prostředek**  >   **data a analýzy**  >  **Data Lake Analytics**.
3. Vyberte hodnoty pro následující položky:
   * **Název:** Pojmenujte svůj účet Data Lake Analytics (povolena jsou pouze malá písmena a číslice).
   * **Předplatné**: Zvolte předplatné Azure použité pro účet Analytics.
   * **Skupina prostředků:** Vyberte některou z existujících skupin prostředků Azure nebo vytvořte novou.
   * **Umístění:** Vyberte datové centrum Azure pro účet Data Lake Analytics.
   * **Data Lake Storage Gen1**: postupujte podle pokynů k vytvoření nového účtu Data Lake Storage Gen1 nebo vyberte existující účet. 
4. Volitelně vyberte cenovou úroveň pro svůj účet Data Lake Analytics.
5. Klikněte na **Vytvořit**. 


## <a name="your-first-u-sql-script"></a>Váš první skript U-SQL

Následující text je velmi jednoduchý skript U-SQL. Vše je v rámci skriptu definováno malým objektem DataSet a pak tuto datovou sadu vypíše jako výchozí Data Lake Storage Gen1 účet jako soubor s názvem `/data.csv` .

```usql
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
5. Vyberte kartu **data** a pak vyberte kartu **výstupy** . Vyberte výstupní soubor s názvem `data.csv` a zobrazte výstupní data.

## <a name="see-also"></a>Viz také

* Pokud chcete začít s vývojem aplikací U-SQL, přejděte k tématu [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informace o úlohách správy najdete v tématu [správa Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-manage-use-portal.md).
