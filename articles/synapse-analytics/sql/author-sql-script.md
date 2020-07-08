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
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921365"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Použití skriptu SQL ve službě Azure synapse Studio (Preview)

Azure synapse Studio (Preview) poskytuje webové rozhraní pro skripty SQL, které vám umožní vytvářet dotazy SQL. Můžete se připojit ke fondu SQL (Preview) nebo SQL na vyžádání (Preview). 

## <a name="begin-authoring-in-sql-script"></a>Začít vytvářet vytváření skriptů SQL 

Existuje několik způsobů, jak spustit prostředí pro vytváření ve skriptech SQL. Můžete vytvořit nový skript SQL pomocí jedné z následujících metod.

1. V nabídce vývoj vyberte ikonu **"+"** a zvolte **skript SQL**.

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. V nabídce **Akce** klikněte na příkaz **Nový skript SQL**.
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

Případně můžete postupovat následovně: 

3. V nabídce **Akce** v části vývoj skriptů SQL zvolte **importovat** a vyberte existující skript SQL z místního úložiště.
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Vytvoření skriptu SQL

1. Zvolte název pro skript SQL tak, že vyberete tlačítko **vlastnosti** a nahradíte výchozí název přiřazený ke skriptu SQL.
  
    ![newsqlscriptrename](media/author-sql-script/newsqlscriptrename.png)

2. Z rozevírací nabídky **připojit k** vyberte konkrétní fond SQL nebo možnost SQL na vyžádání. V případě potřeby vyberte databázi z **databáze použít**.
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. Spusťte vytváření skriptů SQL pomocí funkce technologie IntelliSense.

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Spuštění skriptu SQL

Kliknutím na tlačítko **Spustit** spusťte skript SQL. Ve výchozím nastavení jsou výsledky zobrazeny v tabulce.

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportovat výsledky

Výsledky můžete exportovat do svého místního úložiště v různých formátech (včetně CSV, Excelu, JSON, XML), a to tak, že vyberete Exportovat výsledky a zvolíte rozšíření.

Můžete také vizualizovat výsledky skriptu SQL v grafu, a to tak, že vyberete tlačítko **graf** . Vyberte sloupec typ grafu a **kategorie**. Graf můžete exportovat do obrázku výběrem možnosti **Uložit jako obrázek**. 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Prozkoumat data ze souboru Parquet

Soubory Parquet můžete prozkoumat v účtu úložiště pomocí skriptu SQL pro náhled obsahu souboru.

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Tabulky SQL, externí tabulky, zobrazení

Výběrem nabídky **Akce** v části data můžete vybrat několik akcí, jako například:

- Nový skript SQL
- Vybrat horní 1000 řádků
- VYTVOŘENY
- Vyřadit a vytvořit 
 
Prozkoumejte dostupný gesto kliknutím pravým tlačítkem myši na uzly fondu SQL a na vyžádání SQL.
 
![newscriptdatabase](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Další kroky

Další informace o vytváření skriptů SQL najdete v tématu [Azure synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
