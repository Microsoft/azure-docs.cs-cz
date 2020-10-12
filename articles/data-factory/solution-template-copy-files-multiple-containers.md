---
title: Kopírování souborů z několika kontejnerů
description: Naučte se používat šablonu řešení ke kopírování souborů z více kontejnerů pomocí Azure Data Factory.
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
ms.openlocfilehash: 73560c49e10ab96c934d4dd3cea9395093a26420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82629044"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopírování souborů z více kontejnerů pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje šablonu řešení, kterou můžete použít ke kopírování souborů z více kontejnerů mezi úložišti souborů. Můžete ho například použít k migraci Data Lake z AWS S3 na Azure Data Lake Store. Nebo můžete použít šablonu k replikaci všeho z jednoho účtu služby Azure Blob Storage do jiného.

> [!NOTE]
> Pokud chcete kopírovat soubory z jednoho kontejneru, je efektivnější použít [nástroj kopírování dat](copy-data-tool.md) k vytvoření kanálu s jednou aktivitou kopírování. Šablona v tomto článku je více, než kolik jich v jednoduchém scénáři potřebujete.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona vytvoří výčet kontejnerů z úložiště zdrojového úložiště. Pak tyto kontejnery zkopíruje do cílového úložiště.

Šablona obsahuje tři aktivity:
- **GetMetadata** prověří úložiště zdrojového úložiště a získá seznam kontejnerů.
- **Foreach** získá seznam kontejnerů z aktivity **GetMetadata** a pak projde seznam a předá do aktivity kopírování každý kontejner.
- **Kopírovat** zkopíruje každý kontejner ze zdrojového úložiště úložiště do cílového úložiště.

Šablona definuje následující parametry:
- *SourceFileFolder* je cesta ke složce úložiště zdrojů dat, kde můžete získat seznam kontejnerů. Cesta je kořenový adresář, který obsahuje více složek kontejnerů. Výchozí hodnota tohoto parametru je `sourcefolder` .
- *SourceFileDirectory* je cesta k podsložce v kořenovém adresáři úložiště zdrojů dat. Výchozí hodnota tohoto parametru je `subfolder` .
- *DestinationFileFolder* je cesta ke složce, do které se zkopírují soubory do cílového úložiště. Výchozí hodnota tohoto parametru je `destinationfolder` .
- *DestinationFileDirectory* je cesta k podsložce, do které se zkopírují soubory do cílového úložiště. Výchozí hodnota tohoto parametru je `subfolder` .

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

6. Prohlédněte si výsledek.

    ![Kontrola výsledku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Další kroky

- [Hromadné kopírování z databáze pomocí řídicí tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
