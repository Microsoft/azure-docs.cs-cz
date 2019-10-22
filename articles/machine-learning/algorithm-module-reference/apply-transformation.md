---
title: 'Použít transformaci: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul použít transformaci ve službě Azure Machine Learning k úpravě vstupní datové sady na základě dříve vypočítané transformace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: adb2477fe2b7eea0dd0a98c7240843d90411d832
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693881"
---
# <a name="apply-transformation-module"></a>Použít transformační modul

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete upravit vstupní datovou sadu založenou na dříve vypočítané transformaci.  
  
Pokud jste například použili z skóre k normalizování školicích dat pomocí modulu **normalizing data** Module, měli byste použít hodnotu z-skóre, která byla vypočítána pro školení v rámci fáze bodování. V Azure Machine Learning můžete metodu normalizace Uložit jako transformaci a pak pomocí **použít transformaci** použít pro vstupní data skóre před vyhodnocením.
  
Azure Machine Learning poskytuje podporu pro vytváření a následné použití mnoha různých druhů vlastních transformací. Můžete například chtít uložit a pak znovu použít transformace na:  
  
- Odebrání nebo nahrazení chybějících hodnot pomocí **Vyčištění chybějících dat**
- Normalizovat data pomocí **normalizovat** data
  

## <a name="how-to-use-apply-transformation"></a>Použití funkce použít transformaci  
  
1. Přidejte do svého kanálu modul **použít transformaci** . Tento modul můžete najít v části **Machine Learning**v kategorii **skóre** . 
  
2. Vyhledejte existující transformaci, která se použije jako vstup.  Dříve uložené transformace lze nalézt ve skupině **transformes** v levém navigačním podokně.  
  
   
  
3. Připojte datovou sadu, kterou chcete transformovat. Datová sada musí mít přesně stejné schéma (počet sloupců, názvy sloupců, datové typy) jako datovou sadu, pro kterou byla transformace poprvé navržena.  
  
4. Není nutné nastavovat žádné další parametry, protože při definování transformace je provedeno veškeré vlastní nastavení.  
  
5. Chcete-li použít transformaci na novou datovou sadu, spusťte kanál.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 