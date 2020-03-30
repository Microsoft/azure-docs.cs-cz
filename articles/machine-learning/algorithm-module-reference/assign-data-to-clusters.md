---
title: 'Přiřazení dat ke clusteru: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul Přiřadit data clusteru v Azure Machine Learning k vytvoření clusteru modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477710"
---
# <a name="module-assign-data-to-clusters"></a>Modul: Přiřazení dat k clusterům

Tento článek popisuje, jak používat modul *Přiřadit data clusterům* v návrháři Azure Machine Learning (preview). Modul generuje předpovědi prostřednictvím clustering modelu, který byl trénovaný s *K-prostředky clustering* algoritmus.

Modul Přiřadit data clusterům vrátí datovou sadu, která obsahuje pravděpodobná přiřazení pro každý nový datový bod. 

## <a name="how-to-use-assign-data-to-clusters"></a>Použití funkce Přiřadit data clusterům
  
1. V návrháři Azure Machine Learning vyhledejte dříve trénovaný model clusteringu. Model clusteringu můžete vytvořit a trénovat pomocí jedné z následujících metod:  
  
    - Nakonfigurujte algoritmus clusteringu K-znamená pomocí modulu [K-Means Clustering](k-means-clustering.md) a trénujte model pomocí datové sady a modulu Model clusteringu vlaků (tento článek).  
  
    - Můžete také přidat existující trénovaný model clusteringu ze **skupiny Uložené modely** ve vašem pracovním prostoru.

2. Připojte trénovaný model k levému vstupnímu portu **Přiřadit data clusterům**.  

3. Připojte novou datovou sadu jako vstup. 

   V této datové sadě jsou popisky volitelné. Clustering je obecně metoda učení bez dozoru. Neočekává se, že byste kategorie znali předem. Vstupní sloupce však musí být stejné jako sloupce, které byly použity při trénování modelu clusteringu, nebo dojde k chybě.

    > [!TIP]
    > Chcete-li snížit počet sloupců zapsaných do návrháře z předpovědí clusteru, použijte [select sloupce v datové sadě](select-columns-in-dataset.md)a vyberte podmnožinu sloupců. 
    
4. Zaškrtnutí políčka **Zkontrolovat připojení nebo Zrušit zaškrtnutí políčka Výsledek ponechte zaškrtnuté,** pokud chcete, aby výsledky obsahovaly úplnou vstupní datovou sadu, včetně sloupce, který zobrazuje výsledky (přiřazení clusteru).
  
    Pokud toto políčko zrušte, budou vráceny pouze výsledky. Tato možnost může být užitečná při vytváření předpovědi jako součást webové služby.
  
5.  Odešlete potrubí.  
  
### <a name="results"></a>Výsledky

+  Chcete-li zobrazit hodnoty v datové sadě, klepněte pravým tlačítkem myši na modul a vyberte příkaz **Visualize**. Nebo Vyberte modul a přepněte na kartu **Výstupy** v pravém panelu, klikněte na ikonu histogramu ve **výstupech portu** a vizualizovat výsledek.

