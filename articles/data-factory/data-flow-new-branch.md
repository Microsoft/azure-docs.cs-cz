---
title: Transformace toku dat nové větve
description: Azure Data Factory transformace toku dat při mapování nové větve
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: b4617689fe1ab14856bde9a4e8134b12aa6d815b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930304"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory transformace toku dat při mapování nové větve

![Možnosti větve](media/data-flow/menu.png "nabídka")

Větvení převezme aktuální datový proud v toku dat a provede replikaci do jiného datového proudu. Pomocí nové větve můžete provádět více sad operací a transformací proti stejnému datovému proudu.

Příklad: datový tok obsahuje zdrojovou transformaci s vybranou sadou sloupců a převody datových typů. Poté umístěte odvozený sloupec hned za tento zdroj. V odvozeném sloupci vytvoříte nové pole, které kombinuje jméno a příjmení, aby bylo vytvořeno nové pole "celé jméno".

Můžete s tímto novým datovým proudem zacházet ze sady transformací a jímky na jednom řádku a pomocí nové větve vytvořit kopii tohoto datového proudu, kde můžete stejná data transformovat pomocí jiné sady transformací. Po transformaci, která zkopírovala data v samostatné větvi, můžete tato data následně zajímky do samostatného umístění.

> [!NOTE]
> "Nová větev" bude v nabídce + Transform zobrazovat jenom v případě, že se po aktuálním umístění, kde se pokoušíte o větev, vyskytne následná transformace. To znamená, že se na konci tady nezobrazuje možnost Nová větev, dokud nepřidáte další transformaci po výběru.

![Větev](media/data-flow/branch2.png "Větev 2")
