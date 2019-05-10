---
title: Azure Data Factory Data toku přesunout uzly
description: Jak přesouvat uzly v Azure Data Factory Diagram toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: c41ab1c0c8a26488c476d187fbc1bcac2e624ac8
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472014"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapování datového toku přesunutí uzlu

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Možnosti transformace agregace](media/data-flow/agghead.png "agregátoru záhlaví")

Na návrhovou plochu toku dat objekt pro vytváření dat Azure je surface "vytváření", kde sestavení data toky shora dolů, zleva doprava. Je nástrojů připojené k každý transformace ve formě znaménka plus (+) symbol. Soustředit na obchodní logiku namísto spojující uzly prostřednictvím hrany v prostředí DAG volného tvaru.

Ano bez paradigma přetažení myší, způsob, jak "přesunout" uzlu transformace se změnit příchozího datového proudu. Místo toho bude pohybu transformace tak, že změníte "příchozího datového proudu".

Ve službě Azure Data Factory Data Flow datové proudy představují datový tok. V podokně nastavení transformace uvidíte polem "Příchozí Stream". To zjistíte, které příchozí datový proud je předáte transformace. Fyzické umístění uzlu transformace v grafu můžete změnit kliknutím na název příchozí Stream a výběrem jiného datového proudu. Aktuální transformace spolu s všechny následné transformace v tomto datovém proudu se přesuňte do nového umístění.

Pokud přesouváte transformace s využitím jedné nebo více transformací po ní, se na nové místo v toku dat připojené přes novou větev.

Pokud budete mít bez dalších transformací po uzlu, na který jste vybrali, se přesune pouze této transformace do nového umístění.
