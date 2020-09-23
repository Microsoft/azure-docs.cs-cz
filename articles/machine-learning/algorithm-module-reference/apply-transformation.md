---
title: 'Použít transformaci: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul použít transformaci v Azure Machine Learning k úpravě vstupní datové sady na základě dříve vypočítané transformace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: 7573abbbee479bfb0d1710beba3b95d084a5e657
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898879"
---
# <a name="apply-transformation-module"></a>Použít transformační modul

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete upravit vstupní datovou sadu založenou na dříve vypočítané transformaci.

Pokud jste například použili z skóre k normalizování školicích dat pomocí modulu **normalizing data** Module, měli byste použít hodnotu z-skóre, která byla vypočítána pro školení v rámci fáze bodování. V Azure Machine Learning můžete metodu normalizace Uložit jako transformaci a pak pomocí **použít transformaci** použít pro vstupní data skóre před vyhodnocením.

## <a name="how-to-save-transformations"></a>Uložení transformací

Návrhář umožňuje uložit transformace dat jako datové **sady** , abyste je mohli použít v jiných kanálech.

1. Vyberte modul transformace dat, který byl úspěšně spuštěn.

1. Vyberte kartu **výstupy + protokoly** .

1. Najděte výstup transformace a vyberte **datovou sadu registru** , kterou chcete uložit jako modul v kategorii **datových sad** v paletě modulu.

## <a name="how-to-use-apply-transformation"></a>Použití funkce použít transformaci  
  
1. Přidejte do svého kanálu modul **použít transformaci** . Tento modul najdete v části **model bodování & vyhodnocení** v paletě modulu. 
  
1. V paletě modulu Najděte uloženou transformaci, kterou chcete použít v **datových sadách** .

1. Připojte výstup uložené transformace k levému vstupnímu portu modulu **použít transformaci** .

    Datová sada musí mít přesně stejné schéma (počet sloupců, názvy sloupců, datové typy) jako datovou sadu, pro kterou byla transformace poprvé navržena.  
  
1. Připojte výstup objektu požadovaného modulu ke správnému vstupnímu portu modulu **použít transformaci** .
  
1. Chcete-li použít transformaci na novou datovou sadu, spusťte kanál.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 