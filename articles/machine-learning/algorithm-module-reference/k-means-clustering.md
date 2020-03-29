---
title: Shlukování prostředků K:Reference modulu
titleSuffix: Azure Machine Learning
description: Zjistěte, jak používat modul K-Means Clustering v Azure Machine Learning k trénování modelů clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921160"
---
# <a name="module-k-means-clustering"></a>Modul: K-prostředky clustering

Tento článek popisuje, jak pomocí modulu *K-Means Clustering* v návrháři Azure Machine Learning (preview) vytvořit netrénovaný model clusteringu K znamená. 
 
K-prostředky je jedním z nejjednodušších a nejznámějších algoritmů učení *bez dozoru.* Algoritmus můžete použít pro různé úkoly strojového učení, například: 

* [Detekce abnormálních dat](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Shlukování textových dokumentů.
* Analýza datových sad před použitím jiných metod klasifikace nebo regrese. 

Chcete-li vytvořit model clusteringu, můžete:

* Přidejte tento modul do kanálu.
* Připojte datovou sadu.
* Nastavte parametry, jako je například počet clusterů, které očekáváte, metrika vzdálenosti, která se má použít při vytváření clusterů a tak dále. 
  
Po konfiguraci hyperparameterů modulu připojíte netrénovaný model k [modelu clusterování vlaků](train-clustering-model.md). Vzhledem k tomu, že algoritmus K-znamená je metoda učení bez dozoru, sloupec popisku je volitelný. 

+ Pokud data obsahují popisek, můžete pomocí hodnot popisků vést výběr clusterů a optimalizovat model. 

+ Pokud vaše data nemá žádný popisek, algoritmus vytvoří clustery představující možné kategorie, založené výhradně na datech.  

##  <a name="understand-k-means-clustering"></a>Principy clusteringu K-prostředků
 
Clustering obecně používá iterativní techniky k seskupení případů v datové sadě do clusterů, které mají podobné vlastnosti. Tato seskupení jsou užitečné pro zkoumání dat, identifikaci anomálií v datech a nakonec pro vytváření předpovědí. Clustering modely můžete také pomoci identifikovat vztahy v datové sadě, které nemusí logicky odvodit procházením nebo jednoduché pozorování. Z těchto důvodů clustering se často používá v raných fázích úloh strojového učení, prozkoumat data a zjistit neočekávané korelace.  
  
 Při konfiguraci modelu clusteringu pomocí metody K-means je nutné zadat cílové číslo *k,* které označuje požadovaný počet *centroidů* v modelu. Centroid je bod, který je reprezentativní pro každou hvězdokupu. Algoritmus K-znamená přiřadí každý příchozí datový bod jednomu z clusterů minimalizací součtu čtverců v rámci clusteru. 
 
Když zpracovává trénovací data, algoritmus K-prostředků začíná počáteční sadou náhodně vybraných centroidů. Centroidy slouží jako výchozí body pro klastry a aplikují Lloydův algoritmus na iterativně zdokonalování jejich umístění. Algoritmus K-znamená zastaví vytváření a rafinaci clusterů, když splňuje jednu nebo více z těchto podmínek:  
  
-   Centroidy se stabilizují, což znamená, že přiřazení clusteru pro jednotlivé body se již nemění a algoritmus se konvergoval v řešení.  
  
-   Algoritmus dokončil spuštění zadaného počtu iterací.  
  
 Po dokončení fáze školení pomocí modulu [Přiřadit data clusterům](assign-data-to-clusters.md) přiřadíte nové případy jednomu z clusterů, které jste našli pomocí algoritmu K-znamená. Přiřazení clusteru provedete výpočtem vzdálenosti mezi novým případem a centroidem každého clusteru. Každý nový případ je přiřazen ke clusteru s nejbližším centroidem.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurace modulu Clustering K-Means
  
1.  Přidejte do kanálu modul **Clustering K-Means.**  
  
2.  Chcete-li určit, jak chcete model trénovat, vyberte možnost **Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Pokud znáte přesné parametry, které chcete použít v modelu clusteringu, můžete zadat určitou sadu hodnot jako argumenty.  
  
3.  Do **pole Počet centroidů**zadejte počet clusterů, se kterými má algoritmus začínat.  
  
     Model není zaručeno, že vyrábět přesně tento počet clusterů. Algoritmus začíná tímto počtem datových bodů a itetuje najít optimální konfiguraci.  
  
4.  Vlastnosti **Inicializace** se používá k určení algoritmu, který se používá k definování počáteční konfigurace clusteru.  
  
    -   **První N**: Některé počáteční počet datových bodů jsou vybrány z datové sady a použity jako počáteční prostředek. 
    
         Tato metoda se také nazývá *Forgy metoda*.  
  
    -   **Random**: Algoritmus náhodně umístí datový bod do clusteru a poté vypočítá počáteční střední hodnota jako centroid náhodně přiřazených bodů clusteru. 

         Tato metoda se také nazývá metoda *náhodného oddílu.*  
  
    -   **K-Means++**: Toto je výchozí metoda pro inicializaci clusterů.  
  
         **Algoritmus K-means++** byl navržen v roce 2007 Davidem Arthurem a Sergejem Vassilvitskiim, aby se zabránilo špatnému shlukování standardním algoritmem K-prostředků. **K-means++** vylepšuje standardní K-prostředky pomocí jiné metody pro výběr počátečních center clusteru.  
  
    
5.  Do **pole Osiva náhodného čísla**volitelně zadejte hodnotu, která se má použít jako počáteční hodnota pro inicializaci clusteru. Tato hodnota může mít významný vliv na výběr clusteru.  
  
6.  V **případě metrické**ho zvolte funkci, která se má použít pro měření vzdálenosti mezi vektory clusteru nebo mezi novými datovými body a náhodně vybraným centroidem. Azure Machine Learning podporuje následující metriky vzdálenosti clusteru:  
  
    -   **Euklidovský**: Euklidova vzdálenost se běžně používá jako měřítko rozptylu klastrů pro shlukování K-prostředků. Tato metrika je upřednostňována, protože minimalizuje střední vzdálenost mezi body a centroidy.
  
7.  Pro **iterací**zadejte, kolikrát by měl algoritmus iteraci nad trénovacími daty před dokončením výběru centroidů.  
  
     Tento parametr můžete upravit tak, aby přesnost vyvažovala s dobou tréninku.  
  
8.  V **režimu Přiřadit popisek**zvolte možnost, která určuje, jak má být sloupec popisku, pokud je přítomen v datové sadě, zpracován.  
  
     Vzhledem k tomu, že clustering prostředků K-znamená, že metoda strojového učení bez dohledu, popisky jsou volitelné. Pokud však vaše datová sada již obsahuje sloupec popisků, můžete tyto hodnoty použít k vedení výběru clusterů nebo můžete určit, že hodnoty budou ignorovány.  
  
    -   **Ignorovat sloupec popisku**: Hodnoty ve sloupci popisku jsou ignorovány a při vytváření modelu se nepoužívají.
  
    -   **Chybějící hodnoty výplně**: Hodnoty sloupců popisků se používají jako funkce, které pomáhají vytvářet clustery. Pokud některé řádky chybí popisek, hodnota je imputována pomocí jiných funkcí.  
  
    -   **Přepsat od nejbližšího ke středu**: Hodnoty sloupců popisků jsou nahrazeny předpokládanými hodnotami popisků pomocí popisku bodu, který je nejblíže aktuálnímu centroidu.  

8.  Pokud chcete normalizovat funkce před trénování, vyberte možnost **Normalizovat funkce.**
  
     Pokud použijete normalizaci, před trénování `[0,1]` jsou datové body normalizovány minMaxNormalizer.

10. Trénujte model.  
  
    -   Pokud nastavíte **vytvořit režim treniace** na **jeden parametr**, přidejte tagovované datové sady a trénování modelu pomocí modulu [model clustering vlak.](train-clustering-model.md)  
  
## <a name="results"></a>Výsledky

Po dokončení konfigurace a trénování modelu máte model, který můžete použít ke generování skóre. Existuje však několik způsobů, jak trénovat model a několik způsobů zobrazení a použití výsledků: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Zachycení snímku modelu v pracovním prostoru

Pokud jste použili modul [Model shlukování vlaků:](train-clustering-model.md)

1. Vyberte modul **Model clusteringu vlaků** a otevřete pravý panel.

2. Vyberte **kartu Výstupy.** **Register dataset**

Uložený model představuje trénovací data v době uložení modelu. Pokud později aktualizujete trénovací data použitá v kanálu, neaktualizuje uložený model. 

### <a name="see-the-clustering-result-dataset"></a>Zobrazit datovou sadu výsledků clusteringu 

Pokud jste použili modul [Model shlukování vlaků:](train-clustering-model.md)

1. Klikněte pravým tlačítkem myši na modul **Model clusteringu vlaků.**

2. Vyberte **možnost Vizualizovat**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tipy pro generování nejlepšího modelu clusteringu  

Je známo, že proces *výsevu,* který se používá během clustering může významně ovlivnit model. Výsev znamená počáteční umístění bodů do potenciálních centroidů.
 
Například pokud datová sada obsahuje mnoho odlehlé hodnoty a odlehlé je vybrán a osít clustery, žádné jiné datové body by se vešly dobře s tímto clusterem a clusteru může být singleton. To znamená, že to může mít jen jeden bod.  
  
Tomuto problému se můžete vyhnout několika způsoby:  
  
-   Změňte počet centroidů a vyzkoušejte více hodnot osiva.  
  
-   Vytvořte více modelů, které mění metriku nebo více iterace.  
  
Obecně platí, že s clustering modely, je možné, že každá daná konfigurace bude mít za následek místně optimalizované sady clusterů. Jinými slovy sada clusterů, která je vrácena modelem, vyhovuje pouze aktuálním datovým bodům a nelze ji generalizovat na jiná data. Pokud použijete jinou počáteční konfiguraci, metoda K-means může najít jinou, nadřazenou konfiguraci. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
