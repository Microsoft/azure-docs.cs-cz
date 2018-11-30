---
title: Interpretace výsledků modelu ve službě Machine Learning Studio – Azure | Dokumentace Microsoftu
description: Jak vybrat optimální parametr nastavit pro objekt pomocí algoritmu a vypíše vizualizovat určení skóre modelu.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: c06b05989270b4199e5d97ca399a1411b73a702b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310265"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio"></a>Interpretace výsledků modelu v nástroji Azure Machine Learning Studio
Toto téma vysvětluje, jak vizualizovat a interpretace výsledků predikce v Azure Machine Learning Studio. Po Trénink modelu a provádí predictions nad rámec jeho ("skóre modelu"), musíte pochopit a interpretace výsledků předpovědí.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Existují čtyři hlavní typy modelů v Azure Machine Learning strojového učení:

* Klasifikace
* Clustering
* Regrese
* Doporučené systémy

Moduly používané k předpovědi na základě těchto modelů a mají:

* [Určení skóre modelu] [ score-model] modul pro klasifikačních a regresních
* [Přiřadit ke clusterům] [ assign-to-clusters] modul pro vytváření clusterů
* [Skóre Matchbox doporučené] [ score-matchbox-recommender] systémů doporučení

Tento dokument popisuje, jak interpretovat výsledky předpovědí pro každý z těchto modulů. Přehled těchto modulů najdete v tématu [jak vybrat parametry pro optimalizaci algoritmů ve službě Azure Machine Learning](algorithm-parameters-optimize.md).

Toto téma řeší interpretace predikcí, ale ne zkušební modelu. Další informace o tom, jak vyhodnotit váš model, najdete v části [jak vyhodnotit výkon modelu ve službě Azure Machine Learning](evaluate-model-performance.md).

Pokud teprve začínáte Azure Machine Learning a potřebujete pomoc při vytvoření jednoduchého experimentu Začínáme, přečtěte si téma [vytvoření jednoduchého experimentu v nástroji Azure Machine Learning Studio](create-experiment.md) v Azure Machine Learning Studio.

## <a name="classification"></a>Klasifikace
Existují dva podkategorie klasifikace problémy:

* Problémy s pouze dvěma třídami (dvěma třídami nebo binární klasifikace)
* Problémy s více než dvě třídy (roc klasifikace)

Azure Machine Learning má různé moduly pro každý z těchto typů klasifikace řešení, ale jsou podobné metody pro interpretaci jejich výsledky předpovědí.

### <a name="two-class-classification"></a>Klasifikace dvěma třídami
**Příklad experimentu**

Příklad dvěma třídami klasifikace problému je klasifikace iris květin. Úloha je klasifikace iris květin na základě jejich funkcí. Datové sady Iris k dispozici ve službě Azure Machine Learning je podmnožinou Oblíbené [datovou sadu Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) obsahující instance jen dva květinové druhů (třídy 0 a 1). Existují čtyři funkce pro každou datovou (sepal length, sepal width, petal length a petal width).

![Snímek obrazovky iris experimentu](./media/interpret-model-results/1.png)

Obrázek 1. Experiment problém dvěma třídami klasifikace Iris

Experiment byla provedena pro vyřešení tohoto problému, jak je znázorněno na obrázku 1. Model dvěma třídami Posílený rozhodovací strom byly školení a skóre. Teď můžete vizualizovat výsledky předpovědí z [Score Model] [ score-model] kliknutím na výstupní port modulu [Score Model] [ score-model] modul a pak levým na **vizualizovat**.

![Modul určení skóre modelu](./media/interpret-model-results/1_1.png)

Tím se zobrazí výsledky vyhodnocení jak je znázorněno na obrázku 2.

![Výsledky testu dvěma třídami klasifikace iris](./media/interpret-model-results/2.png)

Obrázek 2. Vizualizace výsledků určení skóre modelu v klasifikaci dvěma třídami

**Interpretace výsledků**

Existuje šest sloupců v tabulce výsledků. Levé čtyři sloupce obsahují čtyři funkce. Vpravo dva sloupce, popisky vyhodnocení a skóre pravděpodobnosti, jsou výsledky předpovědí. Skóre pravděpodobnosti sloupci se zobrazuje pravděpodobnosti, který patří květinu pozitivní třídy (třídy 1). První číslo v sloupec (0.028571) znamená, že je například 0.028571 pravděpodobnost, že první květinu patří do třídy 1. Popisky vyhodnocení sloupci se zobrazuje pro každou datovou předpokládaná třída. To je založen na sloupci skóre pravděpodobnosti. Pokud je větší než 0,5 skóre pravděpodobnosti květinu, očekává se jako třídy 1. V opačném případě shromážděno jako třída 0.

