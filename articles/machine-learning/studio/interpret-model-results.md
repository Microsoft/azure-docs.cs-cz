---
title: Interpretace výsledků modelu
titleSuffix: ML Studio (classic) - Azure
description: Jak zvolit optimální sadu parametrů pro algoritmus pomocí a vizualizaci výstupů modelu skóre.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: 9a0b855f48085138b28e02e0a5d01c5dd0f666be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218073"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretace výsledků modelu v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Toto téma vysvětluje, jak vizualizovat a interpretovat výsledky předpovědi v Azure Machine Learning Studio (klasické). Poté, co jste vycvičili model a provedli předpovědi nad ním ("skóroval model"), je třeba pochopit a interpretovat výsledek předpovědi.



V Azure Machine Learning Studio (klasické) existují čtyři hlavní druhy modelů strojového učení:

* Classification
* Clustering
* Regrese
* Doporučující systémy

Moduly používané pro predikci nad těmito modely jsou:

* Modul [modelu skóre][score-model] pro klasifikaci a regresi
* [Přiřadit clusterům][assign-to-clusters] modul pro clustering
* [Skóre Matchbox Recommender][score-matchbox-recommender] pro doporučení systémů

Tento dokument vysvětluje, jak interpretovat výsledky předpovědi pro každý z těchto modulů. Přehled těchto modulů najdete v tématu [Jak vybrat parametry pro optimalizaci algoritmů v Azure Machine Learning Studio (klasické)](algorithm-parameters-optimize.md).

Toto téma řeší interpretaci předpovědi, ale ne vyhodnocení modelu. Další informace o tom, jak vyhodnotit váš model, najdete v [tématu Jak vyhodnotit výkon modelu v Azure Machine Learning Studio (klasické)](evaluate-model-performance.md).

Pokud s Azure Machine Learning Studio (klasické) tečujete a potřebujete pomoct s vytvořením jednoduchého experimentu, abyste mohli začít, přečtěte si informace o [vytvoření jednoduchého experimentu v Azure Machine Learning Studio (klasickém)](create-experiment.md).

## <a name="classification"></a>Classification
Existují dvě podkategorie problémů klasifikace:

* Problémy pouze se dvěma třídami (dvoutřídní nebo binární klasifikace)
* Problémy s více než dvěma třídami (klasifikace více tříd)

Azure Machine Learning Studio (classic) má různé moduly pro řešení každého z těchto typů klasifikace, ale metody pro interpretaci jejich výsledky předpověď jsou podobné.

### <a name="two-class-classification"></a>Klasifikace dvou tříd
**Příklad experimentu**

