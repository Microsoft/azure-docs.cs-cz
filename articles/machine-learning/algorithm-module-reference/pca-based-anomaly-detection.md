---
title: 'Detekce anomálií založená na DPS: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul detekce anomálií založený na DPS k vytvoření modelu detekce anomálií založeného na analýze základní součásti (DPS).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: f126eb347d11d3203ad3b97abe3606738e67c33c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925836"
---
# <a name="pca-based-anomaly-detection"></a>Detekce anomálií založená na DPS

Tento článek popisuje, jak použít modul pro **detekci anomálií založený na DPS** v Návrháři Azure Machine Learning (Preview) k vytvoření modelu detekce anomálií založeného na analýze základní součásti (DPS).

Tento modul vám pomůže sestavit model ve scénářích, kde je snadné získat školicí data z jedné třídy, jako jsou například platné transakce, ale obtížné získat dostatečné vzorky cílových anomálií. 

Například pro detekci podvodných transakcí velmi často nemáte dostatek příkladů podvodů ke školení, ale mají mnoho příkladů dobrých transakcí. Modul pro **detekci anomálií založený na** přístupnosti vyřeší problémy analýzou dostupných funkcí a určí, co představuje "normální" třídu, a použije metriky vzdálenosti k identifikaci případů, které představují anomálie. To vám umožní vytvořit model s použitím stávajících nevyvážených dat.

## <a name="more-about-principal-component-analysis"></a>Další informace o analýze základních komponent

*Základní analýza komponenty*, která je často zkrácená na DPS, je vytvořená technika ve strojovém učení. DPS se často používá v analýze průzkumného data, protože odhalí vnitřní strukturu dat a vysvětluje odchylku v datech.

Funkce DPS funguje při analýze dat, která obsahují více proměnných. Vyhledá korelaci mezi proměnnými a určí kombinaci hodnot, které nejlépe zachytí rozdíly ve výsledcích. Tyto kombinované hodnoty funkcí slouží k vytvoření kompaktnějšího prostoru funkcí označovaného jako *hlavní komponenty*.

Pro detekci anomálií se analyzují jednotlivé nové vstupy a algoritmus detekce anomálií vypočítá svou projekci na eigenvectors společně s normalizovanou chybou rekonstrukce. Normalizovaná chyba se používá jako skóre anomálií. Čím vyšší je chyba, tím více neobvyklé instance je.

Další informace o tom, jak funguje DPS a o implementaci pro detekci anomálií, najdete v těchto dokladech:

