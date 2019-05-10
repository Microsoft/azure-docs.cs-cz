---
title: 'K-Means Clustering: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu K-Means Clustering ve službě Azure Machine Learning k trénování modelů clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e9b7c8f2cf86245322679198b84b50d2c5edce8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464674"
---
# <a name="module-k-means-clustering"></a>Modul: Clustering K-Means

Tento článek popisuje způsob použití *Clustering K-Means* modulu v Azure Machine Learning Studio k vytvoření Nezkušený model clusteringu K-means. 
 
K-means je jeden z nejjednodušších a nejlepší známé *bez dohledu* učení se supervizí. Použít algoritmus pro širokou škálu strojového učení úkoly, jako například: 

* [Zjištění neobvyklé data](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Clustering textové dokumenty.
* Analýza datových sad, než použijete jiné metody klasifikační nebo regresní. 

Chcete-li vytvořit model clusteringu, můžete:

* Tento modul přidáte do experimentu.
* Připojte datové sady.
* Nastavit parametry, jako je počet clusterů očekávat metrika vzdálenost k použití při vytváření clusterů a tak dále. 
  
Po dokončení konfigurace modulu hyperparameters, připojíte Nezkušený model [Train Model clusteringu](train-clustering-model.md). Protože algoritmus K-means je metodou learning bez dohledu, je volitelný sloupec popisek. 

+ Pokud data obsahují popisek, můžete použít hodnoty popisků výběr clusterů a optimalizovat modelu. 

+ Pokud data obsahují žádný popisek, vytvoří algoritmus clustery představující možných kategorií, které jsou založené výhradně na datech.  

##  <a name="understand-k-means-clustering"></a>Vysvětlení K-means clustering
 
Obecně platí clustering používá iterativní techniky případy skupiny v datové sadě do clusterů, které mají podobnou charakteristikou. Tyto skupiny jsou užitečné při prozkoumávání dat, identifikuje anomálie v datech a nakonec k provádění předpovědí. Clustering s modely také vám může pomoct identifikovat relací v datové sadě, která nemusí logicky odvození podle procházení nebo jednoduše sledovat. Z těchto důvodů clustering se často používá v počátečních fázích strojového učení úkoly, prozkoumat data a zjišťovat neočekávané korelace.  
  
 Když konfigurujete model clusteringu pomocí metody K-means, musíte zadat číslo cílového *k* , která určuje, kolik *centroids* chcete, aby v modelu. Těžiště je bod, který je zástupce každý cluster. Algoritmus K-means datových bodů příchozí přiřadí k jednomu z clusterů minimalizací v rámci clusteru součet kvadratických hodnot. 
 
Když se zpracovává trénovací data, algoritmus K-means začíná počáteční sadu náhodně vybrané centroids. Centroids slouží jako počáteční body pro clustery a jejich použití algoritmu Lloyds pro iterativní upřesnění jejich umístění. Algoritmus K-means zastaví sestavování a ladění clusterů, když splňují jednu nebo více z těchto podmínek:  
  
-   Centroids stabilizujte, což znamená, že již nelze změnit přiřazení clusteru pro jednotlivé body a algoritmus došel na řešení.  
  
-   Algoritmus dokončit spuštění zadaný počet iterací.  
  
 Po dokončení fáze školení, můžete použít [přiřadit Data clustery](assign-data-to-clusters.md) modulu přiřazení nové případy do jednoho clusteru, které jste našli pomocí algoritmu K-means. Cluster přiřazení výpočtem vzdálenost mezi nový případ a těžiště každý cluster. Každý nový případ je přiřazen do clusteru s přesností těžiště.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurace modulu K-Means Clustering
  
1.  Přidat **Clustering K-Means** modulu do experimentu.  
  
2.  Chcete-li určit, jak chcete, modelu, který má být školení, vyberte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Pokud znáte přesnou parametrů, které chcete použít v model clusteringu, můžete zadat konkrétní sadu hodnot jako argumenty.  
  
3.  Pro **číslo Centroids**, zadejte počet clusterů, které chcete, aby algoritmus začínat.  
  
     Model není zaručeno, že k vytvoření přesně tento počet clusterů. Algoritmus začíná tento počet datových bodů a iteruje najít optimální konfiguraci.  
  
4.  Vlastnosti **inicializace** slouží k určení algoritmus, který se používá k definování počáteční konfigurace clusteru.  
  
    -   **Prvních N**: Některé počáteční počet datových bodů jsou vybraná z datové sady a použít jako počáteční prostředky. 
    
         Tato metoda je volána také *Forgy metoda*.  
  
    -   **Náhodné**: Algoritmus náhodně umístí na datový bod v clusteru a potom získá platí, že počáteční střední těžiště z clusteru bude náhodně přiřazeny body. 

         Tato metoda je volána také *náhodné oddílu* metody.  
  
    -   **K – znamená, že ++**: Toto je výchozí metoda pro inicializaci clustery.  
  
         **Tis. – znamená, že ++** algoritmus navrhl 2007 podle Davida Arthur a Sergei Vassilvitskii, aby se zabránilo nízký clustering standardní algoritmus K-means. **K – znamená, že ++** dále to vylepšuje standardní K-means pomocí jiné metody pro výběr centra počáteční clusteru.  
  
    
5.  Pro **náhodná počáteční hodnota čísla**, volitelně zadejte hodnotu, která se použije jako počáteční hodnotu pro inicializaci clusteru. Tato hodnota může mít významný vliv na výběr clusteru.  
  
6.  Pro **metrika**, zvolte funkci použito k měření vzdálenosti mezi vektory clusteru nebo mezi nové datové body a těžiště náhodně vybrané. Azure Machine Learning podporuje následující vzdálenost metriky clusteru:  
  
    -   **Euclidean**: Euclidean vzdálenosti se běžně používá jako míra bodový clusteru pro clustering K-means. Tato metrika je upřednostňované, protože minimalizuje střední vzdálenost mezi body a centroids.
  
7.  Pro **iterací**, zadejte, kolikrát algoritmus by měl předtím, než se dokončí výběr centroids iteraci přes trénovací data.  
  
     Můžete upravit tento parametr k zůstatku přesnost čase školení.  
  
8.  Pro **přiřadit popisek režimu**, zvolte možnost, která určuje, jak popisek sloupce, pokud je k dispozici v datové sadě, by měl být zpracována.  
  
     Vzhledem k tomu K-means clustering bez dohledu strojového učení metoda, popisky jsou volitelné. Ale pokud vaše datová sada už má sloupec popisek, můžete použít tyto hodnoty vodítko pro výběr clusterů nebo můžete určit, že hodnoty ignorovat.  
  
    -   **Popisek sloupce ignorovat**: Hodnoty ve sloupci popisek jsou ignorovány a nepoužívají vytváření modelu.
  
    -   **Vyplnit chybějící hodnoty**: Hodnoty ve sloupcích popisek se používají jako funkce, která pomáhá začlenit clustery. Pokud všechny řádky, chybí popisek, hodnota je splněn pomocí dalších funkcí.  
  
    -   **Přepsat ze do centra pro co nejblíže**: Hodnoty ve sloupcích popisek se nahradí hodnotami předpokládané popisek, použití popisku, který je nejblíž k aktuální těžiště bodu.  

8.  Vyberte **normalizovat funkce** možnost, pokud chcete normalizovat součásti. teprve potom školení.
  
     Pokud použijete normalizace před školení, datové body jsou normalizovány na `[0,1]` podle MinMaxNormalizer.

10. Trénování modelu.  
  
    -   Pokud nastavíte **trainer režimu vytváření** k **jediný parametr**, přidejte příznakem datovou sadu a trénování modelu s použitím [trénování modelu Clustering](train-clustering-model.md) modulu.  
  
### <a name="results"></a>Výsledky

Po dokončení konfigurace a trénování modelu, když máte model, který můžete použít k vygenerování skóre. Existuje ale několik způsobů, jak pro trénování modelu a několik způsobů, jak zobrazit a použít výsledky: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Zachytit snímek modelu ve vašem pracovním prostoru

Pokud jste použili [Train Model clusteringu](train-clustering-model.md) modul:

1. Klikněte pravým tlačítkem myši **Train Model clusteringu** modulu.

2. Vyberte **Trained model**a pak vyberte **uložit jako Trénovaného modelu**.

Uložené model představuje trénovacích dat v době, kdy jste si uložili modelu. Pokud později aktualizovat trénovací data použít v testu, ale neaktualizuje uložené modelu. 

#### <a name="see-the-clustering-result-dataset"></a>Clustering datová sada výsledků 

Pokud jste použili [Train Model clusteringu](train-clustering-model.md) modul:

1. Klikněte pravým tlačítkem myši **Train Model clusteringu** modulu.

2. Vyberte **datovou sadu výsledků**a pak vyberte **vizualizovat**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tipy pro generování nejlepší clusteringový model  

Se ví, že *synchronizace replik indexů* proces, který se používá při vytváření clusterů může významně ovlivnit modelu. Synchronizace replik indexů znamená, že počáteční umístění bodů do potenciální centroids.
 
Pokud datová sada obsahuje mnoho odlehlé hodnoty, a pravdu je vybrán pro clustery, by také k tomuto clusteru nevejde žádné datové body a uvedený cluster může být jednotlivý prvek. To znamená může mít pouze jeden bod.  
  
Možné předejít tomuto problému v několika způsoby:  
  
-   Změňte počet centroids a více počátečních hodnot.  
  
-   Vytvoření více modelů, různé metriky nebo další iterace.  
  
Obecně platí s clusteringem s modely, je možné, že jakékoli dané konfigurace za následek sadu místně optimalizované clustery. Jinými slovy sadu clustery, která je vrácena modelem vyhovuje pouze aktuální datové body a není generalizovatelného s jinými daty. Pokud používáte jinou počáteční konfiguraci, metodu K-means zjistit jiný, možná nadřazené konfigurace. 
