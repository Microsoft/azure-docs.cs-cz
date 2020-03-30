---
title: Vizualizace dat pomocí Power BI Microsoft Azure
description: Vizualizace dat SQL Data Warehouse pomocí Power BI
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e764eeab6f681d90e1a602f02cdb03330d6fd3d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350950"
---
# <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI
V tomto kurzu si ukážeme, jak se pomocí Power BI připojit k SQL Data Warehouse a vytvořit pár základních vizualizací.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* SQL Data Warehouse s předem načtenou databází AdventureWorksDW. Pokud chcete zřídit datový sklad, přečtěte si část [Vytvoření datového skladu SQL](create-data-warehouse-portal.md) a zvolte načtení ukázkových dat. Pokud již máte datový sklad, ale nemáte ukázková data, můžete [načíst WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Připojení k databázi
Pokud chcete otevřít Power BI a připojit se ke své databázi AdventureWorksDW, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klikněte na **Databáze SQL** a zvolte databázi AdventureWorks služby SQL Data Warehouse.
   
    ![Vyhledání databáze](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klikněte na tlačítko Otevřít v Power BI.
   
    ![Tlačítko Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Teď by se vám měla zobrazit stránka pro připojení k SQL Data Warehouse s webovou adresou vaší databáze. Klikněte na Další.
   
    ![Připojení Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Zadejte uživatelské jméno a heslo serveru Azure SQL.
   
    ![Přihlášení k Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Chcete-li otevřít databázi, klepněte na datovou sadu AdventureWorksDW v levém okně.
   
    ![Otevření databáze AdventureWorksDW v Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Vytvoření sestavy
Teď můžete pomocí Power BI analyzovat ukázková data databáze AdventureWorksDW. K provedení analýzy má databáze AdventureWorksDW zobrazení s názvem AggregateSales. Toto zobrazení obsahuje několik klíčových metrik pro analýzu prodeje společnosti.

1. Pokud budete chtít vytvořit mapu částek prodeje podle PSČ, klikněte v podokně polí napravo na zobrazení AggregateSales a tím ho rozbalte. Kliknutím vyberte sloupce PostalCode a SalesAmount.
   
    ![Power BI vybírá AggregateSales](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI automaticky rozpoznal geografická data a dal je do mapy za vás.
   
    ![Mapa Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Tento krok vytvoří pruhový graf, který zobrazí částku prodeje na příjem zákazníka. Chcete-li vytvořit pruhový graf, přejděte do rozšířeného zobrazení AggregateSales. Klikněte na pole SalesAmount. Přetáhněte pole Customer Income (Příjem zákazníka) nalevo do části Axis (Osa).
   
    ![Power BI vybírá osu](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Pruhový graf vlevo.
   
    ![Pruhový graf v Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Tento krok vytvoří spojnicový graf zobrazující prodejní částku k datu objednávky. Chcete-li vytvořit spojnicový graf, přejděte do rozšířeného zobrazení AggregateSales. Klikněte na SalesAmount a OrderDate. Ve sloupci Vizualizace klikněte na ikonu Spojnicový graf, což je první ikona na druhém řádku v rámci vizualizací.
   
    ![Power BI vybírá spojnicový graf](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Teď máte sestavu zobrazující tři různé vizualizace dat.
   
    ![Spojnicový graf v Power BI](./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Kdykoli můžete rozdělanou práci uložit tak, že kliknete na **Soubor** a vyberete **Uložit**.

## <a name="using-direct-connect"></a>Použití přímého připojení
Stejně jako u Azure SQL Database, SQL Data Warehouse Direct Connect umožňuje logické pushdown vedle analytických funkcí Power BI. S Direct Connect se dotazy podobu zkoumání dat posílají zpět do datového skladu Azure SQL v reálném čase.  Tato funkce v kombinaci s škálou datového skladu SQL umožňuje vytvářet dynamické sestavy během několika minut proti terabajtům dat. Zavedení tlačítka Otevřít v Power BI navíc uživatelům umožňuje přímo připojit Power BI k jejich datovému skladu SQL, aniž by shromažďovali informace z jiných částí Azure.

Při použití funkce Direct Connect:

* Při připojování zadejte úplný název serveru.
* Ujistěte se, že pravidla brány firewall pro databázi jsou nakonfigurovaná tak, aby umožňovala přístup ke službám Azure.
* Každá akce, jako je například výběr sloupce nebo přidání filtru, přímo dotazuje datový sklad.
* Dlaždice se aktualizují automaticky a přibližně každých 15 minut.
* Q&A není k dispozici pro datové sady Direct Connect.
* Změny schématu jsou začleněny automaticky.
* Všechny dotazy Direct Connect budou po 2 minutách vyporušovat.

Tato omezení a poznámky se mohou měnit s tím, jak se zlepšují zkušenosti.

## <a name="next-steps"></a>Další kroky
Nyní, když jsme vám poskytli nějaký čas na zahřátí se ukázkovými daty, podívejte se, jak [se vyvíjet](sql-data-warehouse-overview-develop.md) nebo [načítat](design-elt-data-loading.md). Nebo se podívejte na [web Power BI](https://www.powerbi.com/).
