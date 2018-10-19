---
title: 'Kurz služby Azure Analysis Services – Lekce 9: Vytvoření hierarchií | Dokumentace Microsoftu'
description: Popisuje postup vytvoření hierarchie v tabulkovém modelu.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e6591dc25ab498b496f6abff1041e448240896d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426041"
---
# <a name="create-hierarchies"></a>Vytvoření hierarchií

V této lekci vytvoříte hierarchie. Hierarchie jsou skupiny sloupců uspořádané do úrovní. Například hierarchie Geography může mít dílčí úrovně pro zemi, stát, kraj a město. Hierarchie se může zobrazit odděleně od ostatních sloupců v seznamu polí klientských aplikací pro vytváření sestav. Další informace najdete v tématu [Hierarchie](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular).
  
K vytvoření hierarchií použijte návrháře modelů v *zobrazení diagramu*. Vytváření a správa hierarchií se v zobrazení dat nepodporuje.  
  
Odhadovaný čas dokončení této lekce: **20 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 8: Vytvoření perspektiv](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Vytvoření hierarchií  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Postup vytvoření hierarchie Category v tabulce DimProduct  
  
1.  V návrháři modelů (zobrazení diagramu) klikněte pravým tlačítkem myši na tabulku **DimProduct** > **Vytvořit hierarchii**. V dolní části okna tabulky se zobrazí nová hierarchie. Přejmenujte hierarchii na **Category**.  
  
2.  Klikněte na sloupec **ProductCategoryName** a přetáhněte ho do nové hierarchie **Category**.  
  
3.  V hierarchii **Category** klikněte pravým tlačítkem na **ProductCategoryName** > **Přejmenovat** a pak zadejte **Category**.  
  
    > [!NOTE]  
    > Přejmenováním sloupce v hierarchii nedojde k přejmenování tohoto sloupce v tabulce. Sloupec v hierarchii je pouze reprezentací sloupce v tabulce.  
  
4.  Klikněte na sloupec **ProductSubcategoryName** a přetáhněte ho do hierarchie **Category**. Přejmenujte ho na **Subcategory**. 
  
5.  Pravým tlačítkem myši klikněte na sloupec **ModelName** > **Přidat do hierarchie** a potom vyberte **Category**. Přejmenujte ho na **Model**.

6.  Nakonec přidejte **EnglishProductName** do hierarchie Category. Přejmenujte ho na **Product**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Postup vytvoření hierarchií v tabulce DimDate  
  
1.  V tabulce **DimDate** vytvořte hierarchii s názvem **Calendar**.  
  
3.  Přidejte následující sloupce v daném pořadí:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  V tabulce **DimDate** vytvořte hierarchii**Fiscal**. Zahrňte následující sloupce v daném pořadí:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Nakonec v tabulce **DimDate** vytvořte hierarchii **ProductionCalendar**. Zahrňte následující sloupce v daném pořadí:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>Co dále?
[Lekce 10: Vytvoření oddílů](../tutorials/aas-lesson-10-create-partitions.md) 
  
  
