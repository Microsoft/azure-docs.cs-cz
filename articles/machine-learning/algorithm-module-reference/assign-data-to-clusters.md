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
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028767"
---
# <a name="assign-data-to-clusters"></a>Přiřaďte Data do clusterů

Tento článek popisuje způsob použití [přiřadit Data clustery](assign-data-to-clusters.md) modulu ve vizuální rozhraní k vygenerování predikce na základě model clusteringu, které se naučil pomocí algoritmu clusteringu K-Means.

Modul vrací třídu dataset, který obsahuje o přiřazení pro každý nový datový bod. 


## <a name="how-to-use-assign-data-to-clusters"></a>Jak pracovat s daty přiřadit do clusterů
  
1.  Ve vizuální rozhraní vyhledejte dříve trénovaný model clusteringu. Můžete vytvořit a trénování modelu clusteringu pomocí některé z těchto metod:  
  
    - Nakonfigurujte pomocí algoritmu K-means [Clustering K-Means](k-means-clustering.md) modul a poté trénování modelu s použitím datové sady a [trénování modelu Clustering](train-clustering-model.md) modulu.  
  
  
    Můžete také přidat existující trénovaný model clusteringu z **uloží modely** skupiny ve vašem pracovním prostoru.

2. Připojení k levým vstupním portem z trénovaného modelu [přiřadit Data clustery](assign-data-to-clusters.md).  

3. Připojte novou datovou sadu jako vstup. V této datové sadě popisky jsou volitelné. Obecně platí clustering je metoda bez dohledu learning tak neočekává se, že budete vědět kategorie předem.

    Vstupní sloupce však musí být stejný jako sloupce, které byly používány v kurzy, které model clusteringu nebo chybu.

    > [!TIP]
    > Chcete-li snížit počet výstupních sloupců předpovědi clusteru, použijte [výběr sloupců v datové sadě](select-columns-in-dataset.md)a vybrat podmnožinu sloupců. 
    
4. Ponechte možnost **Kontrola připojení nebo zrušte zaškrtnutí pro výsledek pouze** vybrána, pokud chcete výsledky obsahující úplné vstupní datovou sadu, společně s sloupec označující výsledky (přiřazení clusteru).
  
    Pokud výběr této možnosti získáte zpět pouze výsledky. To může být užitečné při vytváření předpovědí jako součást webové služby.
  
5.  Spusťte experiment.  
  
### <a name="results"></a>Výsledky

 
+  Chcete-li zobrazit hodnoty v datové sadě, klikněte pravým tlačítkem na modul, vyberte **způsobit datových sad**a klikněte na tlačítko **vizualizovat**.

