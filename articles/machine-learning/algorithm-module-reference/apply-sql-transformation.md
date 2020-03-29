---
title: Použití transformace SQL
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul Použít transformaci SQL v Azure Machine Learning ke spuštění dotazu SQLite na vstupních datových sadách k transformaci dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314534"
---
# <a name="apply-sql-transformation"></a>Použití transformace SQL

Tento článek popisuje modul Návrháře Azure Machine Learning (preview).

Pomocí modulu Použít transformaci SQL můžete:
  
-   Vytvořte tabulky pro výsledky a uložte datové sady do přenosné databáze.  
  
-   Proveďte vlastní transformace na datových typech nebo vytvořte agregace.  
  
-   Spusťte příkazy dotazu SQL k filtrování nebo změně dat a vrácení výsledků dotazu jako tabulky dat.  

> [!IMPORTANT]
> Sql engine používaný v tomto modulu je **SQLite**. Další informace o syntaxi SQLite naleznete [v tématu SQL as Understood by SQLite](https://www.sqlite.org/index.html) pro další informace.  

## <a name="how-to-configure-apply-sql-transformation"></a>Jak nakonfigurovat použít transformaci SQL  

Modul může trvat až tři datové sady jako vstupy. Když odkazujete na datové sady připojené ke každému `t1`vstupnímu portu, musíte použít názvy , `t2`a `t3`. Číslo tabulky označuje index vstupního portu.  
  
Zbývající parametr je dotaz SQL, který používá syntaxi SQLite. Při zadávání více řádků do textového pole **SKRIPT SQL** použijte středník k ukončení každého příkazu. V opačném případě budou konce řádků převedeny na mezery.  

Tento modul podporuje všechny standardní příkazy syntaxe SQLite. Seznam nepodporovaných výkazů naleznete v části [Technické poznámky.](#technical-notes)

##  <a name="technical-notes"></a>Technické poznámky  

Tato část obsahuje podrobnosti implementace, tipy a odpovědi na často kladené otázky.

-   Vstup je vždy vyžadován na portu 1.  
  
-   U identifikátorů sloupců, které obsahují mezeru nebo jiné speciální znaky, vždy uzavřete identifikátor sloupce `SELECT` do `WHERE` hranatých závorek nebo dvojitých uvozovek, když odkazujete na sloupec v klauzulích nebo.  
  
### <a name="unsupported-statements"></a>Nepodporované příkazy  

Přestože SQLite podporuje velkou část standardu ANSI SQL, neobsahuje mnoho funkcí podporovaných komerčními relačními databázovými systémy. Další informace naleznete [v tématu SQL as Understood by SQLite](http://www.sqlite.org/lang.html). Při vytváření příkazů SQL také uvědomte následující omezení:  
  
- SQLite používá dynamické psaní pro hodnoty, spíše než přiřazení typu ke sloupci jako ve většině relačních databázových systémů. Je slabě zadaný a umožňuje implicitní převod typu.  
  
- `LEFT OUTER JOIN`je implementována, ale není `RIGHT OUTER JOIN` nebo `FULL OUTER JOIN`.  

- Příkazy `RENAME TABLE` můžete `ADD COLUMN` použít `ALTER TABLE` a příkazy s příkazem, `DROP COLUMN`ale `ALTER COLUMN`jiné `ADD CONSTRAINT`klauzule nejsou podporovány, včetně , a .  
  
- Můžete vytvořit zobrazení v rámci SQLite, ale potom zobrazení jsou jen pro čtení. V zobrazení `DELETE`nelze `INSERT`spustit `UPDATE` příkaz , nebo příkaz. Můžete však vytvořit aktivační událost, která `DELETE` `INSERT`se `UPDATE` aktivuje při pokusu o , nebo v zobrazení a provést další operace v těle aktivační události.  
  

Kromě seznamu nepodporovaných funkcí poskytovaných na oficiálních stránkách SQLite, následující wiki poskytuje seznam dalších nepodporovaných funkcí: [SQLite - Nepodporované SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
