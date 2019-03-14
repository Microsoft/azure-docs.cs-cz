---
title: Azure Data Factory mapování dat Flow novou větev transformace
description: Azure Data Factory mapování dat Flow novou větev transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792612"
---
# <a name="mapping-data-flow-union-transformation"></a>Mapování typu union transformace toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Sjednocení bude kombinovat více datových proudů do jednoho se SQL sjednocení těchto datových proudů jako nový výstup z sjednocení transformace. Všechny schéma z každého vstupního datového proudu se budou kombinovat v rámci toku dat, aniž byste museli mít klíč spojení.

Můžete kombinovat n počet datových proudů v tabulce nastavení tak, že vyberete ikonu vedle každého nakonfigurované řádku, včetně zdroje dat i datové proudy z existující transformace ve svém toku dat "+".

![Sjednocení transformace](media/data-flow/union.png "Union")

V takovém případě můžete kombinovat různorodých metadat z více zdrojů (v tomto příkladu tři různé zdrojové soubory) a je zkombinovat do jediného datového proudu:

![Přehled sjednocení transformace](media/data-flow/union111.png "sjednocení 1")

K dosažení tohoto cíle, přidáte další řádky v Union nastavení včetně všechny zdroje, které chcete přidat. Není nutné pro běžné vyhledávání nebo spojení klíč:

![Nastavení typu UNION transformace](media/data-flow/unionsettings.png "sjednocení nastavení")

Pokud nastavíte vyberte transformace po vaší sjednocení, bude možné přejmenovat překrývající se pole nebo polí, která nejsou s názvem z headerless zdrojů. Klikněte na "Zkontrolujte, jestli se" Pokud chcete zobrazit metadata kombinovat s 132 celkový počet sloupců v tomto příkladu ze tří různých zdrojů:

![Konečné sjednocení transformace](media/data-flow/union333.png "sjednocení 3")

## <a name="name-and-position"></a>Název a umístění

Pokud si zvolíte "sjednocení podle názvu", každá hodnota sloupce vyřadí do odpovídajícího sloupce z každého zdroje se nové schéma zřetězených metadat.

Pokud se rozhodnete "sjednocení podle pozice", vyřadí se každá hodnota sloupce do původního umístění z každý odpovídající zdroj, což vede k nové kombinované datový proud kde data z každého zdroje je přidána do stejného datového proudu:

![Sjednocení výstup](media/data-flow/unionoutput.png "sjednocení výstupu")

## <a name="next-steps"></a>Další postup

Prozkoumejte podobné transformaci, včetně [připojení](data-flow-join.md) a [Exists](data-flow-exists.md).
