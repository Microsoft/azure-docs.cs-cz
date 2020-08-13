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
ms.openlocfilehash: 94595bac2febdef1d3739703f0fa49c9ef15f218
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166616"
---
# <a name="featurization-in-automated-machine-learning"></a>Featurization v automatizovaném strojovém učení

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V této příručce se dozvíte, jak:

- Jaká nastavení featurization Azure Machine Learning nabídky.
- Přizpůsobení těchto funkcí pro [automatizované experimenty strojového učení](concept-automated-ml.md).

*Inženýrské funkce* je proces využití dat v doméně k vytváření funkcí, které usnadňují používání algoritmů strojového učení (ml) k lepšímu učení. V Azure Machine Learning se pro usnadnění vývoje funkcí používají techniky pro škálování dat a normalizaci. Souhrnně tyto techniky a technické funkce se nazývají *featurization* v automatizovaném strojovém učení nebo v *AutoML*experimenty.

V tomto článku se předpokládá, že už víte, jak nakonfigurovat AutoML experiment. Informace o konfiguraci najdete v následujících článcích:

- Pro prostředí s prvním kódem: [nakonfigurujte automatizované experimenty ml pomocí sady Azure Machine Learning SDK pro Python](how-to-configure-auto-train.md).
- Pro použití s nízkým kódováním nebo prostředí bez kódu: [Vytvářejte, kontrolujte a nasaďte automatizované modely strojového učení pomocí Azure Machine Learning studia](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Konfigurace featurization

V každém automatizovaném experimentu machine learningu se pro vaše data ve výchozím nastavení aplikují [Automatické škálování a normalizační techniky](#featurization) . Tyto techniky jsou typy featurization, které usnadňují *určité* algoritmy, které jsou citlivé na funkce v různých měřítkech. Můžete ale také povolit další featurization, jako je například imputace, *kódování*a *transformace* *chybějících hodnot*.

> [!NOTE]
> Kroky pro automatizované featurization strojového učení (například normalizace funkcí, zpracování chybějících dat nebo převod textu na číslo) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky featurization, které se použijí během školení.

U experimentů, které nakonfigurujete pomocí sady Python SDK, můžete povolit nebo zakázat nastavení featurization a dále určit kroky featurization, které se mají použít pro váš experiment. Pokud používáte Azure Machine Learning Studio, přečtěte si [Postup povolení featurization](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

V následující tabulce jsou uvedena přijímaná nastavení pro `featurization` ve [třídě AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Konfigurace Featurization | Description|
------------- | ------------- |
|`"featurization": 'auto'`| Určuje, že v rámci předběžného zpracování jsou [kroky guardrails dat a featurization](#featurization) provedeny automaticky. Toto nastavení je výchozí.|
|`"featurization": 'off'`| Určuje, že kroky featurization se nemají automaticky provádět.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Určuje, že se mají použít přizpůsobené kroky featurization. [Přečtěte si, jak přizpůsobit featurization](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automaticky featurization

Následující tabulka shrnuje techniky, které jsou automaticky aplikovány na vaše data. Tyto techniky se aplikují na experimenty, které jsou nakonfigurované pomocí sady SDK nebo studia. Chcete-li toto chování zakázat, nastavte `"featurization": 'off'` v `AutoMLConfig` objektu.

> [!NOTE]
> Pokud plánujete exportovat AutoML vytvořené modely do [modelu ONNX](concept-onnx.md), ve formátu ONNX se podporují jenom možnosti featurization označené hvězdičkou (*). Přečtěte si další informace o [převodu modelů na ONNX](concept-automated-ml.md#use-with-onnx).

|Featurization &nbsp; kroky| Description |
| ------------- | ------------- |
|**Přetáhnout vysokou mohutnost nebo žádné funkce odchylky*** |Tyto funkce přetáhněte ze sady školení a ověření. Platí pro funkce se všemi chybějícími hodnotami, se stejnou hodnotou ve všech řádcích nebo s vysokou mohutnou (například hodnoty hash, ID nebo identifikátory GUID).|
|**Imputace – chybějící hodnoty*** |Pro číselné funkce imputace s průměrem hodnot ve sloupci.<br/><br/>V případě funkcí kategorií se imputac s nejčastější hodnotou.|
|**Generování dalších funkcí*** |Pro funkce DateTime: rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodina, minuta, sekunda.<br><br> *Pro úlohy předpovědi* jsou vytvořeny tyto další funkce DateTime: ISO year, pololetí, kalendářní měsíc jako řetězec, týden, den v týdnu jako řetězec, den čtvrtletí, den v roce, dop. odp. (0, pokud je hodina v hodnotě poledne (12 ODP), 1 jinak), AM/PM jako řetězec, hodina dne (12hr základ)<br/><br/>Pro funkce textu: četnost termínů založená na unigrams, bigrams a trigrams. Přečtěte si další informace o [tom, jak to uděláte pomocí Bert.](#bert-integration)|
|**Transformace a kódování***|Transformujte číselné funkce, které mají v kategorií funkce několik jedinečných hodnot.<br/><br/>Pro funkce kategorií s nízkou mohutnou se používá kódování One-Hot. Kódování One-Hot-hash se používá pro funkce kategorií s vysokou mohutnosti.|
|**Vkládání slov**|Text featurizer převede vektory textových tokenů na vektory věty pomocí předvýukového modelu. Vektory vložení každého slova v dokumentu jsou agregovány s využitím zbytku pro vytvoření vektoru funkce dokumentu.|
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
**Detekce vyrovnávání tříd** |Předaný <br><br>Upozorněni <br><br>Hotovo | Vaše vstupy byly analyzovány a všechny třídy jsou ve vašich školicích datech vyváženy. Datová sada je považována za vyrovnanou, pokud každá třída má dobrou reprezentaci v datové sadě, jak je měřeno počtem a poměr vzorků. <br><br><br> Ve vašich vstupech se zjistily nevyvážené třídy. Pokud chcete opravit posun modelu, vyřešte problém s vyrovnáváním. Přečtěte si další informace o [vyrovnaných datech](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).<br><br><br> Ve vašich vstupech se zjistily nevyvážené třídy a v této logice se určilo použití vyrovnávání.
**Detekce problémů paměti** |Předaný <br><br><br><br> Hotovo |<br> Vybrané hodnoty (horizont, lag, posuvné okno) byly analyzovány a nebyly zjištěny žádné potenciální problémy způsobené nedostatkem paměti. Přečtěte si další informace o [konfiguracích prognózování](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)časových řad. <br><br><br>Vybrané hodnoty (horizont, lag, posuvné okno) byly analyzovány a mohou způsobit nedostatek paměti experimentu. Konfigurace zpoždění nebo posuvných oken byla vypnuta.
**Detekce četnosti** |Předaný <br><br><br><br> Hotovo |<br> Časová řada byla analyzována a všechny datové body jsou zarovnány s zjištěnou frekvencí. <br> <br> Časová řada byla analyzována a byly zjištěny datové body, které neodpovídají zjištěné četnosti. Tyto datové body byly z datové sady odebrány. Další informace o [přípravě dat pro prognózování časových řad](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Přizpůsobení featurization

Nastavení featurization můžete přizpůsobit tak, aby se zajistilo, že data a funkce, které se použijí k tomu, aby využívaly svůj model ML, budou relevantní předpovědi.

Chcete-li přizpůsobit featurizations, zadejte  `"featurization": FeaturizationConfig` v `AutoMLConfig` objektu. Pokud pro váš experiment používáte Azure Machine Learning Studio, přečtěte si článek s [postupem](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Postup přizpůsobení featurization pro prognózy typů úloh najdete v tématu Postup při [Prognózování](how-to-auto-train-forecast.md#customize-featurization).

Mezi podporovaná přizpůsobení patří:

|Přizpůsobení|Definice|
|--|--|
|**Aktualizace pro účely sloupce**|Přepíše automaticky zjištěný typ funkce pro zadaný sloupec.|
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

## <a name="bert-integration"></a>Integrace BERT 
[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) se používá v Featurization vrstvě automatizovaného ml. V této vrstvě zjistíme, jestli sloupec obsahuje bezplatný text nebo jiné typy dat, jako jsou například časová razítka nebo jednoduchá čísla, a my zpracování odpovídajícím způsobem. V případě, že pro BERT vyladíme a naučíte model pomocí uživatelsky definovaných popisků, pak výstupy dokumentů (pro BERT se jedná o finální skrytý stav přidružený ke speciálnímu tokenu [CLS]) jako funkce společně s dalšími funkcemi, jako jsou funkce založené na časových razítkech (např. den v týdnu) nebo čísla, která mnoho typických datových sad obsahuje. 

Pokud chcete povolit BERT, měli byste pro školení použít výpočetní prostředí GPU. Pokud se použije výpočetní výkon procesoru, pak se místo BERT AutoML povolí BiLSTM DNN featurizer. Aby bylo možné vyvolat BERT, je třeba nastavit "enable_dnn: true" v automl_settings a použít výpočetní prostředky GPU (např. vm_size = "STANDARD_NC6" nebo vyšší GPU). Příklad najdete v [tomto poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb).

AutoML provádí následující kroky pro případ BERT (Všimněte si, že musíte nastavit "enable_dnn: true" v automl_settings pro tyto položky):

1. Předzpracování, včetně tokenizace všech textových sloupců (v souhrnu featurization finálního modelu se zobrazí "StringCast" transformátor. Podívejte se na [Tento Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) , kde vidíte příklad, jak vystavit souhrn featurization modelu pomocí `get_featurization_summary()` metody.

```python
text_transformations_used = []
for column_group in fitted_model.named_steps['datatransformer'].get_featurization_summary():
    text_transformations_used.extend(column_group['Transformations'])
text_transformations_used
```

2. Zřetězí všechny textové sloupce do jednoho textového sloupce, takže se v konečném modelu zobrazí text "StringConcatTransformer". 

> [!NOTE]
> Naše implementace BERT omezuje celkovou délku textu školicích ukázek na 128 tokeny. To znamená, že všechny textové sloupce, pokud jsou zřetězené, by ideálně měly mít maximálně 128 tokenů. V ideálním případě, pokud je k dispozici více sloupců, každý sloupec by měl být vyhodnocen tak, aby byl tento stav splněn. Například pokud existují dva textové sloupce v datech, měly by být oba textové sloupce vyřazeny na 64 tokeny (za předpokladu, že chcete, aby oba sloupce byly stejně reprezentovány v konečném zřetězeném textovém sloupci) předtím, než data dostanou do AutoML. Pro zřetězené sloupce délky >128 tokeny provádějících tokenizaci vrstva BERT zkrátí tento vstup na 128 tokeny.

3. V kroku pro mazání funkcí AutoML porovnává BERT proti směrnému plánu (sadu slov) na ukázce dat a určuje, jestli BERT by poskytovala vylepšení přesnosti. Pokud se zjistí, že BERT vykonává lepší hodnotu než standardní hodnoty, AutoML potom použije BERT pro text featurization jako optimální strategii featurization a pokračuje s featurizing celými daty. V takovém případě se v konečném modelu zobrazí "PretrainedTextDNNTransformer".

BERT obvykle běží déle než většina ostatních featurizers. Dá se urychlitovat tím, že ve svém clusteru poskytnete víc výpočetních prostředků. AutoML bude distribuovat školení BERT napříč více uzly, pokud jsou k dispozici (až maximálně 8 uzlů). To se dá udělat nastavením [max_concurrent_iterations](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) na větší než 1. Pro lepší výkon doporučujeme použít skladové položky s možnostmi RDMA (například "STANDARD_NC24r" nebo "STANDARD_NC24rs_V3").

AutoML v současné době podporuje kolem jazyků 100 a v závislosti na jazyku datové sady AutoML zvolí příslušný model BERT. Pro německé údaje používáme model německého BERT. V anglickém jazyce používáme BERT model English. Pro všechny ostatní jazyky používáme model vícejazyčného BERT.

V následujícím kódu se aktivuje německý model BERT, protože jazyk datové sady je určený jako DEU, kód jazyka 3 pro němčinu podle [klasifikace ISO](https://iso639-3.sil.org/code/deu):

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
