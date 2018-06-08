---
title: Strojového učení rychlý přehled algoritmů | Microsoft Docs
description: Tisknutelná strojového učení rychlý přehled algoritmů umožňuje vybrat správné algoritmus prediktivního modelu v Azure Machine Learning Studio.
keywords: rychlý přehled algoritmů, tahák, algoritmu strojového učení
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
ms.openlocfilehash: a448d6931330f7b2f0730add65473097bb2b5a57
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833559"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Tahák k algoritmům služby Machine Learning pro Microsoft Azure Machine Learning Studio
**Aktualizace Microsoft Azure Machine Learning algoritmus cheaty list** umožňuje vybrat správné algoritmus pro model prediktivní analýzy.

[Azure Machine Learning Studio](https://studio.azureml.net/) má velké knihovny algoritmů z ***regrese***, ***klasifikace***, ***clustering***, a  ***detekce anomálií*** rodiny. Každý je určena pro různé typy machine learning problému adres.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Stáhnout: Strojového učení algoritmu – tahák
**Stáhněte si tahák zde: [Machine Learning algoritmus cheaty list (11 × 17 palců.)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Machine Learning algoritmus tahák: Zjistěte, jak vybrat algoritmus Machine Learning.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Stáhnout a vytisknout Machine Learning algoritmus cheaty list ve velikosti tabloid získat pomoc s výběrem algoritmu a mít je k dispozici.

> [!NOTE]
> Najdete v článku [jak zvolit algoritmy pro Microsoft Azure Machine Learning](algorithm-choice.md) podrobné příručce k použití této tahák.
> 
> 

## <a name="more-help-with-algorithms"></a>Další pomoc s algoritmy
* Pomoc při použití této tahák pro výběr správné algoritmus plus podrobnější diskuzi o různé typy algoritmů machine learningu a způsobu jejich použití najdete v tématu [jak zvolit algoritmy pro Microsoft Azure Machine Learning](algorithm-choice.md).
* Infografice ke stažení, který popisuje algoritmy a obsahuje příklady, najdete v části [ke stažení Infografice: strojového učení základy s příklady algoritmus](basics-infographic-with-algorithm-examples.md).
* Seznam podle kategorie všechny algoritmy strojového učení dostupné v nástroji Machine Learning Studio najdete v tématu [inicializovat Model] [ initialize-model] v Machine Learning Studio algoritmus a pomáhají modulu.
* Dokončení abecední seznam algoritmů a modulů v Machine Learning Studio najdete v tématu [seznam A-Z modulů Machine Learning Studio] [ a-z-list] v Machine Learning Studio algoritmus a pomoci modulu.
* Pokud si chcete stáhnout a vytisknout diagram s přehledem funkcí nástroje Machine Learning Studio, nahlédněte do tématu [Diagram s přehledem možností nástroje Machine Learning Studio](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Poznámky a definice terminologie pro strojové učení algoritmu tahák

* Návrhy nenabízí tento rychlý přehled algoritmů jsou přibližné pravidla z jezdce. Některé můžete ohnuty a některé můžou být flagrantly došlo k porušení. Slouží k návrhu počáteční bod. Není třeba obávat spustit head-to-head konfliktům mezi několik algoritmů na vaše data. Neexistuje žádné jednoduše substitute pro seznámení se zásadami jednotlivých algoritmů a pochopení systému, která vygenerovala data.

* Každý algoritmu strojového učení má svůj vlastní styl nebo *induktivní odchylka*. Pro určitý problém může být vhodné několik algoritmů a jeden algoritmus může být lépe odpovídaly než jiné. Ale není vždy možné předem znát, což je nejlepší. V takových případech jsou několik algoritmů uvedené v listu podvést společně. Vhodná strategie by mohla být opakujte jeden algoritmus a pokud nejsou výsledky ještě uspokojivé, zkuste jiné. Tady je příklad z [Azure AI Galerie](http://gallery.cortanaintelligence.com/) experimentu několik algoritmů proti stejná data se pokusí a porovná výsledky: [porovnat více třída třídění: písmeno rozpoznávání](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* Existují tři hlavní kategorie machine learning: **učení se supervizí**, **supervize**, a **posílení learning**.

  * V **učení se supervizí**, každý datový bod je označené nebo ve spojení s kategorie nebo hodnoty, které vás zajímají.  Příklad kategorií popisku je přiřazování bitovou kopii jako 'cat' nebo 'PSA.  Příklad popisku hodnota je prodejní ceny automobilu použitých přidružené. Cílem pod dohledem learning je studovat mnoho s popiskem příklady takovéto a potom mohli provádět předpovědi o budoucích datových bodů. Například identifikace nové fotografie s správné zvíře nebo přiřazení k jiné automobilů přesné prodej ceny. Toto je Oblíbené a užitečné typ strojové učení. Všechny moduly v Azure Machine Learning jsou pod dohledem učení algoritmy s výjimkou [K-Means Clustering][k-means-clustering].

  * V **supervize**, datové body mají žádné popisky s nimi spojených. Cílem algoritmus učení bez dohledu místo toho je k uspořádání dat nějakým způsobem nebo k popisu jeho struktury. To může znamenat seskupení do clusterů, protože K-means, nebo při hledání různé způsoby prohlížení komplexní data tak, aby se jednodušší.

  * V **posílení learning**, algoritmus získá vybrat akci v reakci na každý datový bod. Je běžný postup v robotics, kde sada odečty snímačů v jednom bodě v čase je hodnota datového bodu, a algoritmus musíte zvolit další akce robot. Je také přírodní pro Internet věcí aplikace. Algoritmus učení dále přijímá signál potřebu po krátkou dobu později, znamenající, jak dobrý byla rozhodnutí. Na základě algoritmus upravuje jeho strategie pro dosažení nejvyšší potřebu. Aktuálně neexistují žádné posílení učení algoritmu modulů v Azure ML.

* **Metody Bayesova** se předpokládá statisticky nezávislé datových bodů. To znamená, že je bez korelace nejsou s ostatními unmodeled variabilita jeden datový bod, tedy nelze předpovědět. Například pokud jsou data dále zaznamenávána počet minut, dokud nebude další train podzemní dráha dorazí, dvě měření pořízených za den od sebe jsou statisticky nezávislé. Ale dvě měření pořízených minutu od sebe nejsou statisticky nezávislé – hodnota jednoho je vysoce prediktivní druhá hodnota.

* **Rozhodovací strom regrese boosted** využívá funkce překrývají nebo interakce mezi funkcí. To znamená, že kdykoli daná data hodnotu jedna z funkcí je poněkud prediktivní hodnoty jiného. Například v denních dat vysokou/low teploty znalost nízkou teploty dne vám umožní zajistit přiměřené odhad pro vysoké. Informace obsažené v tyto dvě funkce je poněkud redundantní.

* Klasifikace dat do více než dvou kategorií lze provést pomocí buď třídění ze své podstaty více tříd, nebo kombinace sady two-class třídění do **komplet**. V metodě komplet je samostatný třídění two-class pro každou třídu – každé z nich data dělí do dvou kategorií: "Tato třída" a "není tato třída." Potom tyto třídění hlasovat o správné přiřazení datového bodu. Toto je provozní Princip za [One-vs-All Multiclass][one-vs-all-multiclass].

* Předpokládejme, několik metod, včetně logistic regression a počítač bodu Bayes, **lineární třída hranice**. To znamená že předpokládají, že jsou hranice mezi třídami přibližně přímé řádky (nebo hyperplanes v případě, že další obecné). Často je typické pro data, která neznáte až poté, co jste se pokusili oddělení, ale je něco, co obvykle můžete poznat předem vizualizace. Pokud třída hranice vypadají velmi nestandardní, přilepit s rozhodovací stromy, decision Džungle, podporu vektoru počítače nebo neuronové sítě.

* Neuronové sítě lze použít s kategorií proměnné tak, že vytvoříte **fiktivní proměnné** pro každou kategorii jeho nastavení na hodnotu 1 v případech, kde platí kategorii, 0, kde nepodporuje.


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
