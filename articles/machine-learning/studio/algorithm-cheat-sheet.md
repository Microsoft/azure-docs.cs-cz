---
title: Strojové učení tahák k algoritmům
titleSuffix: Azure Machine Learning Studio
description: Tisknutelný strojového učení tahák k algoritmům pomůže vám zvolit správný algoritmus pro prediktivní model Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.component: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 12/18/2017
ms.openlocfilehash: 69b085faf10b9e22618fa248460d269bd2efdb73
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264304"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Machine learning tahák pro Azure Machine Learning Studio

**Azure Machine Learning algoritmus Ošidit list** pomáhá vybrat správné algoritmu pro model prediktivní analýzy.

[Azure Machine Learning Studio](https://studio.azureml.net/) má velké knihovny algoritmů z ***regrese***, ***klasifikace***, ***clustering***, a  ***detekce anomálií*** rodiny. Každý se zaměřuje na řešení jiného typu problému machine learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Ke stažení: Strojové učení tahák k algoritmům

**Stáhněte si tahák tady: [Ve službě Machine Learning Tahák k algoritmům (11 × 17 palců)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![Strojové učení tahák k algoritmům: Zjistěte, jak zvolit algoritmus Machine Learning.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Stáhnout a vytisknout Machine Learning Studio algoritmus Ošidit listu ve velikosti tabloid zachovat po ruce a získat pomoc s výběrem algoritmus.

> [!NOTE]
> Pomoc při používání tento tahák pro výběr správné algoritmus plus hlubší diskuzi o různých typech algoritmů strojového učení a jak se používají, najdete v části [jak zvolit algoritmy pro Microsoft Azure Machine Learning](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Poznámky a definice terminologie pro algoritmus Machine Learning Studio tahák

* Tento tahák k algoritmům nabízí návrhy jsou přibližné pravidla nástroje jezdce. Některé můžou ohnuty a některé mohou být flagrantly došlo k porušení. To je určený pro návrh výchozí bod. Nebojte se ke spuštění head-to-head konkurence mezi několik algoritmů s vašimi daty. Není jednoduše náhradu pochopení principů jednotlivých algoritmů a Principy systému, která vygenerovala vaše data.

* Každý algoritmu strojového učení má svůj vlastní styl nebo *indukční posun*. Pro určitý problém může být vhodné několik algoritmů a jeden algoritmus může být vhodný lepší než jiné. Ale to není vždy možné předem znát, což je nejvhodnější. V takových případech patří několik algoritmů společně v stručný přehled stylů. Vhodné strategie může být jeden algoritmus a pokud výsledky ještě nejsou uspokojivé kvality, akci ostatní. Tady je příklad z [galerii Azure AI](http://gallery.azure.ai/) experimentu, který se pokusí několik algoritmů na stejná data a porovnává výsledky: [Porovnejte roc třídění: Písmeno rozpoznávání](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

* Existují tři hlavní kategorie služby machine learning: **pod dohledem learning**, **supervize**, a **zpětnovazebnému učení**.

  * V **pod dohledem learning**, každý datový bod je označené nebo související s kategorií nebo hodnoty, které vás zajímají.  Příklad popisku kategorií se přiřazení bitovou kopii jako "cat" nebo "pes.  Příklad popisku hodnoty je prodejní cena přidružené používané automobilu. Cílem učení je cílem studovat mnoho označených příklady takových a pak bude schopen provést předpovědi budoucích datových bodů. Například identifikující novou fotografie s správné zvířat nebo přiřazení k jiné automobilů přesné prodejní ceny. Toto je Oblíbené a vhodné typ služby machine learning. Všechny moduly v Azure Machine Learning jsou pod dohledem učení se supervizí s výjimkou [Clustering K-Means][k-means-clustering].

  * V **supervize**, datové body mají se k nim má přiřazené žádné popisky. Místo toho je cílem algoritmus učení bez dohledu uspořádání dat nějakým způsobem nebo k popisu struktury. To může znamenat seskupení do clusterů, stejně jako K-means nebo vyhledání různé způsoby prohlížení komplexních datových, aby se zobrazovala jednodušší.

  * V **zpětnovazebnému učení**, získá algoritmu a vybrat akci v reakci na každý datový bod. Je běžným přístupem v robotika, kde je sada údajů snímačů přes v jednom bodě v čase na datový bod a algoritmus musíte zvolit robot další akci. Je také přirozený pro Internet of Things aplikací. Algoritmus učení také obdrží signál reward krátkou dobu později, určující, jak kvalitní byl rozhodnutí. Na základě toho algoritmus změní svoji strategii, abyste dosáhli nejvyšší potřebu. Aktuálně nejsou žádné zpětnovazební učení algoritmu moduly ve službě Azure ML.

* **Metody Bayesova** předpokládá statisticky nezávislé datové body. To znamená, že je s ostatními bez korelace nejsou nemodelované variabilita jeden datový bod, to znamená, ho nemůže být předpovězen. Například pokud jsou data zaznamenává počet minut, než dorazí další subway trénování, dvěma měřeními rozebrali za den se statisticky nezávislé. Ale dvěma měřeními rozebrali jednu minutu nejsou statisticky nezávislé – jednu hodnotu vysoce prediktivní druhé hodnoty.

* **Vylepšené regrese rozhodovacího stromu** využívá funkce překrývají nebo interakci mezi funkcemi. Která znamená, že, během čárky daná data hodnotu jednu funkci prediktivní trochu jiné hodnoty. Například v denní data o teplotě vysoká nebo Nízká, znalost nejnižší teplota daného dne umožňuje přiměřené odhad toho, že. Informace obsažené v dvě funkce se poněkud redundantní.

* Klasifikace dat do více než dvou kategorií lze provést buď pomocí ze své podstaty roc třídění, nebo zkombinováním sadu třídění dvě třídy do **komplet**. V metodě komplet je zvláštní třídění dvě třídy pro každou třídu – každé z nich data odděluje do dvou kategorií: "Tato třída" a "není této třídy." Potom tyto třídění hlasovat pro správné přiřazení datového bodu. Je to provozní Princip za [One-vs-All Multiclass][one-vs-all-multiclass].

* Několik metod, včetně logistické regrese a Bayes point machine, předpokládají **lineární třídy hranice**. To znamená předpokládají, že jsou hranice mezi třídami přibližně přímé spojnic (nebo v případě obecnější hyperplanes). Často je jím charakteristiku data, která si nejste jisti až poté, co jste se pokusili oddělení, ale je to něco, obvykle je možné zjistit předem vizualizací. Pokud hranice třídy vypadají velmi nestandardní, Zůstaňte u rozhodovacích stromů, rozhodnutí Džungle, podporu vektoru počítače nebo neuronových sítí.

* Neuronové sítě je možné s kategorií proměnné tak, že vytvoříte **fiktivní proměnná** pro každou kategorii ji nastavíte na 1 v případech, kde platí kategorii, 0, pokud tomu tak není.

## <a name="next-steps"></a>Další postup

* Infografika ke stažení, která popisuje algoritmy a poskytuje příklady, naleznete v tématu [Infografika ke stažení: Základy s příklady algoritmů služby Machine learning](basics-infographic-with-algorithm-examples.md).

* Seznam podle kategorie všechny dostupných algoritmů strojového učení v nástroji Machine Learning Studio najdete v tématu [inicializovat Model] [ initialize-model] Machine Learning Studio algoritmus a pomůže modulu.

* Úplný abecední seznam algoritmů a modulů v Machine Learning Studio najdete v tématu [seznam A-Z modulů Machine Learning Studio] [ a-z-list] Studio algoritmu strojového učení a pomůže modulu.

* Pokud si chcete stáhnout a vytisknout diagram s přehledem funkcí nástroje Machine Learning Studio, nahlédněte do tématu [Diagram s přehledem možností nástroje Machine Learning Studio](studio-overview-diagram.md).



<!-- Module References -->
[a-z-list]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/one-vs-all-multiclass
