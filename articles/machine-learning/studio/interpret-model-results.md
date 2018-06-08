---
title: Interpretovat výsledky modelu v Machine Learning | Microsoft Docs
description: Jak vybrat optimální parametr nastavit pro algoritmus pomocí a vizualizace score model výstupy.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6230e5ab-a5c0-4c21-a061-47675ba3342c
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: e62f4716a95a6c1840afde27361eeb756abff335
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837000"
---
# <a name="interpret-model-results-in-azure-machine-learning"></a>Interpretovat výsledky modelu v Azure Machine Learning
Toto téma vysvětluje, jak interpretovat výsledky předpovědi v Azure Machine Learning Studio a vizualizaci. Po Trénink modelu a provádí předpovědi nad jeho ("vypočítat skóre modelu"), musíte pochopit a interpretovat výsledek předpovědi.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Existují čtyři hlavní druhy strojového učení modely v Azure Machine Learning:

* Klasifikace
* Clustering
* Regrese
* Doporučené systémy

Moduly používané pro předpověď nad tyto modely jsou:

* [Určení skóre modelu] [ score-model] modulu pro klasifikaci a regrese
* [Přiřadit clustery] [ assign-to-clusters] modulu pro clustering
* [Stanovení skóre doporučené Matchbox] [ score-matchbox-recommender] systémů doporučení

Tento dokument vysvětluje, jak interpretovat výsledky předpovědi pro každý z těchto modulů. Přehled těchto modulů najdete v tématu [jak zvolit parametry, které Optimalizujte algoritmy v Azure Machine Learning](algorithm-parameters-optimize.md).

Toto téma nabízí interpretace předpovědi, ale ne zkušební modelu. Další informace o tom, jak vyhodnotit modelu najdete v tématu [postup vyhodnocení modelu výkon v Azure Machine Learning](evaluate-model-performance.md).

Pokud jsou nové pro Azure Machine Learning a potřebujete další pomoc vytvoření jednoduchého experimentu začít, najdete v článku [vytvoření jednoduchého experimentu v nástroji Azure Machine Learning Studio](create-experiment.md) v Azure Machine Learning Studio.

## <a name="classification"></a>Klasifikace
Existují dvě podkategorie klasifikaci problémy:

* Problémy s pouze dvěma třídami (dvě třídy nebo binární klasifikace)
* Problémy s více než dvě třídy (třídy více klasifikace)

Azure Machine Learning obsahuje různé moduly pro každý z těchto typů klasifikace řešení, ale jsou podobné metody pro interpretace jejich výsledky předpovědi.

### <a name="two-class-classification"></a>Klasifikace dva – třída
**Příklad experimentu**

