---
title: Grafy toku dat
description: Jak pracovat s grafy toku dat z továrny na data
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605362"
---
# <a name="mapping-data-flow-graphs"></a>Mapování grafů toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapovací data toky návrhový povrch je "konstrukce" povrch, kde se staví toky dat shora dolů, zleva doprava. Ke každé transformaci je připojena sada nástrojů se symbolem plus (+). Soustřeďte se na obchodní logiku namísto připojení uzlů přes hrany ve volném prostředí DAG.

Níže jsou vestavěné mechanismy pro správu grafu toku dat.

## <a name="move-nodes"></a>Přesun uzlů

![Agregační možnosti transformace](media/data-flow/agghead.png "záhlaví agregátoru")

Bez paradigma drag-and-drop je způsob, jak "přesunout" transformační uzel, změnit příchozí datový proud. Místo toho budete pohybovat transformace kolem změnou "příchozí datový proud".

## <a name="streams-of-data-inside-of-data-flow"></a>Datové proudy dat uvnitř toku dat

V Toku dat Azure Data Factory představují datové toky tok dat. V podokně nastavení transformace se zobrazí pole Příchozí datový proud. To vám řekne, který příchozí datový proud je krmení, že transformace. Fyzické umístění uzlu transformace v grafu můžete změnit kliknutím na název příchozího datového proudu a výběrem jiného datového proudu. Aktuální transformace spolu se všemi následnými transformacemi v tomto datovém proudu se pak přesune do nového umístění.

Pokud přesouváte transformaci s jednou nebo více transformacemi za ní, bude nové umístění v toku dat spojeno prostřednictvím nové větve.

Pokud nemáte žádné následné transformace po uzlu, který jste vybrali, pak pouze tato transformace se přesune do nového umístění.

## <a name="hide-graph-and-show-graph"></a>Skrytí grafu a zobrazení grafu

V pravém dolním konfiguračním podokně je tlačítko, kde můžete při práci na konfiguracích transformace rozbalit dolní podokno na celou obrazovku. To vám umožní používat "předchozí" a "další" tlačítka pro navigaci v konfiguraci grafu. Chcete-li se vrátit zpět do zobrazení grafu, klikněte na tlačítko dolů a vraťte se na rozdělenou obrazovku.

## <a name="search-graph"></a>Graf hledání

Graf můžete prohledávat pomocí vyhledávacího tlačítka na návrhové ploše.

![Hledat](media/data-flow/search001.png "Graf hledání")

## <a name="next-steps"></a>Další kroky

Po dokončení návrhu toku dat zapněte tlačítko ladění a otestujte ho v režimu ladění buď přímo v [návrháři toku dat](concepts-data-flow-debug-mode.md) nebo [ladění kanálu](control-flow-execute-data-flow-activity.md).
