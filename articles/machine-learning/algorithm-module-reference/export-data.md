---
title: 'Exportovat data: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Pomocí modulu export dat v Návrháři Azure Machine Learning můžete ukládat výsledky a mezilehlá data mimo Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: c6e3d56958168cd279c98a4ba4c021c2362c2694
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421307"
---
# <a name="export-data-module"></a>Exportovat datový modul

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete ukládat výsledky, mezilehlá data a pracovní data z vašich kanálů do cílů cloudového úložiště. 

Tento modul podporuje export dat do následujících cloudových datových služeb:

- Kontejner objektů blob Azure
- Sdílená složka Azure
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Databáze Azure SQL

Před exportem dat je třeba nejprve zaregistrovat úložiště dat v pracovním prostoru Azure Machine Learning. Další informace najdete v tématu [přístup k datům ve službě Azure Storage](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Jak nakonfigurovat exportovaná data

1. Přidejte modul **Export dat** do kanálu v návrháři. Tento modul můžete najít v kategorii **vstup a výstup** .

1. Připojte **Export dat** do modulu obsahujícího data, která chcete exportovat.

1. Vyberte **exportovat data** a otevřete podokno **vlastnosti** .

1. V případě **úložiště dat** vyberte v rozevíracím seznamu existující úložiště dat. Můžete také vytvořit nové úložiště dat. Podívejte se, jak navštívíte [přístup k datům ve službě Azure Storage](../how-to-access-data.md).

    > [!NOTE]
    > Export dat určitého datového typu do sloupce databáze SQL, který je zadaný jako jiný datový typ, se nepodporuje. Cílová tabulka nemusí nejprve existovat.

1. Zaškrtávací políčko **znovu vygenerovat výstup** určuje, zda se má spustit modul pro opětovné vygenerování výstupu za běhu. 

    Ve výchozím nastavení je Nevybraná, což znamená, že pokud byl modul spuštěn se stejnými parametry dřív, systém použije výstup z posledního spuštění k omezení doby běhu. 

    Pokud je vybraná, systém znovu spustí modul a obnoví výstup.

1. Definujte cestu v úložišti dat, kde jsou data. Cesta je relativní cesta. Prázdné cesty nebo cesty URL nejsou povoleny.


1. V poli **Formát souboru** vyberte formát, ve kterém mají být data uložena.
 
1. Odešlete kanál.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