- [Náhodný algoritmus pro analýzu hlavní komponenty](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan a Tygert

- [Hledání struktury s náhodností: pravděpodobnostní algoritmy pro vytváření přibližných rozkladů matice](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (stahování PDF). Halko, Martinsson a Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Jak nakonfigurovat detekci anomálií DPS

1. Přidejte modul pro **detekci anomálií založený na DPS** do kanálu v návrháři. Tento modul můžete najít v kategorii **detekce anomálií** .

2. V podokně **vlastnosti** modulu pro **detekci anomálií založeného na DPS** klikněte na možnost **školicí režim** a určete, jestli chcete model vytvořit pomocí konkrétní sady parametrů, nebo použijte k nalezení nejlepších parametrů parametr sweep.

    - **Jeden parametr**: tuto možnost vyberte, pokud víte, jak chcete model konfigurovat, a poskytněte konkrétní sadu hodnot jako argumenty.

3. **Počet komponent, které se mají použít v DPS**: zadejte počet výstupních funkcí nebo součástí, které chcete výstup.

    Rozhodnutí o tom, kolik součástí se má zahrnout, je důležitou součástí návrhu experimentu pomocí DPS. Obecné pokyny je, že byste neměli zahrnovat stejný počet součástí DPS, jako jsou proměnné. Místo toho byste měli začít s nějakým menším počtem komponent a navýšit je až do splnění některých kritérií.

    Nejlepších výsledků je dosaženo, pokud je počet výstupních komponent **menší než** počet sloupců funkcí dostupných v datové sadě.

4. Zadejte množství převzorkování, které se má provést během náhodného školení DPS. Při potížích s detekcí anomálií nevyvážená data obtížně používají standardní techniky DPS. Zadáním určité míry převzorkování můžete zvýšit počet cílových instancí.

    Zadáte-li hodnotu 1, není provedeno žádné převzorkování. Pokud zadáte libovolnou hodnotu větší než 1, vygenerují se další vzorky, které se použijí při výuce modelu.

    Existují dvě možnosti v závislosti na tom, zda používáte parametr Sweep nebo nikoli:

    - **Parametr převzorkování pro náhodný DPS**: Zadejte jedno celé číslo, které představuje poměr převzorkování třídy menšiny přes normální třídu. (K dispozici při použití metody školení s **jedním parametrem** .)

    > [!NOTE]
    > Nemůžete zobrazit sadu dat s převzorkováním. Další podrobnosti o tom, jak se převzorkování používá s DPS, najdete v tématu [technické poznámky](#technical-notes).

5. Možnost **Povolit funkci vstupu, střední normalizace**: tuto možnost vyberte, pokud chcete všechny vstupní funkce normalizovat na střední hodnotu nula. Normalizace nebo škálování na nulu se obecně doporučuje pro DPS, protože cílem DPS je maximalizovat odchylku mezi proměnnými.

     Tato možnost je vybrána jako výchozí nastavení. Zrušte výběr této možnosti, pokud hodnoty již byly normalizovány pomocí jiné metody nebo měřítka.

6. Připojte datovou sadu s tagovaným školením a jeden z školicích modulů:

    - Pokud nastavíte možnost **vytvořit režim Trainer** na **jeden parametr**, použijte modul [detekce anomálií pro vlaky](train-anomaly-detection-model.md) .

7. Spuštění kanálu

## <a name="results"></a>Výsledky

Po dokončení školení můžete buď Uložit školený model, nebo ho propojit s modulem [skóre modelu](score-model.md) , abyste mohli předpovědět výsledky anomálií.

K vyhodnocení výsledků modelů detekce anomálií je potřeba provést několik dalších kroků:

1. Zajistěte, aby byl v datových sadách dostupný sloupec skóre.

    Pokud se pokusíte vyhodnotit model detekce anomálií a získat chybu, "neexistuje žádný sloupec skóre v datové sadě, která by se měla porovnat", znamená to, že používáte typickou zkušební datovou sadu, která obsahuje sloupec popisku, ale žádné skóre. Musíte zvolit datovou sadu, která odpovídá výstupu schématu pro modely detekce anomálií, které zahrnují **popisky s skóre** a sloupec **pravděpodobnosti skóre** .

2. Ujistěte se, že jsou označené sloupce popisků.

    Někdy se Metadata přidružená k sloupci popisek v grafu kanálu odeberou. Pokud k tomu dojde, když použijete modul [vyhodnocení modelu](evaluate-model.md) k porovnání výsledků dvou modelů detekce anomálií, může se zobrazit chyba "neexistuje žádný sloupec popisku v sadě dat s skóre" nebo "neexistuje sloupec popisku v datové sadě s hodnocením k porovnání".

    Této chybě se můžete vyhnout přidáním modulu [Upravit metadata](edit-metadata.md) před modulem [vyhodnocení modelu](evaluate-model.md) . K výběru sloupce třída použijte selektor sloupců a v rozevíracím seznamu **pole** vyberte možnost **popisek**.

3. Pomocí příkazu [Spustit skript](execute-python-script.md) v jazyce Python můžete upravit kategorie sloupců popisku na hodnotu 1 (kladné, normální) a 0 (záporná, abnormální).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Technické poznámky

Tento algoritmus používá DPS k aproximaci podprostoru obsahujícího normální třídu. Podprostor je rozložený podle eigenvectors přidruženého k hornímu eigenvalues matice kovariance dat. U každého nového vstupu detektor anomálií nejprve vypočítá svou projekci na eigenvectors a pak vypočítá normalizovanou chybu rekonstrukce. Tato chyba je skóre anomálie. Čím vyšší je chyba, tím více neobvyklé instanci. Podrobnosti o tom, jak se počítá normální místo, najdete v tématu Wikipedii: [Analýza hlavní komponenty](https://wikipedia.org/wiki/Principal_component_analysis) . 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb specifických pro moduly návrháře naleznete v tématu [výjimky a kódy chyb pro návrháře (Preview)](designer-error-codes.md) .