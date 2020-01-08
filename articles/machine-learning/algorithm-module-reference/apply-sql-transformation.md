---
title: Použití transformace SQL
titleSuffix: Azure Machine Learning
description: Naučte se používat modul použití transformace SQL v Azure Machine Learning ke spuštění dotazu SQLite na vstupních datových sadách pro transformaci dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456523"
---
# <a name="apply-sql-transformation"></a>Použití transformace SQL

Tento článek popisuje modul návrháře Azure Machine Learning (Preview).

Pomocí modulu použít transformaci SQL můžete:
  
-   Vytvořte tabulky pro výsledky a uložte datové sady do přenosné databáze.  
  
-   Proveďte vlastní transformace na datové typy nebo vytvořte agregace.  
  
-   Spusťte příkazy dotazu SQL pro filtrování nebo změnu dat a vrácení výsledků dotazu jako tabulky dat.  

> [!IMPORTANT]
> Modul SQL použitý v tomto modulu je **SQLite**. Další informace o syntaxi SQLite naleznete v tématu [SQL, jak je popsáno ve službě SQLite](https://www.sqlite.org/index.html) , kde najdete další informace.  

## <a name="how-to-configure-apply-sql-transformation"></a>Postup konfigurace použití transformace SQL  

Modul může jako vstupy trvat až tři datové sady. Když odkazujete na datové sady připojené ke každému vstupnímu portu, musíte použít názvy `t1`, `t2`a `t3`. Číslo tabulky označuje index vstupního portu.  
  
Zbývající parametr je dotaz SQL, který používá syntaxi SQLite. Při zadávání více řádků v textovém poli **skript SQL** ukončete jednotlivé příkazy středníkem. V opačném případě jsou zalomení řádků převedeny na mezery.  

Tento modul podporuje všechny standardní příkazy syntaxe SQLite. Seznam nepodporovaných příkazů najdete v části [technické poznámky](#technical-notes) .

##  <a name="technical-notes"></a>Technické poznámky  

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

-   Na portu 1 je vždy vyžadován vstup.  
  
-   Pro identifikátory sloupců, které obsahují mezeru nebo jiné speciální znaky, vždy při odkazování na sloupec v klauzulích `SELECT` nebo `WHERE` uzavřete identifikátor sloupce do hranatých závorek nebo do dvojitých uvozovek.  
  
### <a name="unsupported-statements"></a>Nepodporované příkazy  

I když SQLite podporuje většinu standardu ANSI SQL, nezahrnuje mnoho funkcí podporovaných komerčními relačními databázovými systémy. Další informace najdete v tématu [SQL, jak rozumí SQLite](http://www.sqlite.org/lang.html). Při vytváření příkazů SQL si taky Pamatujte na následující omezení:  
  
- SQLite používá dynamické zadání pro hodnoty namísto přiřazování typu do sloupce jako u většiny relačních databázových systémů. Je slabě typované a umožňuje implicitní převod typu.  
  
- `LEFT OUTER JOIN` je implementován, ale není `RIGHT OUTER JOIN` nebo `FULL OUTER JOIN`.  

- Příkazy `RENAME TABLE` a `ADD COLUMN` můžete použít spolu s příkazem `ALTER TABLE`, ale jiné klauzule nejsou podporovány, včetně `DROP COLUMN`, `ALTER COLUMN`a `ADD CONSTRAINT`.  
  
- Můžete vytvořit zobrazení v rámci SQLite, ale zobrazení jsou až jen pro čtení. Nelze provést příkaz `DELETE`, `INSERT`nebo `UPDATE` pro zobrazení. Můžete ale vytvořit Trigger, který se aktivuje při pokusu o `DELETE`, `INSERT`nebo `UPDATE` zobrazení a provádění dalších operací v těle triggeru.  
  

Kromě seznamu nepodporovaných funkcí poskytovaných na oficiálním webu SQLite je na následujícím wikiwebu uveden seznam dalších nepodporovaných funkcí: [SQLite – nepodporovaný SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
