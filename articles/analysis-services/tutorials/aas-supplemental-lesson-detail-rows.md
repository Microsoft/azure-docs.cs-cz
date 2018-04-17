---
title: 'Kurz služby Azure Analysis Services – Doplňková lekce: Řádky podrobností | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit výraz řádků podrobností v kurzu služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 02e9edd966e64c0bfa32e2b80f4c26f797e58582
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="supplemental-lesson---detail-rows"></a>Doplňková lekce – Řádky podrobností

V této doplňkové lekci použijete Editor DAX k definici vlastního výrazu řádků podrobností. Výraz řádků podrobností je vlastnost míry, která koncovým uživatelům poskytuje další informace o agregovaných výsledcích míry. 
  
Odhadovaný čas dokončení této lekce: **10 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Tato doplňková lekce je součástí kurzu tabulkového modelování. Než začnete provádět úkoly v této doplňkové lekci, měli byste mít dokončené všechny předchozí lekce nebo mít dokončený ukázkový projekt modelu Adventure Works Internet Sales.  
  
## <a name="whats-the-issue"></a>V čem je problém?
Podívejme se na podrobnosti o InternetTotalSales míry, než přidáte výraz řádky podrobností.

1.  V sadě SSDT klikněte na nabídku **Model** > **Analyzovat v aplikaci Excel**, otevřete Excel a vytvořte prázdnou kontingenční tabulku.
  
2.  V části **Pole kontingenční tabulky** přidejte míru **InternetTotalSales** z tabulky FactInternetSales do **Hodnoty**, **CalendarYear** z tabulky DimDate do **Sloupce** a **EnglishCountryRegionName** do **Řádky**. Kontingenční tabulka nyní umožňuje agregované výsledky z míry InternetTotalSales oblasti a roku. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. V kontingenční tabulce poklikejte na agregovanou hodnotu pro rok a název oblasti. Hodnota pro Austrálii a v roce 2014. Otevře se nový list, který obsahuje data. Tato data ale nejsou moc užitečná.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Cílem je tabulku obsahující sloupce a řádky dat, která můžete přispět k agregované výsledek InternetTotalSales míry. K tomu, přidejte výraz řádky podrobností jako vlastnost pro míru.

## <a name="add-a-detail-rows-expression"></a>Přidání výrazu řádků podrobností

#### <a name="to-create-a-detail-rows-expression"></a>Vytvoření výrazu řádků podrobností 
  
1. V mřížce měr tabulky FactInternetSales klikněte na míru **InternetTotalSales**. 

2. V části **Vlastnosti** > **Výraz řádků podrobností** klikněte na tlačítko editoru a otevřete Editor DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. V Editoru DAX zadejte následující výraz:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Tento výraz určuje názvy a sloupce a výsledky měr z tabulky FactInternetSales a souvisejících tabulky se vrátí, když uživatel pokliká na agregovaný výsledek v kontingenční tabulce nebo sestavě.

4. Zpět v aplikaci Excel odstraňte list vytvořený v kroku 3, pak poklikejte na agregovanou hodnotu. Teď s nadefinovanou vlastností výrazu řádků vlastností pro míru se otevře nový list obsahující užitečnější data.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Znovu nasaďte model.

  
## <a name="see-also"></a>Další informace najdete v tématech  

[Funkce SELECTCOLUMNS (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Další lekce - dynamické zabezpečení](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Doplňková lekce – Nepravidelné hierarchie](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 