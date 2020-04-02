---
title: Analýza a sledování posunu dat na datových sadách (náhled)
titleSuffix: Azure Machine Learning
description: Vytvořte monitorování (náhled) datových sad Azure Machine Learning, sledujte posun dat v datových sadách a nastavte výstrahy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 0a7a89b4ff1f6deb94c545e64b4584d7959d573a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546376"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Zjišťování posunu dat (náhled) v datových sadách
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit monitorování datových sad Azure Machine Learning (preview), monitorování posunu dat a statistické změny v datových sadách a nastavení výstrah.

Pomocí monitorování datových sad Azure Machine Learning můžete:
* **Analyzujte posun v datech,** abyste pochopili, jak se v průběhu času mění.
* **Sledujte data modelu** z důvodu rozdílů mezi trénovacími a obsluhujícími datovými sadami.
* **Sledujte nové údaje,** aby se udály rozdíly mezi směrným plánem a cílovou datovou sadou.
* **Funkce profilu v datech** ke sledování toho, jak se statistické vlastnosti v průběhu času mění.
* **Nastavte upozornění na posun dat** pro včasná upozornění na potenciální problémy. 

Metriky a přehledy jsou dostupné prostřednictvím prostředků [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) přidružených k pracovnímu prostoru Azure Machine Learning.

