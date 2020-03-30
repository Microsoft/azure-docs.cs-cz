---
title: 'Použít transformaci: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul Použít transformaci v Azure Machine Learning k úpravě vstupní datové sady založené na dříve vypočítané transformaci.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395265"
---
# <a name="apply-transformation-module"></a>Použít transformační modul

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k úpravě vstupní datové sady založené na dříve vypočítané transformaci.

Například pokud jste použili z-skóre normalizovat trénovací data pomocí **normalize data** modulu, budete chtít použít hodnotu z skóre, která byla vypočtena pro trénování během fáze bodování také. V Azure Machine Learning můžete uložit metodu normalizace jako transformaci a pak pomocí **použít transformaci** použít z-skóre na vstupní data před vyhodnocování.

## <a name="how-to-save-transformations"></a>Jak uložit transformace

Návrhář umožňuje ukládat transformace dat jako **datové sady,** takže je můžete použít v jiných kanálech.

1. Vyberte modul transformace dat, který byl úspěšně spuštěn.

1. Vyberte kartu **Výstupy + protokoly.**

1. Vyberte **ikonu Uložit,** chcete-li **uložit transformaci výsledků**.

## <a name="how-to-use-apply-transformation"></a>Použití použití použít transformaci  
  
1. Přidejte do kanálu modul **Použít transformaci.** Tento modul naleznete v části **Hodnocení & hodnocení modelu** v paletě modulů. 
  
1. Najděte uloženou transformaci, kterou chcete použít v části **Datové sady** > **Moje datové sady** v paletě modulů.

1. Připojte výstup uložené transformace k levému vstupnímu portu modulu **Použít transformaci.**

    Datová sada by měla mít přesně stejné schéma (počet sloupců, názvy sloupců, datové typy) jako datová sada, pro kterou byla transformace poprvé navržena.  
  
1. Připojte výstup datové sady požadovaného modulu k pravému vstupnímu portu modulu **Použít transformaci.**
  
1. Chcete-li použít transformaci na novou datovou sadu, spusťte kanál.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 