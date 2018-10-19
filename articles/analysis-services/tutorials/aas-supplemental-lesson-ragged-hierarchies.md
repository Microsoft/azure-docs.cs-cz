---
title: 'Kurz služby Azure Analysis Services – Doplňková lekce: Nepravidelné hierarchie | Dokumentace Microsoftu'
description: Popisuje, jak opravit nepravidelné hierarchie v kurzu služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f5bd679c11139b7ac439247df81a7adb0b02b0fe
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426323"
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Doplňková lekce – Nepravidelné hierarchie

V této doplňkové lekci vyřešíte běžný problém vyskytující se při přesunu do hierarchií, které obsahují prázdné hodnoty (členy) na různých úrovních. Může se to například týkat organizace, ve které vysoce postavenému manažerovi přímo reportují manažeři a jiní zaměstnanci oddělení. Nebo se může jednat o geografické hierarchie skládající se z položek Země, Oblast, Město, kde některá města nemají nadřazený stát nebo kraj, například Washington D. C. nebo Vatikán. Pokud hierarchie obsahuje prázdné členy, dělí se často do různých nebo nepravidelných úrovni.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Tabulkové modely na úrovni kompatibility 1400 mají pro hierarchie další vlastnost – **Skrýt členy**. Nastavení **Výchozí** předpokládá, že se na žádné z úrovní nevyskytují prázdné členy. Nastavení **Skrýt prázdné členy** vyloučí po přidání do kontingenční tabulky nebo sestavy z hierarchie prázdné členy.  
  
Odhadovaný čas dokončení této lekce: **20 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma doplňkové lekce je součástí kurzu tabulkového modelování. Než začnete provádět úkoly v této doplňkové lekci, měli byste mít dokončené všechny předchozí lekce nebo mít dokončený ukázkový projekt modelu Adventure Works Internet Sales. 

Pokud jste projekt AW Internet Sales vytvořili v rámci kurzu, neobsahuje model zatím žádná data nebo hierarchie, které by byly nepravidelné. Abyste mohli tuto doplňkovou lekci dokončit, musíte nejdřív problém vytvořit tak, že přidáte nějaké další tabulky, vytvoříte relace, počítané sloupce, míru a novou hierarchii Organization. Tato část zabere přibližně 15 minut. Řešení pak vytvoříte za pár minut.  

## <a name="add-tables-and-objects"></a>Přidání tabulek a objektů
  
### <a name="to-add-new-tables-to-your-model"></a>Postup přidání nových tabulek do modelu
  
1.  V Průzkumníku tabulkových modelů rozbalte položku **Zdroje Dat**, potom klikněte pravým tlačítkem myši na vaše připojení > **Importovat nové tabulky**.
  
2.  V Navigátoru vyberte **DimEmployee** a **FactResellerSales** a potom klikněte na **OK**.

3.  V Editoru dotazů klikněte na **Importovat**.

4.  Vytvořte následující [relace](../tutorials/aas-lesson-4-create-relationships.md):

    | Tabulka 1           | Sloupec       | Směr filtru   | Tabulka 2     | Sloupec      | Aktivní |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Výchozí            | DimDate     | Datum        | Ano    |
    | FactResellerSales | DueDate      | Výchozí            | DimDate     | Datum        | Ne     |
    | FactResellerSales | ShipDateKey  | Výchozí            | DimDate     | Datum        | Ne     |
    | FactResellerSales | ProductKey   | Výchozí            | DimProduct  | ProductKey  | Ano    |
    | FactResellerSales | EmployeeKey  | Na obě tabulky | DimEmployee | EmployeeKey | Ano    |

5. V tabulce **DimEmployee** vytvořte následující [počítané sloupce](../tutorials/aas-lesson-5-create-calculated-columns.md): 

    **Cesta** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **Úplný název** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  V tabulce **DimEmployee** vytvořte [hierarchii](../tutorials/aas-lesson-9-create-hierarchies.md) s názvem **Organization**. Přidejte následující sloupce v daném pořadí: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  V tabulce **FactResellerSales** vytvořte následující [míru](../tutorials/aas-lesson-6-create-measures.md):

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Pomocí funkce [Analyzovat v aplikaci Excel](../tutorials/aas-lesson-12-analyze-in-excel.md) otevřete Excel a automaticky vytvořte kontingenční tabulku.

9.  V části **Pole kontingenční tabulky** přidejte hierarchii **Organization** z tabulky **DimEmployee** do **Řádky**a míru **ResellerTotalSales** z tabulky **FactResellerSales** do **Hodnoty**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    V kontingenční tabulce můžete vidět, že hierarchie zobrazuje nepravidelné řádky. Je tam množství řádků, které zobrazují prázdné členy.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Postup opravy nepravidelné hierarchie nastavením vlastnosti Skrýt členy

1.  V **Průzkumníku tabulkových modelů** rozbalte **Tabulky** > **DimEmployee** > **Hierarchie** > **Organization**.

2.  V části **Vlastnosti** > **Skrýt členy** vyberte **Skrýt prázdné členy**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Zpět v aplikaci Excel aktualizujte kontingenční tabulku. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Teď to vypadá mnohem líp!

## <a name="see-also"></a>Viz také   
[Lekce 9: Vytvoření hierarchií](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Doplňková lekce – Dynamické zabezpečení](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Doplňková lekce – Řádky podrobností](../tutorials/aas-supplemental-lesson-detail-rows.md)  
