---
title: Uzel odkazu na datový tok Azure Data Factory mapování
description: Data Factory tok dat přidá referenční uzel pro spojení, vyhledávání, sjednocení
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030493"
---
# <a name="mapping-data-flow-reference-node"></a>Uzel odkazu na tok dat mapování



(media/data-flow/referencenode.png "Uzel odkazu") na ![uzel odkazu]

Do plátna se automaticky přidá uzel odkazu, který označuje, že uzel, který je připojen, odkazuje na jiný existující uzel na plátně. Uzel odkazu si můžete představit jako ukazatel nebo odkaz na jinou transformaci toku dat.

Například: Když spojíte nebo Sjednotete více než jeden datový proud dat, plátno toku dat může přidat referenční uzel, který odráží název a nastavení neprimárního příchozího datového proudu.

Uzel odkazu nejde přesunout ani odstranit. Můžete však kliknout na uzel a upravit tak původní nastavení transformace.

Pravidla uživatelského rozhraní, která určují, kdy tok dat přidává referenční uzel, vycházejí z dostupného místa a svislé mezery mezi řádky.
