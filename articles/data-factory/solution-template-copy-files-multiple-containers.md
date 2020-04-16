---
title: Kopírování souborů z několika kontejnerů
description: Zjistěte, jak pomocí šablony řešení kopírovat soubory z více kontejnerů pomocí Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414824"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopírování souborů z více kontejnerů pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje šablonu řešení, kterou můžete použít ke kopírování souborů z více kontejnerů mezi úložišti souborů. Můžete ji například použít k migraci datového jezera z AWS S3 do Azure Data Lake Store. Nebo můžete použít šablonu k replikaci všeho od jednoho účtu úložiště objektů blob Azure do jiného.

> [!NOTE]
> Pokud chcete kopírovat soubory z jednoho kontejneru, je efektivnější použít [nástroj kopírovat data](copy-data-tool.md) k vytvoření kanálu s aktivitou jedné kopie. Šablona v tomto článku je více, než potřebujete pro tento jednoduchý scénář.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona vyjmenovává kontejnery z úložiště zdrojového úložiště. Potom zkopíruje tyto kontejnery do cílového úložiště.

Šablona obsahuje tři aktivity:
- **GetMetadata** prohledá úložiště zdrojového úložiště a získá seznam kontejnerů.
- **ForEach** získá seznam kontejnerů z **GetMetadata aktivity** a potom iterates přes seznam a předá každý kontejner copy aktivity.
- **Zkopírujte** kopie každého kontejneru ze zdrojového úložiště do cílového úložiště.

Šablona definuje následující parametry:
- *SourceFileFolder* je cesta ke složce úložiště zdrojů dat, kde můžete získat seznam kontejnerů. Cesta je kořenový adresář, který obsahuje více složek kontejneru. Výchozí hodnota tohoto parametru je `sourcefolder`.
- *SourceFileDirectory* je cesta podsložky pod kořenovým adresářem úložiště zdrojů dat. Výchozí hodnota tohoto parametru je `subfolder`.
- *DestinationFileFolder* je cesta ke složce, do které budou soubory zkopírovány v cílovém úložišti. Výchozí hodnota tohoto parametru je `destinationfolder`.
- *DestinationFileDirectory* je cesta podsložky, do které budou soubory zkopírovány v cílovém úložišti. Výchozí hodnota tohoto parametru je `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Použití této šablony řešení

1. Přejděte do **šablony Kopírovat více souborů mezi šablonou Úložiště souborů.** Vytvořte **nové** připojení k úložišti zdrojového úložiště. Úložiště zdrojového úložiště je místo, odkud chcete kopírovat soubory z více kontejnerů.

    ![Vytvoření nového připojení ke zdroji](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Vytvořte **nové** připojení k cílovému úložišti úložiště.

    ![Vytvoření nového připojení k cíli](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Zobrazí se kanál, jako v následujícím příkladu:

    ![Zobrazit potrubí](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Spuštění kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Zkontrolujte výsledek.

    ![Zkontrolujte výsledek](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Další kroky

- [Hromadná kopírování z databáze pomocí ovládací tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
