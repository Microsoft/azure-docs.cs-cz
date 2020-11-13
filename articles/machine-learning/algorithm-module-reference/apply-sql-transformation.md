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
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555596"
---
# <a name="apply-sql-transformation"></a>Použití transformace SQL

Tento článek popisuje modul návrháře Azure Machine Learning.

Pomocí modulu použít transformaci SQL můžete:
  
-   Vytvořte tabulky pro výsledky a uložte datové sady do přenosné databáze.  
  
-   Proveďte vlastní transformace na datové typy nebo vytvořte agregace.  
  
-   Spusťte příkazy dotazu SQL pro filtrování nebo změnu dat a vrácení výsledků dotazu jako tabulky dat.  

> [!IMPORTANT]
> Modul SQL použitý v tomto modulu je **SQLite**. Další informace o syntaxi SQLite naleznete v tématu [SQL, jak rozumí SQLite](https://www.sqlite.org/index.html).
> Tento modul pozastaví data na SQLite, což je databáze paměti, takže spuštění modulu vyžaduje mnohem více paměti a může dojít k `Out of memory` chybě. Ujistěte se, že počítač má dostatek paměti RAM.

## <a name="how-to-configure-apply-sql-transformation"></a>Postup konfigurace použití transformace SQL  

Modul může jako vstupy trvat až tři datové sady. Když odkazujete na datové sady připojené ke každému vstupnímu portu, musíte použít názvy `t1` , `t2` , a `t3` . Číslo tabulky označuje index vstupního portu.  

Následuje ukázkový kód, který ukazuje, jak spojit dvě tabulky. T1 a T2 jsou dvě datové sady připojené k levému a střednímu vstupnímu portu **použití transformace SQL** :

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
Zbývající parametr je dotaz SQL, který používá syntaxi SQLite. Při zadávání více řádků v textovém poli **skript SQL** ukončete jednotlivé příkazy středníkem. V opačném případě jsou zalomení řádků převedeny na mezery.  

Tento modul podporuje všechny standardní příkazy syntaxe SQLite. Seznam nepodporovaných příkazů najdete v části [technické poznámky](#technical-notes) .

##  <a name="technical-notes"></a>Technické poznámky  

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

-   Na portu 1 je vždy vyžadován vstup.  
  
-   U identifikátorů sloupců, které obsahují mezeru nebo jiné speciální znaky, vždy při odkazování na sloupec v klauzulích or uzavřete identifikátor sloupce do hranatých závorek nebo do dvojitých uvozovek `SELECT` `WHERE` .  
  
### <a name="unsupported-statements"></a>Nepodporované příkazy  

I když SQLite podporuje většinu standardu ANSI SQL, nezahrnuje mnoho funkcí podporovaných komerčními relačními databázovými systémy. Další informace najdete v tématu [SQL, jak rozumí SQLite](http://www.sqlite.org/lang.html). Při vytváření příkazů SQL si taky Pamatujte na následující omezení:  
  
- SQLite používá dynamické zadání pro hodnoty namísto přiřazování typu do sloupce jako u většiny relačních databázových systémů. Je slabě typované a umožňuje implicitní převod typu.  
  
- `LEFT OUTER JOIN` je implementován, ale ne `RIGHT OUTER JOIN` nebo `FULL OUTER JOIN` .  

- `RENAME TABLE`Příkazy a se dají použít `ADD COLUMN` spolu s `ALTER TABLE` příkazem, ale jiné klauzule nejsou podporované, včetně `DROP COLUMN` , `ALTER COLUMN` a `ADD CONSTRAINT` .  
  
- Můžete vytvořit zobrazení v rámci SQLite, ale zobrazení jsou až jen pro čtení. Nelze provést `DELETE` `INSERT` příkaz, nebo `UPDATE` v zobrazení. Můžete ale vytvořit Trigger, který se aktivuje při pokusu o spuštění `DELETE` , `INSERT` nebo `UPDATE` v zobrazení a provádění dalších operací v těle triggeru.  
  

Kromě seznamu nepodporovaných funkcí poskytovaných na oficiálním webu SQLite je na následujícím wikiwebu uveden seznam dalších nepodporovaných funkcí: [SQLite – nepodporovaný SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
