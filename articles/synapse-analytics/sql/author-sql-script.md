---
title: Skripty SQL v synapse studiu
description: Seznámení se skripty synapse Studio SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 077815d117af0328a3466346727e135caaf8ff21
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457998"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Skripty synapse Studio SQL ve službě Azure synapse Analytics 

Synapse Studio poskytuje webové rozhraní pro skripty SQL, které vám umožní vytvářet dotazy SQL. 

## <a name="begin-authoring-in-sql-script"></a>Začít vytvářet vytváření skriptů SQL 

Existuje několik způsobů, jak spustit prostředí pro vytváření ve skriptech SQL. Můžete vytvořit nový skript SQL pomocí jedné z následujících metod.

1. V nabídce vývoj vyberte ikonu **"+"** a zvolte **skript SQL**.

2. V nabídce **Akce** klikněte na příkaz **Nový skript SQL**.

3. V nabídce **Akce** v části vývoj skriptů SQL vyberte **importovat** . Vyberte existující skript SQL z místního úložiště.
![nové akce skriptu SQL Script 3](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Vytvoření skriptu SQL

1. Zvolte název pro skript SQL tak, že vyberete tlačítko **vlastnosti** a nahradíte výchozí název přiřazený ke skriptu SQL. 
![Přejmenování nového skriptu SQL](media/author-sql-script/new-sql-script-rename.png)

2. Z rozevírací nabídky **připojit k** vyberte konkrétní vyhrazený fond SQL nebo fond SQL bez serveru. V případě potřeby vyberte databázi z **databáze použít**. 
![nový fond pro výběr SQL](media/author-sql-script/new-sql-choose-pool.png)

3. Spusťte vytváření skriptů SQL pomocí funkce technologie IntelliSense.

## <a name="run-your-sql-script"></a>Spuštění skriptu SQL

Kliknutím na tlačítko **Spustit** spusťte skript SQL. Ve výchozím nastavení jsou výsledky zobrazeny v tabulce.

![Tabulka výsledků nového skriptu SQL](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Exportovat výsledky

Výsledky můžete exportovat do svého místního úložiště v různých formátech (včetně CSV, Excelu, JSON, XML), a to tak, že vyberete Exportovat výsledky a zvolíte rozšíření.

Můžete také vizualizovat výsledky skriptu SQL v grafu, a to tak, že vyberete tlačítko **graf** . Vyberte sloupec typ grafu a **kategorie**. Graf můžete exportovat do obrázku výběrem možnosti **Uložit jako obrázek**. 

![graf výsledků nového skriptu SQL](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Prozkoumat data ze souboru Parquet

Soubory Parquet můžete prozkoumat v účtu úložiště pomocí skriptu SQL pro náhled obsahu souboru.

![nový skript sqlod Parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabulky SQL, externí tabulky, zobrazení

Výběrem nabídky **Akce** v části data můžete vybrat několik akcí, jako například:

- Nový skript SQL
- Vybrat horní 1000 řádků
- CREATE
- Vyřadit a vytvořit 
 
Prozkoumejte dostupný gesto kliknutím pravým tlačítkem myši na uzly databází SQL.
 
![Nová databáze skriptů](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Vytváření složek a přesun skriptů SQL do složky

V nabídce Akce v části vývoj skriptů SQL vyberte v nabídce Akce v části vývoj skriptů SQL možnost Nová složka. A zadejte název nové složky v automaticky otevíraném okně. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-create-folder.png)

Chcete-li přesunout skript SQL do složky, můžete vybrat skript SQL a v nabídce Akce vybrat možnost přesunout do. Pak vyhledejte cílovou složku v novém okně a přesuňte skript SQL do vybrané složky. Můžete také rychle přetáhnout skript SQL a umístit ho do složky.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Další kroky

Další informace o vytváření skriptů SQL najdete v tématu [Azure synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
