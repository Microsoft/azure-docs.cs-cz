---
title: 'Kurz služby Azure Analysis Services – Lekce 2: Získání dat | Dokumentace Microsoftu'
description: Popisuje, jak získat a importovat data v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d66b3f303ab4e19ee645be8768512a451454769b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596970"
---
# <a name="get-data"></a>Získání dat

V této lekci se pomocí funkce získání dat v SSDT připojíte k ukázkové databázi AdventureWorks, vyberete data, zobrazíte jejich náhled, použijete filtr a potom je naimportujete do pracovního prostoru modelu.  
  
Pomocí funkce Získání dat můžete importovat data z celé řady zdrojů: Azure SQL Database, Oracle, Sybase, kanál OData, Teradata, soubory a další. Data umožňují také dotazy pomocí výrazu se vzorci Power Query M.

> [!NOTE]
> Úlohy a obrázky v tomto kurzu ukazují připojení k databázi AdventureWorksDW2014 na místním serveru. V některých případech se databáze AdventureWorks v Azure může lišit.
  
Odhadovaný čas dokončení této lekce: **10 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 1: Vytvoření nového projektu s tabelárním modelem](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Vytvoření připojení  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Vytvoření připojení k databázi AdventureWorksDW2014  
  
1.  V Průzkumníku tabelárních modelů klikněte pravým tlačítkem na **Zdroje dat** > **Importovat ze zdroje dat**.  
  
    Spustí se funkce Získání dat, která vás provede připojením ke zdroji dat. Pokud se Průzkumník tabelárních modelů nezobrazuje, v **Průzkumníku řešení** dvakrát klikněte na **Model.bim** a otevřete model v návrháři. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  Ve funkci Získání dat klikněte na **Databáze** > **Databáze SQL Serveru** > **Připojit**.  
  
3.  V dialogovém okně **Databáze SQL Serveru** v části **Server** zadejte název serveru, na který jste nainstalovali databázi AdventureWorksDW2014, a klikněte na **Připojit**.  

4.  Po zobrazení výzvy k zadání přihlašovacích údajů je potřeba zadat přihlašovací údaje, pomocí kterých se služba Analysis Services připojuje ke zdroji dat při importu a zpracování dat. V části **Režim zosobnění** vyberte **Zosobnit účet**, zadejte přihlašovací údaje a klikněte na **Připojit**. Doporučujeme použít účet, u kterého nedochází k vypršení platnosti hesla.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Nejbezpečnější metodu připojení ke zdroji dat poskytuje použití uživatelského účtu a hesla systému Windows.
  
5.  V části Navigátor vyberte databázi **AdventureWorksDW2014** a klikněte na **OK**. Tím se vytvoří připojení k databázi. 
  
6.  V části Navigátor zaškrtněte políčka u následujících tabulek: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** a **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
Po kliknutí na OK se otevře Editor dotazů. V další části vyberete jenom data, která chcete importovat.

  
## <a name="filter-the-table-data"></a>Filtrování tabulkových dat  
Tabulky v ukázkové databázi AdventureWorksDW2014 obsahují data, která není nutné zahrnout do modelu. Pokud je to možné, nepotřebná data byste měli vyfiltrovat, abyste ušetřili místo v paměti využívané modelem. Vyfiltrujete z tabulek některé ze sloupců, aby se neimportovaly do databáze pracovního prostoru nebo databáze modelu, až bude nasazena. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Filtrování tabulkových dat před importem  
  
1.  V Editoru dotazů vyberte tabulku **DimCustomer**. Otevře se zobrazení tabulky DimCustomer ve zdroji dat (vaše ukázková databáze AdventureWorksDW2014). 
  
2.  Vyberte (Ctrl + kliknutí) sloupce **SpanishEducation**, **FrenchEducation**, **SpanishOccupation** a **FrenchOccupation**, klikněte pravým tlačítkem a potom klikněte na **Odebrat sloupce**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Vzhledem k tomu, že tyto sloupce nejsou pro analýzu prodejů přes internet relevantní, není nutné je importovat. Díky odstranění nepotřebných sloupců bude váš model menší a efektivnější.  

    > [!TIP]
    > Pokud uděláte chybu, můžete se vrátit odstraněním kroku v části **POUŽITÝ POSTUP**.   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  Filtrujte zbývající tabulky odebráním následujících sloupců v každé z nich:  
    
    **DimDate**
    
      |Sloupec|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Sloupec|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Sloupec|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Sloupec|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Sloupec|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      Neodebírejte žádné sloupce.
  
## <a name="Import"></a>Import vybraných tabulek a dat sloupců  
Teď, když jste zobrazili náhled a vyfiltrovali nepotřebná data, můžete importovat zbývající požadovaná data. Průvodce importuje kromě tabulkových dat také případné relace mezi tabulkami. V modelu se vytvoří nové tabulky a sloupce a data, která jste vyfiltrovali, se neimportují.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Import vybraných tabulek a dat sloupců  
  
1.  Zkontrolujte váš výběr. Pokud vše vypadá v pořádku, klikněte na **Importovat**. V dialogovém okně Zpracování dat se zobrazí stav importování dat ze zdroje dat do databáze pracovního prostoru.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Klikněte na **Zavřít**.  

  
## <a name="save-your-model-project"></a>Uložení projektu s modelem  
Je důležité projekt s modelem často ukládat.  
  
#### <a name="to-save-the-model-project"></a>Uložení projektu s modelem  
  
-   Klikněte na **Soubor** > **Uložit vše**.  
  
## <a name="whats-next"></a>Co dále?
[Lekce 3: Označení jako tabulky kalendářních dat](../tutorials/aas-lesson-3-mark-as-date-table.md)

  
  
