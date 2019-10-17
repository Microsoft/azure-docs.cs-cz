---
title: Azure Data Factory transformaci podmíněného rozdělení toku dat při mapování
description: Azure Data Factory transformace podmíněného rozdělení toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387891"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapování podmíněné transformace toku dat



![podmíněná rozdělená sada nástrojů](media/data-flow/conditionalsplit2.png "podmíněná rozdělená sada nástrojů")

Podmíněná transformace rozdělení může směrovat řádky dat do různých datových proudů v závislosti na obsahu dat. Implementace podmíněné transformace rozdělení je podobná struktuře PŘÍPADných rozhodnutí v programovacím jazyce. Transformace vyhodnocuje výrazy a na základě výsledků směruje datový řádek na zadaný datový proud. Tato transformace také poskytuje výchozí výstup, takže pokud řádek nevyhovuje žádnému výrazu, je přesměrován na výchozí výstup.

![podmíněné rozdělení](media/data-flow/conditionalsplit1.png "možnosti podmíněného rozdělení")

## <a name="multiple-paths"></a>Více cest

Pokud chcete přidat další podmínky, v dolním podokně Konfigurace vyberte Přidat Stream a kliknutím do textového pole Tvůrce výrazů sestavte svůj výraz.

![podmíněné rozdělení více](media/data-flow/conditionalsplit3.png "podmíněné rozdělení více")

## <a name="next-steps"></a>Další kroky

Běžné transformace toku dat používané s podmíněným rozdělením: [transformace spojení](data-flow-join.md), [transformace vyhledávání](data-flow-lookup.md), [Výběr transformace](data-flow-select.md)
