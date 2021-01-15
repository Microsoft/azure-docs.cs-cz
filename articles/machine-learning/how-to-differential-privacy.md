---
title: Jak zachovat ochranu dat pomocí balíčků SmartNoise (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí balíčků SmartNoise použít pro Azure Machine Learning modelů rozdílové Doporučené postupy ochrany osobních údajů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 12/21/2020
ms.openlocfilehash: ebc14d6c9d0ebaa6e0258578f94771c9f286bdb4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221931"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Použití rozdílového soukromí v Azure Machine Learning (Preview)

Naučte se, jak pomocí balíčků SmartNoise Pythonu použít pro Azure Machine Learning modelů rozdílové Doporučené postupy ochrany osobních údajů.

Rozdílová ochrana osobních údajů je zlatá standardní definice ochrany osobních údajů. Systémy, které vyhovují této definici ochrany osobních údajů, poskytují silné záruky proti široké škále útoků na obnovu a reidentifikaci dat, včetně útoků nežádoucí osoby, kteří mají pomocné informace. Přečtěte si další informace o [fungování rozdílového soukromí](./concept-differential-privacy.md).


## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-packages"></a>Nainstalovat balíčky SmartNoise

### <a name="standalone-installation"></a>Samostatná instalace

Knihovny jsou navržené tak, aby pracovaly s distribuovanými Clustery Spark a je možné je nainstalovat stejně jako jakýkoli jiný balíček.

Následující pokyny předpokládají, že vaše `python` `pip` příkazy a jsou namapovány na `python3` a `pip3` .

Pomocí PIP nainstalujte [balíčky Pythonu SmartNoise](https://pypi.org/project/opendp-smartnoise/).

`pip install opendp-smartnoise`

Pokud chcete ověřit, že jsou balíčky nainstalované, spusťte příkazový řádek Pythonu a zadejte:

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

Pokud importy proběhly úspěšně, knihovny se nainstalují a připravené k použití.

### <a name="docker-image"></a>Image Dockeru

Balíčky SmartNoise můžete také použít s Docker.

Načetli `opendp/smartnoise` image pro použití knihoven uvnitř kontejneru Docker, který obsahuje Spark, Jupyter a vzorový kód.

```sh
docker pull opendp/smartnoise:privacy
```

Po dokončení image spusťte Jupyter Server:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

Tím se na svém portu spustí server Jupyter `8989` `localhost` s heslem `pass@word99` . Za předpokladu, že jste použili příkazový řádek výše ke spuštění kontejneru s názvem `smartnoise-privacy` , můžete otevřít terminál bash na serveru Jupyter spuštěním příkazu:

```sh
docker exec -it smartnoise-run bash
```

Instance Docker vymaže všechny stavy při vypnutí, takže ztratíte všechny poznámkové bloky, které vytvoříte ve spuštěné instanci. Chcete-li tento problém napravit, můžete připojit místní složku ke kontejneru při jeho spuštění:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

Všechny poznámkové bloky, které vytvoříte ve složce *Moje poznámkové bloky* , budou uloženy v místním systému souborů.

## <a name="perform-data-analysis"></a>Provést analýzu dat

Chcete-li připravit rozdílnou soukromou verzi, je třeba zvolit zdroj dat, statistiku a některé parametry ochrany osobních údajů, které označují úroveň ochrany osobních údajů.  

Tato ukázka odkazuje na veřejné použití microdata (PUMS) pro Kalifornie, který představuje anonymní záznamy občanů v občanech:

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

V tomto příkladu vypočítáme průměr a odchylku stáří.  Používáme celkem `epsilon` 1,0 (Epsilon je náš parametr ochrany osobních údajů a šíříme rozpočet ochrany osobních údajů napříč dvěma množstvími, která chceme počítat. Přečtěte si další informace o [metrikách ochrany osobních údajů](concept-differential-privacy.md#differential-privacy-metrics).

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Výsledky vypadají přibližně takto:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

V tomto příkladu si můžete všimnout některých důležitých věcí.  Nejprve `Analysis` objekt představuje graf zpracování dat.  V tomto příkladu je průměr a odchylka vypočítána ze stejného zdrojového uzlu.  Můžete však zahrnout složitější výrazy, které kombinují vstupy s výstupy v libovolném způsobu.

Graf analýz zahrnuje `data_upper` metadata a `data_lower` , které určují dolní a horní meze stáří.  Tyto hodnoty se používají k přesnému kalibraci hluku, aby bylo zajištěno rozdílové soukromí.  Tyto hodnoty se používají také při některém zpracování mimo jiné nebo chybějících hodnot.

Graf analýzy nakonec udržuje přehled o celkovém počtu spotřebovaných rozpočtů ochrany osobních údajů.

Knihovnu můžete použít k vytvoření složitějších analytických grafů s několika mechanismy, statistikami a obslužnými funkcemi:

| Statistika    | Mechanismy | Nástroje  |
| ------------- |------------|------------|
| Počet         | Gaussovské   | Změna typu       |
| Histogram     | 2D  | Upnutí   |
| Mean          | Laplace    | Digitalizaci   |
| Kvantily     |            | Filtrovat     |
| Sum           |            | Imputace |
| Variance/kovariance |      | Transformace  |

Další podrobnosti najdete v [poznámkovém bloku analýzy dat](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb) .

## <a name="approximate-utility-of-differentially-private-releases"></a>Přibližný nástroj rozdílných privátních vydání

Vzhledem k tomu, že rozdílová ochrana osobních údajů funguje kalibrací hluku, může se nástroj pro vydání lišit v závislosti na riziku ochrany osobních údajů.  Obecně platí, že hluk potřebný k ochraně jednotlivých jednotlivců je zanedbatelný, protože velikosti vzorků mají větší velikost, ale zahltí výsledek pro vydané verze, které cílí na jednu jednotlivou osobu.  Analytiké můžou zkontrolovat informace o přesnosti pro vydání, aby zjistili, jak užitečnou verzi je:

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Výsledek této operace by měl vypadat podobně jako v následujícím příkladu:

```text
Age accuracy is: 0.2995732273553991
```

Tento příklad vypočítá průměr jak je uvedeno výše, a použije `get_accuracy` funkci k vyžádání přesnosti v `alpha` 0,05. `alpha`Hodnota 0,05 představuje 95% interval. v takovém případě se tato vydaná hodnota bude nacházet v rámci hlášené meze přesnosti o 95% času.  V tomto příkladu je nahlášená přesnost 0,3, což znamená, že vydaná hodnota bude v intervalu šířky 0,6, přibližně 95% času.  Není správné považovat tuto hodnotu za chybovou úsečku, protože vydaná hodnota se zachová mimo hlášené rozmezí přesnosti v míře určené hodnotou `alpha` a hodnoty mimo rozsah můžou být mimo libovolný směr.

Analytici se můžou dotazovat na `get_accuracy` různé hodnoty `alpha` , abyste získali zúžené a širší intervaly spolehlivosti, aniž by se museli účtovat další náklady na ochranu osobních údajů.

## <a name="generate-a-histogram"></a>Generování histogramu

Integrovaná `dp_histogram` funkce vytváří rozdílové soukromé histogramy přes kterýkoli z následujících datových typů:

- Souvislá proměnná, ve které je třeba rozdělit sadu čísel do přihrádek
- Logická nebo dichotomous proměnná, která může probíhat pouze pro dvě hodnoty
- Proměnná kategorií, ve které jsou různé kategorie výčtové jako řetězce

Tady je příklad `Analysis` určení přihrádek pro souvislý histogram proměnných:

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Vzhledem k tomu, že jednotlivci jsou mezi přihrádkami bez společného dělení, náklady na ochranu osobních údajů se účtují pouze jednou za histogram, a to i v případě, že histogram obsahuje mnoho přihrádek.

Další informace o histogramech najdete v [poznámkovém bloku histogramy](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Generování matice kovariance

SmartNoise nabízí tři různé funkce s její `dp_covariance` funkcí:

- Kovariance mezi dvěma vektory
- Matice Kovariance matice
- Matice křížových odchylek dvojice matic

Tady je příklad výpočtu skalární kovariance:

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Další informace najdete v [poznámkovém bloku kovariance](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb) .

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [ukázkové poznámkové bloky SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis).