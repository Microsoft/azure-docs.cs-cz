---
title: Azure Data Factory transformace toku dat při mapování nové větve
description: Azure Data Factory transformace toku dat při mapování nové větve
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35d5b2250cb5f2f5bd5b3a0073dc2e3c655ceccb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029913"
---
# <a name="mapping-data-flow-union-transformation"></a>Mapování sjednocení toku dat



Sjednocení sloučí několik datových proudů do jednoho s sjednocením SQL těchto datových proudů jako nový výstup z transformace Union. Všechna schémata z každého vstupního datového proudu budou kombinována v toku dat, aniž by bylo nutné mít klávesu JOIN.

N-Number datových proudů můžete v tabulce nastavení kombinovat tak, že vyberete ikonu "+" vedle každého konfigurovaného řádku, včetně zdrojových dat i datových proudů z existujících transformací v toku dat.

(media/data-flow/union.png "Sjednocení") ![transformace sjednocení]

V takovém případě můžete zkombinovat různá metadata z více zdrojů (v tomto příkladu tři různé zdrojové soubory) a kombinovat je do jednoho datového proudu:

Sjednocení ![transformace sjednocení – přehled](media/data-flow/union111.png "1")

Chcete-li toho dosáhnout, přidejte další řádky do nastavení sjednocení zahrnutím veškerého zdroje, který chcete přidat. Nemusíte mít společné vyhledávání ani klíč JOIN:

![Sjednocení nastavení transformace sjednocení](media/data-flow/unionsettings.png "")

Pokud po sjednocení nastavíte transformaci SELECT, budete moct překrývat překrývající se pole nebo pole, která se nejmenují ze zdrojů bez záhlaví. Kliknutím na zkontrolovat zobrazíte kombinaci metadat s 132 celkovými sloupci v tomto příkladu ze tří různých zdrojů:

![Transformace sjednocení – konečné](media/data-flow/union333.png "sjednocení 3")

## <a name="name-and-position"></a>Název a umístění

Pokud zvolíte "sjednotit podle názvu", každá hodnota sloupce se přetáhne do odpovídajícího sloupce z každého zdroje a vytvoří se nové zřetězené schéma metadat.

Pokud zvolíte "sjednotit podle pozice", každá hodnota sloupce se přetáhne do původní pozice z každého odpovídajícího zdroje, což vede k vytvoření nového kombinovaného datového proudu, kde jsou data z jednotlivých zdrojů přidána do stejného datového proudu:

Výstup sjednocení ![výstupního](media/data-flow/unionoutput.png "sjednocení")

## <a name="next-steps"></a>Další kroky

Prozkoumejte podobné transformace, včetně [spojení](data-flow-join.md) a [existence](data-flow-exists.md).
