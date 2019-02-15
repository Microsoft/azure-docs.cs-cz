---
title: Azure Data Factory mapování dat Flow novou větev transformace
description: Azure Data Factory mapování dat Flow novou větev transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c48c1500a31ce8f22bcef7207c0ea67caca24231
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271745"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory mapování dat Flow novou větev transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Nepodmíněný skok možnosti](media/data-flow/menu.png "nabídky")

Větvení trvat aktuální datový proud ve svém toku dat a replikaci do jiného datového proudu. Nastaví použít novou větev provádět více operací a transformace na stejný datový proud.

Příklad: Váš tok dat má zdroj transformace s vybranou sadou sloupců a převody typů dat. Pak umístěte sloupec odvozené hned za tohoto zdroje. Ve sloupci odvozené jsme vytvořit nové pole, které kombinuje křestní jméno a příjmení, aby nové pole "jméno".

Můžete zpracovávat tohoto nového datového proudu sadu transformací a jímku na jeden řádek a můžete vytvořit kopii tohoto datového proudu, kde můžete transformovat ke stejným datům s jinou sadu transformací pro novou větev. Pomocí transformace této zkopírovaná data v samostatné větvi, můžete následně jímky tato data do samostatné umístění.

> [!NOTE]
> "Nové větve" se zobrazí pouze jako akci na + nabídky transformace při následné transformace podle aktuální umístění, kde se pokoušíte větve. To znamená nezobrazí možnost "Nová větev" na konci tady, dokud nepřidáte další transformaci po Select

![Větev](media/data-flow/branch2.png "větev 2")
