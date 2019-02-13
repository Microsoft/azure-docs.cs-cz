---
title: Kontrolovat tok dat mapování objekt pro vytváření dat Azure a dat ve verzi Preview
description: Azure Data Factory mapování toku dat má podokno, které zobrazuje metadata toku dat (zkontrolujte, jestli se) a náhled dat v režimu ladění (dat ve verzi preview)
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: e7d03ecd8ea4aecf1e0cfdd02d3b9bc5300abe6b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213260"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Transformace toku dat mapování objekt pro vytváření dat Azure Zkontrolujte kartu

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Zkontrolujte podokno](media/data-flow/agg3.png "zkontrolujte podokno")

V podokně zkontrolovat poskytuje přehled o metadata, která jste transformace datového proudu. Budete moci zobrazit počty sloupců, změna sloupců, sloupce, které přidává, datové typy, pořadí sloupců a odkazy na sloupce. "Zkontrolujte" je jen pro čtení zobrazení metadata. Nemusíte mít povolené, pokud chcete zobrazit metadate v podokně zkontrolovat režimu ladění.

Když měníte tvar vašich dat pomocí transformace, zobrazí se metadata změní flow na stránkách v podokně zkontrolovat. Pokud váš zdrojový transformace není definované schéma, metadata nebudou viditelné v podokně zkontrolovat. Chybí metadata dochází ve scénářích odchylek schématu.

![Náhled dat](media/data-flow/datapreview.png "dat ve verzi Preview")

Náhled dat je podokno, které poskytuje zobrazení jen pro čtení dat, protože je prováděna transformace. Můžete zobrazit výstup výrazů pro ověření toku dat a transformace. Musíte mít rozsvícených zobrazíte náhled dat režimu ladění. Po kliknutí na sloupců v datové mřížce ve verzi preview, zobrazí se následující panel napravo. Rozbalení panelu se zobrazí profil statistické údaje o jednotlivých sloupců, které jste vybrali.

![Data grafu ve verzi Preview](media/data-flow/chart.png "Data grafu ve verzi Preview.")

