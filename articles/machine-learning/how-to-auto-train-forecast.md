---
title: Automatické učení modelu prognózy časových řad
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Machine Learning ke studiu odhadu regresního modelu časových řad pomocí automatizovaného strojového učení.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 57b54fbe20df4eb74ee17c7b5ac83d773114463b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437367"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatické učení modelu prognózy časových řad


V tomto článku se dozvíte, jak pomocí automatizovaného strojového učení a AutoML [Azure Machine Learning v sadě SDK Pythonu](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)nakonfigurovat a naučit regresní model předpovědi časových řad. 

Uděláte to takto: 

> [!div class="checklist"]
> * Příprava dat pro modelování časových řad.
> * Nakonfigurujte konkrétní parametry časové řady v [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objektu.
> * Spusťte předpovědi s daty časových řad.

Pro používání s nízkým kódem si přečtěte [kurz: Předpověď poptávky pomocí automatizovaného strojového učení s](tutorial-automated-ml-forecast.md) využitím automatizovaného strojového učení v [Azure Machine Learning Studiu](https://ml.azure.com/).

Na rozdíl od metod klasických časových řad jsou v automatizovaných ML hodnotách časových řad "pivoted", aby se do regresory staly další dimenze společně s jinými koproměnnými. Tento přístup zahrnuje během školení více kontextových proměnných a jejich vztah mezi sebou. Vzhledem k tomu, že předpověď může ovlivnit několik faktorů, tato metoda se dobře zarovnává s scénáři reálného vývoje. Například při prognózování prodejů, interakcí s historickými trendy, směnným kursem a cenou všech společně provedou všechny výsledky prodeje. 

## <a name="prerequisites"></a>Požadavky

Pro tento článek potřebujete, 

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](how-to-manage-workspace.md).

* V tomto článku se předpokládá, že jste nastavili automatizovaný experiment strojového učení. Pomocí [kurzu](tutorial-auto-train-models.md) nebo [postupu](how-to-configure-auto-train.md) si můžete prohlédnout hlavní modely návrhu experimentů automatizovaného strojového učení.

## <a name="preparing-data"></a> Příprava dat

Nejdůležitější rozdíl mezi typem úkolu regrese regrese a typem úlohy regrese v rámci AutoML je, včetně funkce v datech, která představuje platnou časovou řadu. Pravidelná časová řada má jasně definovanou a konzistentní frekvenci a má hodnotu pro každý vzorový bod v souvislém časovém intervalu. 

Vezměte v úvahu následující snímek souboru `sample.csv` .
Tato datová sada má denní prodejní data pro společnost, která má dvě různá úložiště, a a B. 

Kromě toho jsou k dispozici funkce pro

 *  `week_of_year`: umožňuje modelu detekovat sezónnost týdně.
* `day_datetime`: představuje čistou časovou řadu s denní frekvencí.
* `sales_quantity`: cílový sloupec pro spuštění předpovědi. 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Přečtěte si data do PANDAS dataframe a pak použijte `to_datetime` funkci, abyste zajistili, že časová řada je `datetime` typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

V tomto případě jsou data již seřazena vzestupně podle pole čas `day_datetime` . Při nastavování experimentu se ale ujistěte, že požadovaný sloupec čas je seřazen vzestupně, aby se vytvořila platná časová řada. 

Následující kód: 
* Předpokládá, že data obsahují 1 000 záznamů a vytvoří deterministické rozdělení dat pro vytváření školicích a testovacích sad dat. 
* Identifikuje sloupec popisku jako `sales_quantity` .
* Odděluje pole popisku od `test_data` pro vytvoření `test_target` sady.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> Při výuce modelu pro předpověď budoucích hodnot se ujistěte, že všechny funkce používané v rámci školení můžou být použité pro předpovědi pro zamýšlené horizonty. <br> <br>Například při vytváření prognózy poptávky, včetně funkce pro aktuální cenu akcií, se může zvýšit přesnost školení. Pokud máte v úmyslu předpovědi s dlouhým horizontem, možná nebudete schopni přesně předpovědět budoucí hodnoty v budoucích zásobách, které odpovídají budoucím bodům časových řad, a přesnost modelu by mohla být zhoršená.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Školení a ověřovací data

Můžete určit samostatné sady vlaků a ověřovacích sad přímo v `AutoMLConfig` objektu.   Přečtěte si další informace o [AutoMLConfig](#configure-experiment).

V případě prognózy časových řad se pro ověřování ve výchozím nastavení používá pouze **ověřování ROCV (Kumulovaný původ)** . Předejte data o školení a ověření společně a nastavte počet přeložení pro vzájemné ověření pomocí `n_cross_validations` parametru v `AutoMLConfig` . ROCV rozdělí řadu na data o školení a ověření pomocí počátečního časového bodu. Posunutí zdroje v čase generuje skládání křížového ověření. Tato strategie zachovává integritu dat časové řady a eliminuje riziko úniku dat.

![překročení počátečního ověřování](./media/how-to-auto-train-forecast/ROCV.svg)

Můžete také použít vlastní ověřovací data. Další informace najdete v v [AutoML konfigurace rozdělení dat a křížového ověřování v nástroji](how-to-configure-cross-validation-data-splits.md#provide-validation-data).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

Přečtěte si další informace o tom, jak AutoML používá křížové ověřování, aby se [předešlo navýšení modelů](concept-manage-ml-pitfalls.md#prevent-over-fitting)

## <a name="configure-experiment"></a>Konfigurovat experiment

[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py)Objekt definuje nastavení a data potřebná pro úkol automatizovaného strojového učení. Konfigurace pro model prognózy je podobná nastavení standardního regresního modelu, ale některé modely, možnosti konfigurace a featurization kroky existují konkrétně pro data časových řad. 

### <a name="supported-models"></a>Podporované modely
Automatizované strojové učení automaticky zkouší různé modely a algoritmy jako součást procesu vytváření a ladění modelu. Jako uživatel není nutné zadávat algoritmus. Pro předvídání experimentů jsou nativní modely časových řad i obsáhlého učení součástí systému doporučení. Následující tabulka shrnuje tuto podmnožinu modelů. 

>[!Tip]
> Tradiční regresní modely jsou testovány také jako součást systému doporučení pro předpovědi experimentů. Úplný seznam modelů najdete v [tabulce podporovaných modelů](how-to-configure-auto-train.md#supported-models) . 

Modely| Popis | Výhody
----|----|---
Prophet (Preview)|Prophet funguje nejlépe s časovou řadou, která má silné sezónní účinky a několik období historických dat. Pokud chcete tento model využít, nainstalujte ho místně pomocí `pip install fbprophet` . | Přesná & rychlá, robustní k vydaným hodnotám, chybějící data a výrazné změny v časové řadě.
Auto-ARIMA (Preview)|Pokud jsou data stacionární, provede automaticky regresivní integrovaný klouzavý průměr (ARIMA). To znamená, že jeho statistické vlastnosti, jako je střední hodnota a rozptyl, jsou v celé sadě konstantní. Například při překlopení mince je pravděpodobnost, že se vám povede k získání hlav, 50%, bez ohledu na to, jestli jste překlopi dnes, zítra nebo příštího roku.| Skvělé pro univariate Series, protože minulé hodnoty se používají k předpovědi budoucích hodnot.
ForecastTCN (Preview)| ForecastTCN je neuronové síťový model navržený tak, aby se vypořádat s nejnáročnějšími úkoly prognózování, zachytávání nelineárních místních a globálních trendů ve vašich datech a také vztahů mezi časovými řadami.|Umožňuje využití složitých trendů ve vašich datech a umožňuje se snadno škálovat na největší z datových sad.

### <a name="configuration-settings"></a>Nastavení konfigurace

Podobně jako u regresního problému definujete standardní parametry školení, jako je typ úkolu, počet iterací, školicích dat a počet křížových ověření. Pro úlohy prognózy existují další parametry, které musí být nastaveny, které mají vliv na experiment. 

Následující tabulka shrnuje tyto další parametry. Vzory návrhu syntaxe najdete v [referenční dokumentaci třídy ForecastingParameter](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) .

| &nbsp;Název parametru | Popis | Povinné |
|-------|-------|-------|
|`time_column_name`|Slouží k zadání sloupce data a času ve vstupních datech použitých k vytvoření časové řady a odvození frekvence.|✓|
|`forecast_horizon`|Definuje, kolik období má být předáno předpovědi. Horizont je v jednotkách časové řady. Jednotky jsou založené na časovém intervalu vašich školicích dat, například měsíčně, týdně, kdy by měl prognóza předpovědět.|✓|
|`enable_dnn`|[Povolte prognózování hluboké]().||
|`time_series_id_column_names`|Názvy sloupců, které slouží k jednoznačné identifikaci časových řad v datech s více řádky se stejným časovým razítkem. Pokud nejsou definovány identifikátory časových řad, předpokládá se, že datová sada bude jedna časová řada. Další informace o jednotlivých časových řadách najdete v [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).||
|`freq`| Frekvence datových řad časové řady. Tento parametr představuje období, se kterým se mají události očekávat, například denně, týdně, ročně atd. Frekvence musí být [PANDAS odsazený alias](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects).||
|`target_lags`|Počet řádků pro prodlevu cílových hodnot na základě frekvence dat Prodleva je vyjádřena jako seznam nebo jedno celé číslo. Je nutné použít prodlevu v případě, že vztah mezi nezávislými proměnnými a závislou proměnnou se ve výchozím nastavení neshoduje nebo koreluje. ||
|`feature_lags`| Funkce pro prodlevu se automaticky určí pomocí automatického ML, pokud `target_lags` jsou nastavené a `feature_lags` nastavené na `auto` . Povolení funkcí prodlevy může pomoci zlepšit přesnost. Funkce prodlevy jsou ve výchozím nastavení zakázané. ||
|`target_rolling_window_size`|*n* historická období, která se mají použít ke generování předpokládaných hodnot, <= velikost sady školení Pokud tento parametr vynecháte, *n* je úplná velikost sady školení. Tento parametr zadejte, pokud chcete při výuce modelu vzít v úvahu jen určitou velikost historie. Přečtěte si další informace o [agregaci cílového souhrnného okna](#target-rolling-window-aggregation).||
|`short_series_handling_config`| Umožňuje krátkou manipulaci s časovou řadou, aby nedocházelo k selhání během školení z důvodu nedostatečného množství dat. Ve výchozím nastavení je zpracování krátkých řad nastaveno na hodnotu `auto` . Přečtěte si další informace o [zpracování krátkých řad](#short-series-handling).|


Následující kód: 
* Využívá [`ForecastingParameters`](https://docs.microsoft.com/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) třídu k definování parametrů předpovědi pro školení experimentů.
* Nastaví na `time_column_name` `day_datetime` pole v datové sadě. 
* Definuje `time_series_id_column_names` parametr pro `"store"` . Tím se zajistí, že se pro data vytvoří **dvě samostatné skupiny časových řad** . jednu pro úložiště a a B.
* Nastaví na `forecast_horizon` 50, aby bylo možné předpovědět celou sadu testů. 
* Nastaví okno prognózy na 10 teček s `target_rolling_window_size`
* Určuje jednu prodlevu na cílových hodnotách pro dvě tečky předem s `target_lags` parametrem. 
* Nastaví `target_lags` Doporučené nastavení "automatické", které tuto hodnotu automaticky detekuje za vás.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

Ty `forecasting_parameters` se pak předají do standardního `AutoMLConfig` objektu společně s `forecasting` typem úkolu, primární metrikou, kritériem ukončení a školicími daty. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

### <a name="featurization-steps"></a>Featurization kroky

V každém automatizovaném experimentu machine learningu se pro vaše data ve výchozím nastavení aplikují automatické škálování a normalizační techniky. Tyto techniky jsou typy **featurization** , které usnadňují *určité* algoritmy, které jsou citlivé na funkce v různých měřítkech. Další informace o výchozích krocích featurization v [featurization v AutoML](how-to-configure-auto-features.md#automatic-featurization)

Následující kroky jsou však provedeny pouze pro `forecasting` typy úloh:

* Detekuje četnost vzorkování časové řady (například každou hodinu, denně, týdně) a vytvoří nové záznamy pro nepřítomné časové body, aby se řada souvislá.
* Imputace chybějící hodnoty v cíli (prostřednictvím předávaného sloupce) a sloupců funkcí (pomocí hodnot sloupců mediánu)
* Vytváření funkcí založených na identifikátorech časových řad, které umožňují pevné efekty v různých řadách
* Vytváření funkcí založených na čase, které vám pomůžou při učení se sezónními vzory
* Kódovat proměnné kategorií na číselné množství

Informace o tom, jaké funkce jsou vytvořeny v důsledku těchto kroků, najdete v tématu [Featurization transparentnost](how-to-configure-auto-features.md#featurization-transparency) .

> [!NOTE]
> Automatické kroky featurization strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky featurization, jaké jste použili během školení.

#### <a name="customize-featurization"></a>Přizpůsobení featurization

Máte také možnost přizpůsobit si nastavení featurization, abyste zajistili, že data a funkce, které se použijí k tomu, aby využívaly svůj model ML, budou relevantní předpovědi. 

Mezi podporovaná přizpůsobení pro `forecasting` úlohy patří:

|Přizpůsobení|Definice|
|--|--|
|**Aktualizace pro účely sloupce**|Přepíše automaticky zjištěný typ funkce pro zadaný sloupec.|
|**Aktualizace parametrů transformátoru** |Aktualizuje parametry pro zadaný transformátor. V současné době podporuje *imputac* (fill_value a medián).|
|**Odkládací sloupce** |Určuje sloupce, které se mají odpustit z natrénuje.|

Chcete-li přizpůsobit featurizations pomocí sady SDK, zadejte `"featurization": FeaturizationConfig` do `AutoMLConfig` objektu. Přečtěte si další informace o [vlastních featurizations](how-to-configure-auto-features.md#customize-featurization).

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Pokud pro váš experiment používáte Azure Machine Learning Studio, přečtěte si téma [Postup přizpůsobení featurization v studiu](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

## <a name="optional-configurations"></a>Volitelné Konfigurace

Další volitelné konfigurace jsou k dispozici pro úlohy předpovědi, jako je například umožnění hloubkového učení a určení cílové agregované kumulativní okna. 

### <a name="enable-deep-learning"></a>Povolit hloubkové učení

> [!NOTE]
> Podpora DNN pro prognózování v automatizovaných Machine Learning je ve **verzi Preview** a není podporovaná pro místní běhy.

Můžete také využít obsáhlý Learning s hlubokými neuronové sítěmi hluboké, abyste vylepšili skóre svého modelu. Obsáhlý Learning v automatizovaném ML umožňuje prognózování dat univariate a lineární časových řad.

Modely hloubkového učení mají tři vnitřní možnosti:
1. Můžou se učit z libovolného mapování ze vstupů na výstupy.
1. Podporují několik vstupů a výstupů.
1. Mohou automaticky extrahovat vzory ve vstupních datech, která jsou rozložena přes dlouhé sekvence. 

Pokud chcete povolit hloubkové učení, nastavte `enable_dnn=True` v `AutoMLConfig` objektu.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> Pokud povolíte DNN pro experimenty vytvořené pomocí sady SDK, [nejlepší vysvětlení modelů](how-to-machine-learning-interpretability-automl.md) jsou zakázané.

Pokud chcete povolit DNN pro experiment AutoML vytvořený v Azure Machine Learning studiu, přečtěte si téma [Nastavení typu úlohy v tématu Postup](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

Podrobný příklad kódu, který využívá hluboké, najdete v [poznámkovém bloku pro vytváření předpovědí pro produkci nápojů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) .

### <a name="target-rolling-window-aggregation"></a>Cílová agregace návratového okna
Nejlepší informace, které může vytvořit předpověď, jsou často poslední hodnotou cíle.  Cílová agregace kumulovaných oken vám umožní přidat do funkcí hromadnou agregaci hodnot dat. Vytváření a používání těchto dalších funkcí jako dodatečných kontextových dat pomáhá s přesností modelu vlaku.

Řekněme například, že chcete předpovědět spotřebu energie. Pro tepelné změny v zahřívanách prostorech můžete chtít přidat funkci postupného okna na tři dny. V tomto příkladu vytvořte toto okno nastavením `target_rolling_window_size= 3` v `AutoMLConfig` konstruktoru. 

V tabulce je zobrazen výsledný inženýr funkcí, který nastane při použití agregace okna. Sloupce pro **minimální, maximální** a **součet** se generují na posuvné okno tři na základě definovaných nastavení. Každý řádek obsahuje novou vypočítanou funkci v případě časového razítka pro 8. září 2017:10:00 hodnoty maxima, minima a suma se počítají pomocí **hodnot požadavků** pro 8. září 2017 1:10:00-3:10:00. V tomto okně se třemi posunutími naplní data pro zbývající řádky.

![cílové posuvné okno](./media/how-to-auto-train-forecast/target-roll.svg)

Podívejte se na příklad kódu Pythonu s využitím [agregované agregační funkce](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)pro souhrnné okno.

### <a name="short-series-handling"></a>Zpracování krátkých řad

Automatizované ML považuje časovou řadu za **krátkou řadu** , pokud není k dispozici dostatek datových bodů, aby bylo možné provádět fáze vlaků a ověření modelu vývoje. Počet datových bodů se u každého experimentu liší a závisí na max_horizon, počtu rozdělení křížového ověření a délce lookbackí modelu, což je maximální historie, která je potřeba k vytvoření funkcí časové řady. Přesný výpočet najdete v [dokumentaci short_series_handling_config reference](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py#short-series-handling-configuration).

Automatizované ML nabízí ve výchozím nastavení krátké zpracování řady s `short_series_handling_config` parametrem v `ForecastingParameters` objektu. 

Aby bylo možné povolit krátkodobé zpracování řad, je `freq` nutné definovat i parametr. Chcete-li změnit výchozí chování, `short_series_handling_config = auto` , aktualizujte `short_series_handling_config` parametr v `ForecastingParameter` objektu.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_config='auto',
                                            freq = '7',
                                            target_lags='auto')
```
Následující tabulka shrnuje dostupná nastavení pro `short_series_handling_config` .
 
|Nastavení|Popis
|---|---
|`auto`| Toto je výchozí chování pro krátkodobé zpracování řad. <li> *Pokud jsou všechny řady krátké*, dosadí data. <br> <li> *Pokud ne všechny řady jsou krátké*, vyřaďte krátké řady. 
|`pad`| Pokud `short_series_handling_config = pad` a pak automatizovaná ml přidá do každé krátké řady fiktivní hodnoty. Následující seznam uvádí typy sloupců a jejich čalounění: <li>Sloupce objektů s hodnoty NaN <li> Číselné sloupce s 0 <li> Logické a logické sloupce s hodnotou false <li> Cílový sloupec je doplněn náhodnými hodnotami se střední hodnotou nula a směrodatnou odchylku 1. 
|`drop`| Pokud `short_series_handling_config = drop` je, pak automatizované ml ponechá krátkou řadu a nebude se používat pro školení nebo předpovědi. Předpovědi pro tyto řady vrátí NaN.
|`None`| Žádná řada není doplněna nebo vyřazena.

>[!WARNING]
>Odsazení může mít vliv na přesnost výsledného modelu, protože zavádíme umělá data jenom k tomu, abyste získali minulé školení bez chyb. <br> <br> Pokud je mnoho z řad krátkých, může se také zobrazit nějaký dopad na výsledky vysvětlení.

## <a name="run-the-experiment"></a>Spusťte experiment. 

Když máte `AutoMLConfig` objekt připravený, můžete experiment odeslat. Po dokončení modelu načtěte nejlepší iteraci spuštění.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>Prognózování s nejlepším modelem

Použijte nejlepší modelovou iteraci pro předpověď hodnot sady dat testu.

`forecast()`Funkce umožňuje specifikace, pokud má předpovědi začít, na rozdíl od `predict()` , který je obvykle používán pro klasifikaci a regresní úkoly.

V následujícím příkladu je třeba nejprve nahradit všechny hodnoty v `y_pred` `NaN` . V tomto případě bude zdroj prognózy na konci školicích dat. Pokud jste však nahradili pouze druhou polovinu z `y_pred` s `NaN` , funkce by v první polovině nezměněných hodnot nechala tyto číselné hodnoty, ale předpověď `NaN` hodnot v druhé polovině. Funkce vrátí předpovězené hodnoty i zarovnané funkce.

Můžete také použít `forecast_destination` parametr ve `forecast()` funkci k předpovědi hodnot až do zadaného data.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Vypočítat hlavní střední hodnotu chyby (RMSE) mezi `actual_labels` skutečnými hodnotami a předpovězenými hodnotami v `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Teď, když je zjištěná přesnost celkového modelu, je nejrealističtějším dalším krokem použití modelu k předpovědi neznámých budoucích hodnot. 

Poskytněte datovou sadu ve stejném formátu jako sadu testů `test_data` , ale s budoucími DateTime a výslednou předpokládanou sadou je předpověď hodnot pro každý krok časové řady. Předpokládejte, že poslední záznamy časových řad v datové sadě byly pro 12/31/2018. Chcete-li odhadnout poptávku pro následující den (nebo tolik období, kolik potřebujete pro předpověď <= `forecast_horizon` ), vytvořte jeden záznam časových řad pro každé úložiště pro 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Zopakováním potřebných kroků načtěte tato budoucí data do datového rámce a potom spusťte příkaz `best_run.predict(test_data)` pro předpověď budoucích hodnot.

> [!NOTE]
> Hodnoty nelze předpovědět pro počet období, který je větší než `forecast_horizon` . Model musí být znovu vyškolen s větším horizontem, aby bylo možné předpovědět budoucí hodnoty nad rámec aktuálního horizontu.


## <a name="example-notebooks"></a>Příklady poznámkových bloků
Podrobné příklady kódu pro pokročilou konfiguraci prognózování najdete v [poznámkových blocích ukázkových poznámkách k prognózám](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) , včetně:

* [detekce svátků a featurization](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [křížové ověření pro návratové zdroje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurovatelné prodlevy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [souhrnné funkce kumulovaných oken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [tom, jak a kde model nasadit](how-to-deploy-and-where.md).
* Přečtěte si o [výkladu: vysvětlení modelů v automatizovaném strojovém učení (Preview)](how-to-machine-learning-interpretability-automl.md). 
* Naučte se, jak proškolit několik modelů pomocí AutoML v [mnoha akcelerátorech řešení modelů](https://aka.ms/many-models).
* V tomto [kurzu](tutorial-auto-train-models.md) se dozvíte, jak na konci vytvořit experimenty s automatickým Machine Learningem.
