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
ms.openlocfilehash: f78d0b02c9790234a63ef64200dcab72bc64c033
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629421"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Kopírování více složek pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje šablonu řešení, kterou můžete použít k kopírování kontejnerů nebo složek mezi úložišti založené na souborech pomocí více aktivit kopírování, kde každá aktivita kopírování má zkopírovat jeden kontejner nebo složku. 

> [!NOTE]
> Pokud chcete kopírovat soubory z jednoho kontejneru, je efektivnější použít [nástroj kopírování dat](copy-data-tool.md) k vytvoření kanálu s jednou aktivitou kopírování. Šablona v tomto článku je více, než kolik jich v jednoduchém scénáři potřebujete.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona vypíše složky z dané nadřazené složky ve zdrojovém úložišti úložiště. Pak zkopíruje každou složku do cílového úložiště.

Šablona obsahuje tři aktivity:
- **GetMetadata** prověří úložiště zdrojového úložiště a získá seznam podsložek z dané nadřazené složky.
- **Foreach** získá seznam podsložek z aktivity **GetMetadata** a pak projde seznam a předá každou složku aktivitě kopírování.
- **Kopírovat** zkopíruje všechny složky ze zdrojového úložiště úložiště do cílového úložiště.

Šablona definuje následující parametry:
- *SourceFileFolder* je součástí cesty k nadřazené složce vašeho úložiště zdrojů dat: *SourceFileFolder/SourceFileDirectory* , kde můžete získat seznam podsložek. 
- *SourceFileDirectory* je součástí cesty k nadřazené složce vašeho úložiště zdrojů dat: *SourceFileFolder/SourceFileDirectory* , kde můžete získat seznam podsložek. 
- *DestinationFileFolder* je součástí cesty k nadřazené složce: *DestinationFileFolder/DestinationFileDirectory* , kde budou soubory zkopírovány do cílového úložiště. 
- *DestinationFileDirectory* je součástí cesty k nadřazené složce: *DestinationFileFolder/DestinationFileDirectory* , kde budou soubory zkopírovány do cílového úložiště. 

Pokud chcete kopírovat více kontejnerů v rámci kořenových složek mezi úložišti úložiště, můžete zadat všechny čtyři parametry jako */* . Provedete to tak, že provedete replikaci všeho mezi úložišti úložiště.

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. V šabloně **úložiště souborů vyberte kontejnery kopírovat více souborů** . Vytvořte **nové** připojení ke zdrojovému úložišti úložiště. Zdrojové úložiště úložiště, do kterého chcete kopírovat soubory z více kontejnerů.

    ![Vytvoří nové připojení ke zdroji.](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Vytvořte **nové** připojení k cílovému úložišti úložiště.

    ![Vytvoří nové připojení k cíli.](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Vyberte **Použít tuto šablonu** .

    ![Použít tuto šablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Kanál se zobrazí, jako v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Vyberte **ladit** , zadejte **parametry** a pak vyberte **Dokončit** .

    ![Spuštění kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Prohlédněte si výsledek.

    ![Kontrola výsledku](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Další kroky

- [Hromadné kopírování z databáze pomocí řídicí tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
