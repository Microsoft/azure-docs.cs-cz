---
title: 'Přiřazení dat ke clusteru: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul přiřazení dat ke clusteru ve službě Azure Machine Learning ke stanovení skóre modelu clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 3caf568590beb5b1f9d346d472c93f134a4f3a5f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693227"
---
# <a name="module-assign-data-to-clusters"></a>Modul: přiřazení dat clusterům

Tento článek popisuje, jak použít modul *přiřazení dat ke clusterům* v rozhraní Azure Machine Learningho vizuálu. Modul generuje předpovědi pomocí modelu clusteringu, který byl vyškolený pomocí rozhraní *K – označuje algoritmus clusteringu* .

Modul přiřazení dat ke clusterům vrátí datovou sadu, která obsahuje pravděpodobné přiřazení pro každý nový datový bod. 


## <a name="how-to-use-assign-data-to-clusters"></a>Použití přiřazování dat do clusterů
  
1. V Azure Machine Learningm vizuálním rozhraní vyhledejte dříve proučený model clusteringu. Model clusteringu můžete vytvořit a naučit pomocí některé z následujících metod:  
  
    - Konfigurace K znamená algoritmus clusteringu pomocí modulu [k-označuje modul clusteringu](k-means-clustering.md) a pomocí modulu pro vytváření modelů a modelu clusteringu (v tomto článku) vytvořte výuku modelu pomocí datové sady.  
  
    - Můžete také přidat existující model proučeného clusteringu ze skupiny **uložených modelů** v pracovním prostoru.

2. Připojte školený model k levému vstupnímu portu **přiřazení dat ke clusterům**.  

3. Připojí novou datovou sadu jako vstup. 

   V této datové sadě jsou popisky volitelné. Obecně platí, že clustering je bezdohlednou výukovou metodou. Neočekáváte si, že tyto kategorie předem znáte. Vstupní sloupce však musí být stejné jako sloupce, které byly použity při výuce modelu clusteringu, nebo dojde k chybě.

    > [!TIP]
    > Chcete-li snížit počet sloupců, které jsou zapsány do rozhraní z předpovědi clusteru, použijte [možnost vybrat sloupce v datové sadě](select-columns-in-dataset.md)a vyberte podmnožinu sloupců. 
    
4. Zaškrtněte políčko Pokud chcete, aby výsledky obsahovaly úplnou vstupní datovou sadu, včetně sloupce, který zobrazuje výsledky (přiřazení clusteru), ponechejte políčko **zaškrtnout možnost připojit nebo zrušit kontrolu pouze pro výsledek** .
  
    Pokud zrušíte zaškrtnutí tohoto políčka, vrátí se pouze výsledky. Tato možnost může být užitečná, když vytváříte předpovědi jako součást webové služby.
  
5.  Spuštění kanálu  
  
### <a name="results"></a>Výsledky

+  Chcete-li zobrazit hodnoty v datové sadě, klikněte pravým tlačítkem na modul, vyberte **výslednou**datovou sadu a pak vyberte **vizualizovat**.

