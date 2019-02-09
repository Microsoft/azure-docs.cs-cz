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
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967140"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Kopírování souborů z několika kontejnerů pomocí Azure Data Factory

Šablona řešení popsaných v tomto článku vám umožní kopírování souborů z více souborů, kontejnerů nebo intervalů mezi úložišti souborů. Například možná chcete migrovat vaše data lake z AWS S3 do Azure Data Lake Store. Nebo možná budete chtít replikovat vše z jednoho účtu Azure Blob Storage do jiného účtu úložiště objektů Blob v Azure. Tato šablona je určená pro tyto případy použití.

Pokud chcete zkopírovat soubory z jedním kontejnerem nebo kontejneru, je výhodnější používat **nástroj pro kopírování dat** vytvoříte kanál s jednou aktivitou kopírování. Tato šablona je vyšší, než budete potřebovat pro tento jednoduchý případ použití.

## <a name="about-this-solution-template"></a>O tato šablona řešení

Tato šablona vytvoří výčet kontejnerů z úložiště zdrojového úložiště a poté uložit kopie kontejnerů z úložiště zdroje do cílového úložiště. 

Šablona obsahuje tři činnosti:
-   A **GetMetadata** aktivity kontrolovat vašeho úložiště zdrojového úložiště a získat seznam kontejnerů.
-   A **ForEach** aktivita k získání seznamu kontejneru z **GetMetadata** aktivity a iterovat přes seznam a každý kontejner předat aktivitě kopírování.
-   A **kopírování** aktivity kopírování všechny kontejnery ze zdrojového úložiště úložiště do cílového úložiště.

Šablona definuje dva parametry:
-   Parametr *cestakezdrojovemusouboru* je cesta k vaší úložiště zdroje dat, kde můžete získat seznam kontejnerů nebo kontejnerů. Ve většině případů cesta je kořenový adresář, který obsahuje více složek kontejneru. Výchozí hodnota tohoto parametru je `/`.
-   Parametr *cestakcilovemusouboru* je cesta kde soubory budou zkopírovány do cílového úložiště. Výchozí hodnota tohoto parametru je `/`.

## <a name="how-to-use-this-solution-template"></a>Jak použít tuto šablonu řešení

1. Přejděte do šablony **kopírování několika kontejnerů souborů mezi úložišti souborů**a vytvořit **nové připojení** do vašeho úložiště zdrojového úložiště. Zdrojové úložiště úložiště je místo, kam chcete kopírovat soubory z několika kontejnerů nebo kontejnerů.

    ![Vytvoření nového připojení ke zdroji](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Vytvoření **nové připojení** s vaším úložištěm cílového úložiště.

    ![Vytvořit nové připojení k cíli](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Klikněte na tlačítko **pomocí této šablony**.

    ![Použít tuto šablonu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. K dispozici v panelu, kanál se zobrazí, jak je znázorněno v následujícím příkladu:

    ![Zobrazení kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Klikněte na tlačítko **ladění**, zadejte **parametry** a klikněte na tlačítko **Dokončit**.

    ![Spuštění kanálu](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Zkontrolujte výsledky.

    ![Zkontrolujte výsledky](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Další postup

- [Úvod do Azure Data Factory](introduction.md)
