---
title: "Používat Power BI s SQL Data Warehouse | Microsoft Docs"
description: "Tipy pro používání Power BI s datovým skladem SQL Azure pro vývoj řešení."
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: 4ea9a2ff0c95a73b348d3b48e9e62957d5cce31c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
---
# <a name="use-power-bi-with-sql-data-warehouse"></a>SQL Data Warehouse pomocí Power BI
Jako s Azure SQL Database, SQL Data Warehouse přímé připojení umožňuje uživatelům využívat výkonné logické přenos směrem dolů vedle analytické možnosti Power BI.  Přímé připojení dotazy jsou odeslán zpět do Azure SQL Data Warehouse v reálném čase jako zkoumat data.  To, v kombinaci s měřítkem služby SQL Data Warehouse, umožňuje uživatelům vytvářet dynamické sestavy v minutách proti terabajtů dat.  Kromě toho zavedení Open in tlačítko Power BI umožňuje uživatelům připojovat přímo Power BI do jejich SQL Data Warehouse bez shromažďovat informace z dalších částí Azure.

Při používání přímé připojení prosím Poznámka:

* Zadejte název plně kvalifikovaný serveru při připojování (podrobnosti viz níže)
* Ujistěte se, že jsou pravidla brány firewall pro databázi nakonfigurovaná na "Povolit přístup ke službám Azure".
* Všechny akce, jako výběrem sloupce nebo přidáním filtru se přímo dotazování datového skladu
* Dlaždice se aktualizuje přibližně každých 15 minut (aktualizace nemusí být naplánováno)
* Modul otázky A odpovědi není k dispozici pro přímé připojení datové sady
* Změny schématu nejsou automaticky převzata
* Všechny dotazy přímé připojení bude časový limit po dvou minutách

Těchto omezení a poznámky může změnit, protože zdokonalování zkušenosti. Kroky pro připojení jsou podrobně popsány níže.  

## <a name="using-the-open-in-power-bi-button"></a>Pomocí tlačítko Otevřít v Power BI
Nejjednodušší způsob, jak přesunout mezi SQL Data Warehouse a Power BI je s Open in tlačítko Power BI. Toto tlačítko umožňuje bezproblémově začněte vytvářet nové řídicí panely v Power BI.  

1. Abyste mohli začít přejděte k instanci SQL Data Warehouse na portálu Azure.
2. Klikněte na tlačítko Otevřít v Power BI.
3. Pokud nelze pro přihlášení přímo, nebo pokud nemáte účet Power BI, musíte se přihlásit.  
4. Budete přesměrováni na stránku pro připojení k SQL Data Warehouse, informace z SQL Data Warehouse předem vyplnit.
5. Po zadání přihlašovacích údajů budete plně připojení k SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>Připojení prostřednictvím portálu Power BI
Kromě používání Open in tlačítko Power BI, můžete také uživatelé připojit ke své SQL Data Warehouse prostřednictvím portálu Power BI.

1. Klikněte na tlačítko 'načíst Data, v dolní části navigačního podokna.
2. Vyberte "Databáze".
3. Jednou na stránce databáze vyberte 'Azure SQL Data Warehouse a klepněte na tlačítko "Připojit".
4. Zadejte připojovací údaje.  Název serveru a název databáze naleznete na webu Azure Portal.
5. Budete přesměrováni zpět na hlavní stránce Power BI a po připojení se nový záznam v části "datové sady, zobrazí se název vaší instance.  
6. Kliknutím na nová datová sada a prozkoumejte všechny tabulky a zobrazení v databázi. Výběrem sloupce odešle dotaz zpět do zdroje, dynamicky vytváření vizuál. Tyto vizuály lze uložit v nové sestavy a připnuté zpět na řídicí panel.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
