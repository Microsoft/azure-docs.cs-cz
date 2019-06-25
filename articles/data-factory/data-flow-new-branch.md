---
title: Azure Data Factory mapování dat Flow novou větev transformace
description: Azure Data Factory mapování dat Flow novou větev transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 9fac78f21f2f128ccb040e176891c33d39bf2820
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348930"
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
