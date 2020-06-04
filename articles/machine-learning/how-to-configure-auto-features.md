---
title: Featurization v AutoML experimenty
titleSuffix: Azure Machine Learning
description: Přečtěte si, jaká nastavení featurization Azure Machine Learning nabídky, a jak se v automatizovaných experimentech ml podporuje inženýry funkcí.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 87cb5714a1f4b362e2f73879d93f5739f0fc20cf
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324279"
---
# <a name="featurization-with-automated-machine-learning"></a>Featurization pomocí automatizovaného strojového učení

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V této příručce se dozvíte, jaká nastavení featurization jsou nabízená a jak je přizpůsobit pro [automatizované experimenty strojového učení](concept-automated-ml.md).

Inženýrské funkce je proces využití dat v doméně k vytváření funkcí, které pomůžou lépe zjistit algoritmy ML. V Azure Machine Learning se pro usnadnění strojírenství funkcí používají techniky škálování dat a normalizace. Souhrnně se tyto techniky a strojírenství funkcí označují jako featurization v automatizovaném strojovém učení, AutoML a experimentech.

V tomto článku se předpokládá, že jste už obeznámeni s postupem konfigurace experimentu AutoML. Podrobnosti najdete v následujících článcích.

* Pro prostředí Code First: [nakonfigurujte automatizované experimenty ml pomocí sady Python SDK](how-to-configure-auto-train.md).
* Nedostatečné prostředí pro kód: [vytváření, revize a nasazování automatizovaných modelů strojového učení pomocí Azure Machine Learning studia](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>Konfigurace featurization

V každém automatizovaném experimentu machine learningu se pro vaše data ve výchozím nastavení aplikují [Automatické škálování a normalizační techniky](#featurization) . Tyto techniky škálování a normalizace jsou typy featurization, které usnadňují *určité* algoritmy, které jsou citlivé na funkce v různých měřítkech. Můžete ale také povolit další featurization, například **chybějící hodnoty imputac, Encoding** a **transformes**.

> [!NOTE]
> Automatické kroky featurization strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky featurization, jaké jste použili během školení.

U experimentů nakonfigurovaných pomocí sady SDK můžete povolit nebo zakázat nastavení `featurization` a dále určovat featurization kroky, které by se měly použít pro váš experiment. Pokud používáte Azure Machine Learning Studio, přečtěte si článek Jak povolit featurization [pomocí těchto kroků](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

V následující tabulce jsou uvedena přijímaná nastavení pro `featurization` ve [třídě AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Konfigurace Featurization | Description|
------------- | ------------- |
|**`"featurization": 'auto'`**| Označuje, že v rámci předběžného zpracování se [kroky guardrails a featurization](#featurization) provádějí automaticky. **Výchozí nastavení**.|
|**`"featurization": 'off'`**| Indikuje, že kroky featurization by se neměly provádět automaticky.|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| Indikuje, že by se měl použít upravený krok featurization. [Přečtěte si, jak přizpůsobit featurization](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automaticky featurization

Následující tabulka shrnuje techniky, které jsou automaticky aplikovány na vaše data. K tomu dochází pro experimenty nakonfigurované prostřednictvím sady SDK nebo studia. Chcete-li toto chování zakázat, nastavte `"featurization": 'off'` v `AutoMLConfig` objektu.

> [!NOTE]
> Pokud plánujete exportovat vytvořené modely automatického ML do [modelu ONNX](concept-onnx.md), ve formátu ONNX se podporují jenom možnosti featurization označené *. Přečtěte si další informace o [převodu modelů na ONNX](concept-automated-ml.md#use-with-onnx). 

|Featurization &nbsp; kroky| Description |
| ------------- | ------------- |
|**Přetáhnout vysokou mohutnost nebo žádné funkce odchylky*** |Odřaďte je ze školicích a ověřovacích sad, včetně funkcí se všemi chybějícími hodnotami, stejné hodnoty ve všech řádcích nebo s vysokou mohutnou (například hodnoty hash, ID nebo identifikátory GUID).|
|**Imputace – chybějící hodnoty*** |Pro numerické funkce, imputac s průměrem hodnot ve sloupci.<br/><br/>V případě funkcí kategorií se imputac s nejčastěji hodnotou.|
|**Generování dalších funkcí*** |Pro funkce DateTime: rok, měsíc, den, den v týdnu, den roku, čtvrtletí, týden v roce, hodina, minuta, sekunda.<br/><br/>Pro funkce textu: četnost termínů založená na unigrams, g-gramech a Tri-Character-gramech.|
|**Transformace a kódování***|Číselné funkce s malým počtem jedinečných hodnot jsou transformovány do funkcí kategorií.<br/><br/>Pro nízkou mohutnost se provádí kódování s jedním aktivním kategorií; v případě vysoké mohutnosti je kódování One-Hot-hash.|
|**Vkládání slov**|Featurizer textu, který převede vektory textových tokenů na vektory vět pomocí předem připraveného modelu. Vektory vložení každého slova v dokumentu jsou agregovány dohromady, aby se vytvořil vektor funkce dokumentu.|
|**Cílová kódování**|V případě funkcí kategorií mapuje každou kategorii s průměrnou cílovou hodnotou pro regresní problémy a pravděpodobností třídy pro jednotlivé třídy pro problémy s klasifikací. Pro snížení velikosti mapování a hluku způsobených kategoriemi zhuštěných dat se použije vyvážení na základě frekvencí a k přeložení.|
|**Kódování cílového textu**|V případě textového vstupu se pro generování pravděpodobnosti každé třídy používá skládaný lineární model s použitím typu penalta-slova.|
|**Váha důkazů (WoE)**|Vypočítá WoE jako míru korelace kategorií sloupců do cílového sloupce. Počítá se jako protokol poměru mezi třídou a pravděpodobnosti mimo třídu. Tento krok provede výstup jednoho sloupce číselné funkce na každou třídu a odstraní nutnost explicitně imputace chybějících hodnot a izolované zpracování.|
|**Vzdálenost clusteru**|Vlaky a k – znamenají model clusteringu ve všech číselných sloupcích.  Výstupy k novým funkcím, jedna nová číselná funkce na cluster, která obsahuje vzdálenost jednotlivých vzorků k těžiště každého clusteru.|

## <a name="data-guardrails"></a>Guardrails dat

Data guardrails vám pomůžou identifikovat možné problémy s Vašimi daty (například chybějící hodnoty, [nerovnováha tříd](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)) a pomáhat provést opravné akce pro lepší výsledky. 

Data guardrails se aplikují. 

* V případě **experimentů sady SDK**, pokud jsou buď parametry, `"featurization": 'auto'` nebo `validation=auto` jsou zadány v `AutoMLConfig` objektu.
* **Pro experimenty studia**, když je povolený *Automatický featurization*  

Můžete zkontrolovat guardrails dat, která se týkají vašeho experimentu.

* Nastavením `show_output=True` při odesílání experimentu pomocí sady Python SDK.

* V studiu na kartě **Guardrails dat** automatizovaného běhu ml.

### <a name="data-guardrail-states"></a>Stavy guardrail dat

Data guardrails se zobrazí v jednom ze tří stavů: **dokončeno**, **dokončeno**nebo **výstrahy**.

|State| Popis |
|----|---- |
|**Předaný**| Nebyly zjištěny žádné problémy s daty a není třeba žádné akce uživatele. |
|**hotovo**| Změny byly aplikovány na vaše data. Pro uživatele doporučujeme, aby zkontrolovali, že se u očekávaných výsledků prováděla automatizovaná opatření, aby se zajistilo, že změny budou zarovnané. |
|**Upozorněni**| Byl zjištěn problém s daty, který nebyl odstraněn. Doporučujeme uživatelům, aby tento problém opravili a vyřešili.| 

Následující tabulka popisuje aktuálně podporované guardrails dat a související stavy, se kterými se uživatelé můžou setkat při odesílání jejich experimentu.

Guardrail|Status|Podmínka &nbsp; pro &nbsp; aktivační událost
---|---|---
**Chybějící hodnoty funkcí imputace** |*Předaný* <br><br><br> *hotovo*| Ve školicích datech se nezjistily žádné chybějící hodnoty funkcí. Přečtěte si další informace o [imputaci chybějících hodnot.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Ve vašich školicích datech se zjistily chybějící hodnoty funkcí a imputované.
**Zpracování funkcí vysoké mohutnosti** |*Předaný* <br><br><br> *hotovo*| Vaše vstupy byly analyzovány a nebyly zjištěny žádné funkce vysoké mohutnosti. Přečtěte si další informace o [detekci funkcí vysoké mohutnosti.](#automatic-featurization) <br><br> Ve vašich vstupech se zjistily funkce vysoké mohutnosti a zpracovaly se.
**Zpracování děleného ověřování** |*hotovo*| Konfigurace ověření byla nastavena na hodnotu auto a školicí data obsahují **méně než 20 000 řádků**. <br> Každá iterace proučeného modelu byla ověřena prostřednictvím křížového ověření. Přečtěte si další informace o [ověřovacích datech.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> Konfigurace ověření byla nastavena na hodnotu auto a školicí data obsahují **více než 20 000 řádků**. <br> Vstupní data byla rozdělena na školicí datovou sadu a datovou sadu ověřování pro ověřování modelu.
**Detekce vyrovnávání tříd** |*Předaný* <br><br><br><br><br> *Upozorněni* | Vaše vstupy byly analyzovány a všechny třídy jsou ve vašich školicích datech vyváženy. Datová sada je považována za vyváženou, pokud každá třída má v datové sadě dobrý reprezentace, měřená podle počtu a poměru vzorků. <br><br><br> Ve vašich vstupech se zjistily nevyvážené třídy. Pokud chcete opravit posun modelu, vyřešte problém s vyrovnáváním. Přečtěte si další informace o [vyrovnaných datech.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
**Detekce problémů paměti** |*Předaný* <br><br><br><br> *hotovo* |<br> Vybrané hodnoty {horizont, lag, kumulovaný interval} byly analyzovány a nebyly zjištěny žádné potenciální problémy způsobené nedostatkem paměti. Přečtěte si další informace o [konfiguracích prognózování](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) časových řad. <br><br><br>Vybrané hodnoty {horizont, lag, kumulovaný interval} byly analyzovány a mohou způsobit nedostatek paměti experimentu. Konfigurace zpoždění nebo posuvných oken byla vypnuta.
**Detekce četnosti** |*Předaný* <br><br><br><br> *hotovo* |<br> Časová řada byla analyzována a všechny datové body jsou zarovnány s zjištěnou frekvencí. <br> <br> Časové řady byly analyzovány a byly zjištěny datové body, které nebyly zarovnány se zjištěnou frekvencí. Tyto datové body byly z datové sady odebrány. Další informace o [přípravě dat pro prognózování časových řad.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="customize-featurization"></a>Přizpůsobení featurization

Nastavení featurization můžete přizpůsobit tak, aby se zajistilo, že data a funkce používané při proškolování modelu ML mají za následek relevantní předpovědi. 

Chcete-li přizpůsobit featurizations, zadejte  `"featurization": FeaturizationConfig` v `AutoMLConfig` objektu. Pokud pro váš experiment používáte Azure Machine Learning Studio, přečtěte si téma [postupy](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Podporované vlastní nastavení zahrnuje:

|Přizpůsobení|Definice|
|--|--|
|**Aktualizace pro účely sloupce**|Přepište typ funkce pro zadaný sloupec.|
|**Aktualizace parametrů transformátoru** |Aktualizuje parametry pro zadaný transformátor. V současné době podporuje Imputac (střední, nejčastější & medián) a HashOneHotEncoder.|
|**Odkládací sloupce** |Sloupce, které se mají odpustit z natrénuje|
|**Blokovat transformátory**| Blokuje transformátory, které se mají použít na featurization procesu.|

Vytvoření objektu FeaturizationConfig pomocí volání rozhraní API:
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

* Přečtěte si, jak nastavit automatizované experimenty ML,

    * Pro zákazníky se zkušenostmi [s kódem: Nakonfigurujte automatizované experimenty ml pomocí Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * Pro zákazníky s nízkým a nezkušeným kódem: [vytvořte automatizované experimenty strojového učení v Azure Machine Learning Studiu](how-to-use-automated-ml-for-ml-models.md).

* Přečtěte si další informace o [tom, jak a kde model nasadit](how-to-deploy-and-where.md).

* Přečtěte si další informace o [tom, jak vytvořit regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak pomocí automatizovaného strojového učení na vzdáleném prostředku vyškolit](how-to-auto-train-remote.md).
