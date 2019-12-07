---
title: Přesun souborů mezi úložištěm založeném na souborech
description: Naučte se používat šablonu řešení k přesouvání souborů mezi úložištěm založeném na souborech pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: e0f9179918016b9eaec04b0d559e25ed7973f18a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896203"
---
# <a name="move-files-with-azure-data-factory"></a>Přesunout soubory pomocí Azure Data Factory

Tento článek popisuje šablonu řešení, kterou můžete použít k přesunutí souborů z jedné složky do jiné mezi úložišti na základě souborů. Jeden ze běžných scénářů použití této šablony: soubory se v úložišti zdrojového úložiště trvale přecházejí do cílové složky. Když vytvoříte aktivační událost plánovače, kanál ADF může pravidelně přesouvat soubory ze zdroje do cílového úložiště.  Způsob, jakým kanál ADF dosáhne "přesunutí souborů", získává soubory z cílové složky, kopíruje každou z nich do jiné složky v cílovém úložišti a pak odstraní stejné soubory z cílové složky ve zdrojovém úložišti.

> [!NOTE]
> Uvědomte si, že tato šablona je navržená tak, aby místo přesouvání složek přesunula soubory.  Pokud chcete přesunout složku tím, že změníte datovou sadu tak, aby obsahovala pouze cestu ke složce, a potom pomocí aktivity kopírování a odstranění odkazuje na stejnou datovou sadu, která představuje složku, je nutné mít velmi opatrní. Je to proto, že je nutné zajistit, aby do složky mezi operacemi kopírování a odstraňování nedocházelo nové soubory. Pokud se ve složce v okamžiku, kdy vaše aktivita kopírování právě dokončila úloha kopírování, přirazily nové soubory, ale aktivita odstranění nebyla postará, je možné, že aktivita odstranění odstraní tento nový doručený soubor, který se nezkopíroval do destinati. Tím se ještě odstraní celá složka.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona získá soubory ze zdrojového úložiště založeného na souborech. Pak je přesune do cílového úložiště.

Šablona obsahuje pět aktivit:
- **GetMetadata** získá seznam objektů, včetně souborů a podsložek z vaší složky ve zdrojovém úložišti. Nevrátí objekty rekurzivně. 
- **Filtr** filtruje seznam objektů z aktivity **GetMetadata** a vybere pouze soubory. 
- **Foreach** získá seznam souborů z aktivity **filtru** a pak projde seznam a předá aktivity kopírování a odstranění každého souboru.
- **Kopírovat** zkopíruje jeden soubor ze zdroje do cílového úložiště.
- **Odstranění** odstraní stejný soubor ze zdrojového úložiště.

Šablona definuje dva parametry:
- *FolderPath_SourceStore* je cesta ke složce vašeho zdrojového úložiště, ze které chcete soubory přesunout. 
- *FolderPath_DestinationStore* je cesta ke složce cílového úložiště, do které chcete soubory přesunout. 

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. Přejděte do šablony **přesunout soubory** . Vyberte existující připojení nebo vytvořte **nové** připojení ke zdrojovému úložišti souborů, ze kterého chcete soubory přesunout. Upozorňujeme, že **DataSource_Folder** a **DataSource_File** odkazují na stejné připojení ke zdrojovému úložišti souborů.

    ![Vytvoří nové připojení ke zdroji.](media/solution-template-move-files/move-files1.png)

2. Vyberte existující připojení nebo vytvořte **nové** připojení k cílovému úložišti souborů, do kterého chcete soubory přesunout.

    ![Vytvoří nové připojení k cíli.](media/solution-template-move-files/move-files2.png)

3. Vyberte **Použít tuto šablonu**.

    ![Použít tuto šablonu](media/solution-template-move-files/move-files3.png)
    
4. Kanál se zobrazí, jako v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-move-files/move-files4.png)

5. Vyberte **ladit**, zadejte **parametry**a pak vyberte **Dokončit**.   Parametry jsou cesta ke složce, ze které chcete přesunout soubory, a cestu ke složce, do které chcete soubory přesunout. 

    ![Spuštění kanálu](media/solution-template-move-files/move-files5.png)

6. Zkontrolujte výsledek.

    ![Kontrola výsledku](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Další kroky

- [Kopírování nových a změněných souborů pomocí LastModifiedDate s Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)