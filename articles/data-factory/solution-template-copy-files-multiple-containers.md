---
title: Kopírování souborů z několika kontejnerů pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak použít šablonu řešení pro kopírování souborů z několika kontejnerů pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635133"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopírování souborů z několika kontejnerů pomocí Azure Data Factory

Tento článek popisuje šablonu řešení, která slouží ke kopírování souborů z několika kontejnerů mezi úložišti souborů. Například můžete použít k migraci vaše data lake z AWS S3 do Azure Data Lake Store. Nebo můžete použít šablonu k replikaci vše z jednoho účtu úložiště objektů Blob v Azure do jiné.

> [!NOTE]
> Pokud chcete zkopírovat soubory z jednoho kontejneru, je výhodnější používat [nástroj pro kopírování dat](copy-data-tool.md) k vytvoření kanálu s aktivitou jedna kopie. Šablony v tomto článku je vyšší, než budete potřebovat pro tohoto jednoduchého scénáře.

## <a name="about-this-solution-template"></a>O tato šablona řešení

Tato šablona vytvoří výčet kontejnerů z úložiště zdrojového úložiště. Potom zkopíruje těchto kontejnerů do cílového úložiště.

Šablona obsahuje tři činnosti:
- **GetMetadata** vyhledá vašeho úložiště zdrojového úložiště a získá seznam kontejnerů.
- **ForEach** získá seznam kontejnerů z **GetMetadata** aktivita Iteruje přes seznam a předá každý kontejner aktivitě kopírování.
- **Kopírování** zkopíruje všechny kontejnery ze zdrojového úložiště úložiště do cílového úložiště.

Šablona definuje dva parametry:
- *Cestakezdrojovemusouboru* je cesta zdrojového úložiště dat, kde můžete získat seznam kontejnerů. Ve většině případů cesta je kořenový adresář, který obsahuje více složek kontejneru. Výchozí hodnota tohoto parametru je `/`.
- *Cestakcilovemusouboru* je cesta kde soubory budou zkopírovány do cílového úložiště. Výchozí hodnota tohoto parametru je `/`.

## <a name="how-to-use-this-solution-template"></a>Jak použít tuto šablonu řešení

1. Přejděte **kopírování několika kontejnerů souborů mezi úložišti souborů** šablony. Vytvoření **nový** připojení k úložišti zdrojového úložiště. Zdrojové úložiště úložiště je, ve které chcete kopírovat soubory z několika kontejnerů z.

    ![Vytvoření nového připojení ke zdroji](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Vytvoření **nový** připojení k úložišti cílového úložiště.

    ![Vytvořit nové připojení k cíli](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Vyberte **pomocí této šablony**.

    ![Pomocí této šablony](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Zobrazí se vám kanálu, jako v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Vyberte **ladění**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Spuštění kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Zkontrolujte výsledky.

    ![Zkontrolujte výsledky](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Další postup

- [Hromadné kopírování databází pomocí ovládacího prvku tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopírování souborů z několika kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)