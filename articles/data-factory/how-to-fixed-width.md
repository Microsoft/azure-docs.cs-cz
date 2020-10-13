---
title: Zpracování textových souborů s pevnou délkou s použitím mapování toků dat v Azure Data Factory
description: Naučte se zpracovávat textové soubory s pevnou délkou v Azure Data Factory pomocí mapování toků dat.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 23b812da8c84ebf055ac4eabdc4649828c139a7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051011"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Zpracování textových souborů s pevnou délkou pomocí Data Factory mapování datových toků

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí mapování toků dat v Microsoft Azure Data Factory můžete transformovat data z textových souborů s pevnou šířkou. V následujícím úkolu definujeme datovou sadu pro textový soubor bez oddělovače a pak nastavíte dílčí řetězce rozdělení na základě pořadového čísla pozice.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte **+ Nový kanál** a vytvořte nový kanál.

2. Přidání aktivity toku dat, která bude použita pro zpracování souborů s pevnou šířkou:

    ![Kanál s pevnou šířkou](media/data-flow/fwpipe.png)

3. V aktivitě toku dat vyberte **Nový tok dat mapování**.

4. Přidat zdroj, odvozený sloupec, výběr a transformaci jímky:

    ![Tok dat pevné šířky](media/data-flow/fw2.png)

5. Nakonfigurujte transformaci zdrojového kódu tak, aby používala novou datovou sadu, která bude obsahovat textový typ s oddělovači.

6. Nenastaví žádné oddělovače sloupců ani záhlaví.

   Nyní nastavíme počáteční body a délky polí pro obsah tohoto souboru:

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

7. Na kartě **projekce** ve zdrojové transformaci byste měli vidět sloupec s názvem *Column_1*.

8. V odvozeném sloupci vytvořte nový sloupec.

9. Pro sloupce poskytneme jednoduché názvy, jako je *Sloupec1*.

10. V Tvůrci výrazů zadejte následující:

    ```substring(Column_1,1,4)```

    ![odvozený sloupec](media/data-flow/fwderivedcol1.png)

11. Opakujte krok 10 pro všechny sloupce, které potřebujete analyzovat.

12. Kliknutím na kartu **zkontrolovat** zobrazíte nové sloupce, které budou vygenerovány:

    ![prohlížen](media/data-flow/fwinspect.png)

13. Pomocí transformace vybrat Transform odstraňte všechny sloupce, které nepotřebujete pro transformaci:

    ![vybrat transformaci](media/data-flow/fwselect.png)

14. Pro výstup dat do složky použijte jímku:

    ![jímka pevné šířky](media/data-flow/fwsink.png)

    Výstup vypadá takto:

    ![výstup pevné šířky](media/data-flow/fxdoutput.png)

  Data s pevnou šířkou jsou nyní rozdělena se čtyřmi znaky a přiřazena ke sloupci Sloupec1, col2, Col3, Col4 a tak dále. Na základě předchozího příkladu jsou data rozdělena do čtyř sloupců.

## <a name="next-steps"></a>Další kroky

* Sestavte zbývající část logiky toku dat pomocí [transformací](concepts-data-flow-overview.md)mapování toků dat.
