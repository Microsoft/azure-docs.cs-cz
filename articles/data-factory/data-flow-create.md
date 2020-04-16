---
title: Vytvoření toku dat mapování
description: Jak vytvořit tok dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416511"
---
# <a name="create-azure-data-factory-data-flow"></a>Vytvoření toku dat Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mapování toků dat v adf poskytují způsob, jak transformovat data ve velkém měřítku bez nutnosti kódování. Můžete navrhnout úlohu transformace dat v návrháři toku dat vytvořením řady transformací. Začněte s libovolným počtem zdrojových transformací následovaných kroky transformace dat. Potom dokončete tok dat jímkou, abyste výsledky získali v cíli.

Začni tím, že nejdřív vytvoříte novou V2 Data Factory z webu Azure Portal. Po vytvoření nové továrny klikněte na dlaždici "Author & Monitor" a spusťte ui.

![Možnosti toku dat](media/data-flow/v2portal.png "vytváření toku dat")

Jakmile jste v uzdu factory dat, můžete použít ukázkové toky dat. Ukázky jsou k dispozici v Galerii šablon ADF. V podavaču ADF vytvořte "Pipeline from Template" a vyberte kategorii Tok dat z galerie šablon.

![Možnosti toku dat](media/data-flow/template.png "vytváření toku dat")

Budete vyzváni k zadání informací o účtu úložiště objektů blob Azure.

![Možnosti toku dat](media/data-flow/template2.png "tok dat vytvořit 2")

[Údaje použité pro tyto vzorky naleznete zde](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Stáhněte si ukázková data a uložte soubory do účtů úložiště objektů Blob Azure, abyste mohli spouštět ukázky.

## <a name="create-new-data-flow"></a>Vytvoření nového toku dat

K vytvoření datových toků použijte tlačítko Vytvořit prostředek "znaménko plus" v uzdu ADF.

![Možnosti toku dat](media/data-flow/newresource.png "Nový zdroj")

## <a name="next-steps"></a>Další kroky

Začněte vytvářet transformaci dat pomocí [transformace zdroje](data-flow-source.md).
