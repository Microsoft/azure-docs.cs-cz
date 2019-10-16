---
title: Azure Data Factory transformace toku dat při mapování nové větve
description: Azure Data Factory transformace toku dat při mapování nové větve
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387219"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory transformace toku dat při mapování nové větve



![Možnosti větve](media/data-flow/menu.png "nabídce")

Větvení převezme aktuální datový proud v toku dat a provede replikaci do jiného datového proudu. Pomocí nové větve můžete provádět více sad operací a transformací proti stejnému datovému proudu.

Příklad: datový tok obsahuje zdrojovou transformaci s vybranou sadou sloupců a převody datových typů. Poté umístěte odvozený sloupec hned za tento zdroj. V odvozeném sloupci vytvoříte nové pole, které kombinuje jméno a příjmení, aby bylo vytvořeno nové pole "celé jméno".

Můžete s tímto novým datovým proudem zacházet ze sady transformací a jímky na jednom řádku a pomocí nové větve vytvořit kopii tohoto datového proudu, kde můžete stejná data transformovat pomocí jiné sady transformací. Po transformaci, která zkopírovala data v samostatné větvi, můžete tato data následně zajímky do samostatného umístění.

> [!NOTE]
> "Nová větev" bude v nabídce + Transform zobrazovat jenom v případě, že se po aktuálním umístění, kde se pokoušíte o větev, vyskytne následná transformace. To znamená, že se na konci tady nezobrazuje možnost Nová větev, dokud nepřidáte další transformaci po výběru.

![Součástí](media/data-flow/branch2.png "Větev 2")
