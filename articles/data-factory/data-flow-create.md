---
title: Vytvoření toku dat mapování
description: Vytvoření toku dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416511"
---
# <a name="create-azure-data-factory-data-flow"></a>Vytvoření toku dat Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mapování toků dat v ADF nabízí způsob, jak transformovat data ve velkém měřítku bez nutnosti jakéhokoli kódování. Můžete navrhnout úlohu transformace dat v Návrháři toku dat vytvořením řady transformací. Začněte s libovolným počtem zdrojových transformací následovaných kroky transformace dat. Potom dokončete tok dat pomocí jímky, aby se vaše výsledky nakládal do cíle.

Začněte tím, že nejprve vytvoříte nový Data Factory v2 z Azure Portal. Po vytvoření nové továrny klikněte na dlaždici "Author & monitor" a spusťte Data Factory uživatelské rozhraní.

![Možnosti toku dat](media/data-flow/v2portal.png "Vytvoření toku dat")

Když jste v uživatelském rozhraní Data Factory, můžete použít ukázkové toky dat. Ukázky jsou k dispozici v galerii šablon ADF. V podavači ADF vytvořte "kanál ze šablony" a vyberte kategorii toku dat z galerie šablon.

![Možnosti toku dat](media/data-flow/template.png "Vytvoření toku dat")

Zobrazí se výzva k zadání informací o účtu Azure Blob Storage.

![Možnosti toku dat](media/data-flow/template2.png "Vytvoření toku dat 2")

[Data použitá pro tyto ukázky najdete tady](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Stáhněte si ukázková data a uložte soubory do svých účtů služby Azure Blob Storage, abyste mohli spustit ukázky.

## <a name="create-new-data-flow"></a>Vytvořit nový tok dat

K vytvoření toků dat použijte tlačítko pro vytvoření prostředku plus v uživatelském rozhraní ADF.

![Možnosti toku dat](media/data-flow/newresource.png "Nový prostředek")

## <a name="next-steps"></a>Další kroky

Začněte sestavovat transformaci dat pomocí [zdrojové transformace](data-flow-source.md).
