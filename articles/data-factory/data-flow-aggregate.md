---
title: Agregovat transformace v mapování toku dat – Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak agregovat data ve velkém měřítku v Azure Data Factory s agregační tok mapování dat transformace.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312122"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformace agregace v mapování toku dat 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace agregace je, kde definujete agregace sloupců v datové proudy. Pomocí Tvůrce výrazů, můžete definovat různé typy agregací, jako je SUM, MIN, MAX a počet, který lze seskupovat podle existující nebo počítaného sloupce.

## <a name="group-by"></a>Seskupit podle
Vyberte existující sloupec nebo vytvořte nový počítaný sloupec, který chcete použít jako skupinu by – klauzule pro vaše agregace. Pokud chcete použít existující sloupec, vyberte z rozevíracího seznamu na požadovaný sloupec. Chcete-li vytvořit nový počítaný sloupec, najeďte myší v klauzuli a klikněte na tlačítko 'Počítaný sloupec'. Tím se otevře [Tvůrce výrazů tok dat](concepts-data-flow-expression-builder.md). Po vytvoření počítaného sloupce zadejte název výstupního sloupce v rámci pole "Název jako". Pokud chcete přidat další skupiny by – klauzule, najeďte myší na existující klauzuli a klikněte na tlačítko "+".

![Transformace agregace Seskupit podle nastavení](media/data-flow/agg.png "agregační transformace Seskupit podle nastavení")

> [!NOTE]
> Group by – klauzule je volitelné v agregované transformace.

## <a name="aggregate-column"></a>Agregovaný sloupec 
Klepněte na kartu 'Agregace' pro sestavení výrazy agregace. Můžete buď zvolit existující sloupec a hodnotu přepsat agregace nebo vytvořit nové pole s novým názvem. Do pole vpravo vedle názvu selektor sloupců je zadán agregační výraz. Upravit výraz, klikněte v textovém poli otevřete Tvůrce výrazů. Přidejte další agregace, podržte ukazatel myši nad stávající výraz a klikněte na '+' Vytvoří nový sloupec, agregace nebo [sloupec vzorek](concepts-data-flow-column-pattern.md).

![Transformace agregace nastavení agregovat](media/data-flow/agg2.png "agregovat agregační nastavení transformace")

> [!NOTE]
> Výraz každé agregace musí obsahovat alespoň jeden agregační funkci.

> [!NOTE]
> V režimu ladění Tvůrce výrazů nejde produkovat data náhledy agregační funkce. Chcete-li zobrazit náhledy dat pro agregační transformace, zavřete Tvůrce výrazů a zobrazení dat na kartě "Náhled dat".

## <a name="next-steps"></a>Další postup

-Definovat pomocí agregace založených na oknech [okno transformace](data-flow-window.md)