---
title: 'Detekce anomálií na základě PCA: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul detekce anomálií na základě PCA k vytvoření modelu detekce anomálií na základě analýzy primárních součástí (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502981"
---
# <a name="pca-based-anomaly-detection"></a>Detekce anomálií založených na PCA

Tento článek popisuje, jak používat modul **detekce anomálií založených na PCA** v návrháři Azure Machine Learning (preview) k vytvoření modelu detekce anomálií na základě analýzy primárních součástí (PCA).

Tento modul pomáhá vytvořit model ve scénářích, kde je snadné získat trénovací data z jedné třídy, jako jsou platné transakce, ale obtížné získat dostatečné vzorky cílené anomálie. 

Chcete-li například odhalit podvodné transakce, velmi často nemáte dostatek příkladů podvodů, na kterých byste mohli trénovat, ale máte mnoho příkladů dobrých transakcí. Modul **detekce anomálií založených na PCA** řeší problém analýzou dostupných funkcí k určení, co představuje "normální" třídu, a použitím metrik vzdálenosti k identifikaci případů, které představují anomálie. To umožňuje trénování modelu pomocí existujících nevyvážených dat.

## <a name="more-about-principal-component-analysis"></a>Další informace o analýze hlavních součástí

*Analýza hlavních součástí*, která je často zkrácena na PCA, je zavedená technika ve strojovém učení. PcA se často používá v analýze průzkumných dat, protože odhaluje vnitřní strukturu dat a vysvětluje odchylku v datech.

PCA funguje tak, že analyzuje data, která obsahují více proměnných. Hledá korelace mezi proměnnými a určuje kombinaci hodnot, které nejlépe zachycuje rozdíly ve výsledcích. Tyto kombinované hodnoty prvků se používají k vytvoření kompaktnějšího prostoru prvku nazývaného *hlavní součásti*.

Pro detekci anomálií je analyzován každý nový vstup a algoritmus detekce anomálií vypočítá jeho projekci na vlastních vektorech spolu s chybou normalizované rekonstrukce. Normalizovaná chyba se používá jako skóre anomálií. Čím vyšší je chyba, tím více anomální instance je.

Další informace o tom, jak funguje pca a o implementaci pro detekci anomálií, naleznete v těchto dokumentech:

