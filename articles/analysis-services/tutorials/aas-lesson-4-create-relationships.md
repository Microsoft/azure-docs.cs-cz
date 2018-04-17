---
title: 'Kurz služby Azure Analysis Services – Lekce 4: Vytvoření relací | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit relace v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 694043454ba2564900416135e1f143bdeb63c202
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-relationships"></a>Vytvoření relace

V této lekci ověříte relace, které se automaticky vytvořily při importu dat, a přidáte nové relace mezi různými tabulkami. Relace je propojení dvou tabulek, které určuje, jakým způsobem se data v těchto tabulkách mají korelovat. Například tabulky DimProduct a DimProductSubcategory mají relaci na základě toho, že každý produkt patří do nějaké podkategorie. Další informace najdete v tématu [Relace](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular).
  
Odhadovaný čas dokončení této lekce: **10 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 3: Označení jako tabulky kalendářních dat](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Kontrola existujících relací a přidání nových relací  
Při importu dat pomocí funkce Získání dat jste z databáze AdventureWorksDW2014 získali sedm tabulek. Obecně platí, že při importu dat z relačního zdroje se společně s daty automaticky importují i existující relace. Aby mohla funkce Získání dat automaticky vytvořit relace v datovém modelu, musí existovat relace mezi tabulkami ve zdroji dat.

Než budete pokračovat ve vytváření modelu, měli byste ověřit správné vytvoření těchto relací mezi tabulkami. Pro účely tohoto kurzu také přidáte tři nové relace.  

  
#### <a name="to-review-existing-relationships"></a>Kontrola existujících relací  
  
1.  Klikněte na nabídku **Model** > **Zobrazení modelu** > **Zobrazení diagramu**.  

    V zobrazení diagramu se zobrazí návrhář modelů – grafický formát zobrazující všechny importované tabulky propojené čárami. Čáry mezi tabulkami označují relace, které se automaticky vytvořily při importu dat.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > Pokud nevidíte žádné relace mezi tabulkami, pravděpodobně to znamená, že mezi těmito tabulkami ve zdroji dat nejsou žádné relace.

    Zahrnuje co nejvíc tabulek je možné s využitím ovládacích prvků minimapy v pravém dolním rohu návrháře modelů. Můžete také kliknout na tabulky a přetáhnout je jinam, přiblížit tabulky k sobě nebo je uspořádat do určitého pořadí. Přesun tabulek nemá vliv na relace mezi nimi. Pokud chcete zobrazit všechny sloupce konkrétní tabulky, klikněte na okraj tabulky a tažením ji zvětšete nebo zmenšete.  
  
2.  Klikněte na plnou čáru mezi tabulkami **DimCustomer** a **DimGeography**. Plná čára mezi těmito dvěma tabulkami ukazuje, že je tato relace aktivní, tzn. že se používá ve výchozím nastavení při výpočtech vzorců DAX.  
  
    Všimněte si, že se teď v rámečku zobrazuje sloupec **GeographyKey** v tabulce **DimCustomer** i sloupec **GeographyKey** v tabulce **DimGeography**. Tyto sloupce se používají v relaci. V okně **Vlastnosti** se teď také zobrazují vlastnosti relace.  
  
    > [!TIP]  
    > Kromě použití návrháře modelů v zobrazení diagramu můžete také zobrazit relace mezi všemi tabulkami ve formátu tabulky pomocí dialogového okna Správa relací. V Průzkumníku tabelárních modelů klikněte pravým tlačítkem na **Relace** > **Správa relací**.
  
3.  Ověřte, že se při importu jednotlivých tabulek z databáze AdventureWorksDW vytvořily následující relace:  
  
    |Aktivní|Table|Související vyhledávací tabulka|  
    |----------|---------|------------------------|  
    |Ano|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Ano|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Ano|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Ano|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Ano|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Pokud jakákoli z těchto relací chybí, ověřte, že váš model obsahuje následující tabulky: DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory a FactInternetSales. Pokud se tabulky ze stejného připojení ke zdroji dat importují v různou dobu, případné relace mezi těmito tabulkami se nevytvoří a musí být vytvořeny ručně. Pokud se nezobrazí žádné relace, znamená to, že ve zdroji dat nejsou žádné relace. Můžete je vytvořit ručně v datovém modelu.

### <a name="take-a-closer-look"></a>Bližší prozkoumání
V zobrazení diagramu si všimněte šipky, hvězdičky a čísla na čárách znázorňujících relace mezi tabulkami.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

Šipka zobrazuje směr filtru. Hvězdička znázorňuje, že tabulka je v kardinalitě relace 1:N na straně N, zatímco jednička znázorňuje, že tabulka je v relaci na straně 1. Pokud potřebujete upravit relaci, například změnit směr filtru relace nebo kardinalitu, dvakrát klikněte na čáru relace a otevřete dialogové okno Upravit relaci.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Tyto funkce jsou určené pro pokročilé modelování dat a jsou nad rámec tohoto kurzu. Další informace najdete v tématu [Obousměrné křížové filtry pro tabelární modely ve službě Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

V některých případech můžete potřebovat vytvořit mezi tabulkami ve vašem modelu další relace pro podporu určité obchodní logiky. Pro účely tohoto kurzu potřebujete vytvořit další tři relace mezi tabulkami FactInternetSales a DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Přidání nových relací mezi tabulkami  
  
1.  V návrháři modelů v tabulce **FactInternetSales** klikněte a přidržte sloupec **OrderDate**, přesuňte kurzor na sloupec **Date** v tabulce **DimDate** a uvolněte tlačítko.  

    Zobrazí se plná čára znázorňující, že jste vytvořili aktivní relaci mezi sloupcem **OrderDate** v tabulce **Internet Sales** a sloupcem **Date** v tabulce **DimDate**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Při vytváření relací se kardinalita a směr filtru mezi primární tabulkou a související vyhledávací tabulkou vybere automaticky.  
  
2.  V tabulce **FactInternetSales** klikněte a přidržte sloupec **DueDate**, přesuňte kurzor na sloupec **Date** v tabulce **DimDate** a uvolněte tlačítko.  
  
    Zobrazí se tečkovaná čára znázorňující, že jste vytvořili neaktivní relaci mezi sloupcem **DueDate** v tabulce **FactInternetSales** a sloupcem **Date** v tabulce **DimDate**. Mezi tabulkami můžete mít víc relací, ale aktivní může vždy být pouze jedna relace. Neaktivní relace se můžou aktivovat pro provádění speciálních agregací ve vlastních výrazech DAX.  
  
3.  Nakonec vytvořte ještě jednu relaci. V tabulce **FactInternetSales** klikněte a přidržte sloupec **ShipDate**, přesuňte kurzor na sloupec **Date** v tabulce **DimDate** a uvolněte tlačítko.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>Co dále?
[Lekce 5: Vytvoření počítaných sloupců](../tutorials/aas-lesson-5-create-calculated-columns.md)
  
  
  
