---
title: Skripty SQL v Azure Synapse Studio (preview)
description: Úvod Azure Synapse Studio (preview) SQL skripty
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431069"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Použití skriptu SQL v Azure Synapse Studio (preview)

Azure Synapse Studio (preview) poskytuje webové rozhraní skriptu SQL pro vytváření dotazů SQL. Můžete se připojit k fondu SQL (náhled) nebo SQL na vyžádání (náhled). 

## <a name="begin-authoring-in-sql-script"></a>Zahájení vytváření ve skriptu SQL 

Existuje několik způsobů, jak spustit vývojové prostředí ve skriptu SQL. Nový skript SQL můžete vytvořit pomocí jedné z následujících metod.

1. Vyberte ikonu "+" a zvolte skript SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Z nabídky Akce v části Vývoj skriptů SQL zvolte "Nový skript SQL" z nabídky "Akce" v části Vývoj skriptů SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

– nebo – 

3. Zvolte "Importovat" z nabídky "Akce" v části Vývoj skriptů SQL a vyberte existující skript SQL z místního úložiště.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Vytvoření skriptu SQL

1. Zvolte název skriptu SQL tak, že vyberete tlačítko Vlastnosti a nahradíte výchozí název přiřazený skriptu SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Zvolte konkrétní fond SQL nebo SQL na vyžádání z rozevírací nabídky "Připojit k". Nebo v případě potřeby zvolte databázi z "Použít databázi".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Začněte vytvářet skript SQL pomocí funkce intellisense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Spuštění skriptu SQL

Vyberte tlačítko "Spustit" pro spuštění skriptu SQL. Výsledky jsou ve výchozím nastavení zobrazeny v tabulce.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Export výsledků

Výsledky můžete exportovat do místního úložiště v různých formátech (včetně CSV, Excel, JSON, XML) výběrem možnosti "Exportovat výsledky" a výběrem rozšíření.

Můžete také vizualizovat výsledky skriptu SQL v grafu výběrem tlačítka "Graf". Vyberte "Typ grafu" a "Sloupec kategorie". Graf můžete exportovat do obrázku tak, že vyberete "Uložit jako obrázek". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Prozkoumejte data ze souboru parket.

Parquet soubory v účtu úložiště můžete prozkoumat pomocí skriptu SQL a zobrazit náhled obsahu souboru. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabulky SQL, externí tabulky, zobrazení

Výběrem nabídky "Akce" v části data můžete vybrat několik akcí, jako jsou: "Nový skript SQL", "Vyberte TOP 1000 řádků", "CREATE", "DROP a CREATE". Prozkoumejte dostupné gesto kliknutím pravým tlačítkem myši na uzly fondu SQL a SQL na vyžádání.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Další kroky

Další informace o vytváření skriptu SQL naleznete v [tématu Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).