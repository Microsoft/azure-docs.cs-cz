---
title: Služby Machine learning tahák k algoritmům – Azure | Dokumentace Microsoftu
description: Tisknutelný strojového učení tahák k algoritmům pomůže vám zvolit správný algoritmus pro prediktivní model Azure Machine Learning Studio.
keywords: tahák k algoritmům, tahák, algoritmu strojového učení
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: b080a739aa73e3c8ef95c7db9a6358d942e94bba
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238382"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Tahák k algoritmům služby Machine Learning pro Microsoft Azure Machine Learning Studio
**Aktualizace Microsoft Azure Machine Learning algoritmus Ošidit list** pomáhá vybrat správné algoritmu pro model prediktivní analýzy.

[Azure Machine Learning Studio](https://studio.azureml.net/) má velké knihovny algoritmů z ***regrese***, ***klasifikace***, ***clustering***, a  ***detekce anomálií*** rodiny. Každý se zaměřuje na řešení jiného typu problému machine learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Stažení: Machine learning tahák k algoritmům
**Stáhněte si tahák tady: [Machine Learning algoritmus Ošidit list (11 × 17 palců)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Machine Learning tahák: Zjistěte, jak zvolit algoritmus Machine Learning.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Stáhnout a vytisknout Machine Learning algoritmus Ošidit listu ve velikosti tabloid zachovat po ruce a získat pomoc s výběrem algoritmus.

> [!NOTE]
> Přečtěte si článek [jak zvolit algoritmy pro Microsoft Azure Machine Learning](algorithm-choice.md) podrobné pokyny k používání tento tahák.
> 
> 

## <a name="more-help-with-algorithms"></a>Další pomoc s algoritmy
* Pomoc při používání tento tahák pro výběr správné algoritmus plus hlubší diskuzi o různých typech algoritmů strojového učení a jak se používají, najdete v části [jak zvolit algoritmy pro Microsoft Azure Machine Learning](algorithm-choice.md).
* Infografika ke stažení, která popisuje algoritmy a poskytuje příklady, naleznete v tématu [Infografika ke stažení: Základy s příklady algoritmů služby Machine learning](basics-infographic-with-algorithm-examples.md).
* Seznam podle kategorie všechny dostupných algoritmů strojového učení v nástroji Machine Learning Studio najdete v tématu [inicializovat Model] [ initialize-model] Machine Learning Studio algoritmus a pomůže modulu.
* Úplný abecední seznam algoritmů a modulů v Machine Learning Studio najdete v tématu [seznam A-Z modulů Machine Learning Studio] [ a-z-list] Studio algoritmu strojového učení a pomůže modulu.
* Pokud si chcete stáhnout a vytisknout diagram s přehledem funkcí nástroje Machine Learning Studio, nahlédněte do tématu [Diagram s přehledem možností nástroje Machine Learning Studio](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Poznámky a definice terminologie pro strojové učení algoritmu tahák

* Tento tahák k algoritmům nabízí návrhy jsou přibližné pravidla nástroje jezdce. Některé můžou ohnuty a některé mohou být flagrantly došlo k porušení. To je určený pro návrh výchozí bod. Nebojte se ke spuštění head-to-head konkurence mezi několik algoritmů s vašimi daty. Není jednoduše náhradu pochopení principů jednotlivých algoritmů a Principy systému, která vygenerovala vaše data.

* Každý algoritmu strojového učení má svůj vlastní styl nebo *indukční posun*. Pro určitý problém může být vhodné několik algoritmů a jeden algoritmus může být vhodný lepší než jiné. Ale to není vždy možné předem znát, což je nejvhodnější. V takových případech patří několik algoritmů společně v stručný přehled stylů. Vhodné strategie může být jeden algoritmus a pokud výsledky ještě nejsou uspokojivé kvality, akci ostatní. Tady je příklad z [galerii Azure AI](http://gallery.cortanaintelligence.com/) experimentu, který se pokusí několik algoritmů na stejná data a porovnává výsledky: [porovnání třídění roc: písmeno rozpoznávání](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* Existují tři hlavní kategorie služby machine learning: **pod dohledem learning**, **supervize**, a **zpětnovazebnému učení**.

  * V **pod dohledem learning**, každý datový bod je označené nebo související s kategorií nebo hodnoty, které vás zajímají.  Příklad popisku kategorií se přiřazení bitovou kopii jako "cat" nebo "pes.  Příklad popisku hodnoty je prodejní cena přidružené používané automobilu. Cílem učení je cílem studovat mnoho označených příklady takových a pak bude schopen provést předpovědi budoucích datových bodů. Například identifikující novou fotografie s správné zvířat nebo přiřazení k jiné automobilů přesné prodejní ceny. Toto je Oblíbené a vhodné typ služby machine learning. Všechny moduly v Azure Machine Learning jsou pod dohledem učení se supervizí s výjimkou [Clustering K-Means][k-means-clustering].

  * V **supervize**, datové body mají se k nim má přiřazené žádné popisky. Místo toho je cílem algoritmus učení bez dohledu uspořádání dat nějakým způsobem nebo k popisu struktury. To může znamenat seskupení do clusterů, stejně jako K-means nebo vyhledání různé způsoby prohlížení komplexních datových, aby se zobrazovala jednodušší.

  * V **zpětnovazebnému učení**, získá algoritmu a vybrat akci v reakci na každý datový bod. Je běžným přístupem v robotika, kde je sada údajů snímačů přes v jednom bodě v čase na datový bod a algoritmus musíte zvolit robot další akci. Je také přirozený pro Internet of Things aplikací. Algoritmus učení také obdrží signál reward krátkou dobu později, určující, jak kvalitní byl rozhodnutí. Na základě toho algoritmus změní svoji strategii, abyste dosáhli nejvyšší potřebu. Aktuálně nejsou žádné zpětnovazební učení algoritmu moduly ve službě Azure ML.

* **Metody Bayesova** předpokládá statisticky nezávislé datové body. To znamená, že je s ostatními bez korelace nejsou nemodelované variabilita jeden datový bod, to znamená, ho nemůže být předpovězen. Například pokud jsou data zaznamenává počet minut, než dorazí další subway trénování, dvěma měřeními rozebrali za den se statisticky nezávislé. Ale dvěma měřeními rozebrali jednu minutu nejsou statisticky nezávislé – jednu hodnotu vysoce prediktivní druhé hodnoty.

* **Vylepšené regrese rozhodovacího stromu** využívá funkce překrývají nebo interakci mezi funkcemi. Která znamená, že, během čárky daná data hodnotu jednu funkci prediktivní trochu jiné hodnoty. Například v denní data o teplotě vysoká nebo Nízká, znalost nejnižší teplota daného dne umožňuje přiměřené odhad toho, že. Informace obsažené v dvě funkce se poněkud redundantní.

* Klasifikace dat do více než dvou kategorií lze provést buď pomocí ze své podstaty roc třídění, nebo zkombinováním sadu třídění dvě třídy do **komplet**. V metodě komplet je zvláštní třídění dvě třídy pro každou třídu – každé z nich data odděluje do dvou kategorií: "Tato třída" a "není této třídy." Potom tyto třídění hlasovat pro správné přiřazení datového bodu. Je to provozní Princip za [One-vs-All Multiclass][one-vs-all-multiclass].

* Několik metod, včetně logistické regrese a Bayes point machine, předpokládají **lineární třídy hranice**. To znamená předpokládají, že jsou hranice mezi třídami přibližně přímé spojnic (nebo v případě obecnější hyperplanes). Často je jím charakteristiku data, která si nejste jisti až poté, co jste se pokusili oddělení, ale je to něco, obvykle je možné zjistit předem vizualizací. Pokud hranice třídy vypadají velmi nestandardní, Zůstaňte u rozhodovacích stromů, rozhodnutí Džungle, podporu vektoru počítače nebo neuronových sítí.

* Neuronové sítě je možné s kategorií proměnné tak, že vytvoříte **fiktivní proměnná** pro každou kategorii ji nastavíte na 1 v případech, kde platí kategorii, 0, pokud tomu tak není.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
