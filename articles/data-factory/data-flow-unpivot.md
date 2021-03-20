---
title: Transformace Unpivot v toku dat mapování
description: Azure Data Factory transformace Unpivot toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040204"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Transformace Unpivot v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte Unpivot v toku dat mapování ADF jako způsob, jak změnit nenormalizovanou datovou sadu na podrobnější verzi tím, že rozbalíte hodnoty z více sloupců v jednom záznamu do několika záznamů se stejnými hodnotami v jednom sloupci.

![Snímek obrazovky zobrazuje Unpivot vybrané z nabídky.](media/data-flow/unpivot1.png "Unpivot možnosti 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Oddělit podle

![Snímek obrazovky s vybraným nastavením Unpivot zobrazí na kartě zrušit seskupení podle.](media/data-flow/unpivot5.png "Unpivot možnosti 2")

Nejprve nastavte sloupce, které chcete oddělit podle pro agregaci Unpivot. Nastavte jeden nebo více sloupců pro odseskupení pomocí znaku + vedle seznamu sloupců.

## <a name="unpivot-key"></a>Unpivot klíč

![Snímek obrazovky s vybraným klíčem Unpivot zobrazuje nastavení Unpivot.](media/data-flow/unpivot6.png "Unpivot možnosti 3")

Unpivot klíč je sloupec, který ADF bude překlopit ze sloupce na řádek. Ve výchozím nastavení se všechny jedinečné hodnoty v datové sadě pro toto pole budou překlopit na řádek. Můžete však volitelně zadat hodnoty z datové sady, které chcete překlopit na hodnoty řádků.

## <a name="unpivoted-columns"></a>Sloupce s nekontingenčními tabulkami

![Snímek obrazovky s vybraným nastavením na kartě náhled dat zobrazuje Unpivot.](media/data-flow//unpivot7.png "Unpivot možnosti 4")

Nakonec vyberte název sloupce pro uložení hodnot pro sloupce, které se transformují do řádků.

Volitelné Můžete vynechat řádky s hodnotami null.

SumCost je například název sloupce, který je vybrán v příkladu sdíleném výše.

![Obrázek ukazující sloupce No, dodavatel a ovocné sloupce před a po transformaci unipivot pomocí sloupce ovoce jako unipivot Key.](media/data-flow/unpivot3.png)

Když nastavíte uspořádání sloupců na "normální", budou se seskupovat všechny nové sloupce, které nejsou Překlopné z jedné hodnoty. Nastavení uspořádání sloupců na příčné bude seskupovat nové nekontingenční sloupce vygenerované z existujícího sloupce.

![Snímek obrazovky ukazuje výsledek transformace.](media/data-flow//unpivot7.png "Unpivot možnosti 5")

Poslední nepivotovaná sada výsledků dat zobrazuje celkový počet sloupců, které se teď nepivotují na samostatné hodnoty řádků.

## <a name="next-steps"></a>Další kroky

K pivotování řádků na sloupce použijte [transformaci pivotu](data-flow-pivot.md) .
