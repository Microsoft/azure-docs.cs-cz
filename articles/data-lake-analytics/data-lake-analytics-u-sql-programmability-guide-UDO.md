---
title: Příručka pro programovatelnost u-SQL UDO pro Azure Data Lake
description: Přečtěte si o Azure Data Lake Analytics programovatelnost U-SQL, které vám umožní vytvořit dobrý USQL skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512541"
---
# <a name="u-sql-user-defined-objects-overview"></a>Přehled uživatelem definovaných objektů U-SQL


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: uživatelsky definované objekty: UDO
U-SQL umožňuje definovat vlastní programovatelné objekty, které se nazývají uživatelsky definované objekty nebo UDO.

Níže je seznam UDO v U-SQL:

* Uživatelem definované extraktory
    * Extrahovat řádek po řádku
    * Slouží k implementaci extrakce dat z vlastních strukturovaných souborů.

* Uživatelem definované výstupy
    * Výstup řádku podle řádku
    * Používá se pro výstup vlastních datových typů nebo vlastních formátů souborů.

* Uživatelsky definované procesory
    * Vezměte jeden řádek a vytvořte jeden řádek.
    * Slouží ke snížení počtu sloupců nebo vytvoření nových sloupců s hodnotami odvozenými z existující sady sloupců.

* Uživatelsky definované appliers
    * Vezměte jeden řádek a vytvořte 0 až n řádků.
    * Používá se s VNĚJŠÍm/PŘÍČNým použitím

* Uživatelsky definované kombinace
    * Kombinuje sady řádků – uživatelsky definované spojení

* Uživatelsky definované reduktorů
    * Vezměte n řádků a vytvořte jeden řádek.
    * Slouží ke snížení počtu řádků.

UDO se obvykle explicitně volá ve skriptu U-SQL jako součást následujících příkazů U-SQL:

* EXTRACT
* VÝKONEM
* PŘIHLÁŠENÍ
* SPOJEN
* ÚROVNĚ

> [!NOTE]  
> UDO je omezené na využívání paměti 0,5 GB.  Toto omezení paměti se nevztahuje na místní spuštění.

## <a name="next-steps"></a>Další kroky
* [Průvodce programovatelností U-SQL – přehled](data-lake-analytics-u-sql-programmability-guide.md)
* [Průvodce programovatelností U-SQL – UDT a UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)