Příkladem problému klasifikace dvou tříd je klasifikace květů duhovky. Úkolem je klasifikovat iris květiny na základě jejich vlastností. Datová sada Iris k dispozici v Azure Machine Learning Studio (classic) je podmnožinou populární [iris datové sady](https://en.wikipedia.org/wiki/Iris_flower_data_set) obsahující instance pouze dva druhy květin (třídy 0 a 1). Existují čtyři funkce pro každou květinu (sepal délka, sepal šířka, délka okvětního lístku, a šířka okvětnílístky).

![Snímek obrazovky experimentu duhovky](./media/interpret-model-results/1.png)

Obrázek 1: Iris dvoutřídní klasifikační experiment

K vyřešení tohoto problému byl proveden experiment, jak je znázorněno na obrázku 1. Dvoutřídní posílený rozhodovací stromový model byl trénován a hodnocen. Nyní můžete vizualizovat výsledky předpovědi z modulu [Model skóre][score-model] kliknutím na výstupní port modulu [Model skóre][score-model] a následným kliknutím na **položku Visualize**.

![Modul modelu skóre](./media/interpret-model-results/1_1.png)

Tím se zobrazí výsledky bodování, jak je znázorněno na obrázku 2.

![Výsledky dvoutřídního klasifikačního experimentu duhovky](./media/interpret-model-results/2.png)

Obrázek 2. Vizualizujte výsledek výsledku výsledku výsledku výsledku výsledku dvoutřídní klasifikace

**Interpretace výsledků**

V tabulce výsledků je šest sloupců. Levé čtyři sloupce jsou čtyři funkce. Pravé dva sloupce, Scored Labels a Scored Probabilities, jsou výsledky předpovědi. Sloupec Pravděpodobnosti skóre zobrazuje pravděpodobnost, že květina patří do kladné třídy (třída 1). Například první číslo ve sloupci (0,028571) znamená, že je 0,028571 pravděpodobnost, že první květina patří do třídy 1. Sloupec Popisky s vykreslovaným skóre zobrazuje předpovídanou třídu pro každou květinu. To je založeno na sloupci Pravděpodobnosti skórovány. Pokud je pravděpodobnost výskytu květiny větší než 0,5, předpokládá se jako třída 1. V opačném případě se předpokládá jako třída 0.

**Publikace webové služby**

Poté, co byly pochopeny a posouzeny výsledky předpovědi, může být experiment publikován jako webová služba, takže jej můžete nasadit v různých aplikacích a zavolat ji k získání předpovědí třídy na jakékoli nové květině duhovky. Informace o tom, jak změnit trénovací experiment na experiment hodnocení a publikovat ho jako webovou službu, naleznete [v tématu Nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md). Tento postup poskytuje bodovací experiment, jak je znázorněno na obrázku 3.

![Snímek obrazovky s experimentem bodování](./media/interpret-model-results/3.png)

Obrázek 3: Bodování iris dvoutřídní klasifikace problém experiment

Nyní je třeba nastavit vstup a výstup pro webovou službu. Vstup je správný vstupní port [score modelu][score-model], což je iris květina funkce vstup. Volba výstupu závisí na tom, zda máte zájem o předpovídanou třídu (scored label), pravděpodobnost skóre nebo obojí. V tomto příkladu se předpokládá, že máte zájem o obojí. Chcete-li vybrat požadované výstupní sloupce, použijte modul [Vybrat sloupce v datové sadě.][select-columns] Klepněte na [vybrat sloupce v datové sadě][select-columns], klepněte na tlačítko Spustit volič **sloupců**a vyberte **Možnost Iosaa** a **Pravděpodobnosti se skóre**. Po nastavení výstupního portu [vybrat sloupce v sadě dat][select-columns] a jeho opětovném spuštění byste měli být připraveni publikovat experiment hodnocení jako webovou službu klepnutím na tlačítko PUBLIKOVAT **WEBOVOU SLUŽBU**. Konečný experiment vypadá jako obrázek 4.

![Dvoutřídní klasifikační experiment duhovky](./media/interpret-model-results/4.png)

Obrázek 4. Konečný bodovací experiment problému klasifikace iris dvou tříd

Po spuštění webové služby a zadání některých hodnot funkcí testovací instance vrátí výsledek dvě čísla. První číslo je skórovaný popisek a druhý je pravděpodobnost skóre. Tato květina je předpovězena jako třída 1 s pravděpodobností 0,9655.

![Test interpretačního modelu skóre](./media/interpret-model-results/4_1.png)

![Výsledky testů hodnocení](./media/interpret-model-results/5.png)

Obrázek 5. Výsledek dvoutřídní klasifikace iris

### <a name="multi-class-classification"></a>Klasifikace více tříd
**Příklad experimentu**

V tomto experimentu provedete úlohu rozpoznávání písmen jako příklad klasifikace více tříd. Třídění se pokusí předpovědět určité písmeno (třída) na základě některých ručně psaných hodnot atributů extrahovaných z ručně psaných obrázků.

![Příklad rozpoznávání dopisů](./media/interpret-model-results/5_1.png)

V trénovacích datech je 16 funkcí extrahovaných z ručně psaných obrázků dopisů. 26 písmen tvoří našich 26 tříd. Obrázek 6 znázorňuje experiment, který bude trénovat vícetřídní klasifikační model pro rozpoznávání písmen a předpovědět na stejné sadě funkcí na testovací datové sady.

![Víceoborový klasifikační experiment rozpoznávání písmen](./media/interpret-model-results/6.png)

Obrázek 6. Experiment s víceoborovou klasifikací rozpoznávání písmen

Vizualizace výsledků z modulu [Model skóre][score-model] kliknutím na výstupní port modulu [Model skóre][score-model] a následným kliknutím na **položku Vizuace**, měli byste vidět obsah, jak je znázorněno na obrázku 7.

![Výsledky modelu skóre](./media/interpret-model-results/7.png)

Obrázek 7. Vizualizujte výsledky modelu skóre ve vícetřídové klasifikaci

**Interpretace výsledků**

Levé sloupce 16 představují hodnoty prvku testovací sady. Sloupce s názvy jako Pravděpodobnosti skórovány pro třídu "XX" jsou stejné jako sloupec Pravděpodobnosti skórovány ve dvoutřídním případě. Ukazují pravděpodobnost, že odpovídající položka spadá do určité třídy. Například pro první položku je 0,003571 pravděpodobnost, že je "A", 0,000451 pravděpodobnost, že se jedná o "B" a tak dále. Poslední sloupec (Scoredlabels) je stejný jako scoredlabels ve dvoutřídním případě. Vybere třídu s největší pravděpodobností skóre jako předpovídanou třídu odpovídající položky. Například pro první položku je popisek s vyhodnoceným skóre "F", protože má největší pravděpodobnost, že bude "F" (0,916995).

**Publikace webové služby**

Můžete také získat skórovaný popisek pro každou položku a pravděpodobnost argumentace s konstatováno. Základní logikou je najít největší pravděpodobnost mezi všemi hodnocených pravděpodobností. Chcete-li to provést, musíte použít modul [Spustit skript R.][execute-r-script] Kód R je znázorněn na obrázku 8 a výsledek experimentu je znázorněn na obrázku 9.

![Příklad kódu R](./media/interpret-model-results/8.png)

Obrázek 8. Kód R pro extrahování Popisky se skórováním a související pravděpodobnosti popisků

![Výsledek experimentu](./media/interpret-model-results/9.png)

Obrázek 9. Konečný bodovací experiment problému klasifikace vícetřídy rozpoznávání písmen

Po publikování a spuštění webové služby a zadání některých hodnot vstupních funkcí bude vrácený výsledek vypadat na obrázku 10. Tento ručně psaný dopis, s jeho extrahované 16 funkcí, se předpokládá, že je "T" s pravděpodobností 0,9715.

![Modul testovat interpretační skóre](./media/interpret-model-results/9_1.png)

![Výsledek zkoušky](./media/interpret-model-results/10.png)

Obrázek 10. Výsledek webové služby klasifikace více tříd

## <a name="regression"></a>Regrese
Regresní problémy se liší od problémů klasifikace. V klasifikačním problému se snažíte předpovědět diskrétní třídy, například do které třídy patří květina duhovky. Ale jak můžete vidět v následujícím příkladu regresního problému, snažíte se předpovědět nepřetržitou proměnnou, například cenu auta.

**Příklad experimentu**

Jako příklad pro regresi použijte predikci cen automobilu. Snažíte se předpovědět cenu vozu na základě jeho vlastností, včetně make, typ paliva, typ karoserie, a hnací kolo. Experiment je znázorněn na obrázku 11.

![Experiment regrese cen automobilů](./media/interpret-model-results/11.png)

Obrázek 11. Experiment s regresí cen automobilů

Vizualizace modulu [Model skóre,][score-model] výsledek vypadá jako obrázek 12.

![Bodování výsledky pro predikci cen automobilů problém](./media/interpret-model-results/12.png)

Obrázek 12. Výsledek bodování pro problém predikce cen automobilů

**Interpretace výsledků**

Popisky s vyhodnocenou s konkretem je sloupec výsledků v tomto výsledku hodnocení. Čísla jsou předpokládaná cena za každé auto.

**Publikace webové služby**

Regresní experiment můžete publikovat do webové služby a volat pro předpověď cen automobilů stejným způsobem jako v případě použití klasifikace dvou tříd.

![Bodovací experiment pro problém regrese cen automobilů](./media/interpret-model-results/13.png)

Obrázek 13. Bodovací experiment problému regrese cen automobilů

Spuštění webové služby, vrácený výsledek vypadá na obrázku 14. Předpokládaná cena tohoto vozu je 15.085,52 dolarů.

![Testovací modul hodnocení tlumočení](./media/interpret-model-results/13_1.png)

![Výsledky modulu hodnocení](./media/interpret-model-results/14.png)

Obrázek 14. Výsledek regrese cen automobilů

## <a name="clustering"></a>Clustering
**Příklad experimentu**

Použijeme sadu dat Iris znovu k vytvoření clustering experimentu. Zde můžete odfiltrovat popisky tříd v sadě dat tak, aby měl pouze funkce a lze je použít pro clustering. V tomto případě použití duhovky zadejte počet clusterů, které mají být dva během procesu školení, což znamená, že by cluster květiny do dvou tříd. Experiment je znázorněn na obrázku 15.

![Experiment s problémem shlukování duhovky](./media/interpret-model-results/15.png)

Obrázek 15. Experiment s problémem shlukování duhovky

Clustering se liší od klasifikace v tom, že sada dat školení nemá země pravda popisky samy o sobě. Clustering seskupuje instance trénovací sady dat do různých clusterů. Během procesu školení model označuje položky učením rozdílů mezi jejich funkcemi. Poté lze trénovaný model použít k další klasifikaci budoucích položek. Existují dvě části výsledku máme zájem v rámci clustering problém. První část je označení trénovací datové sady a druhá je klasifikace nové datové sady s trénovaný model.

První část výsledku lze vizualizovat klepnutím na levý výstupní port [modelu clusterování vlaků][train-clustering-model] a následným klepnutím na příkaz **Visualize**. Vizualizace je znázorněna na obrázku 16.

![Výsledek clusteringu](./media/interpret-model-results/16.png)

Obrázek 16. Vizualizujte výsledek clusteringu pro trénovací datovou sadu

Výsledek druhé části, clustering nové položky s trénované clustering model, je znázorněno na obrázku 17.

![Vizualizujte výsledek clusteringu](./media/interpret-model-results/17.png)

Obrázek 17. Vizualizujte výsledek clusteringu v nové sadě dat

**Interpretace výsledků**

Ačkoli výsledky obou částí pocházejí z různých fází experimentu, vypadají stejně a jsou interpretovány stejným způsobem. První čtyři sloupce jsou funkce. Poslední sloupec, Přiřazení, je výsledek předpovědi. Položky přiřazené stejné číslo jsou předpovídány být ve stejném clusteru, to znamená, že sdílejí podobnosti nějakým způsobem (tento experiment používá výchozí Euclidean vzdálenost metriky). Vzhledem k tomu, že jste zadali počet clusterů, které mají být 2, jsou položky v přiřazení označeny buď 0 nebo 1.

**Publikace webové služby**

Můžete publikovat clustering experiment do webové služby a volat pro clustering předpovědi stejným způsobem jako v případě použití klasifikace dvou tříd.

![Bodovací experiment pro problém shlukování duhovky](./media/interpret-model-results/18.png)

Obrázek 18. Bodovací experiment problému s clusteringu duhovky

Po spuštění webové služby vrácený výsledek vypadá jako obrázek 19. Předpokládá se, že tato květina bude ve skupině 0.

![Testovací modul interpretu](./media/interpret-model-results/18_1.png)

![Výsledek bodovacího modulu](./media/interpret-model-results/19.png)

Obrázek 19. Výsledek dvoutřídní klasifikace iris

## <a name="recommender-system"></a>Doporučující systém
**Příklad experimentu**

Pro doporučující systémy můžete jako příklad použít problém s doporučením restaurace: můžete doporučit restaurace zákazníkům na základě jejich historie hodnocení. Vstupní data se skládají ze tří částí:

* Hodnocení restaurací od zákazníků
* Údaje o zákaznických funkcích
* Data funkcí restaurace

S modulem [Train Matchbox Recommender][train-matchbox-recommender] v Azure Machine Learning Studio (klasické) můžeme udělat několik věcí:

* Předvídání hodnocení pro daného uživatele a položku
* Doporučit položky danému uživateli
* Vyhledání uživatelů souvisejících s daným uživatelem
* Vyhledání položek souvisejících s danou položkou

Můžete si vybrat, co chcete udělat výběrem ze čtyř možností v nabídce **doporučení předpověď druhu.** Zde si můžete projít všechny čtyři scénáře.

![Matchbox doporučující](./media/interpret-model-results/19_1.png)

Typický experiment Azure Machine Learning Studio (klasický) pro doporučující systém vypadá na obrázku 20. Informace o tom, jak používat tyto moduly systému doporučení, naleznete [v tématu Train matchbox recommender][train-matchbox-recommender] and [Score matchbox recommender][score-matchbox-recommender].

![Experiment systému doporučení](./media/interpret-model-results/20.png)

Obrázek 20. Experiment systému doporučení

**Interpretace výsledků**

**Předvídání hodnocení pro daného uživatele a položku**

Výběrem **predikce hodnocení** v části **Doporučení předpověď druhu**, žádáte systém doporučení předpovědět hodnocení pro daného uživatele a položku. Vizualizace výstupu [Score Matchbox Recommender][score-matchbox-recommender] vypadá jako obrázek 21.

![Výsledek skóre systému doporučení -- predikce hodnocení](./media/interpret-model-results/21.png)

Obrázek 21. Vizualizujte výsledek skóre systému doporučení – predikce hodnocení

První dva sloupce jsou dvojice uživatelských položek poskytované vstupními daty. Třetí sloupec je předpokládané hodnocení uživatele pro určitou položku. Například v prvním řádku se předpokládá, že zákazník U1048 bude hodnotit restauraci 135026 jako 2.

**Doporučit položky danému uživateli**

Výběrem **položky doporučení** v části **Doporučení předpověď druhu**, žádáte systém doporučení doporučit položky pro daného uživatele. Poslední parametr, který vyberete v tomto *scénáři,* je Doporučený výběr položky . Možnost **Z hodnocené položky (pro vyhodnocení modelu)** je především pro vyhodnocení modelu během procesu školení. Pro tuto fázi predikce zvolíme **ze všech položek**. Vizualizace výstupu [Score Matchbox Recommender][score-matchbox-recommender] vypadá jako obrázek 22.

![Výsledek skóre doporučujícího systému -- doporučení položky](./media/interpret-model-results/22.png)

Obrázek 22. Vizualizujte výsledek skóre systému doporučení – doporučení položky

První ze šesti sloupců představuje dané ID uživatele doporučit položky, jak je stanoveno vstupní data. Dalších pět sloupců představuje položky doporučené uživateli v sestupném pořadí podle relevance. Například v prvním řádku je nejvíce doporučená restaurace pro zákazníka U1048 134986, následovaná 135018, 134975, 135021 a 132862.

**Vyhledání uživatelů souvisejících s daným uživatelem**

Výběrem **souvisejících uživatelů** v části **Doporučení předpověď druhu**, žádáte systém doporučení najít související uživatele k danému uživateli. Příbuzní uživatelé jsou uživatelé, kteří mají podobné preference. Poslední parametr, který zvolíte v tomto scénáři, je *Výběr uživatele související*. Možnost **od uživatelů, kteří jmenovité položky (pro vyhodnocení modelu)** je především pro vyhodnocení modelu během procesu školení. V této fázi předpovědi zvolte **ze všech uživatelů.** Vizualizace výstupu [Score Matchbox Recommender][score-matchbox-recommender] vypadá jako obrázek 23.

![Výsledek skóre doporučujícího systému --related users](./media/interpret-model-results/23.png)

Obrázek 23. Vizualizujte výsledky skóre systému doporučení – příbuzní uživatelé

První ze šesti sloupců zobrazuje daná ID uživatelů potřebná k vyhledání souvisejících uživatelů, jak je stanoveno vstupními daty. Dalších pět sloupců ukládá předpokládané související uživatele uživatele v sestupném pořadí podle relevance. Například v prvním řádku je nejrelevantnějším zákazníkem pro zákazníka U1048 U1051, následovaný U1066, U1044, U1017 a U1072.

**Vyhledání položek souvisejících s danou položkou**

Výběrem **související položky** v části **Doporučující předpověď druhu**, žádáte systém doporučení najít související položky na danou položku. Související položky jsou položky, které s největší pravděpodobností budou stejného uživatele oblíbené. Poslední parametr, který vyberete v tomto *scénáři,* je Výběr položky Související . Možnost **Z hodnocené položky (pro vyhodnocení modelu)** je především pro vyhodnocení modelu během procesu školení. Pro tuto fázi předpovědi vybereme **ze všech položek.** Vizualizace výstupu [Score Matchbox Recommender][score-matchbox-recommender] vypadá jako obrázek 24.

![Výsledek skóre doporučujícího systému --související položky](./media/interpret-model-results/24.png)

Obrázek 24. Vizualizujte výsledky skóre systému doporučení – související položky

První ze šesti sloupců představuje id dané položky potřebné k vyhledání souvisejících položek, jak je uvedeno vstupními daty. Dalších pět sloupců ukládá předpokládané související položky položky v sestupném pořadí z hlediska relevance. Například v prvním řádku nejrelevantnější položka pro položku 135026 je 135074, následuje 135035, 132875, 135055 a 134992.

**Publikace webové služby**

Proces publikování těchto experimentů jako webové služby získat předpovědi je podobný pro každý ze čtyř scénářů. Zde bereme druhý scénář (doporučit položky danému uživateli) jako příklad. Můžete postupovat stejným způsobem s ostatními třemi.

Uložení trénovaného systému pro doporučování jako trénovaného modelu a filtrování vstupních dat do sloupce ID jednoho uživatele podle požadavku můžete připojit experiment jako na obrázku 25 a publikovat jej jako webovou službu.

![Bodovací experiment problému s doporučením restaurace](./media/interpret-model-results/25.png)

Obrázek 25. Bodovací experiment problému s doporučením restaurace

Spuštění webové služby, vrácený výsledek vypadá jako obrázek 26. Pět doporučených restaurací pro uživatele U1048 je 134986, 135018, 134975, 135021 a 132862.

![Ukázka systémové služby doporučujícího](./media/interpret-model-results/25_1.png)

![Ukázkové výsledky experimentu](./media/interpret-model-results/26.png)

Obrázek 26. Výsledek potíží s doporučením restaurace webovou službou

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
