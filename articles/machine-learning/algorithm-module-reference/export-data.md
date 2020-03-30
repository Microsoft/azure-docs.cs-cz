---
title: 'Exportovat data: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Exportovat data v Azure Machine Learning ukládat výsledky, zprostředkující data a pracovní data z vašich kanálů do cílů cloudového úložiště mimo Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456297"
---
# <a name="export-data-module"></a>Modul Exportovat data

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k ukládání výsledků, zprostředkujících dat a pracovních dat z vašich kanálů do cílů cloudového úložiště. 

Tento modul podporuje export dat do následujících cloudových datových služeb:

- Kontejner objektů blob Azure
- Sdílená složka Azure
- Azure Data Lake
- Azure Data Lake Gen2

Před exportem dat musíte nejprve zaregistrovat úložiště dat v pracovním prostoru Azure Machine Learning. Další informace najdete [v tématu Přístup k datům ve službách úložiště Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Konfigurace exportních dat

1. Přidejte modul **Export dat** do kanálu v návrháři. Tento modul naleznete v kategorii **Vstup a Výstup.**

1. Připojte **exportní data** k modulu, který obsahuje data, která chcete exportovat.

1. Vyberte **Exportovat data,** chcete-li otevřít podokno **Vlastnosti.**

1. V **části Úložiště dat**vyberte existující úložiště dat z rozevíracího seznamu. Můžete také vytvořit nové úložiště dat. Podívejte se, jak se můžete podívat na [data Accessu ve službách úložiště Azure](../how-to-access-data.md).

1. Zaškrtávací políčko **Regenerate output**rozhodne, zda má být modul spuštěn za běhu. 

    Ve výchozím nastavení není vybrán, což znamená, že pokud byl modul dříve proveden se stejnými parametry, systém znovu použije výstup z posledního spuštění, aby zkrátil dobu běhu. 

    Pokud je vybrána, systém modul znovu spustí, aby se znovu vygeneroval výstup.

1. Definujte cestu v úložišti dat, kde jsou data. Cesta je relativní cesta. Prázdné cesty nebo cesty URL nejsou povoleny.


1. Ve **formátu souboru**vyberte formát, ve kterém mají být data uložena.
 
1. Odešlete potrubí.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
