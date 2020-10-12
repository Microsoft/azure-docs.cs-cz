---
title: 'K-znamená clustering: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat K tomu modul clustering v Azure Machine Learning ke školení modelů clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/04/2020
ms.openlocfilehash: 97cadfb8f5004cfd2701335172d4416c64f05259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907871"
---
# <a name="module-k-means-clustering"></a>Modul: K-znamená clustering

Tento článek popisuje, jak použít modul pro *clusteringu* v Návrháři Azure Machine Learning k vytvoření nevýukového modelu clusteringu. 
 
K-znamená, že je jedním z nejjednodušších a osvědčených *bezdohledných* algoritmů učení. Můžete použít algoritmus pro nejrůznější úlohy strojového učení, například: 

* [Zjišťování neobvyklých dat](https://msdn.microsoft.com/magazine/jj891054.aspx)
* Vytváření clusterů textových dokumentů.
* Analýza datových sad před použitím jiných metod klasifikace nebo regrese. 

Chcete-li vytvořit model clusteringu, postupujte takto:

* Přidejte tento modul do vašeho kanálu.
* Připojte datovou sadu.
* Nastavte parametry, třeba počet očekávaných clusterů, metriku vzdálenosti, která se má použít při vytváření clusterů, a tak dále. 
  
Až nakonfigurujete parametry modulu, připojíte nevlakový model k [modelu clusteringu](train-clustering-model.md). Vzhledem k tomu, že se jedná o bezdohledový způsob učení, sloupec popisku je nepovinný. 

+ Pokud data obsahují popisek, můžete použít hodnoty popisku k seznámení s výběrem clusterů a optimalizaci modelu. 

+ Pokud vaše data nemají žádný popisek, algoritmus Vytvoří clustery reprezentující možné kategorie založené výhradně na datech.  

##  <a name="understand-k-means-clustering"></a>Porozumění K-znamená clusteringu
 
Obecně platí, že Clustering používá iterativní techniky k seskupení případů v datové sadě do clusterů, které mají podobné charakteristiky. Tato seskupení jsou užitečná pro zkoumání dat, identifikaci anomálií v datech a nakonec pro provádění předpovědi. Modely clusteringu vám také pomůžou identifikovat relace v datové sadě, které nemůžete logicky odvodit pomocí procházení nebo jednoduchého sledování. Z těchto důvodů se clustering často používá v počátečních fázích úloh strojového učení, aby bylo možné prozkoumat data a zjišťovat neočekávané korelace.  
  
 Když nakonfigurujete model clusteringu pomocí metody K-označuje, je nutné zadat cílové číslo *K* , které určuje počet *centroids* v modelu. Těžiště je bod, který je reprezentativní pro každý cluster. Algoritmus K přiřadí každému příchozímu datovému bodu jeden z clusterů tím, že minimalizuje součet čtverců v rámci clusteru. 
 
Při zpracovávání dat školení začíná algoritmus K, který začíná počáteční sadou náhodně zvolených centroids. Centroids slouží jako počáteční body pro clustery a používají algoritmus Lloyd 's pro iterativní upřesnění jejich umístění. Algoritmus K zastaví sestavování a upřesnění clusterů, když splní jednu nebo více z těchto podmínek:  
  
-   Centroids stabilizovat, což znamená, že přiřazení clusteru pro jednotlivé body se už nemění a algoritmus se sblížen na řešení.  
  
-   Algoritmus dokončil běh zadaného počtu iterací.  
  
 Po dokončení fáze školení použijete modul [přiřazení dat ke clusterům](assign-data-to-clusters.md) k přiřazení nových případů k jednomu z clusterů, které jste našli pomocí algoritmu k. Přiřazení clusteru provedete pomocí výpočtu vzdálenosti mezi novým a těžiště každého clusteru. Každému novému případu se přiřadí cluster s nejbližším těžiště.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurace K – znamená modul clusteringu
  
1.  Přidejte k vašemu kanálu modul **clusteringu** .  
  
2.  Chcete-li určit, jakým způsobem chcete model vyškolet, vyberte možnost **vytvořit režim Trainer** .  
  
    -   **Jediný parametr**: Pokud znáte přesné parametry, které chcete použít v modelu clusteringu, můžete zadat konkrétní sadu hodnot jako argumenty.  
  
3.  Do pole **počet centroids**zadejte počet clusterů, se kterými má algoritmus začínat.  
  
     Model není zaručený, aby vyprodukoval přesně tento počet clusterů. Algoritmus začíná tímto počtem datových bodů a provede iterace k nalezení optimální konfigurace. Můžete se podívat na [zdrojový kód skriptu sklearn](https://github.com/scikit-learn/scikit-learn/blob/fd237278e/sklearn/cluster/_kmeans.py#L1069).
  
4.  **Inicializace** vlastností slouží k určení algoritmu, který se používá k definování počáteční konfigurace clusteru.  
  
    -   **First N**: z datové sady se vybere nějaký počáteční počet datových bodů, který se používá jako počáteční prostředky. 
    
         Tato metoda se označuje také jako *Metoda padělání*.  
  
    -   **Náhodný**: algoritmus náhodně umístí datový bod do clusteru a pak vypočítá počáteční střední hodnotu jako těžištěy náhodně přiřazených bodů clusteru. 

         Tato metoda se označuje také jako metoda *náhodného dělení* .  
  
    -   **K-znamená + +**: Toto je výchozí metoda pro inicializaci clusterů.  
  
         Algoritmus **k** byl navržený v 2007. David Arthur a Sergei Vassilvitskii, aby nedocházelo ke špatnému clusteringu pomocí standardu k. **K-znamená + +** se zlepšuje na standardu k – znamená použití jiné metody pro výběr počátečních Center clusterů.  
  
    
5.  V případě **náhodného čísla**můžete zadat hodnotu, která se má použít jako počáteční hodnota pro inicializaci clusteru. Tato hodnota může mít významný vliv na výběr clusteru.  
  
6.  V části **metrika**vyberte funkci, která se má použít pro měření vzdálenosti mezi vektory clusteru nebo mezi novými datovými body a náhodně zvoleným těžiště. Azure Machine Learning podporuje následující metriky na dálku clusteru:  
  
    -   **Euclidean**: Euclideaná vzdálenost se běžně používá jako míra bodových clusterů pro k, znamená clusteringu. Tato metrika je preferována, protože minimalizuje střední vzdálenost mezi body a centroids.
  
7.  Pro **iterace**zadejte počet, kolikrát se má algoritmus iterovat přes školicí data před tím, než dokončí výběr centroids.  
  
     Tento parametr můžete upravit tak, aby vyrovnal přesnost na dobu školení.  
  
8.  V případě **režimu přiřazení popisků**vyberte možnost, která určuje, jak má být zpracován sloupec popisku, pokud je přítomen v datové sadě.  
  
     Vzhledem K tomu, že clustering je nekontrolovaný způsob strojového učení, popisky jsou volitelné. Pokud však vaše datová sada již obsahuje sloupec popisku, můžete tyto hodnoty použít k nastavení služby pro výběr clusterů nebo můžete určit, že se mají hodnoty ignorovat.  
  
    -   **Ignorovat popisek sloupce**: hodnoty ve sloupci popisek jsou ignorovány a nejsou použity při sestavování modelu.
  
    -   **Vyplnit chybějící hodnoty**: hodnoty sloupců popisku se používají jako funkce, které vám pomůžou sestavení clusterů. Pokud v některých řádcích chybí popisek, hodnota se připravuje pomocí dalších funkcí.  
  
    -   **Přepsat z nejbližšího středu**: hodnoty sloupce Label jsou nahrazeny hodnotami předpokládaných popisků, které používají popisek bodu, který je nejblíže aktuálnímu těžiště.  

8.  Vyberte možnost **normalizovat funkce** , pokud chcete před školením normalizovat funkce.
  
     Použijete-li normalizaci, před školením jsou datové body normalizovány na `[0,1]` MinMaxNormalizer.

10. Proveďte výuku modelu.  
  
    -   Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, přidáte s příznakovou datovou sadu a pomocí modulu [model clusteringu](train-clustering-model.md) provedete model.  
  
## <a name="results"></a>Výsledky

Po dokončení konfigurace a školení modelu máte model, který můžete použít ke generování skóre. Existuje však několik způsobů, jak model naučit, a více způsobů zobrazení a používání výsledků: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Zachycení snímku modelu ve vašem pracovním prostoru

Pokud jste použili modul [výukového modelu clusteringu](train-clustering-model.md) :

1. Vyberte modul **výuka clusteringu modelů** a otevřete pravý panel.

2. Vyberte kartu **výstupy** . Výběrem ikony **Registrovat datovou sadu** uložte kopii proučeného modelu.

Uložený model představuje školicí data v době, kdy jste model uložili. Pokud později aktualizujete školicí data použitá v kanálu, neaktualizuje uložený model. 

### <a name="see-the-clustering-result-dataset"></a>Zobrazit datovou sadu výsledků clusteringu 

Pokud jste použili modul [výukového modelu clusteringu](train-clustering-model.md) :

1. Klikněte pravým tlačítkem na modul **výuka clusteringu modelu** .

2. Vyberte **vizualizovat**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tipy pro generování nejlepšího modelu clusteringu  

Je známo, že proces *osazení* , který se používá během clusteringu, může významně ovlivnit model. Osazení označuje počáteční umístění bodů do potenciálního centroids.
 
Pokud například datová sada obsahuje mnoho nezaložených hodnot a izolované se vybere pro osazení clusterů, žádné další datové body by se do tohoto clusteru nevešly a cluster by mohl být typu singleton. To znamená, že může mít pouze jeden bod.  
  
Tomuto problému se můžete vyhnout několika způsoby:  
  
-   Změňte počet centroids a zkuste použít více počátečních hodnot.  
  
-   Vytvořte si více modelů, různou metriku nebo více iterací.  
  
Obecně platí, že u modelů clusteringu je možné, že kterákoli z těchto konfigurací bude mít za následek místně optimalizovanou sadu clusterů. Jinými slovy, sada clusterů, které model vrací, barevně vyhovuje pouze aktuálním datovým bodům a nelze je zobecnit do dalších dat. Pokud používáte jinou počáteční konfiguraci, znamená to, že metoda K tomu může najít jinou nadřazenou konfiguraci. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
