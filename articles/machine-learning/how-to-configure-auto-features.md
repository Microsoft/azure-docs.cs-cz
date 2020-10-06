---
title: Featurization v AutoML experimenty
titleSuffix: Azure Machine Learning
description: Přečtěte si, jaká nastavení featurization Azure Machine Learning nabídky a jak se v automatizovaných experimentech v ML podporují technické funkce.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 0138715e4c9df8ae05c9a3eade64d539eb7cdeda
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756547"
---
# <a name="featurization-in-automated-machine-learning"></a>Featurization v automatizovaném strojovém učení



V této příručce se naučíte:

- Jaká nastavení featurization Azure Machine Learning nabídky.
- Přizpůsobení těchto funkcí pro [automatizované experimenty strojového učení](concept-automated-ml.md).

*Inženýrské funkce* je proces využití dat v doméně k vytváření funkcí, které usnadňují používání algoritmů strojového učení (ml) k lepšímu učení. V Azure Machine Learning se pro usnadnění vývoje funkcí používají techniky pro škálování dat a normalizaci. Souhrnně tyto techniky a technické funkce se nazývají *featurization* v automatizovaném strojovém učení nebo v *AutoML*experimenty.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že už víte, jak nakonfigurovat AutoML experiment. Informace o konfiguraci najdete v následujících článcích:

