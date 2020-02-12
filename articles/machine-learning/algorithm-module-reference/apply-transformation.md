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
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137579"
---
# <a name="apply-transformation-module"></a>Použít modul transformace

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete upravit vstupní datovou sadu založenou na dříve vypočítané transformaci.  
  
Pokud jste například použili z skóre k normalizování školicích dat pomocí modulu **normalizing data** Module, měli byste použít hodnotu z-skóre, která byla vypočítána pro školení v rámci fáze bodování. V Azure Machine Learning můžete metodu normalizace Uložit jako transformaci a pak pomocí **použít transformaci** použít pro vstupní data skóre před vyhodnocením.
  
Azure Machine Learning poskytuje podporu pro vytváření a následné použití mnoha různých druhů vlastních transformací. Můžete například chtít uložit a pak znovu použít transformace na:  
  
- Odebrání nebo nahrazení chybějících hodnot pomocí **Vyčištění chybějících dat**
- Normalizovat data pomocí **normalizovat** data
  

## <a name="how-to-use-apply-transformation"></a>Použití funkce použít transformaci  
  
1. Přidejte do svého kanálu modul **použít transformaci** . Tento modul najdete v kategorii **vyhodnocení & modelu** hodnocení. 
  
2. Vyhledejte existující transformaci, která se použije jako vstup. Dříve uložené transformace lze nalézt ve skupině **Moje datové sady** **v části datové sady v** levém stromu modulu.  
  
   
  
3. Připojte datovou sadu, kterou chcete transformovat. Datová sada musí mít přesně stejné schéma (počet sloupců, názvy sloupců, datové typy) jako datovou sadu, pro kterou byla transformace poprvé navržena.  
  
4. Není nutné nastavovat žádné další parametry, protože při definování transformace je provedeno veškeré vlastní nastavení.  
  
5. Chcete-li použít transformaci na novou datovou sadu, spusťte kanál.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 