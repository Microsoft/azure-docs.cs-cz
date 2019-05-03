---
title: 'Použije transformace: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak použít modul použije transformace ve službě Azure Machine Learning k úpravě vstupní datové sady založené na dříve vypočítané transformace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028707"
---
# <a name="apply-transformation-module"></a>Použít modul transformace

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k úpravě vstupní datové sady založené na dříve vypočítané transformace.  
  
Například, pokud jste použili k normalizaci trénovacích dat pomocí skóre z **normalizovat Data** modulu, je vhodné použít hodnotu z skóre, která se spočítala k trénování během vyhodnocování fáze. Ve službě Azure Machine Learning, můžete uložit jako transformace a poté použijete metodu normalizace **použít transformace** vyrovnat skóre z vstupní data před vyhodnocování.
  
Azure Machine Learning poskytuje podporu pro vytváření a následným použitím různých druhů vlastní transformace. Například můžete chtít uložit a potom budou transformace:  
  
- Odeberte nebo nahraďte chybějící hodnoty pomocí **vyčištění chybějících dat**
- Normalizovat data pomocí **normalizovat Data**
  

## <a name="how-to-use-apply-transformation"></a>Jak používat použít transformace  
  
1. Přidat **použít transformace** modulu do experimentu. Můžete najít tento modul v rámci **Machine Learning**v **skóre** kategorie. 
  
2. Vyhledejte existující transformace použít jako vstup.  Dříve uložené transformace najdete v **transformuje** skupinu v levém navigačním podokně.  
  
   
  
3. Připojte datové sady, který chcete transformovat. Datová sada musí mít přesně stejné schéma (počet sloupců, názvy sloupců, datových typů) jako datovou sadu, pro který byl původně navržený transformace.  
  
4. Žádné další parametry musí být nastavena, protože všechna přizpůsobení se provádí při definování transformace.  
  
5. Použití transformace na novou datovou sadu, spusťte experiment.  

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 