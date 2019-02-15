---
title: Azure Data Factory Data toku přesunout uzly
description: Jak přesouvat uzly v Azure Data Factory Diagram toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: f600d6113fd52829b7ad22134f3fc208776eadf2
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267143"
---
# <a name="data-factory-data-flow-move-nodes"></a>Data Factory Data toku přesunout uzly

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Možnosti transformace agregace](media/data-flow/agghead.png "agregátoru záhlaví")

Na návrhovou plochu toku dat objekt pro vytváření dat Azure je surface "vytváření", kde sestavení data toky shora dolů, zleva doprava. Je nástrojů připojené k každý transformace ve formě znaménka plus (+) symbol. Soustředit na obchodní logiku namísto spojující uzly prostřednictvím hrany v prostředí DAG volného tvaru.

Ano bez paradigma přetažení myší, způsob, jak "přesunout" uzlu transformace se změnit příchozího datového proudu. Místo toho bude pohybu transformace tak, že změníte "příchozího datového proudu".

Ve službě Azure Data Factory Data Flow datové proudy představují datový tok. V podokně nastavení transformace uvidíte polem "Příchozí datový proud". To zjistíte, které příchozí datový proud je předáte transformace. Fyzické umístění uzlu transformace v grafu můžete změnit kliknutím na název příchozí Stream a výběrem jiného datového proudu. Aktuální transformace spolu s všechny následné transformace v tomto datovém proudu se přesuňte do nového umístění.

Pokud přesouváte transformace s využitím jedné nebo více transformací po ní, se na nové místo v toku dat připojené přes novou větev.

Pokud budete mít bez dalších transformací po uzlu, na který jste vybrali, se přesune pouze této transformace do nového umístění.
