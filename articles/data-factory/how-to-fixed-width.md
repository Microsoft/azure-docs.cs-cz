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
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210673"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Zpracování textových souborů s pevnou délkou pomocí Data Factory mapování datových toků

Data Factory mapování datových toků podporuje transformační data z textových souborů s pevnou šířkou. Definujete datovou sadu pro textový soubor bez oddělovače a pak nastavíte dílčí řetězce rozdělení na základě pořadového čísla.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Přejít na **+ Nový kanál** a spustit nový kanál

2. Přidání aktivity toku dat, která bude použita pro zpracování souborů s pevnou šířkou

  ![Kanál s pevnou šířkou](media/data-flow/fwpipe.png)

3. V aktivitě toku dat vyberte nový tok dat mapování.

4. Přidat transformaci zdroje, odvozený sloupec, výběr a transformaci jímky

  ![Tok dat pevné šířky](media/data-flow/fw2.png)

5. Nakonfigurujte transformaci zdroje tak, aby používala novou datovou sadu, která bude typu text s oddělovači.

6. Nastavit žádný oddělovač sloupců a žádná záhlaví

Jednoduše nastavíme počáteční body a délky polí pro tento obsah souboru:

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

7. Na kartě projekce zdrojové transformace by se měl zobrazit jeden řetězcový sloupec s názvem "Column_1".

8. Nyní v odvozeném sloupci vytvořte nový sloupec.

9. Pro sloupce poskytneme jednoduché názvy, jako je Sloupec1.

10. Pak v Tvůrci výrazů zadejte:

  ```substring(Column_1,1,4)```

  ![odvozený sloupec](media/data-flow/fwderivedcol1.png)

10. Tento postup opakujte pro všechny sloupce, které potřebujete k analýze.

12. Kliknutím na kartu zkontrolovat zobrazíte nové sloupce, které budou vygenerovány.

  ![prohlížen](media/data-flow/fwinspect.png)

13. Pomocí transformace vybrat Transform odstraňte všechny sloupce, které nebudete potřebovat pro transformaci.

  ![vybrat transformaci](media/data-flow/fwselect.png)

14. Nakonec použijte jímku pro výstup dat do složky:

  ![jímka pevné šířky](media/data-flow/fwsink.png)

  Výstup bude vypadat nějak takto:

  ![výstup pevné šířky](media/data-flow/fxdoutput.png)

  Data s pevnou šířkou se teď rozdělí se čtyřmi znaky a přiřazují se ke sloupci Sloupec1, col2, Col3, Col4,... Na základě výše uvedeného příkladu se data rozdělí do 4 sloupců.

## <a name="next-steps"></a>Další postup

* Sestavte zbytek logiky toku dat pomocí mapování [transformací](concepts-data-flow-overview.md) toku dat.