Příklad two-class klasifikace problému je klasifikace iris květy. Tato úloha je klasifikovat iris květy podle jejich funkce. Sada dat Iris uvedené v Azure Machine Learning je podmnožinou oblíbených [Iris datové sady](http://en.wikipedia.org/wiki/Iris_flower_data_set) obsahující instance jen dvě květinové druhů (třídy 0 a 1). Existují čtyři funkce pro každý květina (sepal délka, šířka sepal, Okvětní lístek délku a šířku Okvětní lístek).

![Snímek obrazovky iris experimentu](./media/interpret-model-results/1.png)

Obrázek 1. Iris two-class klasifikace problému experimentu

Experiment nebylo provedeno k vyřešení tohoto problému, jak je znázorněno na obrázku 1. Two-class boosted decision stromu modelu byla vycvičena a vypočítat skóre. Teď můžete vizualizovat předpovědi výsledků [Score Model] [ score-model] modulu kliknutím na výstupní port modulu [Score Model] [ score-model] modul a potom kliknutím na **vizualizovat**.

![Modul určení skóre modelu](./media/interpret-model-results/1_1.png)

Po výběru této možnosti vyhodnocování výsledky jak je znázorněno na obrázku 2.

![Výsledky iris two-class klasifikace experimentu](./media/interpret-model-results/2.png)

Obrázek 2. Vizualizace výsledků skóre v klasifikaci two-class

**Výsledek vyhodnocení**

Existují šesti sloupce v tabulce výsledků. Levé čtyři sloupce jsou čtyři funkce. Právo dva sloupce, popisky vyhodnocení a skóre pro Magnitudu Pravděpodobnostech jsou výsledky předpovědi. Skóre pro Magnitudu Pravděpodobnostech sloupci se zobrazuje pravděpodobnost náležející květiny kladné třídy (třídy 1). První číslo v existuje sloupec (0.028571) znamená je například 0.028571 pravděpodobnost, že první květina patří do třídy 1. Popisky vyhodnocení sloupci se zobrazuje předpokládaných třídu pro každý květina. To je založené na sloupci Pravděpodobnostech vypočítat skóre. Pokud je větší než 0,5 scored pravděpodobnost květiny, je předpovědět jako třída 1. Jinak je předpovědět jako třída 0.

**Publikování webové služby**

Po předpovědi výsledky byly porozuměl jsem jim a zvukových, která, experimentu lze publikovat jako webovou službu, aby mohli nasadit v různých aplikacích a pojmenujte ji k získání třídy předpovědi na všechny nové květina iris. Zjistěte, jak změnit výukový experiment do vyhodnocování experiment a publikujete ji jako webové služby, najdete v tématu [publikovat webovou službu Azure Machine Learning](walkthrough-5-publish-web-service.md). Tento postup obsahuje vyhodnocování experiment, jak je vidět na obrázku 3.

![Snímek obrazovky vyhodnocování experimentu](./media/interpret-model-results/3.png)

Obrázek 3. Vyhodnocování experimentu iris two-class klasifikace problému

Teď je potřeba nastavit vstupní a výstupní pro webovou službu. Vstup je pravý vstupní port z [Score Model][score-model], což je květina Iris funkce vstup. Volba výstup závisí na tom, jestli máte zájem předpokládaných – třída (scored popisek), scored pravděpodobnost nebo obojí. V tomto příkladu se předpokládá, že máte zájem obě. Pokud chcete vybrat požadovaný výstupní sloupce, použijte [výběr sloupců v datové sadě] [ select-columns] modulu. Klikněte na tlačítko [výběr sloupců v datové sadě][select-columns], klikněte na tlačítko **spustit selektor sloupců**a vyberte **popisky vyhodnocení** a **Scored Pravděpodobnostech**. Po nastavení výstupní port modulu [výběr sloupců v datové sadě] [ select-columns] a znovu spustit, byste měli být připravena k publikování vyhodnocování experimentu jako webovou službu kliknutím **publikování webové služby** . Konečný experiment vypadá jako na obrázku 4.

![Experiment iris klasifikace dva – třída](./media/interpret-model-results/4.png)

Obrázek 4. Konečný experiment vyhodnocování iris two-class klasifikace problému

Po spuštění webové služby a zadejte hodnoty některé funkce testovací instance, vrátí výsledek dvou čísel. První číslo je scored popisek a druhý je scored pravděpodobnosti. Tato květina je předpovědět jako třída 1 s 0.9655 pravděpodobnosti.

![Interpretace score model testů](./media/interpret-model-results/4_1.png)

![Vyhodnocování výsledků testu](./media/interpret-model-results/5.png)

Obrázek 5. Webové služby výsledek iris two-class klasifikace

### <a name="multi-class-classification"></a>Klasifikace více – třída
**Příklad experimentu**

V této experimentu můžete provést úlohu písmeno rozpoznávání jako příklad více třídami klasifikace. Třídění pokusí odhadnout určité písmenem (třída) na základě některé ručně psané atribut hodnot z bitové kopie ručně psané extrahovat.

![Příklad rozpoznávání písmeno](./media/interpret-model-results/5_1.png)

V Cvičná data jsou 16 funkce, které se extrahují z bitové kopie ručně psané písmeno. 26 písmena formuláři naše 26 třídy. Obrázek 6 ukazuje experimentu, který bude trénování modelu více třídami klasifikace pro rozpoznávání písmeno a předvídání na stejné funkce nastavit na testovací datové sady.

![Písmeno rozpoznávání více třídami klasifikace experimentu](./media/interpret-model-results/6.png)

Obrázek 6. Písmeno rozpoznávání více třídami klasifikace problému experimentu

Vizualizace výsledků [Score Model] [ score-model] modulu kliknutím na výstupní port modulu [Score Model] [ score-model] modul a potom kliknete na **Vizualizovat**, obsah byste měli vidět, jak je znázorněno na obrázku 7.

![Určení skóre modelu výsledky](./media/interpret-model-results/7.png)

Na obrázku 7. Vizualizace výsledků skóre modelu v klasifikaci s více – třída

**Výsledek vyhodnocení**

Levé 16 sloupce představují funkce hodnoty testovací sada. Sloupce s názvy jako skóre pro Magnitudu Pravděpodobnostech pro třídu "XX" jsou stejně jako sloupec skóre pro Magnitudu Pravděpodobnostech v případě, že dvě třídy. Ukazují pravděpodobnost která odpovídající položku spadá do určité třídy. Například pro první položku není 0.003571 pravděpodobnost, že se jedná "A", 0.000451 pravděpodobnost, že je "B" a tak dále. Poslední sloupec (popisky vyhodnocení) je stejný jako popisky vyhodnocení v případě, že dvě třídy. Vybere třídu s největší pravděpodobností scored jako předpokládaných třída odpovídající položky. Například pro první položku scored popisek je "F" vzhledem k tomu, že má největší pravděpodobnost jako "F" (0.916995).

**Publikování webové služby**

Můžete také získat scored popisek pro každou položku a pravděpodobnost vzniku scored popisku. Základní logika se má najít největší pravděpodobnost mezi scored pravděpodobnostech. K tomuto účelu, budete muset použít [spustit skript jazyka R] [ execute-r-script] modulu. Kód R je vidět na obrázku 8 a výsledek experimentu je vidět na obrázku 9.

![Ukázkový kód R](./media/interpret-model-results/8.png)

Obrázek 8. Kód R pro extrahování popisky vyhodnocení a spojena pravděpodobnost popisků

![Výsledek experimentu](./media/interpret-model-results/9.png)

Obrázek 9. Konečný experiment vyhodnocování písmeno rozpoznávání více třídami klasifikace problému

Po publikování a spuštění webové služby a zadejte některé funkce vstupní hodnoty vrácených výsledků vypadá jako obrázek 10. Toto ručně psané písmeno s jeho extrahované 16 funkce, je jako "T", s 0.9715 pravděpodobnosti předpovědět.

![Interpretace modul skóre test](./media/interpret-model-results/9_1.png)

![Výsledek testu](./media/interpret-model-results/10.png)

Obrázek 10. Webové služby výsledek více třídami klasifikace

## <a name="regression"></a>Regrese
Regrese problémy se liší od klasifikaci problémy. Problém klasifikace které se snažíte předpovědi diskrétních třídy, jako je například, které patří třída iris květina k. Ale jak můžete vidět v následujícím příkladu regrese problému, které se snažíte k předpovědi souvislých proměnná, jako například ceny automobilu.

**Příklad experimentu**

Použijte předpověď cen automobilů jako příkladu pro regresní. Pokoušíte se předpovídat cenu automobilu podle jeho funkce, včetně Ujistěte se, typ paliva, typ těla zprávy a kolečka jednotky. Obrázek 11 ukazuje experimentu.

![Experiment regrese automobilů cena](./media/interpret-model-results/11.png)

Obrázek 11. Experiment problém regrese automobilů cena

Vizualizace [Score Model] [ score-model] modul, výsledkem vypadá jako obrázek 12.

![Vyhodnocování výsledky pro problém předpověď cen automobilů](./media/interpret-model-results/12.png)

Obrázek 12. Vyhodnocování výsledek problém předpověď cen automobilů

**Výsledek vyhodnocení**

Scored popisky je sloupec výsledků v této vyhodnocování výsledek. Čísla, která jsou předpokládaných ceny pro každý Auto.

**Publikování webové služby**

Můžete publikovat experimentu regrese do webové služby a volání pro předpověď cen automobilů stejným způsobem jako v případě použití two-class klasifikace.

![Vyhodnocování experimentu pro problém regrese automobilů cena](./media/interpret-model-results/13.png)

Obrázek 13. Vyhodnocování experimentu problémem regrese automobilů cena

Spuštění webové služby, bude jako výsledek vypadá jako obrázek 14. Předpokládaných ceny pro tento car je $15,085.52.

![Interpretace vyhodnocování modulu testování](./media/interpret-model-results/13_1.png)

![Vyhodnocování výsledky modulu](./media/interpret-model-results/14.png)

Obrázek 14. Webové služby výsledek problémem regrese automobilů cena

## <a name="clustering"></a>Clustering
**Příklad experimentu**

K vytvoření clustering experiment znovu použijeme Iris datové sady. Zde můžete filtrovat se popisky třídy v sadě dat tak, aby pouze funkce a lze použít pro clustering. V této iris případ použití, zadejte počet clusterů, které mají být dva během procesu školení, což znamená, že by clusteru květy do dvou tříd. Experiment vidíte na obrázku 15.

![Experiment Iris clustering problém](./media/interpret-model-results/15.png)

Obrázek 15. Experiment Iris clustering problém

Clustering se liší od klasifikace, v tom, že v datové sadě školení nemá základů pravdivosti popisky samostatně. Clustering skupiny instancí školení datové sady do různých clusterů. Během procesu školení modelu popisků položky podle učení rozdíly mezi jejich funkce. Potom pro cvičný model slouží k další klasifikaci budoucí položky. Existují dvě části výsledků, které jsme zajímají v rámci clustering problém. První část je označování trénovací datové sady a druhý je klasifikace novou sadu dat pro cvičný model.

Kliknutím na levý výstupní port modulu můžete vizualizovat první část výsledku [Train Model clusteringu] [ train-clustering-model] a pak levým na **vizualizovat**. Tato vizualizace se zobrazí v obrázku 16.

![Clustering výsledek](./media/interpret-model-results/16.png)

Obrázek 16. Vizualizace clustering výsledek trénovací datové sady

Výsledek druhou část clustering nové položky s modelem vyškolení clustering vidíte na obrázku 17.

![Vizualizace výsledků clustering](./media/interpret-model-results/17.png)

Obrázek 17. Vizualizace clustering výsledek pro nové sady dat

**Výsledek vyhodnocení**

I když výsledky ze dvou částí kmen z různých experimentu fázích, vypadají stejně a vyhodnocovány stejným způsobem. První čtyři sloupce jsou funkce. Poslední sloupec přiřazení, je výsledkem předpovědi. Položky přiřazené stejné číslo jsou předpovědět být ve stejném clusteru, který je že sdílejí podobnosti nějakým způsobem (Tento experiment používá výchozí Euclidean vzdálenost metriku). Protože jste určili počet clusterů, které mají být 2, položky v přiřazení jsou označeny 0 nebo 1.

**Publikování webové služby**

Můžete publikovat clustering experimentu do webové služby a volání pro clustering předpovědi stejným způsobem jako klasifikace two-class případy použití.

![Vyhodnocování experimentu pro clustering problém iris](./media/interpret-model-results/18.png)

Obrázek 18. Vyhodnocování experimentu problémem iris clustering

Po spuštění webové služby na vrácený výsledek vypadá jako obrázek 19. Tato květina je předpovědět v clusteru 0.

![Test interpretovat vyhodnocování modulu](./media/interpret-model-results/18_1.png)

![Vyhodnocování výsledek modulu](./media/interpret-model-results/19.png)

Obrázek 19. Webové služby výsledek iris two-class klasifikace

## <a name="recommender-system"></a>Doporučené systému
**Příklad experimentu**

Pro systémy doporučené, můžete problém doporučení restaurace jako příklad: Doporučujete restaurace pro zákazníky na základě jejich historie hodnocení. Vstupní data se skládá ze tří částí:

* Restaurace hodnocení zákazníků
* Data funkce zákazníka
* Data funkce restaurace

Existuje několik věcí, můžeme dělat s [Train Matchbox doporučené] [ train-matchbox-recommender] modulu v Azure Machine Learning:

* Předpověď hodnocení pro daného uživatele a položek
* Doporučeným položky pro daného uživatele
* Vyhledá uživatele týkající se daného uživatele
* Hledání položek související s danou položku

Můžete zvolit, co chcete udělat tak, že výběr ze čtyř možností v **doporučené předpovědi druh** nabídky. Zde si můžete projít všechny čtyři scénáře.

![Doporučené matchbox](./media/interpret-model-results/19_1.png)

Typické experimentu Azure Machine Learning pro systém doporučené vypadá jako obrázek 20. Informace o tom, jak používat tyto doporučené moduly systému najdete v tématu [Train matchbox doporučené] [ train-matchbox-recommender] a [skóre matchbox doporučené] [ score-matchbox-recommender].

![Doporučené systému experimentu](./media/interpret-model-results/20.png)

Obrázek 20. Doporučené systému experimentu

**Výsledek vyhodnocení**

**Předpověď hodnocení pro daného uživatele a položek**

Výběrem **hodnocení předpovědi** pod **doporučené předpovědi druh**, žádáme doporučené systému k předvídání hodnocení pro daného uživatele a položku. Vizualizace z [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup vypadá jako obrázek 21.

![Stanovení skóre výsledek doporučené systému – hodnocení předpovědi](./media/interpret-model-results/21.png)

Obrázek 21. Vizualizace výsledků skóre doporučené systému – hodnocení předpovědi

První dva sloupce jsou páry položka uživatele poskytované vstupní data. Třetí sloupec je předpokládaných hodnocení uživatele určité položky. Například v prvním řádku, zákazník U1048 je předpovědět na rychlost restaurace 135026 jako 2.

**Doporučeným položky pro daného uživatele**

Výběrem **položky doporučení** pod **doporučené předpovědi druh**, nemůže ověřit systému doporučené doporučeným položky pro daného uživatele. Poslední parametr vybrat v tomto scénáři je *doporučená výběr položek*. Možnost **z hodnocení položky (pro vyhodnocení modelu)** je určen pro vyhodnocení modelu během procesu školení. Pro tuto fázi předpovědi vybereme možnost **z všechny položky**. Vizualizace z [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup vypadá jako obrázek 22.

![Výsledek skóre doporučené systému – položka doporučení](./media/interpret-model-results/22.png)

Obrázek 22. Vizualizace výsledků skóre doporučené systému – položka doporučení

První šesti sloupců představuje daného uživatele ID doporučit položky, jako poskytované vstupní data. Pět sloupce představují doporučené uživateli v sestupném pořadí podle relevance položky. Například v prvním řádku, je většina doporučené restaurace pro zákazníka U1048 134986, za nímž následuje 135018, 134975, 135021 a 132862.

**Vyhledá uživatele týkající se daného uživatele**

Výběrem **související uživatelé** pod **doporučené předpovědi druh**, nemůže ověřit systému doporučené najít související uživatelům daného uživatele. Související uživatelé jsou uživatelé, kteří mají podobné předvolby. Poslední parametr vybrat v tomto scénáři je *související výběr uživatele*. Možnost **z uživatelů, hodnocení položky (pro vyhodnocení modelu)** je určen pro vyhodnocení modelu během procesu školení. Zvolte **ze všech uživatelů** pro tuto fázi předpovědi. Vizualizace z [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup vypadá jako obrázek 23.

![Výsledek skóre doporučené systému – související uživatelé](./media/interpret-model-results/23.png)

Obrázek 23. Vizualizace výsledků skóre doporučené systému – související uživatelé

První šesti sloupců zobrazuje že ID potřeby najít související uživatele, protože poskytované vstupních dat daného uživatele. Pět sloupců ukládat předpokládaných související uživatelé uživatele v sestupném pořadí podle závažnosti. Například v prvním řádku, je nejdůležitější zákazník pro zákazníka U1048 nástroje U1051, za nímž následuje U1066, U1044, U1017 a U1072.

**Hledání položek související s danou položku**

Výběrem **související položky** pod **doporučené předpovědi druh**, žádáme doporučené systém k nalezení souvisejících položek pro danou položku. Související položky se nejpravděpodobněji být líbilo stejným uživatelem položky. Poslední parametr vybrat v tomto scénáři je *související výběr položek*. Možnost **z hodnocení položky (pro vyhodnocení modelu)** je určen pro vyhodnocení modelu během procesu školení. Vybereme možnost **z všechny položky** pro tuto fázi předpovědi. Vizualizace z [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup vypadá jako obrázek 24.

![Výsledek skóre doporučené systému – související položky](./media/interpret-model-results/24.png)

Obrázek 24. Vizualizace výsledků skóre doporučené systému – související položky

První šesti sloupců představuje daná položka ID, které jsou potřeba k vyhledání související položky, protože poskytuje vstupní data. Pět sloupců ukládat předpokládaných související položky položky v sestupném pořadí z hlediska relevance. Například v prvním řádku, je nejdůležitější položku pro položku 135026 135074, za nímž následuje 135035, 132875, 135055 a 134992.

**Publikování webové služby**

Proces publikování těchto experimenty jako webové služby, chcete-li získat předpovědi je podobné pro každý ze čtyř scénářů. Zde jsme trvat druhý scénář (doporučujeme položky pro daného uživatele) jako příklad. Můžete provést stejný postup s další tři.

Ukládání vyškolení doporučené systému jako modulu trained model a filtrování vstupní data pro sloupec ID jednoho uživatele, jak si vyžádal, můžete spojit experimentu jako obrázek 25 a publikujete ji jako webovou službu.

![Vyhodnocování experimentu restaurace doporučení problému](./media/interpret-model-results/25.png)

Obrázek 25. Vyhodnocování experimentu restaurace doporučení problému

Spuštění webové služby, 26 obrázek vypadá na vrácený výsledek. Pět doporučené restaurace pro uživatele U1048 jsou 134986, 135018, 134975, 135021 a 132862.

![Ukázka doporučené systémové služby](./media/interpret-model-results/25_1.png)

![Ukázkový experiment výsledky](./media/interpret-model-results/26.png)

Obrázek 26. Webové služby výsledek restaurace doporučení problému

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