**Publikování webové služby**

Poté, co byly porozuměl jsem jim a neobsahuje zvukový výsledků předpovědí, experiment můžete publikovat jako webovou službu tak, že můžete nasadit v různých aplikacích a volat ho k získání třídy předpovědí na všechny nové květinu iris. Zjistěte, jak změnit výukového experimentu na hodnocení experiment a publikovat jako webovou službu, najdete v článku [publikovat webové služby Azure Machine Learning](walkthrough-5-publish-web-service.md). Tento postup poskytuje bodování experiment, jak je znázorněno na obrázku 3.

![Snímek obrazovky vyhodnocování experimentu](./media/interpret-model-results/3.png)

Obrázek 3. Vyhodnocování experiment problém dvěma třídami klasifikace iris

Teď je potřeba nastavit vstupů a výstupů pro webovou službu. Vstup je pravý vstupní port z [Score Model][score-model], což je květinu Iris funkce vstup. Volba výstupu závisí na tom, jestli vás zajímá předpokládaná třída (skóre popisek), skóre pravděpodobnosti nebo obojí. V tomto příkladu se předpokládá, že máte zájem o obojí. Chcete-li vybrat požadovaný výstupní sloupce, použijte [výběr sloupců v datové sadě] [ select-columns] modulu. Klikněte na tlačítko [výběr sloupců v datové sadě][select-columns], klikněte na tlačítko **spustit selektor sloupců**a vyberte **popisky vyhodnocení** a **Scored Pravděpodobnost**. Po nastavení na výstupní port modulu [výběr sloupců v datové sadě] [ select-columns] a znovu spustit, byste měli být připraveni publikovat bodovací experiment jako webové služby kliknutím **publikovat webové služby** . Konečný experiment vypadá jako obrázek 4.

![Experiment dvěma třídami klasifikace iris](./media/interpret-model-results/4.png)

Obrázek 4. Konečný experiment bodovací problematiky dvěma třídami klasifikace iris

Po spuštění webové služby a zadejte hodnoty některé funkce instance test vrátí výsledek dvou čísel. První číslo bude Vyhodnocená popisek a druhá skóre pravděpodobnosti. Tuto datovou očekává se, že jako třídy 1 s 0.9655 pravděpodobnosti.

![Test interpretace určení skóre modelu](./media/interpret-model-results/4_1.png)

![Vyhodnocení výsledků testů](./media/interpret-model-results/5.png)

Obrázek 5. Webové služby výsledek klasifikace iris dvěma třídami

### <a name="multi-class-classification"></a>Klasifikace víc tříd
**Příklad experimentu**

V tento experiment můžete provést úlohu rozpoznávání písmeno s ukázkovým klasifikace víc tříd. Třídění se pokouší předvídat určitým písmenem (třídy) založené na některé hodnoty atributu ručně psanou extrahovat z obrázků ručně psanou.

![Příklad rozpoznávání písmeno](./media/interpret-model-results/5_1.png)

V trénovací data je 16 funkce extrahovat z obrázků, ručně psanou písmeno. 26 písmena tvoří naše 26 třídy. Obrázek 6 ukazuje experiment, který bude trénování modelů klasifikace víc tříd pro písmeno rozpoznávání a předvídání na stejnou sadu na testovací datové sadě funkcí.

![Písmeno rozpoznávání klasifikace víc tříd experimentu](./media/interpret-model-results/6.png)

Obrázek 6. Experiment problém klasifikace víc tříd rozpoznávání písmeno

Vizualizace výsledků [určení skóre modelu] [ score-model] kliknutím na výstupní port modulu [určení skóre modelu] [ score-model] modulu a pak kliknete na **Vizualizovat**, obsah byste měli vidět, jak je znázorněno na obrázku 7.

![Model výsledků skóre](./media/interpret-model-results/7.png)

Obrázek 7. Vizualizace výsledků skóre model klasifikace víc tříd

**Interpretace výsledků**

