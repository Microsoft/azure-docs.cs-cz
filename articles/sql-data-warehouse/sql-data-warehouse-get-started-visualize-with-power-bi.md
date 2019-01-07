---
title: Vizualizace dat SQL Data Warehouse pomocí Power BI – Microsoft Azure
description: Vizualizace dat SQL Data Warehouse pomocí Power BI
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 0fbb389120857a167867a5804c670e181f5232b6
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051437"
---
# <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI
V tomto kurzu si ukážeme, jak se pomocí Power BI připojit k SQL Data Warehouse a vytvořit pár základních vizualizací.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* SQL Data Warehouse s předem načtenou databází AdventureWorksDW. Zřiďte si datový sklad, naleznete v tématu [vytvořit SQL Data Warehouse](create-data-warehouse-portal.md) a zvolte načtení ukázkových dat. Pokud už máte datový sklad, ale nemáte ukázková data, můžete si [načíst WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Připojení k databázi
Pokud chcete otevřít Power BI a připojit se ke své databázi AdventureWorksDW, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klikněte na **Databáze SQL** a zvolte databázi AdventureWorks služby SQL Data Warehouse.
   
    ![Vyhledání databáze](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klikněte na tlačítko Otevřít v Power BI.
   
    ![Tlačítko Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Teď by se vám měla zobrazit stránka pro připojení k SQL Data Warehouse s webovou adresou vaší databáze. Klikněte na Další.
   
    ![Připojení Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Zadejte svoje uživatelské jméno pro server Azure SQL a heslo.
   
    ![Power BI přihlášení](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Chcete-li otevřít databázi, klikněte na datovou sadu AdventureWorksDW v levém okně.
   
    ![Otevření databáze AdventureWorksDW v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Vytvoření sestavy
Teď můžete pomocí Power BI analyzovat ukázková data databáze AdventureWorksDW. K provedení analýzy má databáze AdventureWorksDW zobrazení s názvem AggregateSales. Toto zobrazení obsahuje několik klíčových metrik pro analýzu prodeje společnosti.

1. Pokud budete chtít vytvořit mapu částek prodeje podle PSČ, klikněte v podokně polí napravo na zobrazení AggregateSales a tím ho rozbalte. Kliknutím vyberte sloupce PostalCode a SalesAmount.
   
    ![Vybere AggregateSales v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI automaticky rozpoznat zeměpisná data a vložit ho do mapy za vás.
   
    ![Mapa Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Tento krok vytvoří pruhový graf, který zobrazí částku prodeje na příjem zákazníka. Vytvoření pruhového grafu, přejděte v rozbaleném zobrazení AggregateSales. Klikněte na pole SalesAmount. Přetáhněte pole Customer Income (Příjem zákazníka) nalevo do části Axis (Osa).
   
    ![Osy vybere Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Pruhový graf přes vlevo.
   
    ![Pruhový graf v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Tento krok vytvoří spojnicový graf zobrazující prodejní částku k datu objednávky. Chcete-li vytvořit spojnicový graf, přejděte v rozbaleném zobrazení AggregateSales. Klikněte na SalesAmount a OrderDate. Ve sloupci vizualizace klikněte na ikonu spojnicového grafu, který je první ikona na druhém řádku pod vizualizacemi.
   
    ![Vybere spojnicový graf v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Teď máte sestavu zobrazující tři různé vizualizace dat.
   
    ![Spojnicový graf v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Kdykoli můžete rozdělanou práci uložit tak, že kliknete na **Soubor** a vyberete **Uložit**.

## <a name="using-direct-connect"></a>Pomocí přímé připojení
Jako s Azure SQL Database, SQL Data Warehouse přímé připojení umožňuje logické přenosu směrem dolů vedle analytických funkcí Power BI. Přímé připojení dotazy odesílají pomocí zpět do služby Azure SQL Data Warehouse v reálném čase když zkoumáte data.  Tato funkce ve spojení se Škálováním služby SQL Data Warehouse umožňuje vytvářet dynamické sestavy během několika minut terabajtech dat. Kromě toho po zavedení služby otevřít v Power BI tlačítko umožňuje uživatelům přímo připojit Power BI pro svůj SQL Data Warehouse bez shromažďování informací o z jiných částí Azure.

Při používání s přímým přístupem připojení:

* Při připojování zadejte plně kvalifikovaný název.
* Ujistěte se, že pravidla firewallu pro databázi jsou nakonfigurovaná na povolit přístup ke službám Azure.
* Každá akce, jako je například výběr sloupce nebo přidání filtru, dotazuje přímo k datovému skladu.
* Dlaždice se aktualizují automaticky a přibližně každých 15 minut.
* Funkce Q & A není k dispozici pro přímé připojení datové sady.
* Změny schématu jsou zahrnuty automaticky.
* Všechny dotazy na přímé připojení vyprší časový limit po 2 minuty.

Těchto omezení a poznámky může změnit, protože prostředí vylepšit.

## <a name="next-steps"></a>Další postup
Vyzkoušeli jste si tedy práci s ukázkovými daty a teď se podívejte, jak na [vývoj](sql-data-warehouse-overview-develop.md), [načítání](design-elt-data-loading.md) nebo [migraci](sql-data-warehouse-overview-migrate.md). Nebo se podívejte na [web Power BI](http://www.powerbi.com/).
