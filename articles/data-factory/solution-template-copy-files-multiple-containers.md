---
title: Kopírování souborů z několika kontejnerů
description: Naučte se používat šablonu řešení ke kopírování souborů z více kontejnerů pomocí Azure Data Factory.
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
ms.openlocfilehash: fffe356225fc813b027bf1d6e29b3afe19b7fb01
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890789"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopírování souborů z více kontejnerů pomocí Azure Data Factory

Tento článek popisuje šablonu řešení, kterou můžete použít ke kopírování souborů z více kontejnerů mezi úložišti souborů. Můžete ho například použít k migraci Data Lake z AWS S3 na Azure Data Lake Store. Nebo můžete použít šablonu k replikaci všeho z jednoho účtu služby Azure Blob Storage do jiného.

> [!NOTE]
> Pokud chcete kopírovat soubory z jednoho kontejneru, je efektivnější použít [nástroj kopírování dat](copy-data-tool.md) k vytvoření kanálu s jednou aktivitou kopírování. Šablona v tomto článku je více, než kolik jich v jednoduchém scénáři potřebujete.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona vytvoří výčet kontejnerů z úložiště zdrojového úložiště. Pak tyto kontejnery zkopíruje do cílového úložiště.

Šablona obsahuje tři aktivity:
- **GetMetadata** prověří úložiště zdrojového úložiště a získá seznam kontejnerů.
- **Foreach** získá seznam kontejnerů z aktivity **GetMetadata** a pak projde seznam a předá do aktivity kopírování každý kontejner.
- **Kopírovat** zkopíruje každý kontejner ze zdrojového úložiště úložiště do cílového úložiště.

Šablona definuje dva parametry:
- *SourceFilePath* je cesta k úložišti zdrojů dat, kde můžete získat seznam kontejnerů. Ve většině případů je cesta kořenovým adresářem, který obsahuje více složek kontejnerů. Výchozí hodnota tohoto parametru je `/`.
- *DestinationFilePath* je cesta, do které budou soubory zkopírovány do cílového úložiště. Výchozí hodnota tohoto parametru je `/`.

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. V šabloně **úložiště souborů vyberte kontejnery kopírovat více souborů** . Vytvořte **nové** připojení ke zdrojovému úložišti úložiště. Zdrojové úložiště úložiště, do kterého chcete kopírovat soubory z více kontejnerů.

    ![Vytvoří nové připojení ke zdroji.](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Vytvořte **nové** připojení k cílovému úložišti úložiště.

    ![Vytvoří nové připojení k cíli.](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Kanál se zobrazí, jako v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Vyberte **ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Spuštění kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Zkontrolujte výsledek.

    ![Kontrola výsledku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Další kroky

- [Hromadné kopírování z databáze pomocí řídicí tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)