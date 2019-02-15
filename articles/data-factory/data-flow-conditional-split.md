---
title: Azure Data Factory mapování transformace podmíněné rozdělení toku dat
description: Azure Data Factory Data tok podmíněného rozdělení transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 9fe650465160ab837d8c8c191887d0f952976682
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271666"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Azure Data Factory mapování transformace podmíněné rozdělení toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace podmíněné rozdělení může směrovat řádky dat do různých datových proudů v závislosti na obsahu data. Implementace podmíněného rozdělení transformace je podobný struktuře případu rozhodnutí v programovacím jazyce. Transformace vyhodnotí výrazy a na základě výsledků, přesměruje data řádku, který má zadaný datový proud. Tato transformace také poskytuje výchozí výstup, takže pokud řádek odpovídá žádný výraz směřuje na výchozí výstup.

![Podmíněné rozdělení](media/data-flow/cd1.png "podmíněné rozdělení")

Chcete-li přidat další podmínky, vyberte "Přidat Stream" v dolním podokně Konfigurace a klikněte do textového pole Tvůrce výrazů do sestavení výrazu.
