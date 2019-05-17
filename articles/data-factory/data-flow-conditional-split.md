---
title: Azure Data Factory mapování transformace podmíněné rozdělení toku dat
description: Azure Data Factory Data tok podmíněného rozdělení transformace
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795636"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mapování toku dat podmíněné rozdělit transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Podmíněné rozdělit nástrojů](media/data-flow/conditionalsplit2.png "podmíněné rozdělit sady nástrojů")

Transformace podmíněné rozdělení může směrovat řádky dat do různých datových proudů v závislosti na obsahu data. Implementace podmíněného rozdělení transformace je podobný struktuře případu rozhodnutí v programovacím jazyce. Transformace vyhodnotí výrazy a na základě výsledků, přesměruje data řádku, který má zadaný datový proud. Tato transformace také poskytuje výchozí výstup, takže pokud řádek odpovídá žádný výraz směřuje na výchozí výstup.

![Podmíněné rozdělení](media/data-flow/conditionalsplit1.png "možností rozdělení, podmíněné")

## <a name="multiple-paths"></a>Víc cest

Chcete-li přidat další podmínky, vyberte "Přidat Stream" v dolním podokně Konfigurace a klikněte do textového pole Tvůrce výrazů do sestavení výrazu.

![Podmíněné rozdělit více](media/data-flow/conditionalsplit3.png "podmíněné rozdělit více")

## <a name="next-steps"></a>Další postup

Použít s podmíněným rozdělení běžné transformace s toku dat: [Připojte se k transformaci](data-flow-join.md), [Loopup transformace](data-flow-lookup.md), [vyberte transformace](data-flow-select.md)