Levé 16 sloupce, které představují hodnoty funkcí sady testů. Sloupce s názvy jako skóre pravděpodobnosti pro třídu "XX" jsou stejně, jako jsou sloupce skóre pravděpodobnosti v případě dvěma třídami. Zobrazí se pravděpodobnost odpovídající položky spadající do určité třídy. Například pro první položku, je 0.003571 pravděpodobnost, že se jedná "A" 0.000451 pravděpodobnost, že se jedná "B" a tak dále. Poslední sloupec (popisky vyhodnocení) je stejný jako popisky vyhodnocení v případě dvěma třídami. Vybere třídu s největší pravděpodobností Vyhodnocená jako předpokládaná třída odpovídající položky. Pro první položku Vyhodnocená popisek je například "F" protože má největší pravděpodobnost jako "F" (0.916995).

**Publikování webové služby**

Můžete také získat skóre popisek pro každý záznam a pravděpodobnost vzniku Vyhodnocená popisek. Základní logiku se má najít největší pravděpodobnost mezi skóre pravděpodobnosti. K tomuto účelu, budete muset použít [spustit skript jazyka R] [ execute-r-script] modulu. Kód jazyka R je uveden na obrázku 8, a výsledek testu je uveden na obrázku 9.

![Příklad kódu R](./media/interpret-model-results/8.png)

Obrázek 8. Kód R pro extrahování popisky vyhodnocení a přidružené pravděpodobností popisků

![Výsledek testu](./media/interpret-model-results/9.png)

Obrázek 9. Konečný experiment bodování problém klasifikace víc tříd písmeno rozpoznávání

Po publikování a spuštění webové služby a zadejte hodnoty vstupu funkce, vrácený výsledek vypadá jako obrázek 10. Tento ručně psanou písmeno s jeho extrahované 16 funkcí, očekává se, že bude "T", s 0.9715 pravděpodobnosti.

![Interpretace skóre modulu testování](./media/interpret-model-results/9_1.png)

![Výsledek testu](./media/interpret-model-results/10.png)

Obrázek 10. Webové služby výsledek klasifikace víc tříd

## <a name="regression"></a>Regrese
Regrese problémy se liší od klasifikace problémy. V klasifikace problému který zkoušíte předpovědět samostatné třídy, jako je například které třídu iris květinu patří do. Ale jak můžete vidět v následujícím příkladu regresní problém, který zkoušíte předpovědět spojitou proměnnou, jako je například ceny automobilu.

**Příklad experimentu**

Použijte předpovědi cen automobilů jako příkladu regrese. Pokoušíte se předpovídat cenu automobilu na základě jeho funkcí, včetně Ujistěte se, posílit typu, typ těla zprávy a kolečka jednotky. Experimentu se zobrazí v obrázek 11.

![Experiment regrese automobilů cena](./media/interpret-model-results/11.png)

Obrázek 11. Cena automobilů regresní problém experimentu

Vizualizace [Score Model] [ score-model] modul, výsledek bude vypadat jako obrázek 12.

![Výsledky vyhodnocení pro problém předpovědi cen automobilů](./media/interpret-model-results/12.png)

Obrázek 12. Výsledek vyhodnocení pro problém předpovědi cen automobilů

**Interpretace výsledků**

Scored popisků je sloupec výsledků v této hodnoticí výsledek. Čísla jsou předpokládané cena pro každé auto.

**Publikování webové služby**

Můžete publikovat regrese experimentu do webové služby a volání pro předpověď cen automobilů stejným způsobem jako v případě použití klasifikace dvěma třídami.

![Vyhodnocování experimentu pro regresní problém automobilů cena](./media/interpret-model-results/13.png)

Obrázek 13. Vyhodnocování experiment kvůli problému regrese automobilů cena

Spuštění webové služby, vrácený výsledek vypadá jako obrázek 14. Předpokládané cena za tento automobilu je 15,085.52 $.

![Testovací modul pro stanovení skóre interpretace](./media/interpret-model-results/13_1.png)

![Výsledky vyhodnocení modulu](./media/interpret-model-results/14.png)

Obrázek 14. Webové služby výsledek kvůli problému regrese automobilů cena

## <a name="clustering"></a>Clustering
**Příklad experimentu**

K vytvoření clusterů experiment znovu použijeme datové sady Iris. Tady můžete filtrovat si popisky třídy v sadě dat tak, aby pouze funkce a je možné pro clustering. V tomto iris případ použití, zadejte počet clusterů, které mají být dvě během procesu trénování, což znamená, že by cluster květin na dvě třídy. Experimentu se zobrazí v obrázek 15.

![Experimentujte clusteringu problém Iris](./media/interpret-model-results/15.png)

Obrázek 15. Experimentujte clusteringu problém Iris

