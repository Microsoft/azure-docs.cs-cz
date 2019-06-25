---
title: Azure Data Factory Data toku přesunout uzly
description: Jak přesouvat uzly v Azure Data Factory mapování Diagram toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 951a5d4fcbd561b085b0377bde48e820dc8972a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65519956"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapování datového toku přesunutí uzlu

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Možnosti transformace agregace](media/data-flow/agghead.png "agregátoru záhlaví")

Na návrhovou plochu toku dat objekt pro vytváření dat Azure je surface "vytváření", kde sestavení data toky shora dolů, zleva doprava. Je nástrojů připojené k každý transformace ve formě znaménka plus (+) symbol. Soustředit na obchodní logiku namísto spojující uzly prostřednictvím hrany v prostředí DAG volného tvaru.

Ano bez paradigma přetažení myší, způsob, jak "přesunout" uzlu transformace se změnit příchozího datového proudu. Místo toho bude pohybu transformace tak, že změníte "příchozího datového proudu".

## <a name="streams-of-data-inside-of-data-flow"></a>Datové proudy dat v toku dat

Ve službě Azure Data Factory Data Flow datové proudy představují datový tok. V podokně nastavení transformace uvidíte polem "Příchozí datový proud". To zjistíte, které příchozí datový proud je předáte transformace. Fyzické umístění uzlu transformace v grafu můžete změnit kliknutím na název příchozí Stream a výběrem jiného datového proudu. Aktuální transformace spolu s všechny následné transformace v tomto datovém proudu se přesuňte do nového umístění.

Pokud přesouváte transformace s využitím jedné nebo více transformací po ní, se na nové místo v toku dat připojené přes novou větev.

Pokud budete mít bez dalších transformací po uzlu, na který jste vybrali, se přesune pouze této transformace do nového umístění.

## <a name="next-steps"></a>Další postup

Po dokončení návrhu toku dat, zapněte tlačítko ladění a otestování out v režimu ladění buď přímo v [návrháře toku dat](concepts-data-flow-debug-mode.md) nebo [kanálu ladění](control-flow-execute-data-flow-activity.md).
