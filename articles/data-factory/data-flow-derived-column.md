---
title: Mapování toku dat pro vytváření dat Azure odvozené sloupce transformace
description: Jak transformovat data pomocí Azure Data Factory mapování toku odvozené sloupce transformace dat ve velkém měřítku
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917577"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Mapování toku dat odvozené sloupce transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Pomocí transformace odvozených sloupců, vytvořte nové sloupce ve svém toku dat nebo upravovat existující pole.

![odvození sloupce](media/data-flow/dc1.png "odvozené sloupce")

V jedné transformaci odvozených sloupců můžete provádět několik akcí odvozené sloupce. Klikněte na tlačítko "Přidat sloupec" k transformaci více než 1 sloupec v jedné transformace kroku.

V poli sloupce vyberte existující sloupec přepsat s novou hodnotou odvozené, nebo klikněte na "Vytvořit nový sloupec" generovat nový sloupec s hodnotou nově odvozené.

Textové pole výraz se otevře Tvůrce výrazů, kde můžete vytvářet ve výrazu odvozených sloupců pomocí výrazu funkce.

## <a name="column-patterns"></a>Vzory sloupců

Pokud vaše názvy sloupců jsou proměnné ze zdrojů, které staví na sestavení transformace ve sloupci odvozené pomocí vzorce sloupce namísto použití s názvem sloupce. Zobrazit [schématu odchylek](concepts-data-flow-schema-drift.md) , kde najdete další podrobnosti.

![sloupec vzorek](media/data-flow/columnpattern.png "vzory sloupec")

## <a name="next-steps"></a>Další postup

Další informace o [jazyk výrazů služby Data Factory pro transformace](https://aka.ms/dataflowexpressions) a [Tvůrce výrazů](concepts-data-flow-expression-builder.md)