Clustering se liší od klasifikace, v tom, že trénovací datové sady nemá popisky základu pravdy samostatně. Clustering skupiny instancí trénovací datové sady do různých clusterů. Během procesu trénování modelu popisků položky podle studijního rozdíly mezi jejich funkce. Poté je možné dále klasifikovat budoucí položky trénovaného modelu. Existují dvě části, které nás zajímají v rámci clusteru problém výsledku. První část je označování trénovací datové sady a druhý je klasifikace nové datové sady s trénovaného modelu.

První část výsledku lze vizualizovat kliknutím na levý výstupní port modulu [Train Model clusteringu] [ train-clustering-model] a pak levým na **vizualizovat**. Vizualizace se zobrazí v obrázek 16.

![Clustering výsledek](./media/interpret-model-results/16.png)

Obrázek 16. Vizualizujte clustering výsledek pro trénovací datové sady

Výsledek druhé části clustering nové položky s trénovaný model clusteringu, se zobrazí v obrázek 17.

![Vizualizujte clustering výsledek](./media/interpret-model-results/17.png)

Obrázek 17. Vizualizujte clustering výsledek pro nové datové sady

**Interpretace výsledků**

I když výsledky ze dvou částí vyplývají z experimentu různých fází, vypadají stejně a se stejným způsobem interpretují. První čtyři sloupce jsou funkce. Poslední sloupec, přiřazení, je výsledkem předpovědi. Očekává se, že položky přiřazené stejné číslo být ve stejném clusteru, tedy že sdílejí podobnosti nějakým způsobem (Tento experiment používá výchozí Euclidean vzdálenost metriku). Protože jste zadali počet clusterů, které mají být 2, položky v přiřazeních jsou označeny jako 0 nebo 1.

**Publikování webové služby**

Můžete publikovat clusteringu experimentu do webové služby a volání pro clustering předpovědí na stejném principu klasifikace dvěma třídami případu použití.

![Vyhodnocování experiment clusteringu problému iris](./media/interpret-model-results/18.png)

Obrázek 18. Vyhodnocování experiment kvůli problému clusteringu iris

Po spuštění webové služby vrácený výsledek vypadá jako obrázek 19. Tuto datovou očekává se, že v clusteru 0.

![Interpretace testovací modul pro stanovení skóre](./media/interpret-model-results/18_1.png)

![Výsledek vyhodnocení modulu](./media/interpret-model-results/19.png)

Obrázek 19. Webové služby výsledek klasifikace iris dvěma třídami

## <a name="recommender-system"></a>Doporučení system
**Příklad experimentu**

Pro systémy doporučení, můžete problém restaurace doporučení slouží jako příklad: Doporučujete restaurace pro zákazníky na základě jejich historie hodnocení. Vstupní data se skládá ze tří částí:

* Restaurace hodnocení zákazníků
* Zákaznická data funkce
* Data funkce restaurace

Můžeme se několika způsoby [trénování Matchbox doporučené] [ train-matchbox-recommender] modulu ve službě Azure Machine Learning:

* Předpověď hodnocení pro daného uživatele a položky
* Doporučte daného uživatele
* Najít uživatele týkající se daného uživatele
* Najít položky související s danou položku

Můžete také, co chcete udělat tak, že výběr ze čtyř možností v **doporučené predikcí druh** nabídky. Tady si můžete projít všechny čtyři scénáře.

![Matchbox doporučení](./media/interpret-model-results/19_1.png)

Typické experiment Azure Machine Learning pro systém doporučení vypadá jako obrázek 20. Informace o tom, jak používat tyto doporučené moduly systému najdete v tématu [trénování matchbox doporučené] [ train-matchbox-recommender] a [skóre matchbox doporučené] [ score-matchbox-recommender].

![Doporučení system experimentu](./media/interpret-model-results/20.png)

Obrázek 20. Doporučení system experimentu

**Interpretace výsledků**

**Předpověď hodnocení pro daného uživatele a položky**

