---
title: Skripty SQL v Azure synapse Studio (Preview)
description: Úvod ke skriptům SQL pro Azure synapse Studio (Preview)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431069"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Použití skriptu SQL ve službě Azure synapse Studio (Preview)

Azure synapse Studio (Preview) poskytuje webové rozhraní pro skripty SQL, které vám umožní vytvářet dotazy SQL. Můžete se připojit ke fondu SQL (Preview) nebo SQL na vyžádání (Preview). 

## <a name="begin-authoring-in-sql-script"></a>Začít vytvářet vytváření skriptů SQL 

Existuje několik způsobů, jak spustit prostředí pro vytváření ve skriptech SQL. Můžete vytvořit nový skript SQL pomocí jedné z následujících metod.

1. Vyberte ikonu "+" a zvolte skript SQL.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. V nabídce Akce v části vývoj skriptů SQL vyberte nový skript SQL z nabídky akce v části vývoj skriptů SQL. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

– nebo – 

3. V nabídce Akce v části vývoj skriptů SQL zvolte importovat a vyberte existující skript SQL z místního úložiště.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Vytvoření skriptu SQL

1. Zvolte název pro skript SQL tak, že vyberete tlačítko Vlastnosti a nahradíte výchozí název přiřazený ke skriptu SQL.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Z rozevírací nabídky připojit k vyberte konkrétní fond SQL nebo SQL na vyžádání. V případě potřeby vyberte databázi z části použít databázi.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Spusťte vytváření skriptů SQL pomocí funkce technologie IntelliSense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Spuštění skriptu SQL

Kliknutím na tlačítko Spustit spusťte skript SQL. Ve výchozím nastavení jsou výsledky zobrazeny v tabulce.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportovat výsledky

Výsledky můžete exportovat do svého místního úložiště v různých formátech (včetně CSV, Excelu, JSON, XML), a to tak, že vyberete Exportovat výsledky a zvolíte rozšíření.

Můžete také vizualizovat výsledky skriptu SQL v grafu výběrem tlačítka "graf". Vyberte typ grafu a sloupec kategorie. Graf můžete exportovat do obrázku výběrem možnosti Uložit jako obrázek. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Prozkoumejte data ze souboru Parquet.

Soubory Parquet můžete prozkoumat v účtu úložiště pomocí skriptu SQL pro náhled obsahu souboru. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabulky SQL, externí tabulky, zobrazení

Výběrem nabídky akce v části data můžete vybrat několik akcí, jako je například nový skript SQL, vybrat horní 1000 řádků, vytvořit, odstranit a vytvořit. Prozkoumejte dostupný gesto kliknutím pravým tlačítkem myši na uzly fondu SQL a na vyžádání SQL.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Další kroky

Další informace o vytváření skriptů SQL najdete v tématu [Azure synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).