> [!Important]
> Vezměte prosím na vědomí, že sledování dat drift s SDK je k dispozici ve všech edicích, zatímco sledování dat drift přes studio na webu je Enterprise edition pouze.

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvářet monitory datových sad a pracovat s ním, potřebujete:
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.
* Pracovní [prostor Azure Machine Learning](how-to-manage-workspace.md).
* Nainstalovaná [sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která obsahuje balíček azureml-datasets.
* Strukturovaná (tabulková) data s časovým razítkem určeným v cestě k souboru, názvu souboru nebo sloupci v datech.

## <a name="what-is-data-drift"></a>Co je posun dat?

V kontextu strojového učení je posun dat změnou vstupních dat modelu, která vede ke snížení výkonu modelu. Je to jeden z hlavních důvodů, proč přesnost modelu snižuje v průběhu času, a proto sledování dat drift pomáhá detekovat problémy s výkonem modelu.

Příčiny posunu dat zahrnují: 

- Změny procesu proti proudu, například nahrazovaný senzor, který mění jednotky měření z palců na centimetry. 
- Problémy s kvalitou dat, jako je například poškozený senzor, který vždy čte 0.
- Přirozený posun v datech, jako je průměrná teplota měnící se s ročními obdobími.
- Změna vztahu mezi prvky nebo kovariální posun. 

Pomocí monitorování datových sad Azure Machine Learning můžete nastavit výstrahy, které pomáhají při zjišťování posunu dat v datových sadách v průběhu času. 

### <a name="dataset-monitors"></a>Monitorování datové sady 

Můžete vytvořit monitorování datové sady pro detekci a upozornění na posun dat na nová data v datové sadě, analyzovat historická data pro drift a profilovat nová data v čase. Algoritmus posunu dat poskytuje celkové měřítko změny dat a označení, které funkce jsou zodpovědné za další šetření. Monitorování datových sad vytváří řadu dalších metrik profilováním `timeseries` nových dat v datové sadě. Vlastní výstrahy lze nastavit na všechny metriky generované monitorováníprostřednictvím [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Monitorování datových sad lze rychle zachytit problémy s daty a zkrátit dobu ladění problému určením pravděpodobných příčin.  

Koncepčně existují tři primární scénáře pro nastavení monitorování datové sady v Azure Machine Learning.

Scénář | Popis
---|---
Sledování dat obsluhy modelu pro posun od trénovacích dat modelu | Výsledky z tohoto scénáře lze interpretovat jako monitorování proxy pro přesnost modelu, vzhledem k tomu, že přesnost modelu snižuje, pokud obsluha dat posune z dat školení.
Sledování datové sady časových řad pro posun od předchozího časového období. | Tento scénář je obecnější a lze jej použít ke sledování datových sad zapojených před nebo po modelu budovy.  Cílová datová sada musí mít sloupec časového razítka, zatímco datová sada směrného plánu může být libovolná tabulková datová sada, která má společné funkce s cílovou datovou sadou.
Provádění analýzy minulých dat. | Tento scénář lze použít k pochopení historických dat a informovat rozhodnutí v nastavení pro monitorování datové sady.

## <a name="how-dataset-can-monitor-data"></a>Jak může datová sada sledovat data

Pomocí Azure Machine Learning, data drift se monitoruje prostřednictvím datových sad. Chcete-li sledovat posun dat, je zadána základní datová sada - obvykle trénovací datová sada pro model. Cílová datová sada – obvykle vstupní data modelu – se v průběhu času porovnává s vaší datovou sadou směrného plánu. Toto porovnání znamená, že cílová datová sada musí mít zadaný sloupec časového razítka.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Nastavení `timeseries` vlastnosti v cílové datové sadě

Cílová datová sada `timeseries` musí mít vlastnost nastavenou na něm zadáním sloupce časového razítka buď ze sloupce v datech, nebo virtuálního sloupce odvozeného ze vzoru cesty souborů. To lze provést prostřednictvím Python SDK nebo Azure Machine Learning studio. Chcete-li přidat `timeseries` znak datové sady, musí být zadán sloupec představující časové razítko "jemné zrnitosti". Pokud jsou data rozdělena do struktury složek s informacemi o čase, například {yyyy/MM/dd},, můžete vytvořit virtuální sloupec prostřednictvím nastavení vzoru cesty a nastavit jej jako časové razítko "hrubé zrno", abyste zlepšili důležitost funkce časové řady. 

#### <a name="python-sdk"></a>Python SDK

[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) Metoda [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) třídy definuje sloupec časového razítka pro datovou sadu. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Úplný příklad použití vlastnosti `timeseries` datových sad naleznete v [ukázkovém poznámkovém bloku](https://aka.ms/azureml-tsd-notebook) nebo v [dokumentaci k sadami SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Pokud vytvoříte datovou sadu pomocí azure machine learningstudio, ujistěte se, že cesta k datům obsahuje informace o časovém razítku, zahrnout všechny podsložky s daty a nastavit formát oddílu. 

V následujícím příkladu jsou přijata všechna data pod složky *NoaaIsdFlorida/2019* a formát oddílu určuje rok, měsíc a den časového razítka. 

[![Formát oddílu](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

V nastavení **schématu** zadejte sloupec časového razítka z virtuálního nebo reálného sloupce v zadané datové sadě:

![Časové razítko](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Nastavení monitorování datové sady

Jakmile vytvoříte datovou sadu se zadaným nastavením časového razítka, můžete nakonfigurovat monitorování datové sady.

Různá nastavení monitorování datové sady jsou rozdělena do tří skupin: **Základní informace, Nastavení monitoru** a **Nastavení zásypu**.

### <a name="basic-info"></a>Základní informace

Tato tabulka obsahuje základní nastavení použitá pro monitorování datové sady.

| Nastavení | Popis | Tipy | Měnitelné | 
| ------- | ----------- | ---- | ------- | 
| Name (Název) | Název monitoru datové sady. | | Ne |
| Datová sada podle směrného plánu | Tabulková datová sada, která bude použita jako výchozí hodnota pro porovnání cílové datové sady v čase. | Datová sada směrného plánu musí mít společné funkce s cílovou datovou sadou. Obecně by měl být směrný plán nastaven na trénovací datovou sadu modelu nebo na řez cílové datové sady. | Ne |
| Cílová datová sada | Tabulková datová sada se zadaným sloupcem časového razítka, která bude analyzována pro posun dat. | Cílová datová sada musí mít společné funkce s datovou sadou směrného plánu a měla by být datovou sadou, `timeseries` ke které jsou připojena nová data. Historická data v cílové datové sadě mohou být analyzována nebo lze sledovat nová data. | Ne | 
| Frequency | Frekvence, která se použije k naplánování úlohy kanálu a analýze historických dat při spuštění backfill. Možnosti zahrnují denní, týdenní nebo měsíční. | Upravte toto nastavení tak, aby zahrnovalo srovnatelnou velikost dat se směrným plánem. | Ne | 
| Funkce | Seznam funkcí, které budou analyzovány pro posun dat v čase. | Nastavte na výstupní funkce modelu pro měření posunu konceptu. Nezahrnujte funkce, které přirozeně drift v průběhu času (měsíc, rok, index, atd.). Po úpravě seznamu funkcí můžete zaplnit zpětný výplň a stávající monitor posunu dat. | Ano | 
| Cílový výpočetní objekt | Azure Machine Learning výpočetní cíl pro spuštění úloh monitorování datové sady. | | Ano | 

### <a name="monitor-settings"></a>Nastavení monitoru

Tato nastavení jsou pro kanál monitorování naplánované datové sady, který bude vytvořen. 

| Nastavení | Popis | Tipy | Měnitelné | 
| ------- | ----------- | ---- | ------- |
| Povolení | Povolení nebo zakázání plánu v kanálu monitorování datové sady | Zakažte plán pro analýzu historických dat pomocí nastavení backfill. Může být povolena po vytvoření monitorování datové sady. | Ano | 
| Latence | Čas v hodinách trvá, než data dorazí do datové sady. Například pokud trvá tři dny pro data dorazí v DATABÁZI SQL dat dat zapouzdření, nastavte latenci na 72. | Po vytvoření monitorování datové sady nelze změnit. | Ne | 
| E-mailové adresy | E-mailové adresy pro upozorňování na základě porušení prahové hodnoty procentuálního posunu dat. | E-maily se posílají prostřednictvím Služby Azure Monitor. | Ano | 
| Prahová hodnota | Procentuální prahová hodnota posunu dat pro upozornění e-mailů. | Další výstrahy a události lze nastavit na mnoha dalších metrik v oboru přidružené aplikační insights prostředku. | Ano | 

### <a name="backfill-settings"></a>Nastavení zpětnévýplně

Tato nastavení jsou určeny pro spuštění backfill na minulých dat pro metriky posun dat.

| Nastavení | Popis | Tipy |
| ------- | ----------- | ---- |
| Počáteční datum | Počáteční datum úlohy backfill. | | 
| Koncové datum | Koncové datum úlohy backfill. | Koncové datum nemůže být od počátečního data větší než 31*frekvenčních jednotek času. Na existujícím monitoru datové sady lze metriky zaplnit zpět, aby bylo možné analyzovat historická data nebo nahradit metriky aktualizovaným nastavením. |

## <a name="create-dataset-monitors"></a>Vytvoření monitorů datových sad 

Vytvořte monitorování datových sad, které zjišťují a upozorňují na posun dat v nové datové sadě pomocí Azure Machine Learning studio nebo Python SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Chcete-li nastavit výstrahy na monitoru datové sady, musí mít pracovní prostor obsahující datovou sadu, pro kterou chcete vytvořit monitor, možnosti enterprise edition. 

Po potvrzení funkce pracovního prostoru přejděte na domovskou stránku studia a vyberte kartu Datové sady vlevo. Vyberte Monitorování datových sad.

![Seznam monitorů](./media/how-to-monitor-datasets/monitor-list.png)

Klikněte na tlačítko **+Vytvořit monitor** a pokračujte průvodcem klepnutím na tlačítko **Další**.

![Průvodce](./media/how-to-monitor-datasets/wizard.png)

Výsledný monitor datové sady se zobrazí v seznamu. Vyberte ji, chcete-li přejít na stránku s podrobnostmi tohoto monitoru.

### <a name="from-python-sdk"></a>Pochází z Python SDK

Podrobné informace najdete v [referenční dokumentaci sady Python SDK o posunu dat.](/python/api/azureml-datadrift/azureml.datadrift) 

Následující příklad ukazuje, jak vytvořit monitorování datové sady pomocí sady Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Úplný příklad nastavení `timeseries` datové sady a detektoru posunu dat najdete v našem [příkladu notebooku](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Principy výsledků posunu dat

Monitorování dat vytváří dvě skupiny výsledků: Přehled driftu a podrobnosti o funkcích. Následující animace zobrazuje dostupné grafy drift monitoru na základě vybrané funkce a metriky. 

![Ukázkové video](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Drift – přehled

Sekce **Přehled driftobsahuje** přehled nejvyšší úrovně o velikosti posunu dat a o tom, které funkce by měly být dále zkoumány. 

| Metrika | Popis | Tipy | 
| ------ | ----------- | ---- | 
| Velikost posunu dat | Vzhledem k tomu, jako procento mezi směrným plánem a cílové datové sady v čase. V rozsahu od 0 do 100, kde 0 označuje identické datové sady a 100 označuje schopnost unášení dat Azure Machine Learning můžete zcela rozlišit dvě datové sady od sebe. | Hluk v přesném naměřeném procentu se očekává v důsledku technik strojového učení, které se používají k vytvoření tohoto rozsahu. | 
| Drift příspěvek podle funkce | Příspěvek každé funkce v cílové datové sadě k naměřené velikosti driftu. |  Vzhledem ke změně variaté nemusí být základní rozdělení prvku nutné změnit, aby mělo relativně vysoký význam funkce. | 

Následující obrázek je příkladem grafů zobrazených ve výsledcích **přehledu driftve** studiu Azure Machine Learning, které jsou výsledkem zásypu [integrovaných povrchových dat NOAA](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Data byla vzorkována do `stationName contains 'FLORIDA'`, přičemž leden 2019 se používá jako základní datová sada a všechny údaje za rok 2019 se používají jako cíl.
 
![Drift – přehled](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Podrobnosti o funkcích

Část **Podrobnosti o funkci** obsahuje přehledy na úrovni funkce o změně distribuce vybrané funkce a další statistiky v průběhu času. 

Cílová datová sada je také profilována v čase. Statistická vzdálenost mezi směrným rozdělením jednotlivých funkcí je porovnána s cílovými datovými sadami v čase, což je koncepčně podobné velikosti posunu dat s výjimkou, že tato statistická vzdálenost je pro jednotlivé funkce. Min, max, a střední jsou také k dispozici. 

Pokud ve studiu Azure Machine Learning kliknete na datový bod v grafu, rozložení zobrazené funkce se odpovídajícím způsobem upraví. Ve výchozím nastavení zobrazuje distribuci datové sady směrného plánu a distribuci stejné funkce posledního spuštění. 

Tyto metriky lze také načíst v Pythonu `get_metrics()` SDK prostřednictvím metody na `DataDriftDetector` objekt. 

#### <a name="numeric-features"></a>Číselné funkce 

Číselné funkce jsou profilovány v každém spuštění monitoru datové sady. Následující jsou vystaveny ve studiu Azure Machine Learning. Hustota pravděpodobnosti je zobrazena pro rozdělení.

| Metrika | Popis |  
| ------ | ----------- |  
| Wassersteinvzdálenost | Minimální množství práce k transformaci distribuce podle směrného plánu na cílové rozdělení. |
| Průměrná hodnota | Průměrná hodnota prvku. |
| Min. hodnota | Minimální hodnota prvku. |
| Max. hodnota | Maximální hodnota prvku. |

![Číselné podrobnosti o funkci](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Kategorické funkce 

Číselné funkce jsou profilovány v každém spuštění monitoru datové sady. Následující jsou vystaveny ve studiu Azure Machine Learning. Pro distribuci je zobrazen histogram.

| Metrika | Popis |  
| ------ | ----------- |  
| Euclidian vzdálenost | Geometrická vzdálenost mezi směrným plánem a cílovým rozdělením. |
| Jedinečné hodnoty | Počet jedinečných hodnot (mohutnost) prvku. |


![Podrobnosti o funkcích jsou kategorické](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metriky, upozornění a události

Metriky se dají dotazovat v prostředku [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) přidruženém k vašemu pracovnímu prostoru strojového učení. Který poskytuje přístup ke všem funkcím Application Insights, včetně nastavení pro vlastní pravidla výstrah a skupiny akcí pro aktivaci akce, jako je e-mail / SMS / Push / hlasové nebo Azure funkce. Podrobnosti naleznete v úplné dokumentaci k přehledům aplikací. 

Chcete-li začít, přejděte na portál Azure a vyberte stránku **Přehled** pracovního prostoru.  Přidružený prostředek Application Insights je zcela vpravo:

[![Přehled webu Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

V levém podokně vyberte Protokoly (Analytics) v levém podokně:

![Přehled aplikací](./media/how-to-monitor-datasets/ai-overview.png)

Metriky monitorování datové sady `customMetrics`jsou uloženy jako . Po nastavení monitoru datové sady můžete napsat a spustit dotaz a zobrazit je takto:

[![Dotaz analýzy protokolů](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Po identifikaci metrik pro nastavení pravidel výstrah vytvořte nové pravidlo výstrah:

![Nové pravidlo výstrahy](./media/how-to-monitor-datasets/alert-rule.png)

Můžete použít existující skupinu akcí nebo vytvořit novou k definování akce, která má být provedena při splnění nastavených podmínek:

![Nová skupina akcí](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Řešení potíží

Omezení a známé problémy:

* Časový rozsah úloh backfill jsou omezeny na 31 intervalech nastavení frekvence monitoru. 
* Omezení 200 funkcí, pokud není zadán seznam funkcí (všechny použité funkce).
* Výpočetní velikost musí být dostatečně velká pro zpracování dat. 
* Ujistěte se, že vaše datová sada má data v rámci počátečního a koncového data pro daný monitor spustit.
* Monitorování datových sad bude fungovat pouze u datových sad, které obsahují 50 řádků nebo více. 

Sloupce nebo funkce v datové sadě jsou klasifikovány jako kategorické nebo číselné na základě podmínek v následující tabulce. Pokud funkce nesplňuje tyto podmínky - například sloupec typu řetězec s >100 jedinečných hodnot - funkce je vynechána z našeho algoritmu drift dat, ale je stále profilován. 

| Typ prvku | Datový typ | Podmínka | Omezení | 
| ------------ | --------- | --------- | ----------- |
| Kategorické | řetězec, bool, int, plovák | Počet jedinečných hodnot v prvku je menší než 100 a menší než 5 % počtu řádků. | Null je považován za vlastní kategorii. | 
| Číselné | int, plovák | Hodnoty v prvku jsou číselného datového typu a nesplňují podmínku pro kategorický prvek. | Funkce vynechána, pokud >15 % hodnot jsou null. | 

## <a name="next-steps"></a>Další kroky

* Přejdete do [studia Azure Machine Learning nebo](https://ml.azure.com) do [poznámkového bloku Pythonu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) a nastavte monitorování datové sady.
* Podívejte se, jak nastavit posun dat u [modelů nasazených ve službě Azure Kubernetes Service](how-to-monitor-data-drift.md).
* Nastavte monitorování posunu datové sady pomocí [mřížky událostí](how-to-use-event-grid.md). 
