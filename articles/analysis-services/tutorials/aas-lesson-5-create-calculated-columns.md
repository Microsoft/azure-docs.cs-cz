---
title: 'Kurz služby Azure Analysis Services – Lekce 5: Vytvoření počítaných sloupců | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit počítané sloupce v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 39e1da6a855ad88a02550a6c98b0fac61f3701d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596698"
---
# <a name="create-calculated-columns"></a>Vytvoření počítaných sloupců

V této lekci vytvoříte v modelu data přidáním počítaných sloupců. Počítané sloupce (jako vlastní sloupce) můžete vytvořit při používání funkce Získání dat, pomocí editoru dotazů nebo později v návrháři modelů, jako to uděláte tady. Další informace najdete v tématu [Počítané sloupce](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns).
  
Vytvoříte pět nových počítaných sloupců ve třech různých tabulkách. Postupy pro každý úkol se mírně liší, aby ukázaly několik způsobů vytvoření sloupců, jejich přejmenování a umístění na různá místa v tabulce.  

V této lekci také poprvé použijete jazyk DAX (Data Analysis Expressions). DAX je speciální jazyk pro vytváření vysoce přizpůsobitelných výrazů se vzorci pro tabelární modely. V tomto kurzu pomocí jazyka DAX vytvoříte počítané sloupce, míry a filtry rolí. Další informace najdete v tématu [DAX v tabelárních modelech](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular). 
  
Odhadovaný čas dokončení této lekce: **15 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 4: Vytvoření relací](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Vytvoření počítaných sloupců  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Vytvoření počítaného sloupce MonthCalendar v tabulce DimDate  
  
1.  Klikněte na nabídku **Model** > **Zobrazení modelu** > **Zobrazení dat**.  
  
    Počítané sloupce je možné vytvořit pouze pomocí návrháře modelů v zobrazení dat.  
  
2.  V návrháři modelů klikněte na tabulku (kartu) **DimDate**.  
  
3.  Klikněte pravým tlačítkem na záhlaví sloupce **CalendarQuarter** a potom klikněte na **Vložit sloupec**.  
  
    Nový sloupec **Počítaný sloupec 1** se vloží nalevo od sloupce **CalendarQuarter**.  
  
4.  Na řádku vzorců nad tabulkou zadejte následující vzorec DAX: automatické dokončování vám pomůže zadat plně kvalifikované názvy sloupců a tabulek a vypíše dostupné funkce.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    Hodnoty se potom vyplní do všech řádků počítaného sloupce. Když přejdete tabulkou dolů, uvidíte, že řádky v tomto sloupci můžou mít různé hodnoty v závislosti na datech v příslušném řádku.    
  
5.  Přejmenujte tento sloupec na **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
Počítaný sloupec MonthCalendar umožňuje řazení podle názvu měsíce.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Vytvoření počítaného sloupce DayOfWeek v tabulce DimDate  
  
1.  Když je tabulka **DimDate** stále aktivní, klikněte na nabídku **Sloupec** a potom na **Přidat sloupec**.  
  
2.  Na řádku vzorců zadejte následující vzorec:  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Jakmile budete hotovi s vytvářením vzorce, stiskněte klávesu ENTER. Nový sloupec se přidá do tabulky úplně vpravo.  
  
3.  Přejmenujte sloupec na **DayOfWeek**.  
  
4.  Klikněte na záhlaví sloupce a přesuňte ho mezi sloupce **EnglishDayNameOfWeek** a **DayNumberOfMonth**.  
  
    > [!TIP]  
    > Přesouvání sloupců usnadňuje navigaci v tabulce.  
  
Počítaný sloupec DayOfWeek umožňuje řazení podle dne v týdnu.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Vytvoření počítaného sloupce ProductSubcategoryName v tabulce DimProduct  
  
  
1.  V tabulce **DimProduct** přejděte úplně vpravo. Všimněte si, že sloupec úplně vpravo má název **Přidat sloupec** (kurzívou), a klikněte na záhlaví sloupce.  
  
2.  Na řádku vzorců zadejte následující vzorec:  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Přejmenujte sloupec na **ProductSubcategoryName**.  
  
Počítaný sloupec ProductSubcategoryName slouží k vytvoření hierarchie v tabulce DimProduct, která zahrnuje data ze sloupce EnglishProductSubcategoryName v tabulce DimProductSubcategory. Hierarchie nemůžou zahrnovat víc než jednu tabulku. Hierarchie vytvoříte později v lekci 9.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Vytvoření počítaného sloupce ProductCategoryName v tabulce DimProduct  
  
1.  Když je tabulka **DimProduct** stále aktivní, klikněte na nabídku **Sloupec** a potom na **Přidat sloupec**.  
  
2.  Na řádku vzorců zadejte následující vzorec:  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Přejmenujte sloupec na **ProductCategoryName**.  
  
Počítaný sloupec ProductCategoryName slouží k vytvoření hierarchie v tabulce DimProduct, která zahrnuje data ze sloupce EnglishProductCategoryName v tabulce DimProductCategory. Hierarchie nemůžou zahrnovat víc než jednu tabulku.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Vytvoření počítaného sloupce Margin v tabulce FactInternetSales  
  
1.  V návrháři modelů vyberte tabulku **FactInternetSales**.  
  
2.  Vytvořte nový počítaný sloupec mezi sloupci **SalesAmount** a **TaxAmt**.  
  
3.  Na řádku vzorců zadejte následující vzorec:  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Přejmenujte sloupec na **Margin**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    Počítaný sloupec Margin slouží k analýze ziskových marží u jednotlivých prodejů.  
  
## <a name="whats-next"></a>Co dále?
[Lekce 6: Vytvoření měřítek](../tutorials/aas-lesson-6-create-measures.md)
  
  
  
