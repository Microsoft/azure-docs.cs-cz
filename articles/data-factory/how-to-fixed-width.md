---
title: Zpracování textových souborů s pevnou délkou s mapováním toků dat v Azure Data Factory
description: Zjistěte, jak zpracovat textové soubory s pevnou délkou v Azure Data Factory pomocí toků mapovacích dat.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d629a9031f032a77efc953311a45b55996568191
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414374"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Zpracování textových souborů s pevnou délkou pomocí toků dat mapování datové továrny

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pomocí mapování datových toků v Microsoft Azure Data Factory, můžete transformovat data z textových souborů s pevnou šířkou. V následujícím úkolu definujeme datovou sadu pro textový soubor bez oddělovače a pak nastavíme rozdělení podřetězců na základě ordinální polohy.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Chcete-li vytvořit nový kanál, vyberte **možnost +Nový kanál.**

2. Přidejte aktivitu toku dat, která bude použita pro zpracování souborů s pevnou šířkou:

    ![Potrubí s pevnou šířkou](media/data-flow/fwpipe.png)

3. V aktivitě toku dat vyberte **Nový tok dat mapování**.

4. Přidejte transformaci Zdroje, Odvozeného sloupce, Výběr uhloubení a jímky:

    ![Datový tok s pevnou šířkou](media/data-flow/fw2.png)

5. Nakonfigurujte transformaci Source tak, aby používala novou datovou sadu, která bude typu Oddělovač textu.

6. Nenastavujte žádný oddělovač sloupců ani záhlaví.

   Nyní pro obsah tohoto souboru nastavíme počáteční body a délky polí:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Na kartě **Projekce** transformace Zdroj byste měli vidět sloupec řetězce s názvem *Column_1*.

8. Ve sloupci Odvozené vytvořte nový sloupec.

9. Dáme sloupce jednoduché názvy jako *col1*.

10. Do tvůrce výrazů zadejte následující příkaz:

    ```substring(Column_1,1,4)```

    ![odvozený sloupec](media/data-flow/fwderivedcol1.png)

11. Opakujte krok 10 pro všechny sloupce, které potřebujete analyzovat.

12. Výběrem karty **Zkontrolovat** zobrazíte nové sloupce, které budou generovány:

    ![Zkontrolovat](media/data-flow/fwinspect.png)

13. Pomocí transformace Select odeberte všechny sloupce, které pro transformaci nepotřebujete:

    ![vybrat transformaci](media/data-flow/fwselect.png)

14. Pomocí jímky navýstupu dat do složky:

    ![umyvadlo s pevnou šířkou](media/data-flow/fwsink.png)

    Zde je to, co výstup vypadá takto:

    ![výstup s pevnou šířkou](media/data-flow/fxdoutput.png)

  Data s pevnou šířkou jsou nyní rozdělena, každý se čtyřmi znaky a přiřazenými k col1, col2, col3, col4 a tak dále. Na základě předchozího příkladu jsou data rozdělena do čtyř sloupců.

## <a name="next-steps"></a>Další kroky

* Vytvořte zbytek logiky toku dat pomocí mapování [transformace](concepts-data-flow-overview.md)toků dat .
