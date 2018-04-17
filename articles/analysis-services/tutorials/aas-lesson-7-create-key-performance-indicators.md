---
title: 'Kurz služby Azure Analysis Services – Lekce 7: Vytvoření klíčových ukazatelů výkonu | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit klíčové ukazatele výkonu v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8534640822030a7aa93c8eafe2a1b1a4a8bc5bc4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-key-performance-indicators"></a>Vytvoření klíčových ukazatelů výkonu

V této lekci vytvoříte klíčové ukazatele výkonu (KPI). Klíčové ukazatele výkonu se používají k měření výkonu hodnoty definované *základní* mírou oproti *cílové* hodnotě, která je také definovaná mírou nebo absolutní hodnotou. V klientských aplikacích pro vytváření sestav můžou klíčové ukazatele výkonu poskytnout obchodníkům rychlý a snadný způsob, jak pochopit souhrn obchodních úspěchů nebo identifikovat trendy. Další informace najdete v tématu [Klíčové ukazatele výkonu](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular).
  
Odhadovaný čas dokončení této lekce: **15 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 6: Vytvoření měr](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Vytvoření klíčových ukazatelů výkonu  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>Vytvoření klíčového ukazatele výkonu InternetCurrentQuarterSalesPerformance  
  
1.  V návrháři modelů klikněte na tabulku **FactInternetSales**.  
  
2.  V mřížce měr klikněte na prázdnou buňku.  
  
3.  Na řádku vzorců nad tabulkou zadejte následující vzorec: 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Tato míra slouží jako základní míra klíčového ukazatele výkonu.  
  
4.  V mřížce měr klikněte pravým tlačítkem na **InternetCurrentQuarterSalesPerformance** > **Vytvořit klíčový ukazatel výkonu**.   
  
5.  V dialogovém okně Klíčový ukazatel výkonu (KPI) v části **Cíl** vyberte **Absolutní hodnota**a pak zadejte **1,1**.  
  
7.  Do levého (dolního) pole s posuvníkem zadejte **1** a pak do pravého (horního) pole s posuvníkem zadejte **1,07**.  
  
8.  V části **Vybrat styl ikony** vyberte typ ikony – kosočtverec (červený), trojúhelník (žlutý), kolečko (zelený).
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Všimněte si rozbalovacího popisku **Popisy** pod dostupnými styly ikon. Pokud chcete, aby se různé prvky klíčových ukazatelů výkonu daly v klientských aplikacích lépe identifikovat, použijte popisy.  
  
9. Klíčový ukazatel výkonu dokončete kliknutím na **OK**.  
  
    V mřížce měr si všimněte ikony vedle míry **InternetCurrentQuarterSalesPerformance**. Tato ikona označuje, že tato míra slouží jako základní hodnota klíčového ukazatele výkonu.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>Vytvoření klíčového ukazatele výkonu InternetCurrentQuarterMarginPerformance  
  
1.  V mřížce měr tabulky **FactInternetSales** klikněte na prázdnou buňku.  
  
2.  Na řádku vzorců nad tabulkou zadejte následující vzorec:  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Pravým tlačítkem myši klikněte na **InternetCurrentQuarterMarginPerformance** > **Vytvořit klíčový ukazatel výkonu**.  
  
4.  V dialogovém okně Klíčový ukazatel výkonu (KPI) v části **Cíl** vyberte **Absolutní hodnota**a pak zadejte **1,25**.   
  
5.  V levém (dolním) poli s posuvníkem posunujte tak dlouho, dokud se nezobrazí hodnota **0,8**, a pak v pravém (horním) poli s posuvníkem posunujte, dokud se nezobrazí **1,03**.  
  
6.  V části **Vybrat styl ikony** vyberte typ ikony – kosočtverec (červený), trojúhelník (žlutý), kolečko (zelený) – a pak klikněte na **OK**.  
  
## <a name="whats-next"></a>Co dále?
[Lekce 8: Vytvoření perspektiv](../tutorials/aas-lesson-8-create-perspectives.md)
  
  
