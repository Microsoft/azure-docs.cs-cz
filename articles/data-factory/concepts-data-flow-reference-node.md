---
title: Azure Data Factory mapování uzel odkazu toku dat
description: Tok dat data Factory se přidat uzel odkazu pro spojení, vyhledávání, sjednocení
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 2e318210d96822b13f65eadeef79798b1b4595d1
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325889"
---
# <a name="mapping-data-flow-reference-node"></a>Mapování uzel odkazu toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Referenční uzel](media/data-flow/referencenode.png "uzel odkazu")

Uzel odkazu je automaticky přidána do plátna označuje, že uzel, který je připojen k odkazuje na jiný uzel existující na plátně. Uzel odkazu jako ukazatel nebo odkaz na jiný transformace toku dat představte.

Příklad: Když se do programu nebo sjednocení více než jeden datový proud s daty, plátna toku dat může přidat uzel odkazu, který odráží název a nastavení jiné než primární příchozího datového proudu.

Referenční uzel nelze přesunout nebo odstranit. Však můžete kliknout na uzel, který má-li upravit výchozí nastavení transformace.

Uživatelské rozhraní pravidla, která řídí, když tok dat přidá uzel odkazu jsou založeny na dostupné místo a svislé mezery mezi řádky.
