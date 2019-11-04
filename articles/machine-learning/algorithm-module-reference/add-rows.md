---
title: 'Přidat řádky: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul přidat řádky v Azure Machine Learning k zřetězení dvou datových sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a16dc0bfc3fae8852c46435f6407c56149e1b14e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493951"
---
# <a name="add-rows-module"></a>Modul pro přidání řádků

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul slouží ke zřetězení dvou datových sad. Ve zřetězení jsou řádky druhé datové sady přidány na konec první datové sady.  
  
Zřetězení řádků je užitečné ve scénářích, jako jsou tyto:  
  
+ Vygenerovali jste řadu zkušebních statistik a chcete je zkombinovat do jedné tabulky pro snazší vytváření sestav.  
  
+ Pracovali jste s různými datovými sadami a chcete kombinovat datové sady, abyste vytvořili finální datovou sadu.  

## <a name="how-to-use-add-rows"></a>Použití funkce přidat řádky  

Aby bylo možné zřetězit řádky ze dvou datových sad, musí mít řádky přesně stejné schéma. To znamená, že stejný počet sloupců a stejný typ dat ve sloupcích.

1.  Přetáhněte modul **Přidat řádky** do kanálu, který můžete najít v části **transformace dat**v kategorii **manipulace** .

2. Připojte datové sady ke dvěma vstupním portům. Datová sada, kterou chcete připojit, by měla být připojena k druhému (pravému) portu. 
  
3.  Spuštění kanálu Počet řádků ve výstupní datové sadě by se měl rovnat součtu řádků obou vstupních datových sad.

    Pokud přidáte stejnou datovou sadu do obou vstupů modulu **Přidat řádky** , je datová sada duplikována. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 