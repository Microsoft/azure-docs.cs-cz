---
title: 'Trénování Clusteringový Model: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak použít modul trénování modelu Clustering ve službě Azure Machine Learning k trénování modelů clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028077"
---
# <a name="train-clustering-model"></a>Trénování modelu clusteringu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k trénování model clusteringu.

Modul přebírá Nezkušený clusteringu model, který jste už nakonfigurovali pomocí [Clustering K-Means](k-means-clustering.md) modulu a trénovat modelu s použitím datové sady s popiskem nebo bez popisku. Modul vytvoří oba trénovaného modelu, který můžete použít k předpovědi a sadu přiřazení clusteru pro každý případ v trénovací data.

> [!NOTE]
> Clustering databázový model se školení pomocí [Train Model](train-model.md) modul, který je obecný modul pro trénování modelů strojového učení. Důvodem je, že [Train Model](train-model.md) funguje pouze s pod dohledem učení se supervizí. K-means a dalších algoritmů clusteringu povolit bez dohledu učení, to znamená, že algoritmus může učit se z dat bez popisku.  
  
## <a name="how-to-use-train-clustering-model"></a>Jak používat Train Model clusteringu  
  
1.  Přidat **Train Model clusteringu** modulu do experimentu v nástroji Studio. Můžete najít v modulu v rámci **modulů služby Machine Learning**v **Train** kategorie.  
  
2. Přidat [Clustering K-Means](k-means-clustering.md) modulu nebo jiný vlastní modul, který vytvoří kompatibilní clustering modelu a nastavte parametry model clusteringu.  
    
3.  Připojit trénovací datové sady na pravém vstup **Train Model clusteringu**.
  
5.  V **sloupců**, vybrat sloupce, z datové sady používané k vytváření clusterů. Je nutné vybrat sloupce, které byly správné funkce: například nepoužívejte ID nebo další sloupce, které obsahují jedinečné hodnoty nebo sloupce, které obsahují stejné hodnoty.

    Pokud popisek je k dispozici, můžete ho použít jako funkci, nebo nechte.  
  
6. Vyberte možnost, **Kontrola připojení nebo zrušte zaškrtnutí pro výsledek pouze**, pokud chcete výstup trénovacích dat spolu s nový popisek clusteru.

    Pokud výběr této možnosti jsou pouze přiřazení clusteru výstup. 

7. Spusťte experiment, nebo klikněte na tlačítko **Train Model clusteringu** modul a vyberte **spustit vybrané**.  
  
### <a name="results"></a>Výsledky

Po dokončení školení:


+  Chcete-li zobrazit hodnoty v datové sadě, klikněte pravým tlačítkem na modul, vyberte **způsobit datových sad**a klikněte na tlačítko **vizualizovat**.

+ K uložení naučeného modelu pro pozdější znovu použít, klikněte pravým tlačítkem na modul, vyberte **Trained model**a klikněte na tlačítko **uložit jako Trénovaného modelu**.

+ K vygenerování skóre z modelu, použijte [přiřadit Data clustery](assign-data-to-clusters.md).



## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 