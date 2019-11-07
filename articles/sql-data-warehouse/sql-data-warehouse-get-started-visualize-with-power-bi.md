---
title: Vizualizace dat pomocí Power BI Microsoft Azure
description: Vizualizace dat SQL Data Warehouse pomocí Power BI
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: eea4e3b0b1f0e4ec3eaf3e0aba8952f6693d2921
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685789"
---
# <a name="visualize-data-with-power-bi"></a>Vizualizace dat pomocí Power BI
V tomto kurzu si ukážeme, jak se pomocí Power BI připojit k SQL Data Warehouse a vytvořit pár základních vizualizací.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* SQL Data Warehouse s předem načtenou databází AdventureWorksDW. Pokud chcete zřídit datový sklad, přečtěte si téma [vytvoření SQL Data Warehouse](create-data-warehouse-portal.md) a výběr načtení ukázkových dat. Pokud již máte datový sklad, ale nemáte ukázková data, můžete [načíst WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. připojení k databázi
Pokud chcete otevřít Power BI a připojit se ke své databázi AdventureWorksDW, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na **Databáze SQL** a zvolte databázi AdventureWorks služby SQL Data Warehouse.
   
    ![Vyhledání databáze](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Klikněte na tlačítko Otevřít v Power BI.
   
    ![Tlačítko Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Teď by se vám měla zobrazit stránka pro připojení k SQL Data Warehouse s webovou adresou vaší databáze. Klikněte na Další.
   
    ![Připojení Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Zadejte své uživatelské jméno a heslo pro Azure SQL Server.
   
    ![Přihlášení Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Databázi otevřete kliknutím na datovou sadu AdventureWorksDW v levém okně.
   
    ![Otevření databáze AdventureWorksDW v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. vytvoření sestavy
Teď můžete pomocí Power BI analyzovat ukázková data databáze AdventureWorksDW. K provedení analýzy má databáze AdventureWorksDW zobrazení s názvem AggregateSales. Toto zobrazení obsahuje několik klíčových metrik pro analýzu prodeje společnosti.

1. Pokud budete chtít vytvořit mapu částek prodeje podle PSČ, klikněte v podokně polí napravo na zobrazení AggregateSales a tím ho rozbalte. Kliknutím vyberte sloupce PostalCode a SalesAmount.
   
    ![Power BI vybírá AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI automaticky rozpoznaná geografická data a umístí je do mapy.
   
    ![Mapa Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Tento krok vytvoří pruhový graf, který zobrazí částku prodeje na příjem zákazníka. Pruhový graf vytvoříte tak, že přejdete do rozbaleného zobrazení AggregateSales. Klikněte na pole SalesAmount. Přetáhněte pole Customer Income (Příjem zákazníka) nalevo do části Axis (Osa).
   
    ![Power BI vybere osu.](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Pruhový graf na levé straně
   
    ![Pruhový graf v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Tento krok vytvoří spojnicový graf zobrazující prodejní částku k datu objednávky. Spojnicový graf vytvoříte tak, že přejdete do rozbaleného zobrazení AggregateSales. Klikněte na SalesAmount a OrderDate. Ve sloupci vizualizace klikněte na ikonu spojnicového grafu, což je první ikona na druhém řádku v oblasti vizualizace.
   
    ![Power BI vybírá spojnicový graf](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Teď máte sestavu zobrazující tři různé vizualizace dat.
   
    ![Spojnicový graf v Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Kdykoli můžete rozdělanou práci uložit tak, že kliknete na **Soubor** a vyberete **Uložit**.

## <a name="using-direct-connect"></a>Použití přímého připojení
Stejně jako u Azure SQL Database SQL Data Warehouse přímé připojení umožňuje logický přenos podél možností analýzy Power BI. S přímým připojením se dotazy odesílají do vašich Azure SQL Data Warehouse v reálném čase, když prozkoumáte data.  Tato funkce v kombinaci s rozsahem SQL Data Warehouse umožňuje vytvářet dynamické sestavy v řádu minut proti terabajtům dat. Kromě toho, zavedení tlačítka otevřít v Power BI umožňuje uživatelům přímo připojit Power BI ke svým SQL Data Warehouse bez shromažďování informací z jiných částí Azure.

Při použití přímého připojení:

* Při připojování zadejte plně kvalifikovaný název serveru.
* Ujistěte se, že pravidla firewallu pro databázi jsou nakonfigurovaná tak, aby umožňovala přístup ke službám Azure.
* Každá akce, jako je například výběr sloupce nebo přidání filtru, se přímo dotazuje datového skladu.
* Dlaždice se aktualizují automaticky a přibližně každých 15 minut.
* Q & A není k dispozici pro přímé připojení datových sad.
* Změny schématu jsou začleněny automaticky.
* Při všech dotazech přímého připojení dojde po 2 minutách k vypršení časového limitu.

Tato omezení a poznámky se můžou při vylepšování prostředí změnit.

## <a name="next-steps"></a>Další kroky
Teď, když jsme vám poslali čas zahřívání s ukázkovými daty, najdete informace v tématu [vývoj](sql-data-warehouse-overview-develop.md) a [načítání](design-elt-data-loading.md). Nebo se podívejte na [web Power BI](https://www.powerbi.com/).