Tak, že vyberete **hodnocení předpovědi** pod **doporučení předpovědi druh**, vás žádáme, doporučení systému aby předpovídal hodnocení pro daného uživatele a položky. Vizualizaci [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup by měl vypadat podobně jako obrázek 21.

![Hodnotit výsledek doporučení systému – hodnocení predikcí](./media/interpret-model-results/21.png)

Obrázek 21. Vizualizace výsledků skóre doporučení systému – hodnocení predikcí

První dva sloupce jsou páry položka uživatele poskytované vstupní data. Třetí sloupec je predikované hodnocení uživatele k určité položce. Například v prvním řádku zákazníka U1048 očekává se míra restaurace 135026 jako 2.

**Doporučte daného uživatele**

Výběrem **doporučení položky** pod **předpovědi druh doporučení**, vyžadujete systému doporučení k doporučení položek pro daného uživatele. Poslední parametr v tomto scénáři zvolit *doporučuje výběr položek*. Možnost **z hodnocení položky (pro vyhodnocení modelu)** je primárně určen pro vyhodnocení modelu během procesu trénování. Pro tuto fázi předpovědi zvolíme **ze všech položek**. Vizualizaci [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup by měl vypadat podobně jako obrázek 22.

![Výsledek skóre doporučení systému – položka doporučení](./media/interpret-model-results/22.png)

Obrázek 22. Vizualizace výsledků skóre doporučení systému – položka doporučení

Prvních šest sloupců představuje daného uživatele ID k doporučení položek, jak je uvedeno ve vstupní data. Pět sloupce, které představují položky doporučuje uživatelům v sestupném pořadí podle relevance. Například v prvním řádku, je doporučené restaurace zákazníka U1048 134986, za nímž následuje 135018, 134975, 135021 a 132862.

**Najít uživatele týkající se daného uživatele**

Výběrem **související uživatelé** pod **druh předpovědi doporučení**, vyžadujete doporučení systému a vyhledejte související uživatele pro daného uživatele. Související se zobrazí uživatelů, kteří mají podobné předvolby. Poslední parametr v tomto scénáři zvolit *související výběru uživatelem*. Možnost **od uživatele, že hodnocení položky (pro vyhodnocení modelu)** je primárně určen pro vyhodnocení modelu během procesu trénování. Zvolte **ze všech uživatelů** pro tuto fázi předpovědi. Vizualizaci [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup by měl vypadat podobně jako obrázek 23.

![Výsledek skóre doporučení systému – související uživatelé](./media/interpret-model-results/23.png)

Obrázek 23. Vizualizace výsledků skóre doporučení systému – související uživatelé

Prvních šest sloupců zobrazuje že ID potřebné k vyhledání souvisejících uživatelů podle vstupních dat daného uživatele. Pět sloupců ukládání předpokládané související uživatelé uživatele v sestupném pořadí podle relevance. Nejdůležitější zákazníka pro zákazníka U1048 je na prvním řádku nástroje U1051, za nímž následuje U1066, U1044, U1017 a U1072.

**Najít položky související s danou položku**

Výběrem **související položky** pod **druh predikcí doporučené**, vás žádáme, doporučení systému najít související položky na danou položku. Související položky se nejpravděpodobněji líbilo stejným uživatelem položky. Poslední parametr v tomto scénáři zvolit *související výběr položek*. Možnost **z hodnocení položky (pro vyhodnocení modelu)** je primárně určen pro vyhodnocení modelu během procesu trénování. Zvolili jsme **ze všech položek** pro tuto fázi předpovědi. Vizualizaci [skóre Matchbox doporučené] [ score-matchbox-recommender] výstup by měl vypadat podobně jako obrázek 24.

![Výsledek skóre doporučení systému – související položky](./media/interpret-model-results/24.png)

Obrázek 24. Vizualizace výsledků skóre doporučení systému, související položky

Prvních šest sloupců představuje daná položka ID, které jsou potřebné k vyhledání souvisejících položek podle vstupní data. Pět sloupců ukládání předpokládané související položky položky v sestupném pořadí z hlediska relevance. Například v prvním řádku, je relevantní položky pro položky 135026 135074, za nímž následuje 135035, 132875, 135055 a 134992.

**Publikování webové služby**

Proces publikování těchto pokusů jako webové služby, chcete-li získat predikcí se podobá pro všechny čtyři scénáře. Tady jsme trvat druhý scénář (doporučujeme položky pro daného uživatele) jako příklad. Můžete postupujte stejným způsobem s další tři.

Uložení natrénovaného doporučení systému jako trénovaného modelu a filtrování vstupních dat na sloupec ID jednoho uživatele, jak si vyžádal, můžete připojení experimentu jako obrázek 25 a publikovat jako webovou službu.

![Vyhodnocování experiment problému restaurace doporučení](./media/interpret-model-results/25.png)

Obrázek 25. Vyhodnocování experiment problému restaurace doporučení

Spuštění webové služby, vrácený výsledek vypadá jako obrázek 26. Pět doporučené restaurace pro uživatele U1048 jsou 134986, 135018, 134975, 135021 a 132862.

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
