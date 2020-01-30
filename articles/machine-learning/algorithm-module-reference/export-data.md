---
title: 'Exportovat data: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul exportovat data v Azure Machine Learning k uložení výsledků, mezilehlých dat a pracovních dat z vašich kanálů do cloudového úložiště do míst mimo Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: c83bfc4a3ec8d1163669f28dfde64c63e7c6ff89
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775074"
---
# <a name="export-data-module"></a>Exportovat datový modul

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete ukládat výsledky, mezilehlá data a pracovní data z vašich kanálů do cloudového úložiště do míst mimo Azure Machine Learning. 

Tento modul podporuje export dat do následujících cloudových datových služeb:

- Kontejner objektů blob Azure
- Sdílená složka Azure
- Azure Data Lake
- Azure Data Lake Gen2

Před exportem dat musíte nejdřív zaregistrovat úložiště dat do svého pracovního prostoru Azure Machine Learning. Další informace najdete v tématu [přístup k datům ve službě Azure Storage](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Jak nakonfigurovat exportovaná data

1. Přidejte modul **Export dat** do kanálu v návrháři. Tento modul můžete najít v kategorii **vstup a výstup** .

1. Připojte **Export dat** do modulu obsahujícího data, která chcete exportovat.

1. Vyberte **exportovat data** a otevřete podokno **vlastnosti** .

1. V případě **úložiště dat**vyberte v rozevíracím seznamu existující úložiště dat. Můžete také vytvořit nové úložiště dat. Podívejte se, jak navštívíte [přístup k datům ve službě Azure Storage](../how-to-access-data.md).

1. Definujte cestu v úložišti dat, do které se budou data zapisovat. 


1. V poli **Formát souboru**vyberte formát, ve kterém mají být data uložena.
 
1. Spuštění kanálu

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
