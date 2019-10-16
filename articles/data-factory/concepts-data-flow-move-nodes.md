---
title: Azure Data Factory přesouvání uzlů toku dat
description: Postup přesunutí uzlů v diagramu Azure Data Factory mapování toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2031820843342fb7e6b115865297e08cbee28a0a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387338"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapování toku dat přesunutí uzlů



![Možnosti agregované transformace](media/data-flow/agghead.png "záhlaví Agregátoru")

Azure Data Factory plocha pro návrh toku dat je "stavební" plocha, kde můžete vytvářet toky dat shora dolů, zleva doprava. Pro každou transformaci se symbolem plus (+) je připojena sada nástrojů. Soustřeďte se na obchodní logiku místo propojení uzlů pomocí okrajů v DAG prostředí s bezplatným formulářem.

Takže bez možnosti přetahování přetahováním je možné změnit příchozí datový proud tak, aby "přesunul" transformační uzel. Místo toho převedete transformované transformace změnou "příchozího datového proudu".

## <a name="streams-of-data-inside-of-data-flow"></a>Datové proudy dat v toku dat

V Azure Data Factory tok dat prezentují toky dat. V podokně nastavení transformace se zobrazí pole "příchozí pára". Tím se dozvíte, který příchozí datový proud dodává tuto transformaci. Fyzické umístění svého transformačního uzlu můžete změnit v grafu kliknutím na název příchozího datového proudu a výběrem jiného datového proudu. Aktuální transformace spolu se všemi následnými transformacemi v tomto datovém proudu se pak přesune na nové místo.

Pokud přesouváte transformaci s jednou nebo více transformacemi po ní, bude nové umístění v toku dat připojeno prostřednictvím nové větve.

Pokud po zvoleném uzlu nemáte žádné následné transformace, pak se pouze tato transformace přesune do nového umístění.

## <a name="next-steps"></a>Další kroky

Po dokončení návrhu toku dat zapněte tlačítko ladění a otestujte ho v režimu ladění buď přímo v [Návrháři toku dat](concepts-data-flow-debug-mode.md) , nebo v [ladění kanálu](control-flow-execute-data-flow-activity.md).
