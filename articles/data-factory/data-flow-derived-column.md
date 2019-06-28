---
title: Odvozené transformací sloupců v mapování toku dat – Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak transformovat data ve velkém měřítku v Azure Data Factory pomocí transformace mapování datového toku odvozené sloupce.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312202"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Odvozené transformací sloupců v mapování toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Pomocí transformace odvozených sloupců, vytvořte nové sloupce ve svém toku dat nebo upravovat existující pole.

## <a name="derived-column-settings"></a>Odvozené nastavení sloupců

Pokud chcete přepsat existující sloupec, vyberte ho pomocí rozevíracího seznamu sloupců. V opačném případě použijte pole výběru sloupce jako textové pole a zadejte název nového sloupce. Sestavit výraz odvozených sloupců, klikněte na pole 'Zadejte výraz' otevřou [Tvůrce výrazů tok dat](concepts-data-flow-expression-builder.md).

![Odvozené nastavení sloupců](media/data-flow/dc1.png "odvozené nastavení sloupců")

Pokud chcete přidat další odvozených sloupců, podržte ukazatel myši nad existující odvozené sloupce a klikněte na tlačítko "+". Zvolte 'Přidat sloupec' nebo 'Přidat sloupec vzorek'. Vzory sloupce může být užitečné, pokud vaše názvy sloupců jsou proměnné ze zdrojů. Další informace najdete v tématu [sloupec vzory](concepts-data-flow-column-pattern.md).

![Nový odvozených sloupců výběru](media/data-flow/columnpattern.png "nový odvozené výběr sloupce")

## <a name="next-steps"></a>Další postup

- Další informace o [mapování toku dat jazyk výrazů](data-flow-expression-functions.md).
