---
title: Přesouvání souborů mezi úložištěm založeným na souborech
description: Zjistěte, jak pomocí šablony řešení přesouvat soubory mezi úložištěm založeným na souborech pomocí Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b36eb2615e98ee8ea7751c836fd43e81a5a0f4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414747"
---
# <a name="move-files-with-azure-data-factory"></a>Přesunutí souborů pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje šablonu řešení, kterou můžete použít k přesunutí souborů z jedné složky do druhé mezi obchody založenými na souborech. Jeden z běžných scénářů použití této šablony: Soubory jsou neustále vynechány do vstupní složky zdrojového úložiště. Vytvořením aktivační události plánu může kanál ADF pravidelně přesouvat tyto soubory ze zdroje do cílového úložiště.  Způsob, jakým kanál ADF dosahuje "přesunutí souborů", získává soubory ze složky pro přistávání, zkopíruje každý z nich do jiné složky v cílovém úložišti a poté je vymaže ze složky pro přistávání ve zdrojovém úložišti.

> [!NOTE]
> Uvědomte si, že tato šablona je určena k přesunutí souborů, nikoli k přesouvání složek.  Pokud chcete přesunout složku změnou datové sady tak, aby obsahovala pouze cestu ke složce, a potom pomocí aktivity kopírování a odstranění aktivity odkazna stejnou datovou sadu představující složku, musíte být velmi opatrní. Je to proto, že se musíte ujistit, že nebudou nové soubory přicházející do složky mezi operací kopírování a odstraněním. Pokud do složky přicházejí nové soubory v okamžiku, kdy vaše kopírovací činnost právě dokončila úlohu kopírování, ale aktivita Odstranit nebyla zahlédnuta, je možné, že aktivita Delete odstraní tento nový příchozí soubor, který ještě nebyl zkopírován do cíle odstraněním celé složky.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona získá soubory z úložiště založeného na zdrojovém souboru. Potom přesune každý z nich do cílového úložiště.

Šablona obsahuje pět aktivit:
- **GetMetadata** získá seznam objektů včetně souborů a podsložek ze složky ve zdrojovém úložišti. Nebude načíst objekty rekurzivně. 
- **Filtrujte** seznam objektů z **aktivity GetMetadata** a vyberte pouze soubory. 
- **ForEach** získá seznam souborů z **aktivity Filtr** a potom iterates přes seznam a předá každý soubor kopírovat aktivity a odstranit aktivitu.
- **Zkopírujte** kopie jednoho souboru ze zdroje do cílového úložiště.
- **Odstranit** odstraní stejný jeden soubor ze zdrojového úložiště.

Šablona definuje čtyři parametry:
- *SourceStore_Location* je cesta ke složce zdrojového úložiště, ze kterého chcete soubory přesouvat. 
- *SourceStore_Directory* je cesta podsložky zdrojového úložiště, ze kterého chcete soubory přesouvat.
- *DestinationStore_Location* je cesta ke složce cílového úložiště, do kterého chcete soubory přesunout. 
- *DestinationStore_Directory* je cesta podsložky cílového úložiště, do kterého chcete soubory přesunout.

## <a name="how-to-use-this-solution-template"></a>Použití této šablony řešení

1. Přejděte do šablony **Přesunout soubory.** Vyberte existující připojení nebo **vytvořte nové** připojení k úložišti zdrojových souborů, ze kterého chcete soubory přesunout. Uvědomte si, že **DataSource_Folder** a **DataSource_File** jsou odkazem na stejné připojení úložiště zdrojových souborů.

    ![Vytvoření nového připojení ke zdroji](media/solution-template-move-files/move-files1.png)

2. Vyberte existující připojení nebo **vytvořte nové** připojení k cílovému úložišti souborů, kam chcete soubory přesunout.

    ![Vytvoření nového připojení k cíli](media/solution-template-move-files/move-files2.png)

3. Vyberte Použít tuto kartu **šablony.**
    
4. Zobrazí se kanál, jako v následujícím příkladu:

    ![Zobrazit potrubí](media/solution-template-move-files/move-files4.png)

5. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.   Parametry jsou cesta ke složce, ze které chcete přesunout soubory, a cesta ke složce, kam chcete soubory přesunout. 

    ![Spuštění kanálu](media/solution-template-move-files/move-files5.png)

6. Zkontrolujte výsledek.

    ![Zkontrolujte výsledek](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Další kroky

- [Kopírování nových a změněných souborů podle LastModifiedDate pomocí Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)