---
title: 'Přiřaďte Data do clusteru: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití dat přiřazení do clusteru modulu ve službě Azure Machine Learning ke stanovení skóre pro model clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467202"
---
# <a name="module-assign-data-to-clusters"></a>Modul: Přiřaďte Data do clusterů

Tento článek popisuje způsob použití *přiřadit Data clustery* modulu v Azure Machine Learning vizuální rozhraní. Modul generuje predikcí přes model clusteringu, která byla natrénovaný pomocí *clustering K-means* algoritmus.

Přiřadit Data, která mají modul clustery vrací třídu dataset, který obsahuje o přiřazení pro každý nový datový bod. 


## <a name="how-to-use-assign-data-to-clusters"></a>Jak pracovat s daty přiřadit do clusterů
  
1. Ve vizuální rozhraní Azure Machine Learning vyhledejte dříve trénovaný model clusteringu. Můžete vytvořit a trénování modelu clusteringu pomocí některé z následujících metod:  
  
    - Konfigurace s použitím algoritmu clusteringu K-means [Clustering K-Means](k-means-clustering.md) modulu a trénování modelu s použitím datové sady a modul trénování modelu clusteringu (Tento článek).  
  
    - Můžete také přidat existující trénovaný model clusteringu z **uloží modely** skupiny ve vašem pracovním prostoru.

2. Připojení k levým vstupním portem z trénovaného modelu **přiřadit Data clustery**.  

3. Připojte novou datovou sadu jako vstup. 

   V této datové sadě popisky jsou volitelné. Obecně platí clustering je metoda bez dohledu učení. Neočekává předem vědět kategorií. Vstupní sloupce však musí být stejný jako sloupce, které byly používány v kurzy, které model clusteringu nebo chybu.

    > [!TIP]
    > Chcete-li snížit počet sloupců, které jsou zapsány do rozhraní z předpovědi clusteru, použijte [výběr sloupců v datové sadě](select-columns-in-dataset.md)a vybrat podmnožinu sloupců. 
    
4. Nechte **Kontrola připojení nebo zrušte zaškrtnutí pro výsledek pouze** zaškrtávací políčko, pokud chcete výsledky obsahující úplné vstupní datové sady, včetně sloupec, který zobrazuje výsledky (přiřazení clusteru).
  
    Pokud zrušíte zaškrtnutí tohoto políčka, se vrátí pouze výsledky. Tato možnost může být užitečné při vytváření předpovědí jako součást webové služby.
  
5.  Spusťte experiment.  
  
### <a name="results"></a>Výsledky

+  Chcete-li zobrazit hodnoty v datové sadě, klikněte pravým tlačítkem na modul, vyberte **způsobit datových sad**a pak vyberte **vizualizovat**.

