---
title: Azure Data Factory transformaci podmíněného rozdělení toku dat při mapování
description: Azure Data Factory transformace podmíněného rozdělení toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fec2b09b9dc471135d9cdd00ac2465728a47bdbf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026932"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapování podmíněné transformace toku dat



podmíněný panel nástrojů(media/data-flow/conditionalsplit2.png "podmíněné") rozděleného ![panelu nástrojů]

Podmíněná transformace rozdělení může směrovat řádky dat do různých datových proudů v závislosti na obsahu dat. Implementace podmíněné transformace rozdělení je podobná struktuře PŘÍPADných rozhodnutí v programovacím jazyce. Transformace vyhodnocuje výrazy a na základě výsledků směruje datový řádek na zadaný datový proud. Tato transformace také poskytuje výchozí výstup, takže pokud řádek nevyhovuje žádnému výrazu, je přesměrován na výchozí výstup.

![podmíněné rozdělení](media/data-flow/conditionalsplit1.png "možností podmíněného rozdělení")

## <a name="multiple-paths"></a>Více cest

Pokud chcete přidat další podmínky, v dolním podokně Konfigurace vyberte Přidat Stream a kliknutím do textového pole Tvůrce výrazů sestavte svůj výraz.

![podmíněné rozdělení s více](media/data-flow/conditionalsplit3.png "podmíněným rozdělením")

## <a name="next-steps"></a>Další kroky

Běžné transformace toku dat používané s podmíněným rozdělením: [transformace spojení](data-flow-join.md), [transformace vyhledávání](data-flow-lookup.md), [Výběr transformace](data-flow-select.md)
