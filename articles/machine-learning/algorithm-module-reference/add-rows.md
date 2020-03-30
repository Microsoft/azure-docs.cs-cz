---
title: 'Přidat řádky: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Přidat řádky v Azure Machine Learning zřetězit dvě datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477727"
---
# <a name="add-rows-module"></a>Přidat modul Řádky

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží ke zřetězení dvou datových sad. Při zřetězení jsou řádky druhé datové sady přidány na konec první datové sady.  
  
Zřetězení řádků je užitečné ve scénářích, jako jsou tyto:  
  
+ Vygenerovali jste řadu statistik hodnocení a chcete je zkombinovat do jedné tabulky pro snadnější vytváření sestav.  
  
+ Pracovali jste s různými datovými sadami a chcete kombinovat datové sady a vytvořit konečnou datovou sadu.  

## <a name="how-to-use-add-rows"></a>Jak používat přidat řádky  

Chcete-li zřetězit řádky ze dvou datových sad, musí mít řádky přesně stejné schéma. To znamená stejný počet sloupců a stejný typ dat ve sloupcích.

1.  Přetáhněte modul **Přidat řádky** do kanálu, najdete jej v části **Transformace dat**.

2. Připojte datové sady ke dvěma vstupním portům. Datová sada, kterou chcete připojit, by měla být připojena k druhému (pravému) portu. 
  
3.  Odešlete potrubí. Počet řádků ve výstupní datové sadě by se měl rovnat součtu řádků obou vstupních datových sad.

    Pokud přidáte stejnou datovou sadu do obou vstupů modulu **Přidat řádky,** bude datová sada duplikována. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 