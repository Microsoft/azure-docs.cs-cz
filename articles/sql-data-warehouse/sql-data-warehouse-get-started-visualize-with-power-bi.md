---
title: Vizualizace dat SQL Data Warehouse pomocí Power BI – Microsoft Azure
description: Vizualizace dat SQL Data Warehouse pomocí Power BI
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 52581a87caac419a79caab647cc9c5a4ee7453ba
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI
V tomto kurzu si ukážeme, jak se pomocí Power BI připojit k SQL Data Warehouse a vytvořit pár základních vizualizací.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* SQL Data Warehouse s předem načtenou databází AdventureWorksDW. Ke zřízení datového skladu, najdete v části [vytvořit SQL Data Warehouse](create-data-warehouse-portal.md) a zvolte Načíst ukázková data. Pokud už máte datový sklad, ale nemáte ukázková data, můžete [načíst WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Připojení k databázi
Pokud chcete otevřít Power BI a připojit se ke své databázi AdventureWorksDW, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na **Databáze SQL** a zvolte databázi AdventureWorks služby SQL Data Warehouse.
   
    ![Vyhledání databáze](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klikněte na tlačítko Otevřít v Power BI.
   
    ![Tlačítko Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Teď by se vám měla zobrazit stránka pro připojení k SQL Data Warehouse s webovou adresou vaší databáze. Klikněte na Další.
   
    ![Připojení Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Zadejte uživatelské jméno pro server Azure SQL a heslo.
   
    ![Přihlášení Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Chcete-li otevřít databázi, klikněte na datovou sadu AdventureWorksDW v levém okně.
   
    ![Otevření databáze AdventureWorksDW v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Vytvoření sestavy
Teď můžete pomocí Power BI analyzovat ukázková data databáze AdventureWorksDW. K provedení analýzy má databáze AdventureWorksDW zobrazení s názvem AggregateSales. Toto zobrazení obsahuje několik klíčových metrik pro analýzu prodeje společnosti.

1. Pokud budete chtít vytvořit mapu částek prodeje podle PSČ, klikněte v podokně polí napravo na zobrazení AggregateSales a tím ho rozbalte. Kliknutím vyberte sloupce PostalCode a SalesAmount.
   
    ![Power BI vybere AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI automaticky rozpoznána zeměpisné údaje a umístí jej v mapování.
   
    ![Mapa Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Tento krok vytvoří pruhový graf, který zobrazí částku prodeje na příjem zákazníka. Pokud chcete vytvořit pruhový graf, přejděte rozbaleném zobrazení AggregateSales. Klikněte na pole SalesAmount. Přetáhněte pole Customer Income (Příjem zákazníka) nalevo do části Axis (Osa).
   
    ![Power BI vybere osy](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Pruhový graf přes doleva.
   
    ![Pruhový graf v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Tento krok vytvoří spojnicový graf zobrazující prodejní částku k datu objednávky. Chcete-li vytvořit spojnicový graf, přejděte na v rozbaleném zobrazení AggregateSales. Klikněte na SalesAmount a OrderDate. Ve sloupci vizualizace klikněte na ikonu spojnicového grafu, což je první ikona na druhém řádku pod vizualizacemi.
   
    ![Power BI vybere spojnicový graf](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Teď máte sestavu zobrazující tři různé vizualizace dat.
   
    ![Spojnicový graf v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Kdykoli můžete rozdělanou práci uložit tak, že kliknete na **Soubor** a vyberete **Uložit**.

## <a name="using-direct-connnect"></a>Pomocí přímé spojení jednotlivých
Jako s Azure SQL Database, SQL Data Warehouse přímé připojení umožňuje přenos logické směrem dolů vedle analytické možnosti Power BI. Přímé připojení dotazy jsou odeslán zpět do Azure SQL Data Warehouse v v reálném čase jako zkoumat data.  Tato funkce v kombinaci s škálování služby SQL Data Warehouse umožňuje vytvářet dynamické sestavy v minutách proti terabajtů data. Kromě toho zavedení Open in tlačítko Power BI umožňuje uživatelům připojovat přímo Power BI do jejich SQL Data Warehouse bez shromažďovat informace z dalších částí Azure.

Při používání přímé připojení:

* Pokud se připojujete, zadejte název plně kvalifikovaný serveru.
* Ujistěte se, že jsou pravidla brány firewall pro databázi nakonfigurovaná na povolit přístup ke službám Azure.
* Všechny akce, jako výběrem sloupce nebo přidáním filtru, dotazuje přímo do datového skladu.
* Dlaždice se aktualizuje automaticky a přibližně každých 15 minut.
* Modul otázky A odpovědi není k dispozici pro přímé připojení datové sady.
* Změny schématu jsou automaticky součástí.
* Všechny dotazy přímé připojení bude vyprší po 2 minut.

Těchto omezení a poznámky může změnit, protože zlepšení činnosti koncových.

## <a name="next-steps"></a>Další postup
Vyzkoušeli jste si tedy práci s ukázkovými daty a teď se podívejte, jak na [vývoj](sql-data-warehouse-overview-develop.md), [načítání](design-elt-data-loading.md) nebo [migraci](sql-data-warehouse-overview-migrate.md). Nebo se podívejte na [web Power BI](http://www.powerbi.com/).
