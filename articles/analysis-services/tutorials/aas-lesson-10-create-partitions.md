---
title: 'Kurz služby Azure Analysis Services – Lekce 10: Vytvoření oddílů | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit oddíly v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 743eff21154502fc4ff651177abb8df91edb7494
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-partitions"></a>Vytvoření oddílů

V této lekci vytvoříte oddíly pro rozdělení tabulky FactInternetSales na menší logické jednotky s možností zpracování (aktualizace) nezávisle na ostatních oddílech. Ve výchozím nastavení má každá tabulka zahrnutá do modelu jeden oddíl, který obsahuje všechny sloupce a řádky tabulky. Pro tabulku FactInternetSales chceme data rozdělit podle roku – jeden oddíl pro každých pět let v tabulce. Jednotlivé oddíly pak bude možné zpracovávat nezávisle na sobě. Další informace najdete v tématu [Oddíly](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular). 
  
Odhadovaný čas dokončení této lekce: **15 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 9: Vytvoření hierarchií](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Vytvoření oddílů  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Vytvoření oddílů v tabulce FactInternetSales  
  
1.  V Průzkumníku tabelárních modelů rozbalte **Tabulky** a potom klikněte pravým tlačítkem na **FactInternetSales** > **Oddíly**.  
  
2.  Ve správci oddílů klikněte na **Kopírovat** a potom změňte název na **FactInternetSales2010**.
  
    Protože chcete, aby oddíl obsahoval pouze řádky z určitého období (rok 2010), musíte upravit výraz dotazu.
  
4.  Kliknutím na **Návrh** otevřete editor dotazů a klikněte na dotaz **FactInternetSales2010**.

5.  V náhledu klikněte na šipku dolů v záhlaví sloupce **OrderDate** a potom klikněte na **Filtry data a času** > **Mezi**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  V dialogovém okně Filtrovat řádky v části **Zobrazit řádky, kde: OrderDate** ponechte možnost **je po nebo přesně** a do pole kalendářního data zadejte **1. 1. 2010**. Ponechte vybraný operátor **A**, vyberte možnost **je před**, do pole kalendářního data zadejte **1. 1. 2011** a klikněte na **OK**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Všimněte si, že v editoru dotazů se v části POUŽITÝ POSTUP zobrazí další krok s názvem Filtrované řádky. Tento filtr slouží k výběru dat objednávek pouze z roku 2010.

8.  Klikněte na **Importovat**.

    Ve správci oddílů si všimněte, že výraz dotazu teď obsahuje další klauzuli Filtrované řádky.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Tento příkaz určuje, že by tento oddíl měl zahrnovat pouze data v řádcích, kde hodnota ve sloupci OrderDate spadá do kalendářního roku 2010, jak je uvedeno v klauzuli Filtrované řádky.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Vytvoření oddílu pro rok 2011  
  
1.  V seznamu oddílů klikněte na oddíl **FactInternetSales2010**, který jste vytvořili, a potom klikněte na **Kopírovat**.  Změňte název oddílu na **FactInternetSales2011**. 

    Není nutné použít editor dotazů k vytvoření nové klauzule Filtrované řádky. Vzhledem k tomu, že jste vytvořili kopii dotazu pro rok 2010, stačí provést drobnou změnu v dotazu pro rok 2011.
  
2.  Aby tento oddíl obsahoval pouze řádky pro rok 2011, ve **výrazu dotazu** nahraďte roky v klauzuli Filtrované řádky za **2011** a **2012**:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Vytvoření oddílů pro roky 2012, 2013 a 2014  
  
- Stejným postupem jako v předchozích krocích vytvořte oddíly pro roky 2012, 2013 a 2014 a v klauzuli Filtrované řádky vždy změňte rok tak, aby se zahrnuly pouze řádky pro daný rok. 
  

## <a name="delete-the-factinternetsales-partition"></a>Odstranění oddílu FactInternetSales
Když teď máte oddíly pro každý rok, můžete odstranit oddíl FactInternetSales. Zabráníte tím překrývání při výběru možnosti Zpracovat vše v rámci zpracování oddílů.

#### <a name="to-delete-the-factinternetsales-partition"></a>Odstranění oddílu FactInternetSales
-  Klikněte na oddíl FactInternetSales a potom klikněte na **Odstranit**.



## <a name="process-partitions"></a>Zpracování oddílů  
Ve správci oddílů si všimněte, že sloupec **Poslední zpracování** pro každý z nově vytvořených oddílů ukazuje, že oddíly nikdy nebyly zpracovány. Při vytváření oddílů byste měli spustit operaci Zpracování oddílů nebo Zpracování tabulky pro aktualizaci dat v těchto oddílech.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Zpracování oddílů FactInternetSales  
  
1.  Kliknutím na **OK** zavřete správce oddílů.  
  
2.  Klikněte na tabulku **FactInternetSales** a potom na nabídku **Model** > **Zpracovat** > **Zpracovat oddíly**.  
  
3.  V dialogovém okně Zpracovat oddíly zkontrolujte, že je **Režim** nastavený na **Zpracovat výchozí**.  
  
4.  Ve sloupci **Zpracovat** zaškrtněte políčko pro každý z pěti oddílů, které jste vytvořili, a potom klikněte na **OK**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Pokud se zobrazí výzva k zadání přihlašovacích údajů k zosobnění, zadejte uživatelské jméno a heslo systému Windows, které jste zadali v lekci 2.  
  
    Zobrazí se dialogové okno **Zpracování dat** s podrobnostmi o zpracování pro každý oddíl. Všimněte si, že pro každý oddíl se přenáší jiný počet řádků. Každý oddíl obsahuje pouze řádky pro rok zadaný v klauzuli WHERE příkazu jazyka SQL. Po dokončení zpracování zavřete dialogové okno Zpracování dat.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>Co dále?
Přejděte na další lekci: [Lekce 11: Vytvoření rolí](../tutorials/aas-lesson-11-create-roles.md). 
