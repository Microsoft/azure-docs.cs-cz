---
title: 'Přiřazení dat ke clusteru: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul přiřazení dat ke clusteru v Azure Machine Learning k určení skóre modelu clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898854"
---
# <a name="module-assign-data-to-clusters"></a>Modul: přiřazení dat clusterům

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul *přiřadit data ke clusterům* . Modul generuje předpovědi pomocí modelu clusteringu, který byl vyškolený pomocí rozhraní *K – označuje algoritmus clusteringu* .

Modul přiřazení dat ke clusterům vrátí datovou sadu, která obsahuje pravděpodobné přiřazení pro každý nový datový bod. 

## <a name="how-to-use-assign-data-to-clusters"></a>Použití přiřazování dat do clusterů
  
1. V Návrháři Azure Machine Learning vyhledejte dříve proučený model clusteringu. Model clusteringu můžete vytvořit a naučit pomocí některé z následujících metod:  
  
    - Konfigurace K znamená algoritmus clusteringu pomocí modulu [k-označuje modul clusteringu](k-means-clustering.md) a pomocí modulu pro vytváření modelů a modelu clusteringu (v tomto článku) vytvořte výuku modelu pomocí datové sady.  
  
    - Můžete také přidat existující model proučeného clusteringu ze skupiny **uložených modelů** v pracovním prostoru.

2. Připojte školený model k levému vstupnímu portu **přiřazení dat ke clusterům**.  

3. Připojí novou datovou sadu jako vstup. 

   V této datové sadě jsou popisky volitelné. Obecně platí, že clustering je bezdohlednou výukovou metodou. Neočekáváte si, že tyto kategorie předem znáte. Vstupní sloupce však musí být stejné jako sloupce, které byly použity při výuce modelu clusteringu, nebo dojde k chybě.

    > [!TIP]
    > Chcete-li snížit počet sloupců, které jsou zapsány do návrháře z předpovědi clusteru, použijte [možnost vybrat sloupce v datové sadě](select-columns-in-dataset.md)a vyberte podmnožinu sloupců. 
    
4. Zaškrtněte políčko Pokud chcete, aby výsledky obsahovaly úplnou vstupní datovou sadu, včetně sloupce, který zobrazuje výsledky (přiřazení clusteru), ponechejte políčko **zaškrtnout možnost připojit nebo zrušit kontrolu pouze pro výsledek** .
  
    Pokud zrušíte zaškrtnutí tohoto políčka, vrátí se pouze výsledky. Tato možnost může být užitečná, když vytváříte předpovědi jako součást webové služby.
  
5.  Odešlete kanál.  
  
### <a name="results"></a>Výsledky

+  Chcete-li zobrazit hodnoty v datové sadě, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**. Nebo vyberte modul a přepněte na kartu **výstupy** na pravém panelu, klikněte na ikonu histogramu ve **výstupech portů** a vizualizujte tak výsledek.

