---
title: Mapování transformace spojení toku dat
description: Mapování datového toku Azure Tok nové větve transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 3bdf443a052ba8884a361774154f4d68cc180dfb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417823"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Transformace sjednocení toku dat mapování Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Unie bude kombinovat více datových proudů do jednoho, s SQL Unie těchto datových proudů jako nový výstup z transformace Unie. Všechny schéma z každého vstupního datového proudu budou sloučeny uvnitř toku dat, aniž byste museli mít klíč spojení.

N-počet datových proudů v tabulce nastavení můžete kombinovat výběrem ikony "+" vedle každého nakonfigurovaného řádku, včetně zdrojových dat i datových proudů z existujících transformací v toku dat.

![Transformace Unie](media/data-flow/union.png "Sjednocení")

V takovém případě můžete kombinovat různorodá metadata z více zdrojů (v tomto příkladu tři různé zdrojové soubory) a zkombinovat je do jednoho datového proudu:

![Přehled transformace Unie](media/data-flow/union111.png "Unie 1")

Chcete-li toho dosáhnout, přidejte další řádky v nastavení unie zahrnutím všech zdrojů, které chcete přidat. Není nutné pro běžné vyhledávání nebo klíč spojení:

![Nastavení transformace unie](media/data-flow/unionsettings.png "Nastavení sjednocení")

Pokud nastavíte Select transformace po unii, budete moci přejmenovat překrývající se pole nebo pole, které nebyly pojmenovány ze zdrojů bez záhlaví. Kliknutím na "Zkontrolovat" zobrazíte metadata kombinovat s celkovými sloupci 132 v tomto příkladu ze tří různých zdrojů:

![Konečné znění transformace Unie](media/data-flow/union333.png "Unie 3")

## <a name="name-and-position"></a>Jméno a pozice

Zvolíte-li možnost "sjednocení podle názvu", každá hodnota sloupce klesne do odpovídajícího sloupce z každého zdroje s novým schématem zřetězených metadat.

Pokud zvolíte "sjednocení podle pozice", každá hodnota sloupce klesne do původní pozice z každého odpovídajícího zdroje, což povede k novému kombinovanému datovému proudu, kde jsou data z každého zdroje přidána do stejného datového proudu:

![Výstup Unie](media/data-flow/unionoutput.png "Výstup unie")

## <a name="next-steps"></a>Další kroky

Prozkoumejte podobné transformace včetně [Join](data-flow-join.md) a [Exists](data-flow-exists.md).
