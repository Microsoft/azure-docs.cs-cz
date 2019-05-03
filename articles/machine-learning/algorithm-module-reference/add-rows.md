---
title: 'Přidání řádků: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu přidat řádky ve službě Azure Machine Learning zřetězit dvě datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028752"
---
# <a name="add-rows-module"></a>Přidat modul řádků

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k řetězení dvě datové sady. V zřetězení řádky druhé datové sady jsou přidány na konec první datové sady.  
  
Řetězení řádků je užitečné v situacích, jako je například tyto:  
  
+ Vygenerování řadu statistiky hodnocení a chcete je zkombinovat do jedné tabulky pro jednodušší vytváření sestav.  
  
+ Pracujete s různých datových sad a je potřeba sloučit datové sady, chcete-li vytvořit finální datové sady.  

## <a name="how-to-use-add-rows"></a>Jak používat přidat řádky  

Řádky zřetězit řádky z obou datových sad, musí mít přesně stejné schéma. To znamená, stejný počet sloupců a stejný typ dat ve sloupci.

1.  Přetáhněte **přidat řádky** modulu do svého experimentu najdete ho pod **transformace dat**v **manipulovat** kategorie.

2. Připojte se k dva vstupní porty datových sad. Datová sada, kterou chcete připojit, musí být připojené k druhé port (vpravo). 
  
3.  Spusťte experiment. Počet řádků ve vstupní sadě by se měl rovnat součet řádky z obou vstupní datové sady.

    Pokud přidáte stejné datové sady do obou vstupy **přidat řádky** modulu, datová sada je duplicitní. 

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 