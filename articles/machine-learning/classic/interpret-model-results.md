---
title: 'ML Studio (Classic): interpretace výsledků modelu – Azure'
description: Jak zvolit optimální sadu parametrů pro algoritmus pomocí a vizualizace výstupů modelu skóre.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: 74210475cc8c0efd274d6e80c3f1c89b5206a97d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95509506"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretace výsledků modelu v Azure Machine Learning Studio (Classic)

**platí pro:** ![ Toto je značka zaškrtnutí, což znamená, že se tento článek týká Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ Toto je X, což znamená, že se tento článek týká Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Toto téma vysvětluje, jak vizualizovat a interpretovat výsledky předpovědi v Azure Machine Learning Studio (Classic). Po vyškolení modelu a provedení předpovědi nad ním ("skóre modelu") potřebujete pochopit a interpretovat výsledek předpovědi.

Existují čtyři hlavní druhy modelů strojového učení v Azure Machine Learning Studio (Classic):

* Classification
* Clustering
* Regrese
* Doporučené systémy

Moduly používané pro předpověď nad těmito modely jsou:

* Modul určení [skóre modelu][score-model] pro klasifikaci a regresi
* Modul [přiřazení k][assign-to-clusters] clusterům pro clustering
* [Skóre Matchbox doporučuje][score-matchbox-recommender] pro systémy doporučení

Naučte se, jak [zvolit parametry pro optimalizaci algoritmů v ml Studio (Classic)](algorithm-parameters-optimize.md).

Informace o tom, jak vyhodnotit modely, najdete v tématu [jak vyhodnotit výkon modelu](evaluate-model-performance.md).

Pokud se ML Studio (Classic) nezačínáte, [Naučte se vytvářet jednoduchý experiment](create-experiment.md).

## <a name="classification"></a>Classification
Existují dvě podkategorie problémů s klasifikací:

* Problémy s pouze dvěma třídami (klasifikace se dvěma třídami nebo binární)
* Problémy s více než dvěma třídami (klasifikace více tříd)

Azure Machine Learning Studio (Classic) obsahuje různé moduly pro práci s každým z těchto typů klasifikace, ale metody pro interpretaci jejich výsledků předpovědi jsou podobné.

### <a name="two-class-classification"></a>Klasifikace se dvěma třídami
**Příklad experimentu**

Příkladem problému s klasifikací dvou tříd je klasifikace Iris květů. Úkolem je klasifikovat květy Iris na základě jejich funkcí. Sada dat Iris, kterou poskytuje Azure Machine Learning Studio (Classic), je podmnožinou oblíbené [sady dat Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) obsahující instance pouze dvou druhů květů (třídy 0 a 1). Pro každou květinu jsou k dispozici čtyři funkce (sepal Length, sepal Width, Petal Length a Petal Width).

![Snímek obrazovky s experimentem Iris](./media/interpret-model-results/1.png)

Obrázek 1: Experiment pro problémy s klasifikací dvou tříd Iris

Byl proveden experiment pro vyřešení tohoto problému, jak je znázorněno na obrázku 1. Byl vyškolený a vyhodnocený model rozhodovacího stromu se dvěma třídami. Nyní můžete vizualizovat výsledky předpovědi z modulu [skóre modelu][score-model] kliknutím na výstupní port modulu určení [skóre modelu][score-model] a následným kliknutím na **vizualizovat**.

![Modul určení skóre modelu](./media/interpret-model-results/1_1.png)

Tím se zobrazí výsledky bodování, jak je znázorněno na obrázku 2.

![Výsledky experimentu klasifikace dvou tříd Iris](./media/interpret-model-results/2.png)

Obrázek 2. Vizualizace výsledku modelu skóre v klasifikaci se dvěma třídami

**Interpretace výsledku**

Tabulka výsledků obsahuje šest sloupců. Levé čtyři sloupce jsou čtyři funkce. Výsledkem jsou výsledky odhadu, které jsou v obou sloupcích, popisky s skóre a pravděpodobnosti skóre. Sloupec pravděpodobnosti skóre zobrazuje pravděpodobnost, že květina patří do kladné třídy (třída 1). Například první číslo ve sloupci (0,028571) znamená, že je 0,028571 pravděpodobnost, že první květina patří do třídy 1. Sloupec popisky s hodnocením zobrazuje předpokládanou třídu pro každou květinu. To je založeno na sloupci pravděpodobnosti skóre. Je-li pravděpodobnost skóre v květu větší než 0,5, je předpovězena jako třída 1. V opačném případě je předpokládaná jako třída 0.

**Publikace webové služby**

Po pochopení a posouzení výsledků předpovědi můžete experiment publikovat jako webovou službu, abyste ji mohli nasadit do různých aplikací a volat ji, aby získala předpovědi třídy na jakékoli nové Iris květ. Informace o tom, jak změnit zkušební experiment na experiment bodování a publikovat ho jako webovou službu, najdete v [kurzu 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md). Tento postup vám poskytne experiment bodování, jak je znázorněno na obrázku 3.

![Snímek experimentu s bodování](./media/interpret-model-results/3.png)

Obrázek 3: Bodování problému s klasifikací dvou tříd Iris

Nyní je třeba nastavit vstup a výstup webové služby. Vstup je správným vstupním portem [modelu skóre][score-model], který je vstupem funkcí Iris pro květ. Volba výstupu závisí na tom, jestli vás zajímá předpokládanou třídu (popisek s skóre), pravděpodobnost skóre nebo obojí. V tomto příkladu se předpokládá, že máte zájem na obě. Chcete-li vybrat požadované výstupní sloupce, použijte [možnost vybrat sloupce v modulu datové sady][select-columns] . Klikněte na [Vybrat sloupce v datové sadě][select-columns], klikněte na **Spustit selektor sloupců** a vyberte **popisy skóre** a **pravděpodobnost skóre**. Po nastavení výstupní port [vybraných sloupců v sadě dat][select-columns] a jeho opětovném spuštění byste měli být připravení publikovat experiment pro bodování jako webovou službu kliknutím na **publikovat webovou službu**. Konečný experiment vypadá jako obrázek 4.

![Experiment klasifikace dvou tříd Iris](./media/interpret-model-results/4.png)

Obrázek 4. Finální bodování experimentu s klasifikací dvou tříd Iris

Po spuštění webové služby a zadání hodnot některých funkcí instance testu vrátí výsledek dvě čísla. První číslo je popisek s skóre a druhá je pravděpodobnost skóre. Tato květina se předpokládá jako třída 1 s pravděpodobností 0,9655.

![Model skóre testování interpretace](./media/interpret-model-results/4_1.png)

![Výsledky testu bodování](./media/interpret-model-results/5.png)

Obrázek 5. Výsledek webové služby pro klasifikaci Iris dvou tříd

### <a name="multi-class-classification"></a>Klasifikace více tříd
**Příklad experimentu**

V tomto experimentu provedete úlohu rozpoznávání písmen jako příklad klasifikace s více třídami. Klasifikátor se pokusí odhadnout určité písmeno% 28class %29 na základě některých hodnot atributů psaných rukou extrahovaných z imagí psaných rukou.

![Příklad rozpoznávání písmen](./media/interpret-model-results/5_1.png)

Ve školicích datech je více funkcí extrahováno z písemně psaných dopisů. 26 písmen tvoří naši 26 tříd. Obrázek 6 znázorňuje experiment, který bude zaškolit model klasifikace s více třídami pro rozpoznávání písmen a předpověď na stejnou sadu funkcí pro sadu dat testu.

![Experiment klasifikace s více třídami rozpoznávání písmen](./media/interpret-model-results/6.png)

Obrázek 6. Řešení potíží s klasifikací s rozpoznáváním více tříd – experiment

Vizualizace výsledků z modulu [skóre modelu][score-model] kliknutím na výstupní port modulu určení [skóre modelu][score-model] a následným kliknutím na **vizualizovat** by se měl zobrazit obsah, jak je znázorněno na obrázku 7.

![Výsledky modelu skóre](./media/interpret-model-results/7.png)

Obrázek 7. Vizualizace modelu skóre výsledků v klasifikaci více tříd

**Interpretace výsledku**

Levý 16 sloupců představuje hodnoty funkcí sady testů. Sloupce s názvy, jako jsou pravděpodobnosti skóre pro třídu "XX", jsou stejně jako sloupce pravděpodobnosti skóre v případě dvou tříd. Ukazují pravděpodobnost, že odpovídající položka spadá do určité třídy. Například pro první záznam je 0,003571 pravděpodobnost, že se jedná o pravděpodobnost "A," 0,000451, že se jedná o "B" a tak dále. Poslední sloupec (popisky s skóre) je stejný jako návěští s skóre v případě dvou tříd. Vybere třídu s největší pravděpodobností skóre v podobě předpovězené třídy odpovídající položky. Například pro první položku je popisek s skóre "F", protože má největší pravděpodobnost, že je "F" (0,916995).

**Publikace webové služby**

Můžete také získat popisek s skóre pro každou položku a pravděpodobnost popisku s skóre. Základní logikou je najít největší pravděpodobnost mezi všemi pravděpodobnosti skóre. K tomu je potřeba použít modul [spuštění skriptu jazyka R][execute-r-script] . Kód R je zobrazen na obrázku 8 a výsledek experimentu je znázorněn na obrázku 9.

![Příklad kódu R](./media/interpret-model-results/8.png)

Obrázek 8. Kód R pro extrakci popisků s skóre a související pravděpodobnosti popisků

![Výsledek experimentu](./media/interpret-model-results/9.png)

Obrázek 9: Finální bodování experimentu s klasifikací více tříd rozpoznávání písmen

Po publikování a spuštění webové služby a zadání hodnot vstupních funkcí se vrácený výsledek bude nacházet jako obrázek 10. Tato ruka psaná písmena s extrahovanou 16 funkcemi je předpovězená jako "T" s pravděpodobností 0,9715.

![Modul skóre interpretace testů](./media/interpret-model-results/9_1.png)

![Výsledek testu](./media/interpret-model-results/10.png)

Obrázek 10. Výsledek webové služby s klasifikací více tříd

## <a name="regression"></a>Regrese
Regresní problémy se liší od problémů s klasifikací. V rámci klasifikačního problému se snažíte odhadnout diskrétní třídy, jako je například třída, do které patří Iris květ. Jak vidíte v následujícím příkladu regresního problému, snažíte se předpovědět souvislou proměnnou, jako je například cena auta.

**Příklad experimentu**

Jako příklad regrese použijte předpověď ceny automobilu. Pokoušíte se odhadnout cenu automobilu na základě jeho funkcí, včetně druhu, typu paliva, typu textu a kolečka. Experiment se zobrazuje na obrázku 11.

![Experiment s regresí pro cenu za automobil](./media/interpret-model-results/11.png)

Obrázek 11. Experiment při potížích s regresí pro cenu automobilu

Díky vizualizaci modulu [skóre modelu][score-model] vypadá výsledek jako obrázek 12.

![Výsledky bodování problému s předpovědí cen automobilu](./media/interpret-model-results/12.png)

Obrázek 12. Výsledek bodování problému pro předpověď ceny automobilu

**Interpretace výsledku**

Popisky s skóre představují sloupec výsledek v tomto výsledku bodování. Čísla představují předpokládanou cenu pro každé auto.

**Publikace webové služby**

Můžete publikovat regresní experiment do webové služby a volat ho pro předpověď ceny za automobil stejným způsobem jako v případě použití klasifikace dvou tříd.

![Experiment bodování pro problém regrese s cenami za automobil](./media/interpret-model-results/13.png)

Obrázek 13. Bodování experimentu s problémem při regresi cen za automobil

Po spuštění webové služby bude vrácený výsledek vypadat jako obrázek 14. Předpokládaná cena tohoto auta je $15 085,52.

![Modul bodování testu interpretace](./media/interpret-model-results/13_1.png)

![Výsledky modulu bodování](./media/interpret-model-results/14.png)

Obrázek 14. Výsledek webové služby při potížích s regresními cenami za automobil

## <a name="clustering"></a>Clustering
**Příklad experimentu**

Pojďme znovu použít sadu dat Iris k sestavení experimentu clusteringu. Tady můžete vyfiltrovat štítky tříd v sadě dat tak, aby měly jenom funkce a dají se použít pro clustering. V tomto případě Iris zadejte počet clusterů, které mají být během procesu školení dva, což znamená, že se květiny budou zavažovat do clusteru do dvou tříd. Experiment je znázorněn na obrázku 15.

![Experimentování s problémem clusteringu v Iris](./media/interpret-model-results/15.png)

Obrázek 15. Experimentování s problémem clusteringu v Iris

Clustering se liší od klasifikace v tom, že sada školicích dat nemá popisky uzemnění. Clustering seskupuje instance školicích dat do různých clusterů. Během procesu školení model označí položky pomocí učení rozdílů mezi jejich funkcemi. Potom můžete vyškolený model použít k další klasifikaci budoucích položek. V rámci potíží s clustering vás zajímá dvě části výsledku. První část je označena jako popis školicích dat a druhá je klasifikace nové sady dat s poučeným modelem.

První část výsledku se dá vizualizovat tak, že kliknete na levý výstupní port [modelu clusteringu][train-clustering-model] a pak kliknete na **vizualizovat**. Vizualizace je znázorněna na obrázku 16.

![Výsledek clusteringu](./media/interpret-model-results/16.png)

Obrázek 16. Vizualizace výsledku clusteringu pro sadu školicích dat

Výsledkem druhé části je, že clusteruje nové položky s proučeným modelem clusteringu, který je znázorněn na obrázku 17.

![Výsledek vizualizace clusteringu](./media/interpret-model-results/17.png)

Obrázek 17. Vizualizace výsledku clusteringu v nové sadě dat

**Interpretace výsledku**

Ačkoliv výsledky dvou částí vyplývají z různých fází experimentu, vypadají stejně a jsou interpretovány stejným způsobem. První čtyři sloupce jsou funkce. Poslední sloupec, přiřazení je výsledkem předpovědi. Položky, které mají přiřazené stejné číslo, budou předpovězeny ve stejném clusteru, to znamená, že se budou dělit podobným způsobem. (Tento experiment používá výchozí metriku Euclidean na dálku. Vzhledem k tomu, že jste zadali počet clusterů, které mají být 2, jsou položky v přiřazení označeny buď jako 0, nebo 1.

**Publikace webové služby**

Experiment clusteringu můžete publikovat do webové služby a volat ho pro clustering předpovědi stejným způsobem jako v případě použití klasifikace dvou tříd.

![Experiment bodování pro problém clusteringu v Iris](./media/interpret-model-results/18.png)

Obrázek 18. Experiment bodování problému s clustering Iris

Po spuštění webové služby bude vrácený výsledek vypadat jako obrázek 19. Tato květina se předpokládá v clusteru 0.

![Modul bodování test interpretace](./media/interpret-model-results/18_1.png)

![Výsledek modulu bodování](./media/interpret-model-results/19.png)

Obrázek 19. Výsledek webové služby pro klasifikaci Iris dvou tříd

## <a name="recommender-system"></a>Doporučit systém
**Příklad experimentu**

Pro doporučované systémy můžete použít problém doporučení restaurace jako příklad: můžete doporučit restaurace pro zákazníky na základě jejich historie hodnocení. Vstupní data se skládají ze tří částí:

* Hodnocení restaurace od zákazníků
* Data funkcí zákazníka
* Data funkcí restaurace

K dispozici je několik věcí s modulem Matchbox, který je [doporučený pro výuku][train-matchbox-recommender] v Azure Machine Learning Studio (Classic):

* Předpověď hodnocení pro daného uživatele a položku
* Doporučit položky pro daného uživatele
* Vyhledání uživatelů souvisejících s daným uživatelem
* Najít položky související s danou položkou

To, co chcete udělat, můžete vybrat ze čtyř možností v nabídce **druh předpovědi doporučeného** pro výběr. Tady si můžete projít všemi čtyřmi scénáři.

![Doporučení Matchbox](./media/interpret-model-results/19_1.png)

Typický Azure Machine Learning Studio (klasický) experiment pro doporučující systém vypadá jako obrázek 20. Informace o tom, jak používat tyto doporučované systémové moduly, najdete v tématu doporučení [Matchbox pro učení][train-matchbox-recommender] a [hodnocení Matchbox][score-matchbox-recommender].

![Doporučený systém experimentů](./media/interpret-model-results/20.png)

Obrázek 20. Doporučený systém experimentů

**Interpretace výsledku**

**Předpověď hodnocení pro daného uživatele a položku**

Výběrem **předpovědi hodnocení** v části **doporučený druh předpovědi** si vyžádáte, aby systém doporučení předpovídat hodnocení pro daného uživatele a položku. Vizualizace výstupu [doporučeného pro skóre Matchbox][score-matchbox-recommender] vypadá jako obrázek 21.

![Výsledek skóre pro odhad systému doporučeného pro hodnocení systému](./media/interpret-model-results/21.png)

Obrázek 21. Vizualizace výsledku doporučeného odhadu hodnocení systému

První dva sloupce jsou páry uživatelských položek poskytované vstupními daty. Třetí sloupec je předpovězené hodnocení uživatele pro určitou položku. Například v prvním řádku se Customer U1048 odhadne na 135026.2.

**Doporučit položky pro daného uživatele**

Výběrem **doporučení položky** v části **doporučený druh předpovědi** si vyžádáte doporučit systém, aby doporučil položky pro daného uživatele. Jako poslední parametr pro výběr v tomto scénáři se *doporučuje vybrat položku*. Možnost **z hodnocených položek (pro vyhodnocení modelu)** je primárně určena pro vyhodnocení modelu během procesu školení. Pro tuto fázi předpovědi si vybíráme **všechny položky**. Vizualizace výstupu [doporučeného pro skóre Matchbox][score-matchbox-recommender] vypadá jako obrázek 22.

![Výsledek skóre pro doporučení pro systém a položku](./media/interpret-model-results/22.png)

Obrázek 22. Vizualizovat výsledek skóre doporučeného systému--položka doporučení

První z šesti sloupců představuje daná ID uživatele, která doporučují položky pro, jak jsou k dispozici vstupní data. Další pět sloupců představuje položky doporučené pro uživatele v sestupném pořadí podle relevance. Například v prvním řádku je doporučená Restaurace pro Customer U1048 134986, za kterou následuje 135018, 134975, 135021 a 132862.

**Vyhledání uživatelů souvisejících s daným uživatelem**

Výběrem **Možnosti příbuzní uživatelé** v části **doporučený druh předpovědi** si vyžádáte, aby se k danému uživateli našeli uživatelé s doporučením. Související uživatelé jsou uživatelé, kteří mají podobné předvolby. Posledním parametrem, který si zvolíte v tomto scénáři, je *Výběr souvisejícího uživatele*. Možnost **od uživatelů, kteří hodnotili položky (pro vyhodnocení modelu),** je primárně určena pro vyhodnocení modelu během procesu školení. Vyberte **všechny uživatele** pro tuto fázi předpovědi. Vizualizace výstupu [doporučeného pro skóre Matchbox][score-matchbox-recommender] vypadá jako obrázek 23.

![Výsledek skóre pro uživatele s doporučeními pro systém](./media/interpret-model-results/23.png)

Obrázek 23. Vizualizovat výsledky skóre pro uživatele s doporučeními pro systém

První ze šesti sloupců zobrazuje zadaná ID uživatelů potřebná k vyhledání souvisejících uživatelů, jak jsou k dispozici vstupní data. Další pět sloupců ukládá předpovězené související uživatele uživatele v sestupném pořadí podle relevance. Například v prvním řádku je nejvíc relevantní zákazník pro Customer U1048 U1051, následovaný U1066, U1044, U1017 a U1072.

**Najít položky související s danou položkou**

Výběrem **možnosti související položky** v části **doporučený druh předpovědi** si vyžádáte systém doporučení, aby na danou položku našli související položky. Související položky jsou položky, které nejpravděpodobněji stejný uživatel nelíbí. Poslední parametr pro výběr v tomto scénáři je *Výběr související položky*. Možnost **z hodnocených položek (pro vyhodnocení modelu)** je primárně určena pro vyhodnocení modelu během procesu školení. Pro tuto fázi předpovědi si vybíráme **všechny položky** . Vizualizace výstupu [doporučeného pro skóre Matchbox][score-matchbox-recommender] vypadá jako obrázek 24.

![Výsledek skóre pro doporučené systémové položky](./media/interpret-model-results/24.png)

Obrázek 24. Vizualizujte výsledky skóre pro položky, které se týkají systému doporučení

První z šesti sloupců představuje ID daných položek potřebných k nalezení souvisejících položek, které jsou poskytovány vstupními daty. Další pět sloupců ukládá předpovídané související položky položky v sestupném pořadí podle relevance. Například v prvním řádku je nejdůležitější položka pro položku 135026 135074 a za ní následuje 135035, 132875, 135055 a 134992.

**Publikace webové služby**

Proces publikování těchto experimentů jako webových služeb pro získání předpovědi je pro každý ze čtyř scénářů podobný. Tady probereme druhý scénář (doporučit pro daného uživatele položky) jako příklad. Stejný postup můžete provést i u ostatních tří.

Pokud chcete jako vyškolený model Uložit Doporučený systém a vyfiltrovat vstupní data podle požadavků, můžete experiment připojit jako obrázek 25 a publikovat ho jako webovou službu.

![Experiment bodování problému s doporučeními restaurace](./media/interpret-model-results/25.png)

Obrázek 25. Experiment bodování problému s doporučeními restaurace

V případě spuštění webové služby vypadá vrácený výsledek jako obrázek 26. Pět doporučených restaurací pro uživatele U1048 jsou 134986, 135018, 134975, 135021 a 132862.

![Ukázka Doporučené systémové služby](./media/interpret-model-results/25_1.png)

![Ukázka výsledků experimentu](./media/interpret-model-results/26.png)

Obrázek 26. Výsledek webové služby – problém s doporučením pro restaurace

<!-- Module References -->
[assign-to-clusters]: /azure/machine-learning/studio-module-reference/assign-data-to-clusters
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-matchbox-recommender]: /azure/machine-learning/studio-module-reference/score-matchbox-recommender
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-clustering-model]: /azure/machine-learning/studio-module-reference/train-clustering-model
[train-matchbox-recommender]: /azure/machine-learning/studio-module-reference/train-matchbox-recommender