- [Randomizovaný algoritmus pro analýzu hlavních součástí](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan a Tygert

- [Hledání struktury s náhodností: Pravděpodobnostní algoritmy pro konstrukci přibližných rozkladů matic](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (stažení VE FORMÁTU PDF). Halko, Martinsson a Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Jak nakonfigurovat detekci anomálií PCA

1. Přidejte modul **detekce anomálií na základě PCA** do kanálu v návrháři. Tento modul naleznete v kategorii **Detekce anomálií.**

2. V pravém panelu modulu **detekce anomálií založených na PCA** klepněte na možnost **režimu tréninku** a určete, zda chcete trénovat model pomocí určité sady parametrů, nebo použít zatažení parametrů k nalezení nejlepších parametrů.

    - **Jeden parametr**: Tuto možnost vyberte, pokud víte, jak chcete model nakonfigurovat, a zadejte určitou sadu hodnot jako argumenty.

3. **Počet součástí, které se mají použít v pca**: Zadejte počet výstupních prvků nebo součástí, které chcete výstup.

    Rozhodnutí o tom, kolik součástí zahrnout je důležitou součástí návrhu experimentu pomocí PCA. Obecné pokyny je, že byste neměli zahrnovat stejný počet komponent PCA jako existují proměnné. Místo toho byste měli začít s menším počtem součástí a zvýšit je, dokud nebudou splněna některá kritéria.

    Nejlepších výsledků lze dosáhnout, pokud je počet výstupních součástí **menší než** počet sloupců funkcí dostupných v datové sadě.

4. Určete množství převzorkování, které má být během náhodného školení PCA provést. V problémech s detekcí anomálií ztěžují nevyvážená data použití standardních technik PCA. Zadáním určitého množství převzorkování můžete zvýšit počet cílových instancí.

    Pokud zadáte hodnotu 1, nebude provedeno převzorkování. Pokud zadáte libovolnou hodnotu vyšší než 1, další vzorky jsou generovány pro použití v trénování modelu.

    Existují dvě možnosti, v závislosti na tom, zda používáte parametr sweep nebo not:

    - **Parametr převzorkování pro randomizované PCA**: Zadejte jedno celé číslo, které představuje poměr převzorkování menšinové třídy nad normální třídou. (K dispozici při použití metody školení **single parametr.)**

    > [!NOTE]
    > Převzorkovovovovovovovované datové sady nelze zobrazit. Další informace o tom, jak se převzorkování používá s pca, naleznete [v technických poznámkách](#technical-notes).

5. **Povolit střední normalizaci vstupních funkcí**: Tuto možnost vyberte, chcete-li normalizovat všechny vstupní funkce na střední hodnotu nula. Normalizace nebo změna měřítka na nulu se obecně doporučuje pro PCA, protože cílem PCA je maximalizovat rozptyl mezi proměnnými.

     Tato možnost je ve výchozím nastavení zaškrtnutá. Odznačte tuto volbu, pokud již byly hodnoty normalizovány pomocí jiné metody nebo měřítka.

6. Připojte označenou trénovací datovou sadu a jeden z trénovacích modulů:

    - Pokud nastavíte možnost **Vytvořit režim treniace** na **jeden parametr**, použijte modul [Model detekce anomálií vlaku.](train-anomaly-detection-model.md)

7. Odešlete potrubí.

## <a name="results"></a>Výsledky

Po dokončení školení můžete buď uložit trénovaný model, nebo jej připojit k modulu [Model skóre](score-model.md) předpovědět skóre anomálií.

Vyhodnocení výsledků modelu detekce anomálií vyžaduje některé další kroky:

1. Ujistěte se, že sloupec skóre je k dispozici v obou datových sadách

    Pokud se pokusíte vyhodnotit model detekce anomálií a zobrazí se chyba "Neexistuje žádný sloupec skóre v skórované datové sady k porovnání", znamená to, že používáte typickou datovou sadu vyhodnocení, která obsahuje sloupec popisku, ale žádné skóre pravděpodobnosti. Musíte zvolit datovou sadu, která odpovídá výstupu schématu pro modely detekce anomálií, který zahrnuje sloupec **Scoredlabels** a **Scored Probabilities.**

2. Ujistěte se, že jsou označeny sloupce popisků

    Někdy metadata přidružená ke sloupci popisku je odebrána v grafu kanálu. Pokud k tomu dojde, při použití [vyhodnotit model](evaluate-model.md) modulu porovnat výsledky dvou modelů detekce anomálií, může se zobrazit chyba "Neexistuje žádný sloupec popisek v scored datové sady" nebo "Neexistuje žádný sloupec popisek v scored datové sady porovnat".

    Této chybě se můžete vyhnout přidáním modulu [Upravit metadata](edit-metadata.md) před modul [Vyhodnotit model.](evaluate-model.md) Pomocí voliče sloupců vyberte sloupec třídy a v rozevíracím seznamu **Pole** vyberte **Popisek**.

3. Pomocí [příkazu Spustit skript Pythonu](execute-python-script.md) upravte kategorie sloupců popisků jako 1 (pozitivní, normální) a 0 (negativní, abnormální).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technické poznámky

Tento algoritmus používá PCA k aproximaci subprostoru obsahujícího normální třídu. Subprostor je rozložen pomocí eigenvectorů spojených s nejvyššími hodnotami eigence matice kovariance dat. Pro každý nový vstup detektor anomálií nejprve vypočítá jeho projekce na vlastní vektory a pak vypočítá chybu normalizované rekonstrukce. Tato chyba je skóre anomálie. Čím vyšší je chyba, tím více neobvyklé instance. Podrobnosti o tom, jak se počítá normální prostor, naleznete v tématu Wikipedie: [Principal Component Analysis](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb specifických pro moduly návrháře naleznete v části [Výjimky a kódy chyb pro návrháře (náhled).](designer-error-codes.md)