---
title: 'Kurz služby Azure Analysis Services – Lekce 6: Vytvoření měr | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit míry v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 223567112a428818b49dab319bd7c507d71b1f78
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596868"
---
# <a name="create-measures"></a>Vytvoření měr

V této lekci vytvoříte míry, které se mají zahrnout do modelu. Podobně jako počítané sloupce, které jste vytvořili, je míra výpočtem vytvořeným pomocí vzorce DAX. Na rozdíl od počítaných sloupců se ale míry vyhodnocují na základě uživatelem vybraného *filtru*. Příkladem může být konkrétní sloupec nebo průřez přidaný do pole Popisky řádků v kontingenční tabulce. Hodnota pro každou buňku ve filtru se pak vypočítá podle použité míry. Míry jsou efektivní, flexibilní výpočty, které můžete zahrnout do téměř všech tabulkových modelů za účelem provádění dynamických výpočtů na číselných datech. Další informace najdete v tématu [Míry](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular).
  
K vytvoření měr použijete *mřížku měr*. Ve výchozím nastavení má každá tabulka prázdnou mřížku měr. Obvykle ale nevytváříte míry pro každou tabulku. Mřížka měr se zobrazuje pod tabulkou v návrháři modelů v zobrazení dat. Pokud chcete mřížku měr tabulky skrýt, klikněte na nabídku **Tabulka** a pak klikněte na **Zobrazit mřížku měr**.  
  
Míru můžete vytvořit kliknutím na prázdnou buňku v mřížce měr a zadáním vzorce DAX do řádku vzorců. Když po dokončení vzorce stisknete ENTER, míra se zobrazí v buňce. Míry můžete také vytvořit pomocí standardní agregační funkce tak, že kliknete na sloupec a pak kliknete na panelu nástrojů na tlačítko AutoSum (**∑**). Míry vytvořené pomocí funkce AutoSum se objeví v buňce mřížky měr přímo pod příslušným sloupcem, dají se ale přesunout.  
  
V této lekci vytvoříte míry zadáním vzorce DAX do řádku vzorců a také pomocí funkce AutoSum.  
  
Odhadovaný čas dokončení této lekce: **30 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 5: Vytvoření počítaných sloupců](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Vytvoření měr  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Postup vytvoření míry DaysCurrentQuarterToDate v tabulce DimDate  
  
1.  V návrháři modelů klikněte na tabulku **DimDate**.  
  
2.  V mřížce měr klikněte na levou horní prázdnou buňku.  
  
3.  Na řádku vzorců zadejte následující vzorec:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Všimněte si, že levá horní buňka teď obsahuje název míry **DaysCurrentQuarterToDate**, za nímž následuje výsledek **92**. Výsledek v tuto chvíli není důležitý, protože se nepoužil žádný filtr uživatele.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    Na rozdíl od počítaných sloupců u vzorců měr můžete zadat název míry následovaný dvojtečkou a výrazem vzorce.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Postup vytvoření míry DaysInCurrentQuarter v tabulce DimDate  
  
1.  Tabulku **DimDate** mějte v návrháři modelů stále aktivní, v mřížce měr klikněte na prázdnou buňku pod vámi vytvořenou mírou.  
  
2.  Na řádku vzorců zadejte následující vzorec:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    V případě, že vytváříte porovnávací poměr mezi jedním neúplným obdobím a předchozím obdobím. Vzorec musí vypočítat poměrnou část období, která uplynula, a porovnat ji se stejnou poměrnou částí v předchozím období. V tomto případě je poměrná část uplynulého času v aktuálním období vyjádřena jako [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter].  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Postup vytvoření míry InternetDistinctCountSalesOrder v tabulce FactInternetSales  
  
1.  Klikněte na tabulku **FactInternetSales**.   
  
2.  Klikněte na záhlaví sloupce **SalesOrderNumber**.  
  
3.  Na panelu nástrojů klikněte na šipku dolů vedle tlačítka AutoSum (**∑**) a potom vyberte **DistinctCount**.  
  
    Funkce AutoSum automaticky vytvoří pro vybraný sloupec míru pomocí standardního agregačního vzorce DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  V mřížce měr klikněte na novou míru a potom v okně **Vlastnosti** v části **Název míry** přejmenujte míru na **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Postup vytvoření dalších měr v tabulce FactInternetSales  
  
1.  Pomocí funkce AutoSum vytvořte a pojmenujte následující míry:  

    |Sloupec|Název míry|AutoSum (∑)|Vzorec|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Počet|=POČET2([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Součet|=SUMA([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Součet|=SUMA([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Součet|=SUMA([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Součet|=SUMA([SalesAmount])|  
    |Margin|InternetTotalMargin|Součet|=SUMA([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Součet|=SUMA([TaxAmt])|  
    |Freight|InternetTotalFreight|Součet|=SUMA([Freight])|  
  
2.  Kliknutím na prázdnou buňku v mřížce měr a použitím řádku vzorců vytvořte následující vlastní míry v daném pořadí:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
Míry vytvořené pro tabulku FactInternetSales slouží k analýze kritických finančních dat, jako jsou například prodeje, náklady a zisková marže pro položky definované uživatelem vybraným filtrem.  
  
## <a name="whats-next"></a>Co dále?
[Lekce 7: Vytvoření klíčových ukazatelů výkonu](../tutorials/aas-lesson-7-create-key-performance-indicators.md)  

  