- Pro prostředí s prvním kódem: [nakonfigurujte automatizované experimenty ml pomocí sady Azure Machine Learning SDK pro Python](how-to-configure-auto-train.md).
- Pro použití s nízkým kódováním nebo prostředí bez kódu: [Vytvářejte, kontrolujte a nasaďte automatizované modely strojového učení pomocí Azure Machine Learning studia](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Konfigurace featurization

V každém automatizovaném experimentu machine learningu se pro vaše data ve výchozím nastavení aplikují [Automatické škálování a normalizační techniky](#featurization) . Tyto techniky jsou typy featurization, které usnadňují *určité* algoritmy, které jsou citlivé na funkce v různých měřítkech. Můžete ale také povolit další featurization, jako je například imputace, *kódování*a *transformace* *chybějících hodnot*.

> [!NOTE]
> Kroky pro automatizované featurization strojového učení (například normalizace funkcí, zpracování chybějících dat nebo převod textu na číslo) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky featurization, které se použijí během školení.

U experimentů, které nakonfigurujete pomocí sady Python SDK, můžete povolit nebo zakázat nastavení featurization a dále určit kroky featurization, které se mají použít pro váš experiment. Pokud používáte Azure Machine Learning Studio, přečtěte si [Postup povolení featurization](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

V následující tabulce jsou uvedena přijímaná nastavení pro `featurization` ve [třídě AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Konfigurace Featurization | Popis|
------------- | ------------- |
|`"featurization": 'auto'`| Určuje, že v rámci předběžného zpracování jsou [kroky guardrails dat a featurization](#featurization) provedeny automaticky. Toto nastavení je výchozí.|
|`"featurization": 'off'`| Určuje, že kroky featurization se nemají automaticky provádět.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Určuje, že se mají použít přizpůsobené kroky featurization. [Přečtěte si, jak přizpůsobit featurization](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automaticky featurization

Následující tabulka shrnuje techniky, které jsou automaticky aplikovány na vaše data. Tyto techniky se aplikují na experimenty, které jsou nakonfigurované pomocí sady SDK nebo studia. Chcete-li toto chování zakázat, nastavte `"featurization": 'off'` v `AutoMLConfig` objektu.

> [!NOTE]
> Pokud plánujete exportovat AutoML vytvořené modely do [modelu ONNX](concept-onnx.md), ve formátu ONNX se podporují jenom možnosti featurization označené hvězdičkou (*). Přečtěte si další informace o [převodu modelů na ONNX](concept-automated-ml.md#use-with-onnx).

|Featurization &nbsp; kroky| Popis |
| ------------- | ------------- |
|**Přetáhnout vysokou mohutnost nebo žádné funkce odchylky*** |Tyto funkce přetáhněte ze sady školení a ověření. Platí pro funkce se všemi chybějícími hodnotami, se stejnou hodnotou ve všech řádcích nebo s vysokou mohutnou (například hodnoty hash, ID nebo identifikátory GUID).|
|**Imputace – chybějící hodnoty*** |Pro číselné funkce imputace s průměrem hodnot ve sloupci.<br/><br/>V případě funkcí kategorií se imputac s nejčastější hodnotou.|
|**Generování dalších funkcí*** |Pro funkce DateTime: rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodina, minuta, sekunda.<br><br> *Pro úlohy předpovědi* jsou vytvořeny tyto další funkce DateTime: ISO year, pololetí, kalendářní měsíc jako řetězec, týden, den v týdnu jako řetězec, den čtvrtletí, den v roce, dop. odp. (0, pokud je hodina v hodnotě poledne (12 ODP), 1 jinak), AM/PM jako řetězec, hodina dne (12 hodin).<br/><br/>Pro funkce textu: četnost termínů založená na unigrams, bigrams a trigrams. Přečtěte si další informace o [tom, jak to uděláte pomocí Bert.](#bert-integration)|
|**Transformace a kódování***|Transformujte číselné funkce, které mají v kategorií funkce několik jedinečných hodnot.<br/><br/>Pro funkce kategorií s nízkou mohutnou se používá kódování One-Hot. Kódování One-Hot-hash se používá pro funkce kategorií s vysokou mohutnosti.|
|**Vkládání slov**|Text featurizer převede vektory textových tokenů na vektory vět pomocí předem připraveného modelu. Vektory vložení každého slova v dokumentu jsou agregovány s využitím zbytku pro vytvoření vektoru funkce dokumentu.|
|**Cílová kódování**|V případě funkcí kategorií tento krok mapuje každou kategorii s průměrnou cílovou hodnotou pro regresní problémy a pravděpodobností třídy pro jednotlivé třídy pro problémy s klasifikací. Pro snížení přeložení mapování a šumu způsobených kategoriemi zhuštěných dat se aplikují váhy založené na kmitočtech a k skládání k.|
|**Kódování cílového textu**|V případě textového vstupu se pro generování pravděpodobnosti každé třídy používá skládaný lineární model s použitím typu penalta-slova.|
|**Váha důkazů (WoE)**|Vypočítá WoE jako míru korelace kategorií sloupců do cílového sloupce. WoE se počítá jako protokol poměru mezi třídou a pravděpodobnosti mimo třídu. Tento krok vytvoří jeden sloupec číselné funkce na každou třídu a odstraní nutnost explicitně imputace chybějících hodnot a izolované zpracování.|
|**Vzdálenost clusteru**|Vlaky a k – znamenají model clusteringu ve všech číselných sloupcích. Vytvoří nové funkce ( *jedna nová číselná* funkce na cluster), které obsahují vzdálenost jednotlivých vzorků k těžiště každého clusteru.|

## <a name="data-guardrails"></a>Guardrails dat

*Data guardrails* vám pomůžou identifikovat možné problémy s daty (například chybějící hodnoty nebo [nevyrovnanost třídy](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Také vám pomůžou provést opravné akce pro lepší výsledky.

Data guardrails se používají:

- **Pro experimenty sady SDK**: když `"featurization": 'auto'` jsou parametry nebo `validation=auto` zadány v `AutoMLConfig` objektu.
- **Experimenty studia**: když je povolený automatický featurization

Můžete zkontrolovat guardrails dat pro svůj experiment:

- Nastavením `show_output=True` při odeslání experimentu pomocí sady SDK.

- V nástroji Studio na kartě **data guardrails** vašeho automatizovaného běhu ml.

### <a name="data-guardrail-states"></a>Stavy guardrail dat

Guardrails dat zobrazuje jeden ze tří stavů:

|State| Popis |
|----|---- |
|**Předaný**| Nezjistily se žádné problémy s daty a nevyžaduje se žádná akce. |
|**hotovo**| Změny byly aplikovány na vaše data. Doporučujeme, abyste si probrali nápravné akce, které AutoML trvalo, aby se zajistilo, že se změny zarovnají s očekávanými výsledky. |
|**Upozorněni**| Byl zjištěn problém s daty, ale nelze jej odstranit. Doporučujeme, abyste tento problém vyřešili a opravili.|

### <a name="supported-data-guardrails"></a>Podporované guardrails dat

V následující tabulce jsou popsány aktuálně podporované guardrails dat a související stavy, které se mohou zobrazit při odeslání experimentu:

Guardrail|Status|Podmínka &nbsp; pro &nbsp; aktivační událost
---|---|---
**Chybějící hodnoty funkcí imputace** |Předaný <br><br><br> Hotovo| Ve školicích datech se nezjistily žádné chybějící hodnoty funkcí. Přečtěte si další informace o [imputaci chybějících hodnot.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Ve školicích datech se zjistily chybějící hodnoty funkcí a byly imputované.
**Zpracování funkcí vysoké mohutnosti** |Předaný <br><br><br> Hotovo| Vaše vstupy byly analyzovány a nebyly zjištěny žádné funkce vysoké mohutnosti. <br><br> Ve vašich vstupech se zjistily funkce vysoké mohutnosti a zpracovaly se.
**Zpracování děleného ověřování** |Hotovo| Konfigurace ověření byla nastavena na `'auto'` a školicí data obsahovala *méně než 20 000 řádků*. <br> Každá iterace proučeného modelu byla ověřena pomocí křížového ověření. Přečtěte si další informace o [ověřovacích datech](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> Konfigurace ověření byla nastavena na hodnotu `'auto'` a školicí data obsahují *více než 20 000 řádků*. <br> Vstupní data byla rozdělena na školicí datovou sadu a datovou sadu ověřování pro ověřování modelu.
**Detekce vyrovnávání tříd** |Předaný <br><br><br><br>Upozorněni <br><br><br>Hotovo | Vaše vstupy byly analyzovány a všechny třídy jsou ve vašich školicích datech vyváženy. Datová sada je považována za vyrovnanou, pokud každá třída má dobrou reprezentaci v datové sadě, jak je měřeno počtem a poměr vzorků. <br><br> Ve vašich vstupech se zjistily nevyvážené třídy. Pokud chcete opravit posun modelu, vyřešte problém s vyrovnáváním. Přečtěte si další informace o [vyrovnaných datech](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).<br><br> Ve vašich vstupech se zjistily nevyvážené třídy a v této logice se určilo použití vyrovnávání.
**Detekce problémů paměti** |Předaný <br><br><br><br> Hotovo |<br> Vybrané hodnoty (horizont, lag, posuvné okno) byly analyzovány a nebyly zjištěny žádné potenciální problémy způsobené nedostatkem paměti. Přečtěte si další informace o [konfiguracích prognózování](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)časových řad. <br><br><br>Vybrané hodnoty (horizont, lag, posuvné okno) byly analyzovány a mohou způsobit nedostatek paměti experimentu. Konfigurace zpoždění nebo posuvných oken byla vypnuta.
**Detekce četnosti** |Předaný <br><br><br><br> Hotovo |<br> Časová řada byla analyzována a všechny datové body jsou zarovnány s zjištěnou frekvencí. <br> <br> Časová řada byla analyzována a byly zjištěny datové body, které neodpovídají zjištěné četnosti. Tyto datové body byly z datové sady odebrány. Další informace o [přípravě dat pro prognózování časových řad](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Přizpůsobení featurization

Nastavení featurization můžete přizpůsobit tak, aby se zajistilo, že data a funkce, které se použijí k tomu, aby využívaly svůj model ML, budou relevantní předpovědi.

Chcete-li přizpůsobit featurizations, zadejte  `"featurization": FeaturizationConfig` v `AutoMLConfig` objektu. Pokud pro váš experiment používáte Azure Machine Learning Studio, přečtěte si článek s [postupem](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Postup přizpůsobení featurization pro prognózy typů úloh najdete v tématu Postup při [Prognózování](how-to-auto-train-forecast.md#customize-featurization).

Mezi podporovaná přizpůsobení patří:

|Přizpůsobení|Definice|
|--|--|
|**Aktualizace pro účely sloupce**|Pro zadaný sloupec přepište typ funkce automaticky rozpoznán.|
|**Aktualizace parametrů transformátoru** |Aktualizuje parametry pro zadaný transformátor. V současné době podporuje *imputac* (střední, nejčastější a medián) a *HashOneHotEncoder*.|
|**Odkládací sloupce** |Určuje sloupce, které se mají odpustit z natrénuje.|
|**Blokovat transformátory**| Určuje, že se mají v procesu featurization použít transformátory bloku.|

Vytvořte `FeaturizationConfig` objekt pomocí volání rozhraní API:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>Featurization transparentnost

Každý model AutoML má automaticky použit featurization.  Featurization zahrnuje automatické řízení funkcí (kdy `"featurization": 'auto'` ) a škálování a normalizaci, které pak ovlivní vybraný algoritmus a jeho hodnoty parametrů. AutoML podporuje různé metody, které vám zajistí, abyste měli přehled o tom, co bylo použito pro váš model.

Vezměte v úvahu tento příklad předpovědi:

+ Existují čtyři vstupní funkce: A (číselná), B (číselná), C (číselná), D (DateTime).
+ Číselná funkce C je vyřazena, protože se jedná o sloupec ID se všemi jedinečnými hodnotami.
+ Číselné funkce A a B chybějící hodnoty, a proto jsou ve střední části imputované.
+ Funkce DateTime D je natrénuje na 11 různých funkcí navržených pro analýzu.

Chcete-li získat tyto informace, použijte `fitted_model` výstup z automatizovaného testu ml.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Automatizovaná technologie funkcí 
`get_engineered_feature_names()`Vrátí seznam názvů funkcí s technickým názvem.

  >[!Note]
  >Použijte ' timeseriestransformer ' pro Task = ' Forecasting ', jinak použijte ' datatransformer ' pro ' regresi ' nebo ' Classification '.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Tento seznam obsahuje všechny inženýrské názvy funkcí. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()`Vrátí souhrn featurization všech vstupních funkcí.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Výstup

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Výstup|Definice|
   |----|--------|
   |RawFeatureName|Název funkce nebo sloupce vstupu ze zadané datové sady.|
   |TypeDetected|Byl zjištěn datový typ vstupní funkce.|
   |Odpojení|Určuje, zda byla vstupní funkce vyřazena nebo použita.|
   |EngineeringFeatureCount|Počet funkcí generovaných pomocí transformací technologie automatizovaného zpracování funkcí|
   |Transformace|Seznam transformací použitých u vstupních funkcí k vygenerování navržených funkcí|

### <a name="scaling-and-normalization"></a>Škálování a normalizace

Chcete-li pochopit škálování/normalizaci a vybraný algoritmus s jeho hodnotami parametrů, použijte `fitted_model.steps` . 

Následující vzorový výstup je spuštěný `fitted_model.steps` pro zvolený běh:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

Další podrobnosti získáte pomocí této pomocné funkce: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Tato pomocná funkce vrátí následující výstup konkrétního běhu pomocí `LogisticRegression with RobustScalar` jako konkrétního algoritmu.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Předpověď pravděpodobnosti třídy

Modely vytvořené pomocí automatizovaných ML mají objekty obálky, které zrcadlí funkci ze své Open Source třídy původu. Většina objektů obálky modelu klasifikace vrácených pomocí automatizovaného ML implementuje `predict_proba()` funkci, která přijímá ukázku dat typu pole nebo zhuštěné matrice vašich funkcí (hodnot X) a vrací n-dimenzionální pole každé ukázky a její příslušnou pravděpodobnost třídy.

Za předpokladu, že jste načetli nejlepší běh a namontovaný model pomocí stejných volání výše, můžete zavolat `predict_proba()` přímo z namontovaného modelu a zadat `X_test` ukázku v příslušném formátu v závislosti na typu modelu.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Pokud podkladový model nepodporuje `predict_proba()` funkci nebo je nesprávný formát, bude vyvolána výjimka specifická pro třídu modelu. Příklady toho, jak je tato funkce implementovaná pro různé typy modelů, najdete v referenční dokumentaci k [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) a [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) .

## <a name="bert-integration"></a>Integrace BERT

[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) se používá v Featurization vrstvě AutoML. Pokud v této vrstvě sloupec obsahuje bezplatný text nebo jiné typy dat, jako jsou například časová razítka nebo jednoduchá čísla, použije se odpovídajícím způsobem featurization.

V případě BERT je model vyladěný a vycvičený s využitím uživatelem poskytnutých popisků. Z tohoto místa jsou vkládání dokumentů ve formě funkcí společně s dalšími funkcemi, jako jsou funkce založené na časových razítkách, den v týdnu. 


### <a name="bert-steps"></a>BERT kroky

Aby bylo možné vyvolat BERT, je třeba nastavit  `enable_dnn: True` ve svém automl_settings a použít výpočetní prostředí GPU (například `vm_size = "STANDARD_NC6"` nebo vyšší GPU). Pokud se používá výpočetní výkon procesoru, pak místo BERT AutoML povolí BiLSTM DNN featurizer.

AutoML provede následující kroky pro BERT. 

1. **Předzpracování a tokenizace všech textových sloupců**. Například "transformátor" StringCast "můžete najít v souhrnu featurization finálního modelu. Příklad, jak vystavit souhrn featurization modelu, najdete v [tomto poznámkovém bloku](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b).

2. **Zřetězí všechny textové sloupce do jednoho textového sloupce**, takže `StringConcatTransformer` v konečném modelu. 

    Naše implementace BERT omezuje celkovou délku textu školicích ukázek na 128 tokeny. To znamená, že všechny textové sloupce, pokud jsou zřetězené, by ideálně měly mít maximálně 128 tokenů. Pokud je k dispozici více sloupců, je nutné vyřadit každý sloupec, aby byl tento stav splněn. V opačném případě se pro zřetězené sloupce délky >128 tokeny provádějících tokenizaci úrovně BERT zkrátí tento vstup na 128 tokeny.

3. **V rámci rozmazání funkcí AutoML porovnává BERT proti směrnému plánu (sadu slov funkcí) na vzorku dat.** Toto porovnání Určuje, zda BERT poskytuje vylepšení přesnosti. Pokud BERT vykonává větší hodnotu než základní hodnoty, AutoML potom použije BERT pro text featurization pro celá data. V takovém případě se zobrazí `PretrainedTextDNNTransformer` v konečném modelu.

BERT obvykle běží déle než jiné featurizers. Pro lepší výkon doporučujeme pro své možnosti RDMA použít "STANDARD_NC24r" nebo "STANDARD_NC24rs_V3". 

AutoML bude distribuovat školení BERT napříč více uzly, pokud jsou k dispozici (až maximálně osm uzlů). To lze provést v `AutoMLConfig` objektu nastavením `max_concurrent_iterations` parametru na hodnotu vyšší než 1. 
### <a name="supported-languages"></a>Podporované jazyky

AutoML v současné době podporuje kolem jazyků 100 a v závislosti na jazyku datové sady AutoML zvolí příslušný model BERT. Pro německé údaje používáme model německého BERT. V anglickém jazyce používáme BERT model English. Pro všechny ostatní jazyky používáme model vícejazyčného BERT.

V následujícím kódu je spuštěn německý model BERT, protože jazyk datové sady je určen jako `deu` , kód jazyka tři písmen pro němčinu podle [klasifikace ISO](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak nastavit automatizované experimenty ML:

    * Pro prostředí s prvním kódem: [nakonfigurujte automatizované experimenty ml pomocí sady Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * Pro prostředí s nízkým kódem nebo bez kódu: [vytvořte automatizované experimenty ml v Azure Machine Learning Studiu](how-to-use-automated-ml-for-ml-models.md).

* Přečtěte si další informace o [tom, jak a kde model nasadit](how-to-deploy-and-where.md).

* Přečtěte si další informace o [tom, jak vytvořit regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak se naučit pomocí automatizovaného strojového učení na vzdáleném prostředku](how-to-auto-train-remote.md).
