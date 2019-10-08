---
title: Vytvoření toku dat mapování Azure Data Factory
description: Vytvoření toku dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: d8ebc026add9dfd85d7cbe312a8cfffd3e365f82
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026875"
---
# <a name="create-azure-data-factory-data-flow"></a>Vytvoření toku dat Azure Data Factory



Mapování toků dat v ADF nabízí způsob, jak transformovat data ve velkém měřítku bez nutnosti jakéhokoli kódování. Můžete navrhnout úlohu transformace dat v Návrháři toku dat vytvořením řady transformací. Začněte s libovolným počtem zdrojových transformací následovaných kroky transformace dat. Potom dokončete tok dat pomocí jímky, aby se vaše výsledky nakládal do cíle.

Začněte tím, že nejprve vytvoříte nový Data Factory v2 z Azure Portal. Po vytvoření nové továrny klikněte na dlaždici "Author & monitor" a spusťte Data Factory uživatelské rozhraní.

(media/data-flow/v2portal.png "Vytvoření toku dat") ![možností toku dat]

Když jste v uživatelském rozhraní Data Factory, můžete použít ukázkové toky dat. Ukázky jsou k dispozici v galerii šablon ADF. V podavači ADF vytvořte "kanál ze šablony" a vyberte kategorii toku dat z galerie šablon.

(media/data-flow/template.png "Vytvoření toku dat") ![možností toku dat]

Zobrazí se výzva k zadání informací o účtu Azure Blob Storage.

(media/data-flow/template2.png "Vytvoření toku dat") ![možností toku dat]2

[Data použitá pro tyto ukázky najdete tady](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Stáhněte si ukázková data a uložte soubory do svých účtů služby Azure Blob Storage, abyste mohli spustit ukázky.

## <a name="create-new-data-flow"></a>Vytvořit nový tok dat

K vytvoření toků dat použijte tlačítko pro vytvoření prostředku plus v uživatelském rozhraní ADF.

![Možnosti toku dat]–(media/data-flow/newresource.png "nový prostředek")

## <a name="next-steps"></a>Další kroky

Začněte sestavovat transformaci dat pomocí [zdrojové transformace](data-flow-source.md).
