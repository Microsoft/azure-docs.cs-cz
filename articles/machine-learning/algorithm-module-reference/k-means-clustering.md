---
title: 'K-Means Clustering: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu K-means clusteringu ve službě Azure Machine Learning k trénování modelů clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29aeb460581655190b7c3f4256647059f4642d3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029697"
---
# <a name="k-means-clustering"></a>K-Means Clustering

Tento článek popisuje způsob použití **Clustering K-Means** modulu v Azure Machine Learning Studio k vytvoření Nezkušený model clusteringu K-means. 
 
K-means je jeden z nejjednodušších a nejlepší známé *bez dohledu* učení se supervizí a lze použít pro nejrůznější úlohy strojového učení, jako například [detekuje neobvyklé data](https://msdn.microsoft.com/magazine/jj891054.aspx), clusteringu textu dokumenty a analýzu datové sady před dalšími metodami klasifikační nebo regresní. Vytvořte model clusteringu, přidejte tento modul do experimentu, připojit datové sady a nastavte parametry, jako je počet clusterů očekávat metrika vzdálenost k použití při vytváření clusterů a tak dále. 
  
Po nakonfigurování hyperparameters modulu připojení Nezkušený model [Train Model clusteringu](train-clustering-model.md) protože algoritmus K-means je metodou learning bez dohledu, popisek sloupce je volitelný. 

+ Pokud data obsahují popisek, můžete použít hodnoty popisků výběr clusterů a optimalizovat modelu. 

+ Pokud data obsahují žádný popisek, vytvoří algoritmus clustery představující možných kategorií, které jsou založené výhradně na datech.  
  

  
##  <a name="understanding-k-means-clustering"></a>Vysvětlení k-means clustering
 
Obecně platí clustering používá iterativní techniky k skupiny případům v datové sadě do clusterů obsahující podobné charakteristiky. Tyto skupiny jsou užitečné při prozkoumávání dat, identifikuje anomálie v datech a nakonec k provádění předpovědí. Clustering s modely také vám může pomoct identifikovat relací v datové sadě, která nemusí logicky odvození podle procházení nebo jednoduše sledovat. Z těchto důvodů clustering se často používá v počátečních fázích strojového učení úkoly, prozkoumat data a zjišťovat neočekávané korelace.  
  
 Při konfiguraci pomocí metody k-means model clusteringu, musíte zadat číslo cílového *k* určující počet *centroids* chcete, aby v modelu. Těžiště je bod, který je zástupce každý cluster. Algoritmus K-means datových bodů příchozí přiřadí k jednomu z clusterů minimalizací v rámci clusteru součet kvadratických hodnot. 
 
Při zpracování trénovací data, K-means algoritmus začíná počáteční sadu randomnly zvolili centroids, které slouží jako počáteční body pro každý cluster a použije algoritmus Lloyds zavádět postupně upřesněte umístění centroids. Algoritmus K-means zastaví sestavování a ladění clusterů, když splňují jednu nebo více z těchto podmínek:  
  
-   Centroids stabilizujte, což znamená, že již nelze změnit přiřazení clusteru pro jednotlivé body a algoritmus došel na řešení.  
  
-   Algoritmus dokončit spuštění zadaný počet iterací.  
  
 Po dokončení fáze školení, můžete použít [přiřadit Data clustery](assign-data-to-clusters.md) modulu přiřazení nové případy do jednoho clusteru, ke které došlo k nalezení algoritmus k-means. Přiřazení clusteru provádí výpočty vzdálenost mezi nový případ a těžiště každý cluster. Každý nový případ je přiřazen do clusteru s přesností těžiště.  

## <a name="how-to-configure-k-means-clustering"></a>Postup konfigurace clusterů K-Means
  
1.  Přidat **Clustering K-Means** modulu do experimentu.  
  
2.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Pokud znáte přesnou parametrů, které chcete použít v model clusteringu, můžete zadat konkrétní sadu hodnot jako argumenty.  
  
   
  
3.  Pro **číslo Centroids**, zadejte počet clusterů, které chcete, aby algoritmus začínat.  
  
     Model není zaručeno, že k vytvoření přesně tento počet clusterů. Algorithn začíná tento počet datových bodů a iteruje najít optimální konfiguraci.  
  
     
  
4.  Vlastnosti **inicializace** slouží k určení algoritmus, který se používá k definování počáteční konfigurace clusteru.  
  
    -   **Prvních N**: Některé počáteční počet datových bodů jsou vybraná z datové sady a použít jako počáteční prostředky.  
  
         Zkratka *Forgy metoda*.  
  
    -   **Náhodné**: Algoritmus náhodně umístí na datový bod v clusteru a potom získá platí, že počáteční střední těžiště z clusteru bude náhodně přiřazeny body.  
  
         Zkratka *náhodné oddílu* metody.  
  
    -   **K – znamená, že ++**: Toto je výchozí metoda pro inicializaci clustery.  
  
         **K-means ++** algoritmus navrhl 2007 podle Davida Arthur a Sergei Vassilvitskii nízký clustering Standard, aby k-means algoritmus. **K-means ++** dále to vylepšuje standardní K-means pomocí jiné metody pro výběr centra počáteční clusteru.  
  
    
5.  Pro **náhodná počáteční hodnota čísla**, volitelně zadejte hodnotu, která se použije jako počáteční hodnotu pro inicializaci clusteru. Tato hodnota může mít významný vliv na výběr clusteru.  
  
    
  
6.  Pro **metrika**, zvolte funkci použito k měření vzdálenosti mezi vektory clusteru nebo mezi nové datové body a těžiště náhodně vybrané. Azure Machine Learning podporuje následující vzdálenost metriky clusteru:  
  
    -   **Euclidean**: Euclidean vzdálenosti se běžně používá jako míra bodový clusteru pro clustering K-means. Tato metrika je upřednostňované, protože minimalizuje střední vzdálenost mezi body a centroids.
  

  
7.  Pro **iterací**, zadejte, kolikrát by měl algoritmus před dokončením výběr centroids iterovat trénovací data.  
  
     Můžete upravit tento parametr k zůstatku přesnost oproti době školení.  
  
8.  Pro **přiřadit popisek režimu**, zvolte možnost, která určuje, jak popisek sloupce, pokud jsou k dispozici v datové sadě, by měl být zpracována.  
  
     Vzhledem k tomu K-means clustering bez dohledu strojového učení metoda, popisky jsou volitelné. Ale pokud vaše datová sada už má sloupec popisek, můžete použít tyto hodnoty vodítko pro výběr clusterů nebo můžete určit, že hodnoty ignorovat.  
  
    -   **Popisek sloupce ignorovat**: Hodnoty ve sloupci popisek jsou ignorovány a nepoužívají vytváření modelu.
  
    -   **Vyplnit chybějící hodnoty**: Hodnoty ve sloupcích popisek se používají jako funkce, která pomáhá začlenit clustery. Pokud všechny řádky, chybí popisek, hodnota je splněn pomocí dalších funkcí.  
  
    -   **Přepsat ze do centra pro co nejblíže**: Hodnoty ve sloupcích popisek se nahradí hodnotami předpokládané popisek, použití popisku, který je nejblíž k aktuální těžiště bodu.  

8.  Vyberte možnost, **normalizovat funkce**, pokud chcete normalizovat součásti. teprve potom školení.
  
     Pokud použijete normalizace před školení, datové body jsou normalizovány na `[0,1]` podle MinMaxNormalizer.

10. Trénování modelu.  
  
    -   Pokud nastavíte **trainer režimu vytváření** k **jediný parametr**, přidejte příznakem datovou sadu a trénování modelu s použitím [trénování modelu Clustering](train-clustering-model.md) modulu.  
  

### <a name="results"></a>Výsledky

Po dokončení konfigurace a trénování modelu, když máte model, můžete použít k vygenerování skóre. Existuje ale několik způsobů, jak pro trénování modelu a několik způsobů, jak zobrazit a použít výsledky: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Zachytit snímek modelu ve vašem pracovním prostoru

+ Pokud jste použili [Train Model clusteringu](train-clustering-model.md) modulu
    1. Klikněte pravým tlačítkem myši [Train Model clusteringu](train-clustering-model.md) modulu.
    2. Vyberte **Trained model** a potom klikněte na tlačítko **uložit jako Trénovaného modelu**.


Uložené modelu bude reprezentovat trénovacích dat v době, kdy jste si uložili modelu. Pokud budete později aktualizovat trénovací data použít v experimentu, neaktualizuje uložené modelu. 



#### <a name="see-the-clustering-result-dataset"></a>Clustering datová sada výsledků 

+ Pokud jste použili [Train Model clusteringu](train-clustering-model.md) modulu
    1. Klikněte pravým tlačítkem myši [Train Model clusteringu](train-clustering-model.md) modulu.
    2. Vyberte **datovou sadu výsledků** a potom klikněte na tlačítko **vizualizovat**.


### <a name="tips-for-generating-the-best-clustering-model"></a>Tipy pro generování nejlepší clusteringový model  

Se ví, že **synchronizace replik indexů** procesu použít během vytváření clusterů může významně ovlivnit modelu. Synchronizace replik indexů znamená, že počáteční umístění bodů do potental centroids.
 
Například pokud datová sada obsahuje mnoho odlehlé hodnoty, a pravdu je vybrán pro clustery, by také k tomuto clusteru nevejde žádné datové body a uvedený cluster může být jednotlivý prvek: to znamená, že cluster s pouze jeden bod.  
  
Existují různé způsoby, jak tomuto problému zabráníte tak:  
  
-   Změňte počet centroids a více počátečních hodnot.  
  
-   Vytvoření více modelů, různé metriky nebo další iterace.  
  
  
Obecně platí s clusteringem s modely, je možné, že jakékoli dané konfigurace za následek sadu místně optimalizované clustery. Jinými slovy sadu clusterů vrátil modelem vyhovuje pouze aktuální datové body a není generalizovatelného s jinými daty. Pokud jste použili jinou počáteční konfiguraci, metodu K-means zjistit jiný, možná nadřazené konfigurace. 
