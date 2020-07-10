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
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 11bb692027d8a2e5033c7bdaf8eb2c565d1562b0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205691"
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
|**Generování dalších funkcí*** |Pro funkce DateTime: rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodina, minuta, sekunda.<br/><br/>Pro funkce textu: četnost termínů založená na unigrams, bigrams a trigrams.|
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
**Detekce vyrovnávání tříd** |Předaný <br><br><br><br><br> Upozorněni | Vaše vstupy byly analyzovány a všechny třídy jsou ve vašich školicích datech vyváženy. Datová sada je považována za vyrovnanou, pokud každá třída má dobrou reprezentaci v datové sadě, jak je měřeno počtem a poměr vzorků. <br><br><br> Ve vašich vstupech se zjistily nevyvážené třídy. Pokud chcete opravit posun modelu, vyřešte problém s vyrovnáváním. Přečtěte si další informace o [vyrovnaných datech](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).
**Detekce problémů paměti** |Předaný <br><br><br><br> Hotovo |<br> Vybrané hodnoty (horizont, lag, posuvné okno) byly analyzovány a nebyly zjištěny žádné potenciální problémy způsobené nedostatkem paměti. Přečtěte si další informace o [konfiguracích prognózování](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)časových řad. <br><br><br>Vybrané hodnoty (horizont, lag, posuvné okno) byly analyzovány a mohou způsobit nedostatek paměti experimentu. Konfigurace zpoždění nebo posuvných oken byla vypnuta.
**Detekce četnosti** |Předaný <br><br><br><br> Hotovo |<br> Časová řada byla analyzována a všechny datové body jsou zarovnány s zjištěnou frekvencí. <br> <br> Časová řada byla analyzována a byly zjištěny datové body, které neodpovídají zjištěné četnosti. Tyto datové body byly z datové sady odebrány. Další informace o [přípravě dat pro prognózování časových řad](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Přizpůsobení featurization

Nastavení featurization můžete přizpůsobit tak, aby se zajistilo, že data a funkce, které se použijí k tomu, aby využívaly svůj model ML, budou relevantní předpovědi.

Chcete-li přizpůsobit featurizations, zadejte  `"featurization": FeaturizationConfig` v `AutoMLConfig` objektu. Pokud pro váš experiment používáte Azure Machine Learning Studio, přečtěte si článek s [postupem](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Mezi podporovaná přizpůsobení patří:

|Přizpůsobení|Definice|
|--|--|
|**Aktualizace pro účely sloupce**|Přepište typ funkce pro zadaný sloupec.|
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

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak nastavit automatizované experimenty ML:

    * Pro prostředí s prvním kódem: [nakonfigurujte automatizované experimenty ml pomocí sady Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * Pro prostředí s nízkým kódem nebo bez kódu: [vytvořte automatizované experimenty ml v Azure Machine Learning Studiu](how-to-use-automated-ml-for-ml-models.md).

* Přečtěte si další informace o [tom, jak a kde model nasadit](how-to-deploy-and-where.md).

* Přečtěte si další informace o [tom, jak vytvořit regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak se naučit pomocí automatizovaného strojového učení na vzdáleném prostředku](how-to-auto-train-remote.md).
